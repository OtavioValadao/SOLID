# Princípio da Responsabilidade Única (SRP)
## Single Responsibility Principle - Guia Completo

![SOLID Principles](https://img.shields.io/badge/SOLID-SRP-blue)
![Clean Code](https://img.shields.io/badge/Clean%20Code-Best%20Practices-green)

---

## 📋 Sumário

1. [Definição Abrangente do SRP](#1-definição-abrangente-do-srp)
2. [Diretrizes de Implementação](#2-diretrizes-de-implementação)
3. [Exemplos Práticos e Use Cases](#3-exemplos-práticos-e-use-cases)
4. [Caso Real: Refactoring de God Object](#4-caso-real-refactoring-de-god-object)
5. [Abstrações Conceituais](#5-abstrações-conceituais)
6. [SRP e os Princípios SOLID](#6-srp-e-os-princípios-solid)
7. [Armadilhas Comuns](#7-armadilhas-comuns)
8. [Impacto na Qualidade do Software](#8-impacto-na-qualidade-do-software)

---

## 1. Definição Abrangente do SRP

### 1.1 O Conceito Fundamental

> **"Uma classe deve ter apenas uma razão para mudar."** - Robert C. Martin

O Princípio da Responsabilidade Única estabelece que cada módulo, classe ou função deve ter **responsabilidade sobre uma única parte da funcionalidade** do software, e essa responsabilidade deve estar **completamente encapsulada** pela classe.

### 1.2 Entendendo "Uma Responsabilidade"

Uma **responsabilidade** não é simplesmente "fazer uma coisa", mas sim:

- **Servir a um único ator ou stakeholder** do sistema
- **Responder a uma única fonte de mudanças** nos requisitos de negócio
- **Encapsular uma única razão para existir** no contexto da aplicação

**Exemplo conceitual:**

```
❌ Responsabilidade Múltipla:
   "Gerenciar dados do funcionário, calcular salário e gerar relatórios"
   
✅ Responsabilidade Única:
   "Representar as informações básicas de um funcionário"
```

### 1.3 O Critério: "Um Motivo Para Mudar"

Uma classe possui mais de uma responsabilidade quando existem **múltiplos motivos independentes** que poderiam exigir sua modificação:

| Cenário | Motivos para Mudar | Análise |
|---------|-------------------|---------|
| Classe `Employee` que calcula salário E formata relatório | 1. Mudança na regra de cálculo<br>2. Mudança no formato do relatório | ❌ Viola SRP |
| Classe `Employee` que apenas armazena dados | 1. Mudança nos atributos do funcionário | ✅ Segue SRP |

---

## 2. Diretrizes de Implementação

### 2.1 Identificando Múltiplas Responsabilidades

#### Sinais de Alerta (Code Smells)

1. **Nomes de classe com "E" (AND)**: `UserAndOrderManager`
2. **Métodos não relacionados**: classe com `saveToDatabase()` e `sendEmail()`
3. **Dependências excessivas**: muitos imports de bibliotecas diferentes
4. **Tamanho excessivo**: classes com mais de 200-300 linhas
5. **Múltiplos níveis de abstração**: métodos muito técnicos misturados com lógica de negócio

#### Técnica: Análise de Stakeholders

Pergunte: **"Quem solicitaria mudanças nesta classe?"**

```
Classe: RelatorioPedido

Stakeholders identificados:
- Equipe de TI (persistência de dados)
- Equipe Financeira (cálculos de impostos)
- Equipe de Marketing (formato dos relatórios)
- Equipe de Logística (validação de endereços)

Conclusão: 4 stakeholders = 4 responsabilidades = VIOLA SRP
```

### 2.2 Benefícios da Alta Coesão

**Coesão** é o grau em que os elementos de um módulo pertencem juntos. O SRP promove **alta coesão funcional**.

#### Benefícios Diretos:

- **Manutenibilidade**: mudanças isoladas, menor risco de efeitos colaterais
- **Testabilidade**: testes mais simples e focados
- **Reusabilidade**: componentes especializados são mais reutilizáveis
- **Compreensibilidade**: código mais fácil de entender
- **Paralelização**: equipes podem trabalhar em classes diferentes simultaneamente

---

## 3. Exemplos Práticos e Use Cases

### 3.1 Separação: Persistência vs Lógica de Negócios

#### ❌ Violando o SRP

```java
public class Usuario {
    private String nome;
    private String email;
    private String senha;
    
    // Lógica de Negócio
    public boolean validarSenha(String senha) {
        return this.senha.length() >= 8 && 
               senha.matches(".*[A-Z].*") && 
               senha.matches(".*[0-9].*");
    }
    
    // Persistência
    public void salvar() {
        Connection conn = DriverManager.getConnection("jdbc:mysql://...");
        PreparedStatement stmt = conn.prepareStatement(
            "INSERT INTO usuarios (nome, email, senha) VALUES (?, ?, ?)"
        );
        stmt.setString(1, this.nome);
        stmt.setString(2, this.email);
        stmt.setString(3, this.senha);
        stmt.execute();
        conn.close();
    }
    
    // Formatação de Saída
    public String gerarRelatorioHTML() {
        return "<div>" +
               "<h2>" + this.nome + "</h2>" +
               "<p>Email: " + this.email + "</p>" +
               "</div>";
    }
}
```

**Problemas:**
- Mudança no banco de dados afeta a classe `Usuario`
- Mudança no formato HTML afeta a classe `Usuario`
- Impossível testar lógica de negócio sem banco de dados
- Violação do princípio de separação de concerns

#### ✅ Seguindo o SRP

```java
// RESPONSABILIDADE: Representar um usuário (Entidade de Domínio)
public class Usuario {
    private String nome;
    private String email;
    private String senha;
    
    public Usuario(String nome, String email, String senha) {
        this.nome = nome;
        this.email = email;
        this.senha = senha;
    }
    
    // Apenas getters
    public String getNome() { return nome; }
    public String getEmail() { return email; }
    public String getSenha() { return senha; }
}

// RESPONSABILIDADE: Validar regras de negócio de senha
public class ValidadorSenha {
    private static final int TAMANHO_MINIMO = 8;
    
    public boolean validar(String senha) {
        return senha.length() >= TAMANHO_MINIMO &&
               contemMaiuscula(senha) &&
               contemNumero(senha);
    }
    
    private boolean contemMaiuscula(String senha) {
        return senha.matches(".*[A-Z].*");
    }
    
    private boolean contemNumero(String senha) {
        return senha.matches(".*[0-9].*");
    }
}

// RESPONSABILIDADE: Persistir usuários no banco de dados
public class UsuarioRepository {
    private Connection connection;
    
    public UsuarioRepository(Connection connection) {
        this.connection = connection;
    }
    
    public void salvar(Usuario usuario) {
        try {
            PreparedStatement stmt = connection.prepareStatement(
                "INSERT INTO usuarios (nome, email, senha) VALUES (?, ?, ?)"
            );
            stmt.setString(1, usuario.getNome());
            stmt.setString(2, usuario.getEmail());
            stmt.setString(3, usuario.getSenha());
            stmt.execute();
        } catch (SQLException e) {
            throw new RuntimeException("Erro ao salvar usuário", e);
        }
    }
}

// RESPONSABILIDADE: Formatar usuário como HTML
public class UsuarioHTMLFormatter {
    public String formatarComoHTML(Usuario usuario) {
        return String.format(
            "<div><h2>%s</h2><p>Email: %s</p></div>",
            usuario.getNome(),
            usuario.getEmail()
        );
    }
}
```

**Benefícios alcançados:**
- Cada classe tem uma única razão para mudar
- Fácil substituir banco de dados sem tocar na entidade
- Fácil adicionar novos formatos (JSON, XML) sem tocar na entidade
- Regras de validação podem ser testadas isoladamente

### 3.2 Use Case: Sistema de Processamento de Pedidos

#### ❌ Violando o SRP

```java
cimport java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.util.List;

class Cliente {
    private int id;
    private String email;

    public Cliente(int id, String email) {
        this.id = id;
        this.email = email;
    }

    public int getId() {
        return id;
    }

    public String getEmail() {
        return email;
    }
}

class Item {
    private double preco;
    private int quantidade;

    public Item(double preco, int quantidade) {
        this.preco = preco;
        this.quantidade = quantidade;
    }

    public double getPreco() {
        return preco;
    }

    public int getQuantidade() {
        return quantidade;
    }
}

class Pedido {
    private int id;
    private Cliente cliente;
    private List<Item> items;

    public Pedido(int id, Cliente cliente, List<Item> items) {
        this.id = id;
        this.cliente = cliente;
        this.items = items;
    }

    public int getId() {
        return id;
    }

    public Cliente getCliente() {
        return cliente;
    }

    public List<Item> getItems() {
        return items;
    }
}

class ProcessadorPedido {
    public double processarPedido(Pedido pedido) throws SQLException {
        // Validação
        if (pedido.getItems() == null || pedido.getItems().isEmpty()) {
            throw new IllegalArgumentException("Pedido vazio");
        }

        // Cálculo
        double total = 0;
        for (Item item : pedido.getItems()) {
            total += item.getPreco() * item.getQuantidade();
        }

        // Desconto
        if (total > 1000) {
            total *= 0.9;
        }

        // Imposto
        total *= 1.15;

        // Persistência
        try (Connection conn = DriverManager.getConnection("jdbc:sqlite:pedidos.db")) {
            String sql = "INSERT INTO pedidos (cliente_id, total) VALUES (?, ?)";
            try (PreparedStatement stmt = conn.prepareStatement(sql)) {
                stmt.setInt(1, pedido.getCliente().getId());
                stmt.setDouble(2, total);
                stmt.executeUpdate();
            }
        }

        // Notificação (simulação)
        enviarEmail(pedido.getCliente().getEmail(), "Pedido confirmado: R$ " + total);

        // Log
        System.out.println("Pedido " + pedido.getId() + " processado com sucesso");

        return total;
    }

    private void enviarEmail(String email, String mensagem) {
        // Aqui seria a implementação real de envio de e-mail
        System.out.println("Enviando email para " + email + ": " + mensagem);
    }
}

```

#### ✅ Seguindo o SRP

```python
# RESPONSABILIDADE: Validar pedidos
class ValidadorPedido:
    def validar(self, pedido):
        if not pedido.items:
            raise ValueError("Pedido não pode estar vazio")
        
        for item in pedido.items:
            if item.quantidade <= 0:
                raise ValueError("Quantidade deve ser positiva")

# RESPONSABILIDADE: Calcular valores
class CalculadoraPedido:
    def calcular_subtotal(self, items):
        return sum(item.preco * item.quantidade for item in items)
    
    def aplicar_desconto(self, valor, porcentagem):
        return valor * (1 - porcentagem / 100)
    
    def aplicar_imposto(self, valor, taxa):
        return valor * (1 + taxa / 100)

# RESPONSABILIDADE: Aplicar regras de desconto
class ServicoDesconto:
    def calcular_desconto(self, valor_total):
        if valor_total > 1000:
            return 10  # 10% de desconto
        return 0

# RESPONSABILIDADE: Persistir pedidos
class RepositorioPedido:
    def __init__(self, connection):
        self.connection = connection
    
    def salvar(self, pedido):
        cursor = self.connection.cursor()
        cursor.execute(
            "INSERT INTO pedidos (cliente_id, total) VALUES (?, ?)",
            (pedido.cliente_id, pedido.total)
        )
        self.connection.commit()

# RESPONSABILIDADE: Enviar notificações
class ServicoNotificacao:
    def notificar_pedido_confirmado(self, cliente, total):
        self.enviar_email(
            cliente.email,
            "Pedido Confirmado",
            f"Seu pedido foi confirmado. Total: R$ {total:.2f}"
        )
    
    def enviar_email(self, destinatario, assunto, corpo):
        # Implementação de envio de email
        pass

# RESPONSABILIDADE: Registrar logs
class LoggerPedido:
    def log_sucesso(self, pedido_id):
        print(f"[INFO] Pedido {pedido_id} processado com sucesso")
    
    def log_erro(self, pedido_id, erro):
        print(f"[ERRO] Falha ao processar pedido {pedido_id}: {erro}")

# RESPONSABILIDADE: Orquestrar o processo (Use Case)
class ProcessadorPedido:
    def __init__(self, validador, calculadora, servico_desconto, 
                 repositorio, notificacao, logger):
        self.validador = validador
        self.calculadora = calculadora
        self.servico_desconto = servico_desconto
        self.repositorio = repositorio
        self.notificacao = notificacao
        self.logger = logger
    
    def processar(self, pedido):
        try:
            self.validador.validar(pedido)
            
            subtotal = self.calculadora.calcular_subtotal(pedido.items)
            desconto = self.servico_desconto.calcular_desconto(subtotal)
            total_com_desconto = self.calculadora.aplicar_desconto(
                subtotal, desconto
            )
            total_final = self.calculadora.aplicar_imposto(
                total_com_desconto, 15
            )
            
            pedido.total = total_final
            self.repositorio.salvar(pedido)
            self.notificacao.notificar_pedido_confirmado(
                pedido.cliente, total_final
            )
            self.logger.log_sucesso(pedido.id)
            
            return total_final
            
        except Exception as e:
            self.logger.log_erro(pedido.id, str(e))
            raise
```

---

## 4. Caso Real: Refactoring de God Object

### 4.1 Identificando o "God Object"

Um **God Object** (ou **Blob Class**) é uma classe que conhece ou faz demais, centralizando grande parte da lógica da aplicação.

#### ❌ Exemplo: God Object em Sistema de E-commerce

```typescript
class GerenciadorEcommerce {
    private produtos: Produto[] = [];
    private usuarios: Usuario[] = [];
    private pedidos: Pedido[] = [];
    private conexaoDB: Database;
    private emailService: any;
    private pagamentoAPI: any;
    
    // Gestão de Produtos
    adicionarProduto(produto: Produto) { /*...*/ }
    removerProduto(id: string) { /*...*/ }
    atualizarEstoque(id: string, quantidade: number) { /*...*/ }
    buscarProduto(id: string): Produto { /*...*/ }
    listarProdutosPorCategoria(categoria: string): Produto[] { /*...*/ }
    aplicarDesconto(id: string, percentual: number) { /*...*/ }
    
    // Gestão de Usuários
    registrarUsuario(dados: any) { /*...*/ }
    autenticarUsuario(email: string, senha: string) { /*...*/ }
    atualizarPerfil(id: string, dados: any) { /*...*/ }
    validarCPF(cpf: string): boolean { /*...*/ }
    verificarEmailDuplicado(email: string): boolean { /*...*/ }
    
    // Gestão de Pedidos
    criarPedido(usuarioId: string, items: any[]) { /*...*/ }
    calcularTotal(items: any[]): number { /*...*/ }
    calcularFrete(cep: string): number { /*...*/ }
    aplicarCupomDesconto(codigo: string): number { /*...*/ }
    validarEstoqueDisponivel(items: any[]): boolean { /*...*/ }
    
    // Pagamento
    processarPagamentoCartao(dados: any) { /*...*/ }
    processarPagamentoBoleto(dados: any) { /*...*/ }
    processarPagamentoPix(dados: any) { /*...*/ }
    validarCartao(numero: string): boolean { /*...*/ }
    
    // Notificações
    enviarEmailConfirmacao(email: string, pedido: Pedido) { /*...*/ }
    enviarSMS(telefone: string, mensagem: string) { /*...*/ }
    enviarNotificacaoPush(usuarioId: string, mensagem: string) { /*...*/ }
    
    // Relatórios
    gerarRelatorioVendas(dataInicio: Date, dataFim: Date) { /*...*/ }
    gerarRelatorioProdutosMaisVendidos() { /*...*/ }
    exportarRelatorioCSV(dados: any[]) { /*...*/ }
    
    // Persistência
    salvarNoBanco(entidade: any, tabela: string) { /*...*/ }
    buscarNoBanco(id: string, tabela: string) { /*...*/ }
    atualizarNoBanco(id: string, dados: any, tabela: string) { /*...*/ }
    
    // Logs e Auditoria
    registrarLog(acao: string, usuario: string) { /*...*/ }
    registrarAuditoria(entidade: string, acao: string) { /*...*/ }
}
```

**Problemas identificados:**

1. **Mais de 1000 linhas de código**
2. **Pelo menos 8 responsabilidades distintas**
3. **Múltiplos stakeholders** (TI, Financeiro, Marketing, Logística, etc.)
4. **Impossível testar isoladamente**
5. **Alto acoplamento** entre funcionalidades não relacionadas
6. **Difícil manutenção** (qualquer mudança afeta toda a classe)

### 4.2 Processo de Refactoring

#### Passo 1: Identificar Responsabilidades

| Responsabilidade | Métodos Relacionados | Stakeholder |
|-----------------|---------------------|-------------|
| Gestão de Produtos | adicionarProduto, removerProduto, etc. | Equipe de Produtos |
| Gestão de Usuários | registrarUsuario, autenticar, etc. | Equipe de Segurança |
| Gestão de Pedidos | criarPedido, calcularTotal, etc. | Equipe de Vendas |
| Processamento de Pagamento | processarPagamento*, validarCartao | Equipe Financeira |
| Notificações | enviarEmail, enviarSMS, etc. | Equipe de Comunicação |
| Relatórios | gerarRelatorio*, exportar* | Equipe de BI |
| Persistência | salvarNoBanco, buscarNoBanco, etc. | Equipe de TI |
| Auditoria | registrarLog, registrarAuditoria | Equipe de Compliance |

#### Passo 2: Extrair Classes Especializadas

```typescript
// RESPONSABILIDADE: Gerenciar catálogo de produtos
class CatalogoProdutos {
    constructor(private repositorio: ProdutoRepository) {}
    
    adicionar(produto: Produto): void {
        this.repositorio.salvar(produto);
    }
    
    remover(id: string): void {
        this.repositorio.remover(id);
    }
    
    buscar(id: string): Produto {
        return this.repositorio.buscarPorId(id);
    }
    
    listarPorCategoria(categoria: string): Produto[] {
        return this.repositorio.buscarPorCategoria(categoria);
    }
}

// RESPONSABILIDADE: Gerenciar estoque
class GerenciadorEstoque {
    constructor(private repositorio: EstoqueRepository) {}
    
    atualizar(produtoId: string, quantidade: number): void {
        const estoque = this.repositorio.buscarPorProduto(produtoId);
        estoque.quantidade = quantidade;
        this.repositorio.atualizar(estoque);
    }
    
    verificarDisponibilidade(items: ItemPedido[]): boolean {
        return items.every(item => {
            const estoque = this.repositorio.buscarPorProduto(item.produtoId);
            return estoque.quantidade >= item.quantidade;
        });
    }
    
    reservar(items: ItemPedido[]): void {
        items.forEach(item => {
            const estoque = this.repositorio.buscarPorProduto(item.produtoId);
            estoque.quantidade -= item.quantidade;
            this.repositorio.atualizar(estoque);
        });
    }
}

// RESPONSABILIDADE: Autenticar e gerenciar usuários
class ServicoAutenticacao {
    constructor(
        private repositorio: UsuarioRepository,
        private hasher: PasswordHasher,
        private tokenService: TokenService
    ) {}
    
    registrar(dados: DadosRegistro): Usuario {
        this.validarDados(dados);
        const senhaHash = this.hasher.hash(dados.senha);
        const usuario = new Usuario(dados.nome, dados.email, senhaHash);
        return this.repositorio.salvar(usuario);
    }
    
    autenticar(email: string, senha: string): string {
        const usuario = this.repositorio.buscarPorEmail(email);
        if (!usuario || !this.hasher.verificar(senha, usuario.senhaHash)) {
            throw new Error('Credenciais inválidas');
        }
        return this.tokenService.gerar(usuario.id);
    }
    
    private validarDados(dados: DadosRegistro): void {
        if (!this.validarEmail(dados.email)) {
            throw new Error('Email inválido');
        }
        if (this.repositorio.existeEmail(dados.email)) {
            throw new Error('Email já cadastrado');
        }
    }
    
    private validarEmail(email: string): boolean {
        return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
    }
}

// RESPONSABILIDADE: Calcular valores de pedidos
class CalculadoraPedido {
    calcularSubtotal(items: ItemPedido[]): number {
        return items.reduce(
            (total, item) => total + (item.preco * item.quantidade),
            0
        );
    }
    
    calcularFrete(cep: string, peso: number): number {
        // Lógica de cálculo de frete
        const distancia = this.calcularDistancia(cep);
        return (distancia * 0.5) + (peso * 0.3);
    }
    
    aplicarDesconto(valor: number, cupom: Cupom): number {
        if (cupom.tipo === 'PERCENTUAL') {
            return valor * (1 - cupom.valor / 100);
        }
        return valor - cupom.valor;
    }
    
    private calcularDistancia(cep: string): number {
        // Implementação
        return 100;
    }
}

// RESPONSABILIDADE: Processar diferentes tipos de pagamento
interface ProcessadorPagamento {
    processar(dados: DadosPagamento): ResultadoPagamento;
}

class ProcessadorCartaoCredito implements ProcessadorPagamento {
    processar(dados: DadosPagamento): ResultadoPagamento {
        // Integração com gateway de pagamento
        return { sucesso: true, transacaoId: 'xxx' };
    }
    
    private validarCartao(numero: string): boolean {
        // Algoritmo de Luhn
        return true;
    }
}

class ProcessadorPix implements ProcessadorPagamento {
    processar(dados: DadosPagamento): ResultadoPagamento {
        // Gera QR Code e chave PIX
        return { sucesso: true, qrCode: 'xxx', chave: 'yyy' };
    }
}

class ProcessadorBoleto implements ProcessadorPagamento {
    processar(dados: DadosPagamento): ResultadoPagamento {
        // Gera boleto bancário
        return { sucesso: true, linhaDigitavel: 'xxx', pdf: 'yyy' };
    }
}

// RESPONSABILIDADE: Gerenciar o ciclo de vida de pedidos
class ServicoPedido {
    constructor(
        private repositorio: PedidoRepository,
        private calculadora: CalculadoraPedido,
        private gerenciadorEstoque: GerenciadorEstoque,
        private processadorPagamento: ProcessadorPagamento,
        private notificador: ServicoNotificacao
    ) {}
    
    criar(usuarioId: string, items: ItemPedido[], metodoPagamento: string): Pedido {
        // Validar disponibilidade
        if (!this.gerenciadorEstoque.verificarDisponibilidade(items)) {
            throw new Error('Produtos indisponíveis');
        }
        
        // Calcular valores
        const subtotal = this.calculadora.calcularSubtotal(items);
        const frete = this.calculadora.calcularFrete('30000000', 5);
        const total = subtotal + frete;
        
        // Criar pedido
        const pedido = new Pedido(usuarioId, items, total);
        this.repositorio.salvar(pedido);
        
        // Reservar estoque
        this.gerenciadorEstoque.reservar(items);
        
        // Processar pagamento
        const resultado = this.processadorPagamento.processar({
            pedidoId: pedido.id,
            valor: total,
            metodo: metodoPagamento
        });
        
        if (resultado.sucesso) {
            pedido.marcarComoPago();
            this.notificador.enviarConfirmacao(usuarioId, pedido);
        }
        
        return pedido;
    }
}

// RESPONSABILIDADE: Enviar notificações multicanal
class ServicoNotificacao {
    constructor(
        private emailSender: EmailSender,
        private smsSender: SmsSender,
        private pushSender: PushNotificationSender
    ) {}
    
    enviarConfirmacao(usuarioId: string, pedido: Pedido): void {
        const usuario = this.buscarUsuario(usuarioId);
        
        this.emailSender.enviar({
            para: usuario.email,
            assunto: 'Pedido Confirmado',
            corpo: this.gerarEmailConfirmacao(pedido)
        });
        
        if (usuario.telefone) {
            this.smsSender.enviar(
                usuario.telefone,
                `Pedido ${pedido.id} confirmado!`
            );
        }
    }
    
    private gerarEmailConfirmacao(pedido: Pedido): string {
        // Template de email
        return `Seu pedido ${pedido.id} foi confirmado...`;
    }
    
    private buscarUsuario(id: string): Usuario {
        // Busca usuário
        return null as any;
    }
}

// RESPONSABILIDADE: Gerar relatórios de negócio
class GeradorRelatorios {
    constructor(
        private pedidoRepository: PedidoRepository,
        private exportadorCSV: ExportadorCSV
    ) {}
    
    gerarRelatorioVendas(dataInicio: Date, dataFim: Date): RelatorioVendas {
        const pedidos = this.pedidoRepository.buscarPorPeriodo(
            dataInicio,
            dataFim
        );
        
        return {
            totalVendas: pedidos.reduce((sum, p) => sum + p.total, 0),
            quantidadePedidos: pedidos.length,
            ticketMedio: this.calcularTicketMedio(pedidos),
            produtosMaisVendidos: this.identificarMaisVendidos(pedidos)
        };
    }
    
    exportarCSV(relatorio: RelatorioVendas): string {
        return this.exportadorCSV.exportar(relatorio);
    }
    
    private calcularTicketMedio(pedidos: Pedido[]): number {
        if (pedidos.length === 0) return 0;
        const total = pedidos.reduce((sum, p) => sum + p.total, 0);
        return total / pedidos.length;
    }
    
    private identificarMaisVendidos(pedidos: Pedido[]): ProdutoVendas[] {
        // Implementação
        return [];
    }
}
```

#### Passo 3: Aplicar Dependency Injection

```typescript
// Container de Dependências (exemplo com InversifyJS)
class ContainerEcommerce {
    static configurar(): Container {
        const container = new Container();
        
        // Repositories
        container.bind(ProdutoRepository).toSelf();
        container.bind(UsuarioRepository).toSelf();
        container.bind(PedidoRepository).toSelf();
        container.bind(EstoqueRepository).toSelf();
        
        // Services
        container.bind(CatalogoProdutos).toSelf();
        container.bind(GerenciadorEstoque).toSelf();
        container.bind(ServicoAutenticacao).toSelf();
        container.bind(CalculadoraPedido).toSelf();
        container.bind(ServicoPedido).toSelf();
        container.bind(ServicoNotificacao).toSelf();
        container.bind(GeradorRelatorios).toSelf();
        
        // Pagamento
        container.bind(ProcessadorPagamento)
            .to(ProcessadorCartaoCredito)
            .whenTargetNamed('cartao');
        container.bind(ProcessadorPagamento)
            .to(ProcessadorPix)
            .whenTargetNamed('pix');
        
        return container;
    }
}

// Uso
const container = ContainerEcommerce.configurar();
const servicoPedido = container.get(ServicoPedido);
const pedido = servicoPedido.criar(usuarioId, items, 'cartao');
```

### 4.3 Resultados do Refactoring

| Métrica | Antes (God Object) | Depois (SRP) | Melhoria |
|---------|-------------------|--------------|----------|
| Linhas por classe | ~1200 | ~80-150 | **88% redução** |
| Responsabilidades | 8+ | 1 por classe | **Isolamento completo** |
| Testabilidade | Baixa (mock complexo) | Alta (mocks simples) | **Testes 5x mais rápidos** |
| Tempo para adicionar feature | ~8 horas | ~2 horas | **75% mais rápido** |
| Bugs por mudança | 3-5 | 0-1 | **80% redução** |
| Acoplamento (dependencies) | 15+ | 2-4 | **73% redução** |
| Cobertura de testes | 30% | 85% | **183% aumento** |

---
