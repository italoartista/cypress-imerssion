No Cypress, "commands" (comandos) são uma forma poderosa de criar, encapsular e reutilizar ações e interações comuns na sua aplicação. Ao invés de repetir o mesmo código várias vezes nos seus testes, você pode definir um comando personalizado para realizar uma tarefa específica, tornando seus testes mais limpos e organizados.

### O Que São Commands no Cypress?

No Cypress, um comando é uma função customizada que você cria para realizar uma ação repetitiva. Ele é declarado no arquivo `cypress/support/commands.js` e pode ser reutilizado em vários testes.

Esses comandos são adicionados à API do Cypress e ficam disponíveis em todos os testes, como os comandos nativos (`cy.get`, `cy.click`, `cy.visit`, etc.).

### Criando Commands Customizados

Para criar um comando, você usa `Cypress.Commands.add()`, que recebe dois argumentos:

1. **Nome do comando** (uma string)
2. **Função de implementação** (o que o comando fará)

```javascript
// cypress/support/commands.js

Cypress.Commands.add('nomeDoComando', () => {
  // lógica do comando
});
```

### Exemplos de Commands no Cypress

#### Exemplo 1: Login Automático

Vamos criar um comando para fazer login, algo muito comum nos testes.

```javascript
// cypress/support/commands.js

Cypress.Commands.add('login', (email, senha) => {
  cy.visit('/login');
  cy.get('#email').type(email);
  cy.get('#password').type(senha);
  cy.get('button[type="submit"]').click();
});
```

Agora, em vez de repetir esse código de login em cada teste, você pode simplesmente chamar `cy.login()`:

```javascript
// Um teste que exige login
describe('Teste com login', () => {
  it('deve acessar a página do dashboard', () => {
    cy.login('usuario@exemplo.com', 'senha123');
    cy.url().should('include', '/dashboard');
  });
});
```

#### Exemplo 2: Selecionar um Item por Texto

Outro exemplo é criar um comando para selecionar um item em um dropdown pelo texto. Isso ajuda a evitar duplicação de código para essa tarefa específica.

```javascript
// cypress/support/commands.js

Cypress.Commands.add('selecionarPorTexto', (seletor, texto) => {
  cy.get(seletor).contains(texto).click();
});
```

Você pode usar `cy.selecionarPorTexto` sempre que precisar selecionar um item:

```javascript
// Teste usando o comando customizado de seleção por texto
describe('Teste de seleção', () => {
  it('deve selecionar um item no dropdown', () => {
    cy.visit('/pagina-com-dropdown');
    cy.selecionarPorTexto('#meu-dropdown', 'Opção 1');
    cy.get('#meu-dropdown').should('contain', 'Opção 1');
  });
});
```

#### Exemplo 3: Verificar uma Mensagem de Erro

Vamos criar um comando para verificar a exibição de uma mensagem de erro, o que facilita a verificação em diferentes cenários de erro.

```javascript
// cypress/support/commands.js

Cypress.Commands.add('verificarMensagemErro', (mensagem) => {
  cy.get('.erro-mensagem').should('be.visible').and('contain', mensagem);
});
```

Assim, você pode usar o comando em qualquer lugar que precise verificar uma mensagem de erro:

```javascript
describe('Teste de erro', () => {
  it('deve exibir uma mensagem de erro ao tentar logar com senha errada', () => {
    cy.visit('/login');
    cy.get('#email').type('usuario@exemplo.com');
    cy.get('#password').type('senhaErrada');
    cy.get('button[type="submit"]').click();
    
    cy.verificarMensagemErro('Senha incorreta.');
  });
});
```

### Trabalhando com Cypress Commands Assíncronos

No Cypress, os comandos geralmente são assíncronos. Você pode criar comandos customizados que esperam por outros comandos usando `.then()`:

```javascript
// cypress/support/commands.js

Cypress.Commands.add('preencherFormulario', (dados) => {
  cy.get('#nome').type(dados.nome);
  cy.get('#email').type(dados.email);
  cy.get('#telefone').type(dados.telefone);
  cy.get('form').submit().then(() => {
    cy.get('.mensagem-sucesso').should('be.visible');
  });
});
```

```javascript
// Exemplo de teste usando o comando preencherFormulario
describe('Teste de formulário', () => {
  it('deve preencher e enviar o formulário com sucesso', () => {
    const dados = {
      nome: 'Usuário Teste',
      email: 'usuario@teste.com',
      telefone: '1234567890'
    };
    cy.visit('/formulario');
    cy.preencherFormulario(dados);
  });
});
```

### Resumo

- **Definição:** Commands são funções customizadas que encapsulam ações repetitivas.
- **Localização:** São definidos no arquivo `cypress/support/commands.js`.
- **Uso:** Facilita a reutilização de interações comuns na aplicação.

### Dicas Extras

1. **Prefira nomes intuitivos** para comandos, que indiquem claramente a ação realizada.
2. **Organize seus comandos** caso o número aumente, usando arquivos diferentes ou classes de comandos para diferentes partes da aplicação.

Usar commands no Cypress torna os testes mais expressivos e reutilizáveis, aumentando a legibilidade e a organização dos seus testes.
