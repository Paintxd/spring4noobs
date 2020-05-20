# Update de Produto

#### Para começar iremos fazer uma listagem de produtos, crie em seu produtos controller um metodo que retorne o produtos.html
```java
@GetMapping
public String produtos(Model model) {
    model.addAttribute("produtos", produtosRepo.findAll());
    
    return "produtos";
}
```
#### e seu html com uma table, ja com links para o update
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
  <meta charset="utf-8">
  
  <title>He4rt Construções</title>
</head>
<body>
	<div>
	    <a th:href="@{produto/cadastro}"><button>Cadastrar Produto</button></a>
    	<a th:href="@{estoque}"><button>Lista de Estoque</button></a>
	</div>

	<table>
		<thead>
			<tr>
				<th>Id</th>
				<th>Nome</th>
				<th>Preço</th>
				<th>Update</th>
			</tr>		
		</thead>
		<tbody>
			<tr th:each="produto, i : ${produtos}">
				<td th:text="${produto.id}"></td>
				<td th:text="${produto.nome}"></td>
				<td th:text="${produto.preco}"></td>
				<td><a th:href="@{produto/update/__${produto.id}__}"><button>Atualizar produto</button></a><td>
			</tr>
		</tbody>
	</table>

</body>
</html>
```
> Fizemos tambem alguns links para o nosso form de produto e lista de estoque

#### Agora no controller faremos a rota para _/update_ que sera responsavel pela requisição do nosso form
```java
@GetMapping("/update/{id}")
public String updateForm(@PathVariable Long id, Model model) {
    Produto produto = produtosRepo.getOne(id);
    
    model.addAttribute("produto", produto);
    
    return "produtoUpdate";
}
```
* Repare no _/{id}_, isto é a nossa _@PathVriable_, no caso o id do nosso produto que é passado pela requisição, com este id fizemos a busca no banco de dados, adicionamos ao nosso model e retornamos o html
```html
<form action="#" th:action="@{/produto/update}" th:object="${produto}" method="post">
    <div>
        <div>Nome produto: </div>
        <div><input type="text" th:field="*{nome}" id="nome" th:value="${produto.nome}" /></div>
        <div th:if="${#fields.hasErrors('nome')}" th:errors="*{nome}">No error</div>
    </div>
    <div>
        <div>Preço: </div>
        <div><input type="number" step="any" th:field="*{preco}" id="preco" th:value="${produto.preco}" /></div>
        <div th:if="${#fields.hasErrors('preco')}" th:errors="*{preco}">No error</div>
    </div>
    <input type="hidden" name="id" th:value="${produto.id}" />
    <div>
        <input type="submit" value="Criar">
    </div>
</form>
```
> Um simples form que ja estamos cansados de ver, o que fizemos de diferente é apenas bindar o valor previo do produto para popular o form.

#### Agora em nossa classe responsavel pelo update teremos que fazer algumas coisas a mais
```java
@PostMapping("/update")
@Transactional
public String update(@Valid Produto produtoForm, BindingResult bindingResult, Model model) {

    if(bindingResult.hasErrors()) {
        return "produtoUpdate";
    }
    
    Produto produto = produtosRepo.getOne(produtoForm.getId());
    if(produto.getNome().equals(produtoForm.getNome())) {
        produto.setPreco(produtoForm.getPreco());
        return "redirect:/produto";
    }
    
    List<Produto> produtos = produtosRepo.findAll();
    for (Produto produtoTest : produtos)
        if(produtoTest.getNome().equals(produtoForm.getNome())) {
            bindingResult.addError(new FieldError("produto", "nome", "Nome duplicado"));
            return "produtoUpdate";
        }
    

    produto.setNome(produtoForm.getNome());
    produto.setPreco(produtoForm.getPreco());
    
    return "redirect:/produto";
}
```
* Aqui estamos primeiramente se existe algum erro previo, caso não, pegamos no banco de dados o nosso produto previo com o id que passamos pelo form, checamos se o nome continuou o mesmo, caso seja verdade atualizamos apenas o preco do produto e retornamos para a lista de produtos.
* Apos isso, checamos se o nome foi alterado para o de um produto que ja possuimos cadastrados, caso sim adicionamos o erro e retornamos para o form.
  * caso não ocorra nenhum erro, atualizamos o novo nome e possivel novo preço e retornamos para a lista.

[Proxima seção](./deletes.md)