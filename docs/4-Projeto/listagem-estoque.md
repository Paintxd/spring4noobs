#### Antes de tudo, agora que iremos começar trabalhar mais com modificações e visualização, adicione a dependencia _Spring dev tools_, ela serve para o hot reload de sua aplicação, sempre que você salvar alguma alteração seu projeto ira resetar automaticamente
```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-devtools</artifactId>
  <scope>runtime</scope>
</dependency>
```

# Listagem de estoque

#### Com seus models e seus repositories prontos vá ao package controllers e crie seu _EstoqueController_, como ja sabemos precisamos adicionar a annotation _@Controller_ e só para ficar coerente adicione _@RequestMappig_ para _/estoque_.

#### Agora iremos fazer uso de uma nova annotation _@Autowired_, ela é responsavel por deixar o spring resolver toda parte de injeção de dependencia dos beans relacionados no seu bean

- Ok, mas o que é um bean?
  - Beans são os objetos que formam o esqueleto da sua aplicação e são gerenciados pelo sistema de inversão de controle do spring.
- E o que é inversão de controle?
  - É um processo no qual definimos os nossos objetos sem os criar, deixando essa responsabilidade ao container de inversão de controle.

#### Agora que ja sabemos um pouco mais sobre o conceito, crie um atributo privado estoqueRepo, que sera uma instancia de EstoqueRepository e adicione a annotation _@Autowired_

```java
@Controller
@RequestMapping("/estoque")
public class EstoqueController {

	@Autowired
	private EstoqueRepository estoqueRepo;

}
```

#### Após isso crie um metodo com _@GetMapping_ que ira retornar a nossa view estoque

```java
@GetMapping
public String index(Model model) {

	model.addAttribute("listaEstoque", estoqueRepo.findAll());

	return "estoque";
}
```

#### O _estoqueRepo.findAll()_ nos retorna uma lista com todas as instancias de estoque guardadas em banco, para ter algum retorno va ao seu banco e de um simples insert de produtos e algum destes produtos cadastre ao estoque

```sql
insert into produto (nome, preco) values ('cimento', 35.50);
insert into produto (nome, preco) values ('tijolo', 2.50);

insert into estoque (quantia, produto_id) values (50, 1);
```

#### Agora va ao seus templates e crie o _estoque.html_ com uma table e uma iteração para listar nossos produtos com estoque
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>Lista de Estoque</title>
  </head>
  <body>
    <table>
      <thead>
        <tr>
          <th>Id produto</th>
          <th>Nome do produto</th>
          <th>Preço do produto</th>
          <th>Quantia em estoque</th>
        </tr>
      </thead>
      <tbody>
        <tr th:each="estoque : ${listaEstoque}">
          <td th:text="${estoque.id}"></td>
          <td th:text="${estoque.produto.nome}"></td>
          <td th:text="${estoque.produto.preco}"></td>
          <td th:text="${estoque.quantia}"></td>
        </tr>
      </tbody>
    </table>
  </body>
</html>
```
> Em _estoque.produto.nome_, é como se estivessemos dando um _estoque.getProduto().getNome()_

[Proxima seção](./cadastro-produto.md)
