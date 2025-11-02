# Sistema de Farmácia FarmaJá - Java Console

Sistema de gerenciamento de farmácia desenvolvido em Java para trabalho acadêmico, com navegação por console e foco em backend robusto.

---

## Status do Projeto

### Implementado
- [x] Camada de Database (H2) (Pode ser alterada para outro banco)
- [x] Camada DAO (Data Access Object)
- [x] Testes de Conexão

### Em Desenvolvimento
- [ ] Camada Model
- [ ] Camada Controller
- [ ] Camada View
- [ ] Sistema de Menus

---

## Arquitetura Atual

```
src/
├── database/
│   ├── ConnectionFactory.java          # Interface para conexões
│   ├── H2ConnectionFactory.java        # Implementação H2
│   └── DatabaseConnection.java         # Gerenciador de conexão
│
├── dao/
│   ├── UsuarioDAO.java                 # CRUD de usuários
│   ├── EnderecoDAO.java                # CRUD de endereços
│   ├── FornecedorDAO.java              # CRUD de fornecedores
│   ├── MedicamentoDAO.java             # CRUD de medicamentos
│   ├── PedidoDAO.java                  # CRUD de pedidos
│   ├── ItemPedidoDAO.java              # CRUD de itens do pedido
│   └── HistoricoEntregaDAO.java        # CRUD de histórico
│
└── TestDatabase.java                    # Testes de conexão
```

---

## Banco de Dados

### Tecnologia
- **H2 Database** (Embedded) (Pode ser alterado)
- Arquivo: `pharmacydb.mv.db`
- Modo: File-based
- Console H2: `http://localhost:8082` (se habilitado)

### Tabelas Implementadas

#### 1️ **usuarios**
Armazena administradores, clientes e entregadores.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| id | INT (PK) | Identificador único |
| nome | VARCHAR(100) | Nome completo |
| email | VARCHAR(100) | Email (único) |
| senha | VARCHAR(100) | Senha |
| cpf | VARCHAR(14) | CPF (único) |
| telefone | VARCHAR(15) | Telefone |
| tipo_usuario | VARCHAR(20) | ADMINISTRADOR, CLIENTE, ENTREGADOR |
| ativo | BOOLEAN | Status ativo/inativo |
| data_criacao | TIMESTAMP | Data de cadastro |

**Usuário padrão criado:**
- Email: `admin@farmaja.com`
- Senha: `admin123`
- Tipo: ADMINISTRADOR

---

#### 2️ **enderecos**
Endereços de entrega dos usuários.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| id | INT (PK) | Identificador único |
| usuario_id | INT (FK) | Referência ao usuário |
| rua | VARCHAR(200) | Nome da rua |
| numero | VARCHAR(10) | Número |
| complemento | VARCHAR(100) | Complemento (opcional) |
| bairro | VARCHAR(100) | Bairro |
| cidade | VARCHAR(100) | Cidade |
| estado | VARCHAR(2) | UF |
| cep | VARCHAR(9) | CEP |

---

#### 3️ **fornecedores**
Fornecedores de medicamentos.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| id | INT (PK) | Identificador único |
| nome | VARCHAR(100) | Razão social |
| cnpj | VARCHAR(18) | CNPJ (único) |
| telefone | VARCHAR(15) | Telefone |
| email | VARCHAR(100) | Email |
| ativo | BOOLEAN | Status ativo/inativo |

---

#### 4️ **medicamentos**
Catálogo de produtos.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| id | INT (PK) | Identificador único |
| codigo | VARCHAR(50) | Código do produto (único) |
| nome | VARCHAR(200) | Nome do medicamento |
| descricao | TEXT | Descrição detalhada |
| preco | DECIMAL(10,2) | Preço unitário |
| estoque | INT | Quantidade em estoque |
| estoque_minimo | INT | Estoque mínimo (alerta) |
| fornecedor_id | INT (FK) | Referência ao fornecedor |
| requer_receita | BOOLEAN | Se necessita receita médica |
| ativo | BOOLEAN | Status ativo/inativo |
| data_criacao | TIMESTAMP | Data de cadastro |

---

#### 5️ **pedidos**
Pedidos realizados por clientes.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| id | INT (PK) | Identificador único |
| cliente_id | INT (FK) | Referência ao cliente |
| entregador_id | INT (FK) | Referência ao entregador |
| endereco_id | INT (FK) | Endereço de entrega |
| valor_total | DECIMAL(10,2) | Valor total do pedido |
| status | VARCHAR(30) | Status do pedido |
| forma_pagamento | VARCHAR(30) | Forma de pagamento |
| data_pedido | TIMESTAMP | Data do pedido |
| data_entrega | TIMESTAMP | Data da entrega |
| observacoes | TEXT | Observações adicionais |

**Status possíveis:**
- PENDENTE
- CONFIRMADO
- PREPARANDO
- PRONTO_PARA_ENTREGA
- EM_TRANSPORTE
- ENTREGUE
- CANCELADO

---

#### 6️ **itens_pedido**
Itens de cada pedido.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| id | INT (PK) | Identificador único |
| pedido_id | INT (FK) | Referência ao pedido |
| medicamento_id | INT (FK) | Referência ao medicamento |
| quantidade | INT | Quantidade comprada |
| preco_unitario | DECIMAL(10,2) | Preço unitário no momento |
| subtotal | DECIMAL(10,2) | Quantidade × Preço unitário |

---

#### 7️ **historico_entregas**
Rastreamento de mudanças de status.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| id | INT (PK) | Identificador único |
| pedido_id | INT (FK) | Referência ao pedido |
| entregador_id | INT (FK) | Referência ao entregador |
| status_anterior | VARCHAR(30) | Status anterior |
| status_novo | VARCHAR(30) | Novo status |
| data_atualizacao | TIMESTAMP | Data da mudança |
| observacao | TEXT | Observações |

---

## Funcionalidades dos DAOs

### UsuarioDAO
- `criar(Usuario)` - Cadastra novo usuário
- `buscarPorId(int)` - Busca por ID
- `buscarPorEmail(String)` - Busca por email
- `buscarPorCpf(String)` - Busca por CPF
- `buscarTodos()` - Lista todos os usuários
- `buscarPorTipo(String)` - Filtra por tipo (admin/cliente/entregador)
- `atualizar(Usuario)` - Atualiza dados
- `deletar(int)` - Remove usuário
- `ativarDesativar(int, boolean)` - Ativa/desativa usuário
- `autenticar(String, String)` - Login (email + senha)

### EnderecoDAO
- `criar(Endereco)` - Cadastra endereço
- `buscarPorId(int)` - Busca por ID
- `buscarPorUsuario(int)` - Lista endereços do usuário
- `atualizar(Endereco)` - Atualiza endereço
- `deletar(int)` - Remove endereço

### FornecedorDAO
- `criar(Fornecedor)` - Cadastra fornecedor
- `buscarPorId(int)` - Busca por ID
- `buscarPorCnpj(String)` - Busca por CNPJ
- `buscarTodos()` - Lista todos
- `buscarAtivos()` - Lista apenas ativos
- `atualizar(Fornecedor)` - Atualiza dados
- `deletar(int)` - Remove (valida se há medicamentos vinculados)
- `ativarDesativar(int, boolean)` - Ativa/desativa

### MedicamentoDAO
- `criar(Medicamento)` - Cadastra medicamento
- `buscarPorId(int)` - Busca por ID
- `buscarPorCodigo(String)` - Busca por código
- `buscarTodos()` - Lista todos
- `buscarAtivos()` - Lista apenas ativos
- `buscarPorNome(String)` - Busca por nome (LIKE)
- `buscarEstoqueBaixo()` - Lista medicamentos com estoque ≤ mínimo
- `atualizar(Medicamento)` - Atualiza dados
- `atualizarEstoque(int, int)` - Incrementa/decrementa estoque
- `deletar(int)` - Remove medicamento
- `ativarDesativar(int, boolean)` - Ativa/desativa

### PedidoDAO
- `criar(Pedido)` - Cria novo pedido
- `buscarPorId(int)` - Busca por ID
- `buscarTodos()` - Lista todos
- `buscarPorCliente(int)` - Pedidos do cliente
- `buscarPorEntregador(int)` - Pedidos do entregador
- `buscarPorStatus(String)` - Filtra por status
- `buscarPendentesAtribuicao()` - Pedidos sem entregador
- `atualizar(Pedido)` - Atualiza pedido
- `atualizarStatus(int, String)` - Muda apenas o status
- `atribuirEntregador(int, int)` - Atribui entregador ao pedido
- `deletar(int)` - Remove pedido

### ItemPedidoDAO
- `criar(ItemPedido)` - Adiciona item ao pedido
- `buscarPorId(int)` - Busca por ID
- `buscarPorPedido(int)` - Lista itens do pedido
- `atualizar(ItemPedido)` - Atualiza item
- `deletar(int)` - Remove item
- `deletarPorPedido(int)` - Remove todos os itens do pedido

### HistoricoEntregaDAO
- `criar(HistoricoEntrega)` - Registra mudança de status
- `buscarPorId(int)` - Busca por ID
- `buscarPorPedido(int)` - Histórico do pedido
- `buscarPorEntregador(int)` - Histórico do entregador
- `buscarTodos()` - Lista todo o histórico
- `deletar(int)` - Remove registro

---

## Como Usar

### Pré-requisitos
- Java 24
- Maven (para gerenciar dependências)

### Dependências (pom.xml)
```xml
<dependencies>
  <!-- H2 Database -->
  <dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <version>2.2.224</version>
  </dependency>
</dependencies>
```

---

## 🧪 Testes

### Executar Teste de Conexão
```bash
java TestDatabase
```

**Saída esperada:**
```
=== TESTE DE CONEXÃO COM BANCO DE DADOS ===

1. Inicializando banco de dados...
✓ Banco de dados inicializado com sucesso!

2. Testando conexão...
✓ Conexão estabelecida com sucesso!
   Database: H2
   Versão: 2.x.x

3. Verificando tabelas criadas...
   ✓ Tabela 'usuarios' existe (registros: 1)
   ✓ Tabela 'enderecos' existe (registros: 0)
   ✓ Tabela 'fornecedores' existe (registros: 0)
   ✓ Tabela 'medicamentos' existe (registros: 0)
   ✓ Tabela 'pedidos' existe (registros: 0)
   ✓ Tabela 'itens_pedido' existe (registros: 0)
   ✓ Tabela 'historico_entregas' existe (registros: 0)

4. Verificando usuário administrador padrão...
   ✓ Usuário admin encontrado:
      ID: 1
      Nome: Administrador
      Email: admin@farmaja.com
      CPF: 000.000.000-00
      Tipo: ADMINISTRADOR
      Ativo: true

=== TODOS OS TESTES PASSARAM COM SUCESSO! ===
```

---

## Funcionalidades Planejadas

### Tipos de Usuário
- **Administrador**: Gerencia usuários, medicamentos, fornecedores, pedidos e relatórios
- **Cliente**: Navega catálogo, adiciona ao carrinho, finaliza pedidos, acompanha entregas
- **Entregador**: Visualiza entregas atribuídas, aceita entregas, atualiza status

### Menus do Sistema
1. **Menu Login**: Login, Registrar cliente, Sair
2. **Menu Admin**: Gerenciar usuários/medicamentos/fornecedores, atribuir entregadores, relatórios
3. **Menu Cliente**: Catálogo, buscar medicamentos, carrinho, meus pedidos, perfil
4. **Menu Entregador**: Ver entregas, aceitar, atualizar status, histórico

---

## Padrões de Código

### Convenções
- Padrão DAO sem interfaces (modelo simplificado)
- Exceções com `RuntimeException`
- Try-with-resources para conexões
- Métodos helper privados para mapear ResultSet
- Validações antes de deletar registros relacionados

### Tratamento de Erros
```java
try {
    // operação
} catch (SQLException e) {
    throw new RuntimeException("Mensagem descritiva: " + e.getMessage(), e);
}
```

---

## Equipe de Desenvolvimento

- **Pessoa 1**: Database & DAO **CONCLUÍDO**
- **Pessoa 2**: Model & Regras de Negócio **PRÓXIMA ETAPA**
- **Pessoa 3**: Controller & View (Menus)
- **Pessoa 4**: Integração & Testes

---

## Próximos Passos

1. Implementar classes Model (Usuario, Endereco, Fornecedor, etc.)
2. Criar validações de negócio nas classes Model
3. Implementar camada Controller
4. Criar sistema de menus (View)
5. Integrar todas as camadas
6. Testes completos do sistema

---

Projeto acadêmico - Uso educacional

---

**Status:** Em Desenvolvimento | **Versão:** 0.1.0 (Database & DAO)