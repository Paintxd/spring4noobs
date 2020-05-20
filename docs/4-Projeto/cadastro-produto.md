# Cadastro de Produto

#### Antes de tudo, precisamos criar as condições para validação do nosso model produto, adicionaremos algumas annotations
```java
@Column(unique = true)
@Size(max = 20, min = 3, message = "Min 3 max 20 caracteres")
@NotBlank(message = "Obrigatorio Nome")
private String nome;

@Column()
@NotNull(message = "Obrigatorio Preço")
@Positive(message = "Valor deve ser positivo e maior que 0")
private Double preco;
```
* nome
  * Size: Quantia minima e maxima de caracteres o nome do produto deve ter
  * NotBlank: nome deve possuir ao menos 1 caractere alem de espaços em branco
* preco
  * NotNull: preco não pode ser nulo
  * Positive: Valor deve ser positivo e maior que 0
> message é a mensagem que sera exibida caso estas condicionais não sejam satisfeitas

#### Agora vamos criar o _ProdutoController_, com o _@Autowired_ do _ProdutoRepository_.
> Lembre-se sempre de anotar seus controllers com o _@Controller_, e se quiser, fazer um _@RequestMapping_ para uma rota de preferencia.
```java
@Controller
@RequestMapping("/produto")
public class ProdutoController {

	@Autowired
	private ProdutoRepository  produtosRepo;

}
```
#### Agora entra uma logica muito importante, precisamos primeiro de uma rota _Get_ para carregar nosso form com seus dado.
```java
@GetMapping("/cadastro")
public String form(Model model) {
	model.addAttribute("produto", new Produto());
	return "produtoForm";
}
```
#### No html _produtoForm_ teremos um form com algumas tags do thymeleaf
```html
<form action="#" th:action="@{/produto/cadastro}" th:object="${produto}" method="post">
    <div>
        <div>Nomde produto: </div>
        <div><input type="text" th:field="*{nome}" id="nome" /></div>
        <div th:if="${#fields.hasErrors('nome')}" th:errors="*{nome}">No error</div>
    </div>
    <div>
        <div>Preço: </div>
        <div><input type="number" step="any" th:field="*{preco}" id="preco" placeholder="53.50" /></div>
        <div th:if="${#fields.hasErrors('preco')}" th:errors="*{preco}">No error</div>
    </div>
    <div>
        <input type="submit" value="Criar">
    </div>
</form>
```
* form
  * th:action: A rota para qual faremos o post
  * th:object: O objeto qual nós enviaremos, por este motivo temos que enviar um _new Produto()_ na  instanciação do form
* inputs
  * th:field: O campo do objeto que esta sendo passado neste input
  * fields.hasErrors: A validação que é feita por meio das _annotations_ que colocamos em nosso model produto

#### Agora retorne para o controller e crie um metodo com _@PostMapping("/cadastro")_, aqui teremos algumas coisas novas
```java
@PostMapping("/cadastro")
public String cadastro(@Valid Produto produto, BindingResult bindingResult, Model model) {
    
    if(bindingResult.hasErrors()) {
        return "produtoForm";
    }

		List<Produto> produtos = produtosRepo.findAll();
		for (Produto produtoTest : produtos)
			if(produtoTest.getNome().equals(produto.getNome())) {
				bindingResult.addError(new FieldError("produto", "nome", "Nome duplicado"));
				return "produtoUpdate";
			}
    
    return "redirect:/estoque";
}
```
* Valid: verifica se todas as condicionais foram satisfeitas no form passado
* BindingResult: esta interface é responsavel por pegar os resultados do form e utilizamos ela para verificar se existe algum erro
  * bindingResult.hasErrors: Caso ocorra algum erro forçamos o retorno para o produtoForm exibindo agora as mensagens de erro de uma forma elegante
* produtoRepo.save: responsavel por guardar nossos dados no banco de dados.
* Como não existe uma annotation para fazer a verificação da condicional que colocamos no banco para o nome do produto ser unico, fazemos este for, caso algum dos nomes cadastrados seja igual ao do form criamos um novo _field error_ e retornamos a mensagem de este produto ja existe.

[Proxima seção](./cadastro-update-estoque.md)