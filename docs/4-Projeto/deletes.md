# Deletes

#### Aqui faremos o delete apenas no estoque, pois se um item foi deletado do estoque ele não sera mais vendido, então em seu estoque.html após o form de update criaremos um um td com condicional
```html
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
            <td th:if="${estoque.quantia} == 0">
                <a th:href="@{estoque/delete/__${estoque.id}__}"><button>Deletar produto</button></a>
            </td>
        </tr>
    </tbody>
</table>
```
* Aqui checamos, caso a quantia de produtos em estoque seja 0 teremos o botão com opção para deletar este produto
#### E em nosso estoque controller
```java
@GetMapping("/delete/{id}")
public String delete(@PathVariable Long id) {
    
    estoqueRepo.deleteById(id);
    produtosRepo.deleteById(id);
    
    return "redirect:/estoque";
}
```
> Como falamos antes, caso um produto seja deletado do estoque, ele também sera deletado da lista de produtos.

## Finalização

#### Agora que estamos com nossa aplicação basica pronta, sinta-se a vontade para adicionar seu css ou fazer alterações, nos topicos futuros devemos falar sobre autenticação e mais alguns topicos avançados, nos vemos lá!

[Proximo Topico](#)