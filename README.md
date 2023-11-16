# clean-architecture

## SRP no contexto da Clean Architecture.

### Definição do SRP no Contexto da Clean Architecture
No contexto da Clean Architecture, o SRP é aplicado em vários níveis. Em um nível mais macro, a arquitetura é dividida em camadas, como a de apresentação, lógica de negócios e dados. Cada camada tem uma responsabilidade claramente definida e não deve se sobrepor. Por exemplo, a camada de apresentação lida apenas com a interface do usuário, enquanto a camada de lógica de negócios se concentra apenas na lógica específica do domínio.

Em um nível mais micro, como na definição de classes e funções, o SRP é aplicado para garantir que cada componente tenha uma única responsabilidade. Uma classe deve ter apenas um motivo para ser modificada. Se uma classe está fazendo mais do que uma única tarefa, ela está violando o SRP.

Ao abordar o SRP no contexto da Clean Architecture, é crucial destacar como a separação de responsabilidades em diferentes camadas e componentes de software promove a manutenibilidade, a flexibilidade e a testabilidade do sistema como um todo.

## Problemas que podem ser resolvidos com SRP.

### Subtema 1: Duplicação Acidental
A duplicação acidental ocorre quando o mesmo pedaço de lógica é repetido em várias partes do código. Isso pode acontecer quando uma classe ou função tem múltiplas responsabilidades. Por exemplo, se a validação de dados é feita em vários lugares diferentes, há uma duplicação acidental desse código de validação. Ao seguir o SRP, a validação pode ser encapsulada em uma única classe ou função, evitando a duplicação e facilitando futuras alterações na validação.


```python
# Duplicação Acidental com diferentes requisitos de validação

# Ator 1: Gerente de Cadastro de Usuários
def validar_email_gerente_cadastro(email):
	# Verifica se o email possui um formato válido para cadastro
	return "@" in email and ".com" in email

# Ator 2: Analista de Comunicação
def validar_email_analista_comunicacao(email):
	# Verifica se o email possui um formato válido para envio de mensagens
	return "@" in email and len(email) > 5

# Ator 3: Administrador de Contas
def validar_email_admin_contas(email):
	# Verifica se o email possui um formato válido para configurações de conta
	return "@" in email and not email.endswith(".org")

# Utilização das funções pelos diferentes atores

# No departamento de cadastro de usuários
def cadastrar_usuario(email, senha):
	if validar_email_gerente_cadastro(email):
    	# Lógica para cadastrar usuário
    	pass
	else:
    	print("Email inválido para cadastro!")

# No departamento de comunicação
def enviar_mensagem(email, mensagem):
	if validar_email_analista_comunicacao(email):
    	# Lógica para enviar mensagem
    	pass
	else:
    	print("Email inválido para envio de mensagem!")

# No departamento de administração de contas
def alterar_email_conta(email_atual, novo_email):
	if validar_email_admin_contas(novo_email):
    	# Lógica para alterar o email da conta
    	pass
	else:
    	print("Novo email inválido para configurações de conta!")
```

Neste exemplo, temos o Gerente de Cadastro de Usuários, o Analista de Comunicação e o Administrador de Contas, cada um responsável por validar emails de acordo com os requisitos específicos de sua área na empresa. Isso ilustra como diferentes departamentos ou cargos podem necessitar de validações distintas para o mesmo conceito (como validação de email), levando à duplicação acidental de lógica de validação.

#### Possivel solução
Para resolver o problema de duplicação acidental de lógica de validação de email, podemos utilizar abordagens de refatoração e design mais flexível, como a criação de uma única função de validação reutilizável ou a utilização de estratégias de validação configuráveis. Vou apresentar uma possível solução utilizando uma função de validação genérica com estratégias configuráveis:

```python
# Função de validação genérica para email
def validar_email(email, strategy):
	# Usa a estratégia específica para validar o email
	return strategy(email)

# Estratégias de validação para diferentes departamentos/cargos
def validar_email_gerente_cadastro(email):
	return "@" in email and ".com" in email

def validar_email_analista_comunicacao(email):
	return "@" in email and len(email) > 5

def validar_email_admin_contas(email):
	return "@" in email and not email.endswith(".org")

# Utilização das estratégias de validação

# No departamento de cadastro de usuários
def cadastrar_usuario(email, senha):
	if validar_email(email, validar_email_gerente_cadastro):
    	# Lógica para cadastrar usuário
    	pass
	else:
    	print("Email inválido para cadastro!")

# No departamento de comunicação
def enviar_mensagem(email, mensagem):
	if validar_email(email, validar_email_analista_comunicacao):
    	# Lógica para enviar mensagem
    	pass
	else:
    	print("Email inválido para envio de mensagem!")

# No departamento de administração de contas
def alterar_email_conta(email_atual, novo_email):
	if validar_email(novo_email, validar_email_admin_contas):
    	# Lógica para alterar o email da conta
    	pass
	else:
    	print("Novo email inválido para configurações de conta!")
```

Nesta abordagem, criamos uma função `validar_email` genérica que recebe uma estratégia específica de validação como argumento. Cada estratégia de validação é uma função separada que implementa os critérios de validação específicos para cada departamento ou cargo.

Isso evita a duplicação de lógica, pois todas as áreas usam a mesma função `validar_email`, mas passam estratégias diferentes com base em seus requisitos. Se os requisitos de validação mudarem, é necessário modificar apenas a estratégia de validação correspondente, mantendo a consistência em todo o sistema.



### Subtema 2: Fusões de Responsabilidade
A fusão de responsabilidades é o oposto da duplicação acidental. Isso acontece quando múltiplas responsabilidades são agrupadas em uma única classe ou função, tornando-a excessivamente complexa e difícil de entender. Por exemplo, uma classe que lida com a lógica de negócios, interação com o banco de dados e manipulação da interface do usuário está fundindo várias responsabilidades em um único lugar. Ao aplicar o SRP, essas responsabilidades podem ser separadas em diferentes componentes, tornando o sistema mais flexível e mais fácil de manter.







#### Exemplo de Fusão de Responsabilidade:

Suponha que temos uma classe `GestorTarefas` que lida com várias responsabilidades: adicionar uma tarefa, salvar no banco de dados e enviar notificações sobre a tarefa adicionada.

```python
class GestorTarefas:
	def __init__(self, banco_dados):
    	self.banco_dados = banco_dados

	def adicionar_tarefa(self, nome, descricao):
    	# Lógica para adicionar tarefa à lista
    	# Lógica para salvar a tarefa no banco de dados
    	self.banco_dados.salvar_tarefa(nome, descricao)
    	# Lógica para enviar notificação sobre a nova tarefa
    	self.enviar_notificacao(nome)

	def enviar_notificacao(self, nome):
    	# Lógica para enviar notificação
    	print(f"Tarefa '{nome}' adicionada!")
```

Esta classe está fundindo responsabilidades ao lidar com a lógica de adicionar uma tarefa, salvá-la no banco de dados e enviar notificações. Isso viola o princípio da separação de preocupações da Clean Architecture.

#### Possivel Solução:
Uma maneira de resolver isso seria dividir essas responsabilidades em diferentes camadas da Clean Architecture, como a camada de lógica de negócios e a de infraestrutura, por exemplo.

```python
# Camada de Lógica de Negócios
class GestorTarefas:
	def __init__(self, tarefa_service):
    	self.tarefa_service = tarefa_service

	def adicionar_tarefa(self, nome, descricao):
    	self.tarefa_service.adicionar(nome, descricao)

# Camada de Infraestrutura (Banco de Dados e Notificações)
class TarefaService:
	def __init__(self, banco_dados, notificador):
    	self.banco_dados = banco_dados
    	self.notificador = notificador

	def adicionar(self, nome, descricao):
    	self.banco_dados.salvar_tarefa(nome, descricao)
    	self.notificador.enviar_notificacao(nome)

class BancoDeDados:
	def salvar_tarefa(self, nome, descricao):
    	# Lógica para salvar tarefa no banco de dados
    	pass

class Notificador:
	def enviar_notificacao(self, nome):
    	# Lógica para enviar notificação
    	print(f"Tarefa '{nome}' adicionada!")
```

Nessa abordagem, a classe `GestorTarefas` na camada de lógica de negócios delega a responsabilidade de adicionar tarefas ao `TarefaService`, que por sua vez se encarrega de interagir com o banco de dados e o notificador. Isso separa claramente as responsabilidades e mantém a classe `GestorTarefas` focada em ações de alto nível relacionadas à gestão de tarefas, sem se preocupar com a implementação específica de salvar no banco ou enviar notificações.
