# Cadastro e Update de Estoque

#### Antes de tudo, também fazeremos a adição de algumas annotations no model produto
```java
@NotNull(message = "Estoque deve possuir uma quantia")
@Min(value = 0, message = "Quantia deve ser no minimo 1")
@Column()
private Long quantia;
```
> Aqui so tivemos a aparição de uma nova validação _@Min_, ela serve para indicar qual deve ser o menor valor que este campo tera.

#### Agora vamos para o estoque controller e de um _@Autowired_ no _ProdutosRepository_, faremos uso para criar nosso form de estoque. Crie agora o metodo para o form
```java
@GetMapping("/cadastro")
public String form(Model model, Estoque estoque) {
    List<Estoque> estoqueList = estoqueRepo.findAll();
    List<Produto> produtos = produtosRepo.findAll();

    estoqueList.forEach(e -> produtos.remove(e.getProduto()));

    if (produtos.isEmpty()) {
        return "redirect:/estoque";
    }

    model.addAttribute("estoque", estoque);
    model.addAttribute("produtos", produtos);

    return "estoqueForm";
}
```
* Primeiramente estamos dando um findAll para pegar todos os nossos produtos e estoque.
  * Agora fazemos um forEach na lista de estoque removendo da lista de produtos os produtos que ja estão cadastrados em estoque, caso esta lista fique vazia, retornamos para a lista de estoque, pois não existem mais produtos para serem cadastrados, caso tudo esteja ok, adicionamos nosso estoque e a lista de produtos ao form.

#### Agora o nosso estoqueForm
```html
<form action="#" th:action="@{/estoque/cadastro}" th:object="${estoque}" method="post">
    <div>
        <div>Selecione produto:</div>
        <div>
            <select th:field="*{produto}">
                <option th:each="produto : ${produtos}" th:value="${produto.id}" th:text="${produto.nome}"></option>
            </select>
        </div>
        <div th:if="${#fields.hasErrors('produto')}" th:errors="*{produto}">No error</div>
    </div>
    <div>
        <div>Quantia em estoque:</div>
        <div><input type="number" th:field="*{quantia}" placeholder="100" /></div>
        <div th:if="${#fields.hasErrors('quantia')}" th:errors="*{quantia}">No error</div>
    </div>
    <div>
        <button type="submit">Cadastrar</button>
    </div>
</form>
```
* Aqui de diferente temos o _select/option_, aqui é onde usamos a nossa lista filtrada de produtos para fazer um simples dropdown

## Update do estoque

#### Retornando ao _estoque.html_ faremos algumas modificações para permitir o update da quantia de produtos em estoque
```html
<div>
    <a th:href="@{estoque/cadastro}"><button>Cadastrar estoque</button></a>
    <a th:href="@{produto/cadastro}"><button>Cadastrar Produto</button></a>
</div><br>

<table>
    <tbody>
        <tr th:each="estoque, i : ${listaEstoque}" >
            <td><form th:action="@{estoque/update}" method="post">
            <table>
            <tbody>
            <tr>
                <th th:text="${estoque.id}"></th>
                <td th:text="${estoque.produto.nome}"></td>
                <td th:text="${estoque.produto.preco}"></td>
                <td><div>
                    <input type="number" th:value="${estoque.quantia}" name="quantiaEstoque">
                    <button type="submit" value="submit">Atualizar</button>
                </div></td>
                <td><input type="hidden" name="id" th:value="${estoque.id}" /></td>
            </tr>
            </tbody>
            </table>
            </form></td>
        </tr>
    </tbody>
</table>
```
#### Assim deve ficar a estrutura interna da nossa lista de estoque, fizemos algumas modificações:
* Adição de botões para os forms
* Adição de um form interno para atualizar na mesma pagina de listagem a quantidade de produtos em estoque
* Removemos o thead por incompatibilidade na estrutura de form interna que fizemos.

#### Estrutura no controller de estoque para receber o update
```java
@PostMapping("/update")
@Transactional
public String update(@RequestParam Long id, @RequestParam Long quantiaEstoque) {

    if (quantiaEstoque < 0) {
        return "redirect:/estoque";
    }

    Estoque estoque = estoqueRepo.getOne(id);
    estoque.setQuantia(quantiaEstoque);

    return "redirect:/estoque";
}
```
* Temos aqui a apresentação da annotation _@Transactional_, ela permite ao spring entender que, antes de sair no return ela precisa esperar a transação com o banco de dados ser finalizada, dando um ar de assincronismo.
* Temos tambem uma validação para não permitir quantias negativas em estoque.
* Aqui também fazemos nosso primeiro update, tendo que ser desta forma
  * Produramos a entidade a ser atualizada em banco, após isso damos um set no/nos campo(s) que desejamos atualizar.


[Proxima seção](./update-produto.md)