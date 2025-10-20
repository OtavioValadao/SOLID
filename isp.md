# Princípio da Segregação de Interface (ISP)
## Interface Segregation Principle - Guia Completo

![SOLID Principles](https://img.shields.io/badge/SOLID-ISP-blue)
![Clean Code](https://img.shields.io/badge/Clean%20Code-Best%20Practices-green)

---

## 📋 Sumário

1. [Definição Abrangente do ISP](#1-definição-abrangente-do-isp)
2. [Diretrizes de Implementação](#2-diretrizes-de-implementação)
3. [Exemplos Práticos e Use Cases](#3-exemplos-práticos-e-use-cases)
4. [Caso Real: Refactoring de Interface Monolítica](#4-caso-real-refactoring-de-interface-monolítica)
5. [Abstrações Conceituais](#5-abstrações-conceituais)
6. [ISP e os Princípios SOLID](#6-isp-e-os-princípios-solid)
7. [Armadilhas Comuns](#7-armadilhas-comuns)
8. [Impacto na Qualidade do Software](#8-impacto-na-qualidade-do-software)

---

## 1. Definição Abrangente do ISP

### 1.1 O Conceito Fundamental

> **"Nenhum cliente deve ser forçado a depender de métodos que não utiliza."** - Robert C. Martin

O Princípio da Segregação de Interface estabelece que **interfaces grandes e genéricas devem ser divididas em interfaces menores e mais específicas**, para que os clientes implementem apenas os métodos de que realmente precisam.

### 1.2 Entendendo "Segregação de Interface"

**Segregação** significa dividir interfaces "gordas" (fat interfaces) em interfaces "magras" (thin interfaces) baseadas em:

- **Coesão funcional**: agrupar métodos relacionados
- **Necessidades dos clientes**: cada cliente vê apenas o que precisa
- **Responsabilidades específicas**: cada interface tem um propósito claro
- **Contextos de uso**: separar por casos de uso diferentes

**Exemplo conceitual:**

```
❌ Interface Gorda:
   interface Trabalhador {
       trabalhar()
       comer()
       dormir()
       receberSalario()
       tirarFerias()
   }
   
✅ Interfaces Segregadas:
   interface Empregado { trabalhar(), receberSalario() }
   interface SerVivo { comer(), dormir() }
   interface Beneficiario { tirarFerias() }
```

### 1.3 O Problema das Interfaces Gordas

Interfaces grandes criam **acoplamento desnecessário** e **poluição de interface**:

| Problema | Sem ISP | Com ISP |
|----------|---------|---------|
| Implementações forçadas | Classes implementam métodos que não usam | Classes implementam apenas o necessário |
| Acoplamento | Cliente depende de toda a interface | Cliente depende apenas do que usa |
| Manutenção | Mudança afeta todos os implementadores | Mudança afeta apenas implementadores relevantes |
| Clareza | Interface confusa com muitos métodos | Interfaces claras e focadas |
| Testabilidade | Mocks complexos | Mocks simples e específicos |

---

## 2. Diretrizes de Implementação

### 2.1 Identificando Violações do ISP

#### Sinais de Alerta (Code Smells)

1. **Métodos vazios ou com NotImplementedException**: 
   ```java
   void metodo() { throw new UnsupportedOperationException(); }
   ```

2. **Implementações que não fazem nada**:
   ```java
   void metodo() { /* vazio */ }
   void outroMetodo() { return; }
   ```

3. **Comentários indicando "não aplicável"**:
   ```java
   // Este método não se aplica a esta classe
   void metodo() { }
   ```

4. **Interface com muitos métodos** (> 5-7 métodos)

5. **Métodos não relacionados** na mesma interface

6. **Cliente usa apenas parte da interface**

7. **Condicionais para verificar se método é suportado**:
   ```java
   if (objeto.suportaMetodoX()) {
       objeto.metodoX();
   }
   ```

#### Técnica: Análise de Coesão de Interface

Pergunte: **"Todos os clientes desta interface usam todos os seus métodos?"**

```
Interface: Impressora

Métodos:
- imprimir()
- escanear()
- fax()
- imprimirDuplex()

Clientes:
- ImpressoraSimples: usa apenas imprimir()          ❌ 25% de uso
- ImpressoraMultifuncional: usa todos               ✅ 100% de uso
- ImpressoraBasica: usa imprimir() e imprimirDuplex() ❌ 50% de uso

Conclusão: VIOLA ISP - interface deve ser segregada
```

### 2.2 Estratégias de Segregação

#### Estratégia 1: Segregação por Papel (Role Interface)

Dividir interface baseada em **papéis ou responsabilidades diferentes**:

```java
// ❌ Interface gorda
interface Funcionario {
    void trabalhar();
    void receberSalario();
    void gerenciarEquipe();
    void aprovarFerias();
    void darFeedback();
    void contratarPessoas();
}

// ✅ Interfaces segregadas por papel
interface Trabalhador {
    void trabalhar();
}

interface Assalariado {
    void receberSalario();
}

interface Gerente {
    void gerenciarEquipe();
    void aprovarFerias();
    void darFeedback();
}

interface Recrutador {
    void contratarPessoas();
}

// Uso: Classes implementam apenas os papéis relevantes
class Desenvolvedor implements Trabalhador, Assalariado {
    // Implementa apenas o que precisa
}

class GerenteTecnico implements Trabalhador, Assalariado, Gerente {
    // Implementa papéis de gerente
}
```

#### Estratégia 2: Segregação por Cliente

Dividir baseado em **quem usa** a interface:

```typescript
// ❌ Interface única para todos
interface RepositorioUsuario {
    salvar(usuario: Usuario): void;
    atualizar(usuario: Usuario): void;
    deletar(id: number): void;
    buscarPorId(id: number): Usuario;
    listarTodos(): Usuario[];
    buscarPorEmail(email: string): Usuario;
    buscarPorNome(nome: string): Usuario[];
    contarTotal(): number;
    existeEmail(email: string): boolean;
}

// ✅ Segregado por tipo de cliente
// Para operações de escrita
interface EscritorUsuario {
    salvar(usuario: Usuario): void;
    atualizar(usuario: Usuario): void;
    deletar(id: number): void;
}

// Para consultas simples
interface LeitorUsuario {
    buscarPorId(id: number): Usuario;
    buscarPorEmail(email: string): Usuario;
}

// Para consultas complexas (relatórios)
interface ConsultorUsuario {
    listarTodos(): Usuario[];
    buscarPorNome(nome: string): Usuario[];
    contarTotal(): number;
}

// Para validações
interface ValidadorUsuario {
    existeEmail(email: string): boolean;
}

// Clientes usam apenas o que precisam
class ServicoAutenticacao {
    constructor(private leitor: LeitorUsuario) {} // Só leitura
    
    autenticar(email: string, senha: string): Usuario {
        const usuario = this.leitor.buscarPorEmail(email);
        // ...
    }
}

class ServicoCadastro {
    constructor(
        private escritor: EscritorUsuario,
        private validador: ValidadorUsuario
    ) {}
    
    cadastrar(dados: DadosUsuario): void {
        if (this.validador.existeEmail(dados.email)) {
            throw new Error("Email já existe");
        }
        this.escritor.salvar(new Usuario(dados));
    }
}
```

#### Estratégia 3: Segregação por Capacidade

Dividir baseado em **capacidades opcionais**:

```python
# ❌ Interface com capacidades opcionais misturadas
class Veiculo:
    def acelerar(self) -> None: pass
    def frear(self) -> None: pass
    def ligar_farol(self) -> None: pass
    def buzinar(self) -> None: pass
    def ligar_ar_condicionado(self) -> None: pass  # Opcional
    def abrir_teto_solar(self) -> None: pass  # Opcional
    def ativar_piloto_automatico(self) -> None: pass  # Opcional

# ✅ Interfaces segregadas por capacidade
class VeiculoBasico:
    def acelerar(self) -> None: pass
    def frear(self) -> None: pass
    def ligar_farol(self) -> None: pass
    def buzinar(self) -> None: pass

class VeiculoComArCondicionado:
    def ligar_ar_condicionado(self) -> None: pass
    def desligar_ar_condicionado(self) -> None: pass
    def ajustar_temperatura(self, graus: int) -> None: pass

class VeiculoComTetoSolar:
    def abrir_teto_solar(self) -> None: pass
    def fechar_teto_solar(self) -> None: pass

class VeiculoAutonomo:
    def ativar_piloto_automatico(self) -> None: pass
    def desativar_piloto_automatico(self) -> None: pass
    def definir_destino(self, destino: str) -> None: pass

# Composição de capacidades
class CarroPopular(VeiculoBasico):
    # Apenas funcionalidades básicas
    pass

class CarroMedioCompleto(
    VeiculoBasico, 
    VeiculoComArCondicionado, 
    VeiculoComTetoSolar
):
    # Funcionalidades intermediárias
    pass

class CarroLuxo(
    VeiculoBasico,
    VeiculoComArCondicionado,
    VeiculoComTetoSolar,
    VeiculoAutonomo
):
    # Todas as funcionalidades
    pass
```

### 2.3 Granularidade: Quanto Segregar?

**Cuidado:** Segregação excessiva também é problemática!

#### ❌ Sobre-segregação (Interfaces Pulverizadas)

```java
// Segregação excessiva: uma interface por método
interface Salvavel { void salvar(); }
interface Atualizavel { void atualizar(); }
interface Deletavel { void deletar(); }
interface Buscavel { void buscar(); }
interface Listavel { void listar(); }

// Classe precisa implementar 5 interfaces para operações básicas
class RepositorioUsuario 
    implements Salvavel, Atualizavel, Deletavel, Buscavel, Listavel {
    // Explosão de interfaces!
}
```

#### ✅ Granularidade Equilibrada

```java
// Agrupa operações coesas
interface OperacoesEscrita {
    void salvar(T entidade);
    void atualizar(T entidade);
    void deletar(Long id);
}

interface OperacoesLeitura {
    T buscarPorId(Long id);
    List<T> listar();
}

// Classe implementa interfaces coesas
class RepositorioUsuario 
    implements OperacoesEscrita<Usuario>, OperacoesLeitura<Usuario> {
    // Equilibrado: 2 interfaces coesas
}
```

**Regra prática:**
- **Agrupe métodos que sempre são usados juntos**
- **Separe métodos que são usados independentemente**
- **Evite interfaces com 1 só método (exceto Strategy/Command)**
- **Prefira 2-5 interfaces a 1 interface com 20 métodos**

---

## 3. Exemplos Práticos e Use Cases

### 3.1 Sistema de Impressoras

Um exemplo clássico do ISP.

#### ❌ Violando o ISP

```java
// Interface gorda que força todos a implementar tudo
interface Impressora {
    void imprimir(Documento doc);
    void escanear(Documento doc);
    void enviarFax(Documento doc);
    void imprimirDuplex(Documento doc);
    void imprimirColorido(Documento doc);
    void copiar(Documento doc);
}

// PROBLEMA 1: Impressora simples não tem essas capacidades
class ImpressoraSimples implements Impressora {
    @Override
    public void imprimir(Documento doc) {
        System.out.println("Imprimindo...");
    }
    
    @Override
    public void escanear(Documento doc) {
        throw new UnsupportedOperationException("Não suporta scanner");
    }
    
    @Override
    public void enviarFax(Documento doc) {
        throw new UnsupportedOperationException("Não suporta fax");
    }
    
    @Override
    public void imprimirDuplex(Documento doc) {
        throw new UnsupportedOperationException("Não suporta duplex");
    }
    
    @Override
    public void imprimirColorido(Documento doc) {
        throw new UnsupportedOperationException("Não suporta cor");
    }
    
    @Override
    public void copiar(Documento doc) {
        throw new UnsupportedOperationException("Não suporta cópia");
    }
}

// Cliente quebra em runtime
void processarDocumento(Impressora impressora, Documento doc) {
    impressora.imprimir(doc);
    impressora.escanear(doc); // EXCEÇÃO se for ImpressoraSimples!
}
```

#### ✅ Seguindo o ISP

```java
// Interfaces segregadas por capacidade
interface Imprimivel {
    void imprimir(Documento doc);
}

interface Escaneavel {
    void escanear(Documento doc);
}

interface EnviadorFax {
    void enviarFax(Documento doc);
}

interface ImpressaoDuplex {
    void imprimirDuplex(Documento doc);
}

interface ImpressaoColorida {
    void imprimirColorido(Documento doc);
}

interface Copiadora {
    void copiar(Documento doc);
}

// IMPLEMENTAÇÕES: Cada uma com suas capacidades
class ImpressoraSimples implements Imprimivel {
    @Override
    public void imprimir(Documento doc) {
        System.out.println("Imprimindo em preto e branco...");
    }
}

class ImpressoraLaser implements Imprimivel, ImpressaoDuplex, ImpressaoColorida {
    @Override
    public void imprimir(Documento doc) {
        System.out.println("Imprimindo com laser...");
    }
    
    @Override
    public void imprimirDuplex(Documento doc) {
        System.out.println("Imprimindo frente e verso...");
    }
    
    @Override
    public void imprimirColorido(Documento doc) {
        System.out.println("Imprimindo em cores...");
    }
}

class ImpressoraMultifuncional 
    implements Imprimivel, Escaneavel, EnviadorFax, Copiadora, 
               ImpressaoDuplex, ImpressaoColorida {
    
    @Override
    public void imprimir(Documento doc) {
        System.out.println("Imprimindo...");
    }
    
    @Override
    public void escanear(Documento doc) {
        System.out.println("Escaneando...");
    }
    
    @Override
    public void enviarFax(Documento doc) {
        System.out.println("Enviando fax...");
    }
    
    @Override
    public void copiar(Documento doc) {
        System.out.println("Copiando...");
    }
    
    @Override
    public void imprimirDuplex(Documento doc) {
        System.out.println("Imprimindo duplex...");
    }
    
    @Override
    public void imprimirColorido(Documento doc) {
        System.out.println("Imprimindo em cores...");
    }
}

// CLIENTE: Usa apenas a capacidade necessária
class ServicoImpressao {
    void imprimir(Imprimivel impressora, Documento doc) {
        impressora.imprimir(doc);
        // Só depende de Imprimivel - funciona com qualquer impressora
    }
}

class ServicoDigitalizacao {
    void digitalizar(Escaneavel scanner, Documento doc) {
        scanner.escanear(doc);
        // Só depende de Escaneavel - não força impressoras simples
    }
}

// Verificação de capacidade em tempo de compilação
void processar(Object dispositivo, Documento doc) {
    if (dispositivo instanceof Imprimivel) {
        ((Imprimivel) dispositivo).imprimir(doc);
    }
    
    if (dispositivo instanceof Escaneavel) {
        ((Escaneavel) dispositivo).escanear(doc);
    }
    
    // Type-safe: só chama métodos se capacidade existe
}
```

### 3.2 Sistema de Autenticação

Exemplo de segregação por necessidade do cliente.

#### ❌ Violando o ISP

```typescript
// Interface gorda com todos os métodos de autenticação
interface ServicoAutenticacao {
    // Autenticação básica
    login(email: string, senha: string): Usuario;
    logout(token: string): void;
    
    // Autenticação social
    loginComGoogle(token: string): Usuario;
    loginComFacebook(token: string): Usuario;
    loginComGithub(token: string): Usuario;
    
    // Two-factor
    enviarCodigoSMS(telefone: string): void;
    verificarCodigoSMS(codigo: string): boolean;
    enviarCodigoEmail(email: string): void;
    verificarCodigoEmail(codigo: string): boolean;
    
    // Recuperação de senha
    solicitarRecuperacaoSenha(email: string): void;
    validarTokenRecuperacao(token: string): boolean;
    redefinirSenha(token: string, novaSenha: string): void;
    
    // OAuth
    gerarTokenOAuth(usuario: Usuario): string;
    validarTokenOAuth(token: string): Usuario;
    revogarTokenOAuth(token: string): void;
}

// PROBLEMA: Implementação simples precisa de tudo
class AutenticacaoSimples implements ServicoAutenticacao {
    login(email: string, senha: string): Usuario { /* ... */ }
    logout(token: string): void { /* ... */ }
    
    // Não usa social login
    loginComGoogle(token: string): Usuario {
        throw new Error("Social login não suportado");
    }
    loginComFacebook(token: string): Usuario {
        throw new Error("Social login não suportado");
    }
    loginComGithub(token: string): Usuario {
        throw new Error("Social login não suportado");
    }
    
    // Não usa 2FA
    enviarCodigoSMS(telefone: string): void {
        throw new Error("2FA não suportado");
    }
    verificarCodigoSMS(codigo: string): boolean {
        throw new Error("2FA não suportado");
    }
    enviarCodigoEmail(email: string): void {
        throw new Error("2FA não suportado");
    }
    verificarCodigoEmail(codigo: string): boolean {
        throw new Error("2FA não suportado");
    }
    
    // Implementa apenas recuperação
    solicitarRecuperacaoSenha(email: string): void { /* ... */ }
    validarTokenRecuperacao(token: string): boolean { /* ... */ }
    redefinirSenha(token: string, novaSenha: string): void { /* ... */ }
    
    // Não usa OAuth
    gerarTokenOAuth(usuario: Usuario): string {
        throw new Error("OAuth não suportado");
    }
    validarTokenOAuth(token: string): Usuario {
        throw new Error("OAuth não suportado");
    }
    revogarTokenOAuth(token: string): void {
        throw new Error("OAuth não suportado");
    }
}
```

#### ✅ Seguindo o ISP

```typescript
// Interfaces segregadas por funcionalidade
interface AutenticacaoBasica {
    login(email: string, senha: string): Usuario;
    logout(token: string): void;
}

interface AutenticacaoSocial {
    loginComProvider(provider: string, token: string): Usuario;
}

interface AutenticacaoTwoFactor {
    enviarCodigo(destino: string, canal: 'SMS' | 'EMAIL'): void;
    verificarCodigo(codigo: string): boolean;
}

interface RecuperacaoSenha {
    solicitarRecuperacao(email: string): void;
    validarToken(token: string): boolean;
    redefinirSenha(token: string, novaSenha: string): void;
}

interface ProvedorOAuth {
    gerarToken(usuario: Usuario): string;
    validarToken(token: string): Usuario;
    revogarToken(token: string): void;
}

// IMPLEMENTAÇÕES: Cada sistema implementa o que precisa
class AutenticacaoSimples implements AutenticacaoBasica, RecuperacaoSenha {
    login(email: string, senha: string): Usuario {
        // Validação de credenciais
        const usuario = this.repositorio.buscarPorEmail(email);
        if (this.validarSenha(senha, usuario.senhaHash)) {
            return usuario;
        }
        throw new Error("Credenciais inválidas");
    }
    
    logout(token: string): void {
        this.sessaoService.invalidar(token);
    }
    
    solicitarRecuperacao(email: string): void {
        const token = this.gerarTokenRecuperacao();
        this.emailService.enviar(email, token);
    }
    
    validarToken(token: string): boolean {
        return this.tokenService.validar(token);
    }
    
    redefinirSenha(token: string, novaSenha: string): void {
        if (!this.validarToken(token)) {
            throw new Error("Token inválido");
        }
        const usuario = this.usuarioDoToken(token);
        usuario.senhaHash = this.hashear(novaSenha);
        this.repositorio.atualizar(usuario);
    }
}

class AutenticacaoCompleta 
    implements AutenticacaoBasica, AutenticacaoSocial, 
               AutenticacaoTwoFactor, RecuperacaoSenha, ProvedorOAuth {
    
    // Implementa TODAS as interfaces
    login(email: string, senha: string): Usuario { /* ... */ }
    logout(token: string): void { /* ... */ }
    
    loginComProvider(provider: string, token: string): Usuario {
        switch (provider) {
            case 'google':
                return this.googleAuth.validar(token);
            case 'facebook':
                return this.facebookAuth.validar(token);
            case 'github':
                return this.githubAuth.validar(token);
            default:
                throw new Error("Provider não suportado");
        }
    }
    
    enviarCodigo(destino: string, canal: 'SMS' | 'EMAIL'): void {
        const codigo = this.gerarCodigo();
        if (canal === 'SMS') {
            this.smsService.enviar(destino, codigo);
        } else {
            this.emailService.enviar(destino, codigo);
        }
    }
    
    verificarCodigo(codigo: string): boolean {
        return this.codigoService.validar(codigo);
    }
    
    solicitarRecuperacao(email: string): void { /* ... */ }
    validarToken(token: string): boolean { /* ... */ }
    redefinirSenha(token: string, novaSenha: string): void { /* ... */ }
    
    gerarToken(usuario: Usuario): string {
        return this.jwtService.gerar(usuario);
    }
    
    validarToken(token: string): Usuario {
        return this.jwtService.validar(token);
    }
    
    revogarToken(token: string): void {
        this.tokenBlacklist.adicionar(token);
    }
}

// CLIENTES: Dependem apenas do que usam
class ControladorLogin {
    constructor(private auth: AutenticacaoBasica) {}
    
    async login(req: Request, res: Response): Promise<void> {
        const { email, senha } = req.body;
        const usuario = this.auth.login(email, senha);
        res.json({ usuario });
    }
}

class ControladorRecuperacao {
    constructor(private recuperacao: RecuperacaoSenha) {}
    
    async solicitar(req: Request, res: Response): Promise<void> {
        const { email } = req.body;
        this.recuperacao.solicitarRecuperacao(email);
        res.json({ mensagem: "Email enviado" });
    }
}

class ControladorSocial {
    constructor(private social: AutenticacaoSocial) {}
    
    async loginSocial(req: Request, res: Response): Promise<void> {
        const { provider, token } = req.body;
        const usuario = this.social.loginComProvider(provider, token);
        res.json({ usuario });
    }
}

// Configuração: Escolhe implementação baseada nas necessidades
function criarAutenticacao(config: Config): AutenticacaoBasica {
    if (config.modoSimples) {
        return new AutenticacaoSimples(/* deps */);
    } else {
        return new AutenticacaoCompleta(/* deps */);
    }
}
```

### 3.3 Repositório de Dados

Exemplo de segregação por operação.

#### ❌ Violando o ISP

```csharp
// Interface gorda com todas as operações CRUD + queries
public interface IRepositorio<T>
{
    // Create
    void Criar(T entidade);
    void CriarEmLote(IEnumerable<T> entidades);
    
    // Read
    T BuscarPorId(int id);
    IEnumerable<T> BuscarTodos();
    IEnumerable<T> BuscarComFiltro(Expression<Func<T, bool>> predicado);
    IEnumerable<T> BuscarComPaginacao(int pagina, int tamanhoPagina);
    T BuscarPrimeiro(Expression<Func<T, bool>> predicado);
    
    // Update
    void Atualizar(T entidade);
    void AtualizarEmLote(IEnumerable<T> entidades);
    void AtualizarParcial(int id, object camposAtualizados);
    
    // Delete
    void Deletar(int id);
    void DeletarEmLote(IEnumerable<int> ids);
    void DeletarComFiltro(Expression<Func<T, bool>> predicado);
    
    // Aggregations
    int Contar();
    int ContarComFiltro(Expression<Func<T, bool>> predicado);
    bool Existe(int id);
    
    // Transações
    void IniciarTransacao();
    void ComitarTransacao();
    void ReverterTransacao();
}

// PROBLEMA: Repositório read-only precisa implementar tudo
public class RepositorioLeitura<T> : IRepositorio<T>
{
    // Leitura funciona
    public T BuscarPorId(int id)
    {
        return _context.Set<T>().Find(id);
    }
    
    public IEnumerable<T> BuscarTodos()
    {
        return _context.Set<T>().ToList();
    }
    
    public T BuscarPrimeiro(Expression<Func<T, bool>> predicado)
    {
        return _context.Set<T>().FirstOrDefault(predicado);
    }
    
    public IEnumerable<T> BuscarComFiltro(Expression<Func<T, bool>> predicado)
    {
        return _context.Set<T>().Where(predicado).ToList();
    }
    
    public IEnumerable<T> BuscarComPaginacao(int pagina, int tamanhoPagina)
    {
        return _context.Set<T>()
            .Skip((pagina - 1) * tamanhoPagina)
            .Take(tamanhoPagina)
            .ToList();
    }
    
    public int Contar()
    {
        return _context.Set<T>().Count();
    }
    
    public int ContarComFiltro(Expression<Func<T, bool>> predicado)
    {
        return _context.Set<T>().Count(predicado);
    }
    
    public bool Existe(int id)
    {
        return _context.Set<T>().Any(e => EF.Property<int>(e, "Id") == id);
    }
}

public class RepositorioCompleto<T> : IRepositorioCompleto<T> where T : class
{
    private readonly DbContext _context;
    private IDbContextTransaction _transacao;
    
    public RepositorioCompleto(DbContext context)
    {
        _context = context;
    }
    
    // Leitura
    public T BuscarPorId(int id) => _context.Set<T>().Find(id);
    public IEnumerable<T> BuscarTodos() => _context.Set<T>().ToList();
    public T BuscarPrimeiro(Expression<Func<T, bool>> predicado) 
        => _context.Set<T>().FirstOrDefault(predicado);
    public IEnumerable<T> BuscarComFiltro(Expression<Func<T, bool>> predicado) 
        => _context.Set<T>().Where(predicado).ToList();
    public IEnumerable<T> BuscarComPaginacao(int pagina, int tamanhoPagina)
        => _context.Set<T>().Skip((pagina - 1) * tamanhoPagina).Take(tamanhoPagina).ToList();
    public int Contar() => _context.Set<T>().Count();
    public int ContarComFiltro(Expression<Func<T, bool>> predicado) 
        => _context.Set<T>().Count(predicado);
    public bool Existe(int id) 
        => _context.Set<T>().Any(e => EF.Property<int>(e, "Id") == id);
    
    // Escrita
    public void Criar(T entidade)
    {
        _context.Set<T>().Add(entidade);
        _context.SaveChanges();
    }
    
    public void Atualizar(T entidade)
    {
        _context.Set<T>().Update(entidade);
        _context.SaveChanges();
    }
    
    public void Deletar(int id)
    {
        var entidade = BuscarPorId(id);
        if (entidade != null)
        {
            _context.Set<T>().Remove(entidade);
            _context.SaveChanges();
        }
    }
    
    // Lote
    public void CriarEmLote(IEnumerable<T> entidades)
    {
        _context.Set<T>().AddRange(entidades);
        _context.SaveChanges();
    }
    
    public void AtualizarEmLote(IEnumerable<T> entidades)
    {
        _context.Set<T>().UpdateRange(entidades);
        _context.SaveChanges();
    }
    
    public void DeletarEmLote(IEnumerable<int> ids)
    {
        var entidades = _context.Set<T>()
            .Where(e => ids.Contains(EF.Property<int>(e, "Id")));
        _context.Set<T>().RemoveRange(entidades);
        _context.SaveChanges();
    }
    
    // Transações
    public void IniciarTransacao()
    {
        _transacao = _context.Database.BeginTransaction();
    }
    
    public void ComitarTransacao()
    {
        _transacao?.Commit();
        _transacao?.Dispose();
        _transacao = null;
    }
    
    public void ReverterTransacao()
    {
        _transacao?.Rollback();
        _transacao?.Dispose();
        _transacao = null;
    }
}

// CLIENTES: Usam apenas o que precisam
public class ServicoRelatorio
{
    private readonly IRepositorioConsulta<Pedido> _repositorio;
    
    public ServicoRelatorio(IRepositorioConsulta<Pedido> repositorio)
    {
        _repositorio = repositorio; // Só leitura!
    }
    
    public RelatorioVendas GerarRelatorio(DateTime inicio, DateTime fim)
    {
        var pedidos = _repositorio.BuscarComFiltro(
            p => p.Data >= inicio && p.Data <= fim
        );
        
        return new RelatorioVendas(pedidos);
    }
}

public class ServicoPedido
{
    private readonly IRepositorioCompleto<Pedido> _repositorio;
    
    public ServicoPedido(IRepositorioCompleto<Pedido> repositorio)
    {
        _repositorio = repositorio; // Leitura + escrita + transações
    }
    
    public void CriarPedido(Pedido pedido)
    {
        _repositorio.IniciarTransacao();
        
        try
        {
            _repositorio.Criar(pedido);
            // ... outras operações
            _repositorio.ComitarTransacao();
        }
        catch
        {
            _repositorio.ReverterTransacao();
            throw;
        }
    }
}
```

---

## 4. Caso Real: Refactoring de Interface Monolítica

### 4.1 O Problema: Sistema de E-commerce

Uma empresa de e-commerce tinha uma interface `IProduto` que forçava todas as implementações a ter métodos irrelevantes.

#### ❌ Interface Monolítica Original

```typescript
// Interface gigante que força todos os tipos de produto
interface IProduto {
    // Informações básicas
    getId(): string;
    getNome(): string;
    getDescricao(): string;
    getPreco(): number;
    
    // Estoque físico
    getQuantidadeEstoque(): number;
    reservarEstoque(quantidade: number): void;
    liberarEstoque(quantidade: number): void;
    
    // Variações (tamanho, cor)
    getVariacoes(): Variacao[];
    selecionarVariacao(variacaoId: string): void;
    
    // Download (produtos digitais)
    getUrlDownload(): string;
    getLicenca(): string;
    getValidadeLicenca(): Date;
    
    // Assinatura (serviços)
    getPeriodoAssinatura(): 'MENSAL' | 'ANUAL';
    getRenovacaoAutomatica(): boolean;
    cancelarAssinatura(): void;
    
    // Personalização
    podePersonalizar(): boolean;
    getOpcoesPersonalizacao(): OpcaoPersonalizacao[];
    aplicarPersonalizacao(opcoes: Map<string, any>): void;
    
    // Frete
    calcularFrete(cep: string): number;
    getPrazoEntrega(cep: string): number;
    
    // Avaliações
    getAvaliacoes(): Avaliacao[];
    adicionarAvaliacao(avaliacao: Avaliacao): void;
    getMediaAvaliacoes(): number;
}

// PROBLEMA 1: Produto físico não precisa de download
class ProdutoFisico implements IProduto {
    getId(): string { return this.id; }
    getNome(): string { return this.nome; }
    getPreco(): number { return this.preco; }
    getQuantidadeEstoque(): number { return this.estoque; }
    
    // Implementa variações
    getVariacoes(): Variacao[] { return this.variacoes; }
    selecionarVariacao(id: string): void { /* ... */ }
    
    // Implementa frete
    calcularFrete(cep: string): number { /* ... */ }
    getPrazoEntrega(cep: string): number { /* ... */ }
    
    // NÃO USA mas é FORÇADO a implementar
    getUrlDownload(): string {
        throw new Error("Produto físico não tem download");
    }
    
    getLicenca(): string {
        throw new Error("Produto físico não tem licença");
    }
    
    getValidadeLicenca(): Date {
        throw new Error("Produto físico não tem licença");
    }
    
    getPeriodoAssinatura(): 'MENSAL' | 'ANUAL' {
        throw new Error("Produto físico não é assinatura");
    }
    
    getRenovacaoAutomatica(): boolean {
        throw new Error("Produto físico não é assinatura");
    }
    
    cancelarAssinatura(): void {
        throw new Error("Produto físico não é assinatura");
    }
    
    // ... mais métodos irrelevantes
}

// PROBLEMA 2: Produto digital não precisa de estoque físico
class ProdutoDigital implements IProduto {
    getId(): string { return this.id; }
    getNome(): string { return this.nome; }
    getPreco(): number { return this.preco; }
    
    // Implementa download
    getUrlDownload(): string { return this.urlDownload; }
    getLicenca(): string { return this.chave; }
    getValidadeLicenca(): Date { return this.validade; }
    
    // NÃO USA estoque físico
    getQuantidadeEstoque(): number {
        return 999999; // "Infinito"
    }
    
    reservarEstoque(quantidade: number): void {
        // Não faz nada
    }
    
    liberarEstoque(quantidade: number): void {
        // Não faz nada
    }
    
    // NÃO USA variações físicas
    getVariacoes(): Variacao[] {
        return [];
    }
    
    selecionarVariacao(id: string): void {
        throw new Error("Produto digital não tem variações");
    }
    
    // NÃO USA frete
    calcularFrete(cep: string): number {
        return 0;
    }
    
    getPrazoEntrega(cep: string): number {
        return 0; // Entrega instantânea
    }
    
    // ... mais métodos irrelevantes
}

// PROBLEMA 3: Assinatura é completamente diferente
class Assinatura implements IProduto {
    // ... implementações específicas de assinatura
    
    getPeriodoAssinatura(): 'MENSAL' | 'ANUAL' { return this.periodo; }
    getRenovacaoAutomatica(): boolean { return this.autoRenova; }
    cancelarAssinatura(): void { /* ... */ }
    
    // Forçado a implementar coisas de produto físico
    getQuantidadeEstoque(): number {
        throw new Error("Assinatura não tem estoque");
    }
    
    calcularFrete(cep: string): number {
        throw new Error("Assinatura não tem frete");
    }
    
    // Forçado a implementar download
    getUrlDownload(): string {
        throw new Error("Assinatura não tem download direto");
    }
    
    // ... muitos métodos irrelevantes
}
```

**Problemas identificados:**

| Problema | Impacto | Métrica |
|----------|---------|---------|
| Métodos não utilizados | 60-70% dos métodos lançam exceções | ❌ **Alta violação ISP** |
| Código poluído | Classes com 200+ linhas sendo 70% exceções | ❌ **Manutenção difícil** |
| Confusão de API | Clientes não sabem quais métodos funcionam | ❌ **DX ruim** |
| Testes complexos | Mocks precisam implementar 20+ métodos | ❌ **Testabilidade baixa** |
| Crashes em produção | 15 exceções/mês por chamar método errado | ❌ **Instabilidade** |

### 4.2 Refactoring: Aplicando ISP

#### Passo 1: Identificar Grupos de Responsabilidade

```
Análise de métodos por tipo de produto:

TODOS os produtos:
- getId(), getNome(), getDescricao(), getPreco()
- getAvaliacoes(), adicionarAvaliacao(), getMediaAvaliacoes()

Produtos FÍSICOS:
- getQuantidadeEstoque(), reservarEstoque(), liberarEstoque()
- getVariacoes(), selecionarVariacao()
- calcularFrete(), getPrazoEntrega()

Produtos DIGITAIS:
- getUrlDownload(), getLicenca(), getValidadeLicenca()

Produtos PERSONALIZÁVEIS:
- podePersonalizar(), getOpcoesPersonalizacao(), aplicarPersonalizacao()

ASSINATURAS:
- getPeriodoAssinatura(), getRenovacaoAutomatica(), cancelarAssinatura()
```

#### Passo 2: Criar Interfaces Segregadas

```typescript
// INTERFACE BASE: Comum a todos
interface Produto {
    getId(): string;
    getNome(): string;
    getDescricao(): string;
    getPreco(): number;
}

// INTERFACE: Produtos com avaliações
interface Avaliavel {
    getAvaliacoes(): Avaliacao[];
    adicionarAvaliacao(avaliacao: Avaliacao): void;
    getMediaAvaliacoes(): number;
}

// INTERFACE: Produtos com estoque físico
interface ProdutoEstocavel {
    getQuantidadeEstoque(): number;
    reservarEstoque(quantidade: number): boolean;
    liberarEstoque(quantidade: number): void;
    estaDisponivel(quantidade: number): boolean;
}

// INTERFACE: Produtos com variações
interface ProdutoVariavel {
    getVariacoes(): Variacao[];
    selecionarVariacao(variacaoId: string): void;
    getVariacaoSelecionada(): Variacao | null;
}

// INTERFACE: Produtos digitais
interface ProdutoDigitalInterface {
    getUrlDownload(): string;
    getLicenca(): string;
    getValidadeLicenca(): Date;
    validarLicenca(): boolean;
}

// INTERFACE: Produtos personalizáveis
interface ProdutoPersonalizavel {
    getOpcoesPersonalizacao(): OpcaoPersonalizacao[];
    aplicarPersonalizacao(opcoes: Map<string, any>): void;
    getPersonalizacaoAtual(): Map<string, any>;
}

// INTERFACE: Produtos com frete
interface ProdutoEntregavel {
    calcularFrete(cep: string): number;
    getPrazoEntrega(cep: string): number;
    getDimensoes(): Dimensoes;
    getPeso(): number;
}

// INTERFACE: Assinaturas
interface AssinaturaInterface {
    getPeriodoAssinatura(): PeriodoAssinatura;
    getRenovacaoAutomatica(): boolean;
    setRenovacaoAutomatica(renovar: boolean): void;
    cancelarAssinatura(): void;
    getProximaCobranca(): Date;
}

// TIPOS AUXILIARES
type PeriodoAssinatura = 'MENSAL' | 'TRIMESTRAL' | 'ANUAL';

interface Dimensoes {
    altura: number;
    largura: number;
    profundidade: number;
}

interface Variacao {
    id: string;
    nome: string;
    precoAdicional: number;
    disponivel: boolean;
}

interface OpcaoPersonalizacao {
    id: string;
    nome: string;
    tipo: 'TEXTO' | 'COR' | 'IMAGEM';
    obrigatoria: boolean;
}

interface Avaliacao {
    usuario: string;
    nota: number;
    comentario: string;
    data: Date;
}
```

#### Passo 3: Implementações Limpas

```typescript
// PRODUTO FÍSICO: Implementa apenas o que usa
class ProdutoFisico 
    implements Produto, Avaliavel, ProdutoEstocavel, 
               ProdutoVariavel, ProdutoEntregavel {
    
    constructor(
        private id: string,
        private nome: string,
        private descricao: string,
        private preco: number,
        private estoque: number,
        private variacoes: Variacao[],
        private peso: number,
        private dimensoes: Dimensoes
    ) {}
    
    // Produto
    getId(): string { return this.id; }
    getNome(): string { return this.nome; }
    getDescricao(): string { return this.descricao; }
    getPreco(): number { return this.preco; }
    
    // Avaliavel
    private avaliacoes: Avaliacao[] = [];
    
    getAvaliacoes(): Avaliacao[] {
        return [...this.avaliacoes];
    }
    
    adicionarAvaliacao(avaliacao: Avaliacao): void {
        this.avaliacoes.push(avaliacao);
    }
    
    getMediaAvaliacoes(): number {
        if (this.avaliacoes.length === 0) return 0;
        const soma = this.avaliacoes.reduce((acc, av) => acc + av.nota, 0);
        return soma / this.avaliacoes.length;
    }
    
    // ProdutoEstocavel
    getQuantidadeEstoque(): number {
        return this.estoque;
    }
    
    reservarEstoque(quantidade: number): boolean {
        if (quantidade > this.estoque) {
            return false;
        }
        this.estoque -= quantidade;
        return true;
    }
    
    liberarEstoque(quantidade: number): void {
        this.estoque += quantidade;
    }
    
    estaDisponivel(quantidade: number): boolean {
        return this.estoque >= quantidade;
    }
    
    // ProdutoVariavel
    private variacaoSelecionada: Variacao | null = null;
    
    getVariacoes(): Variacao[] {
        return [...this.variacoes];
    }
    
    selecionarVariacao(variacaoId: string): void {
        const variacao = this.variacoes.find(v => v.id === variacaoId);
        if (!variacao) {
            throw new Error(`Variação ${variacaoId} não encontrada`);
        }
        if (!variacao.disponivel) {
            throw new Error(`Variação ${variacaoId} indisponível`);
        }
        this.variacaoSelecionada = variacao;
    }
    
    getVariacaoSelecionada(): Variacao | null {
        return this.variacaoSelecionada;
    }
    
    // ProdutoEntregavel
    calcularFrete(cep: string): number {
        const distancia = this.calcularDistancia(cep);
        const pesoFator = this.peso * 0.1;
        const volumeFator = this.calcularVolume() * 0.05;
        return distancia * (pesoFator + volumeFator);
    }
    
    getPrazoEntrega(cep: string): number {
        const distancia = this.calcularDistancia(cep);
        return Math.ceil(distancia / 100) + 2; // dias
    }
    
    getDimensoes(): Dimensoes {
        return { ...this.dimensoes };
    }
    
    getPeso(): number {
        return this.peso;
    }
    
    private calcularDistancia(cep: string): number {
        // Lógica de cálculo de distância
        return 500; // km (exemplo)
    }
    
    private calcularVolume(): number {
        const { altura, largura, profundidade } = this.dimensoes;
        return altura * largura * profundidade;
    }
}

// PRODUTO DIGITAL: Implementa apenas o que usa
class ProdutoDigital 
    implements Produto, Avaliavel, ProdutoDigitalInterface {
    
    constructor(
        private id: string,
        private nome: string,
        private descricao: string,
        private preco: number,
        private urlDownload: string,
        private chave: string,
        private validade: Date
    ) {}
    
    // Produto
    getId(): string { return this.id; }
    getNome(): string { return this.nome; }
    getDescricao(): string { return this.descricao; }
    getPreco(): number { return this.preco; }
    
    // Avaliavel
    private avaliacoes: Avaliacao[] = [];
    
    getAvaliacoes(): Avaliacao[] {
        return [...this.avaliacoes];
    }
    
    adicionarAvaliacao(avaliacao: Avaliacao): void {
        this.avaliacoes.push(avaliacao);
    }
    
    getMediaAvaliacoes(): number {
        if (this.avaliacoes.length === 0) return 0;
        const soma = this.avaliacoes.reduce((acc, av) => acc + av.nota, 0);
        return soma / this.avaliacoes.length;
    }
    
    // ProdutoDigitalInterface
    getUrlDownload(): string {
        if (!this.validarLicenca()) {
            throw new Error("Licença expirada");
        }
        return this.urlDownload;
    }
    
    getLicenca(): string {
        return this.chave;
    }
    
    getValidadeLicenca(): Date {
        return this.validade;
    }
    
    validarLicenca(): boolean {
        return new Date() < this.validade;
    }
}

// PRODUTO FÍSICO PERSONALIZÁVEL
class ProdutoPersonalizado extends ProdutoFisico implements ProdutoPersonalizavel {
    private opcoesPersonalizacao: OpcaoPersonalizacao[];
    private personalizacaoAtual: Map<string, any> = new Map();
    
    constructor(
        id: string,
        nome: string,
        descricao: string,
        preco: number,
        estoque: number,
        variacoes: Variacao[],
        peso: number,
        dimensoes: Dimensoes,
        opcoesPersonalizacao: OpcaoPersonalizacao[]
    ) {
        super(id, nome, descricao, preco, estoque, variacoes, peso, dimensoes);
        this.opcoesPersonalizacao = opcoesPersonalizacao;
    }
    
    // ProdutoPersonalizavel
    getOpcoesPersonalizacao(): OpcaoPersonalizacao[] {
        return [...this.opcoesPersonalizacao];
    }
    
    aplicarPersonalizacao(opcoes: Map<string, any>): void {
        // Validar opções obrigatórias
        const obrigatorias = this.opcoesPersonalizacao
            .filter(op => op.obrigatoria);
        
        for (const opcao of obrigatorias) {
            if (!opcoes.has(opcao.id)) {
                throw new Error(`Opção ${opcao.nome} é obrigatória`);
            }
        }
        
        this.personalizacaoAtual = new Map(opcoes);
    }
    
    getPersonalizacaoAtual(): Map<string, any> {
        return new Map(this.personalizacaoAtual);
    }
    
    // Override de preço para incluir personalização
    getPreco(): number {
        let precoBase = super.getPreco();
        // Adicionar custo de personalização
        return precoBase + 15.00;
    }
}

// ASSINATURA: Interface completamente diferente
class Assinatura implements Produto, AssinaturaInterface {
    private ativa: boolean = true;
    
    constructor(
        private id: string,
        private nome: string,
        private descricao: string,
        private preco: number,
        private periodo: PeriodoAssinatura,
        private renovacaoAutomatica: boolean,
        private dataInicio: Date
    ) {}
    
    // Produto
    getId(): string { return this.id; }
    getNome(): string { return this.nome; }
    getDescricao(): string { return this.descricao; }
    getPreco(): number { return this.preco; }
    
    // AssinaturaInterface
    getPeriodoAssinatura(): PeriodoAssinatura {
        return this.periodo;
    }
    
    getRenovacaoAutomatica(): boolean {
        return this.renovacaoAutomatica;
    }
    
    setRenovacaoAutomatica(renovar: boolean): void {
        this.renovacaoAutomatica = renovar;
    }
    
    cancelarAssinatura(): void {
        this.ativa = false;
        this.renovacaoAutomatica = false;
    }
    
    getProximaCobranca(): Date {
        const diasPorPeriodo = {
            'MENSAL': 30,
            'TRIMESTRAL': 90,
            'ANUAL': 365
        };
        
        const proxima = new Date(this.dataInicio);
        proxima.setDate(proxima.getDate() + diasPorPeriodo[this.periodo]);
        return proxima;
    }
    
    estaAtiva(): boolean {
        return this.ativa;
    }
}
```

#### Passo 4: Clientes Específicos e Seguros

```typescript
// SERVIÇO DE CARRINHO: Usa apenas interfaces necessárias
class ServicoCarrinho {
    adicionarProdutoFisico(
        produto: Produto & ProdutoEstocavel,
        quantidade: number
    ): void {
        if (!produto.estaDisponivel(quantidade)) {
            throw new Error("Produto sem estoque suficiente");
        }
        
        if (produto.reservarEstoque(quantidade)) {
            console.log(`${quantidade}x ${produto.getNome()} adicionado`);
        }
    }
    
    adicionarProdutoDigital(produto: Produto & ProdutoDigitalInterface): void {
        if (!produto.validarLicenca()) {
            throw new Error("Licença do produto expirada");
        }
        
        console.log(`${produto.getNome()} adicionado ao carrinho`);
    }
    
    adicionarAssinatura(assinatura: Produto & AssinaturaInterface): void {
        if (!assinatura.estaAtiva()) {
            throw new Error("Assinatura inativa");
        }
        
        console.log(`Assinatura ${assinatura.getNome()} adicionada`);
    }
}

// SERVIÇO DE FRETE: Só trabalha com produtos entregáveis
class ServicoFrete {
    calcularFrete(produto: ProdutoEntregavel, cep: string): InfoFrete {
        const valor = produto.calcularFrete(cep);
        const prazo = produto.getPrazoEntrega(cep);
        const peso = produto.getPeso();
        const dimensoes = produto.getDimensoes();
        
        return {
            valor,
            prazo,
            peso,
            dimensoes
        };
    }
    
    validarDimensoes(produto: ProdutoEntregavel): boolean {
        const dim = produto.getDimensoes();
        const maxDimensao = 100; // cm
        
        return dim.altura <= maxDimensao &&
               dim.largura <= maxDimensao &&
               dim.profundidade <= maxDimensao;
    }
}

// SERVIÇO DE DOWNLOAD: Só trabalha com produtos digitais
class ServicoDownload {
    gerarLinkDownload(produto: ProdutoDigitalInterface): string {
        if (!produto.validarLicenca()) {
            throw new Error("Licença expirada. Renove para continuar.");
        }
        
        const url = produto.getUrlDownload();
        const licenca = produto.getLicenca();
        
        return `${url}?key=${licenca}&timestamp=${Date.now()}`;
    }
    
    verificarAcesso(produto: ProdutoDigitalInterface): StatusAcesso {
        const validade = produto.getValidadeLicenca();
        const agora = new Date();
        const diasRestantes = Math.ceil(
            (validade.getTime() - agora.getTime()) / (1000 * 60 * 60 * 24)
        );
        
        return {
            temAcesso: diasRestantes > 0,
            diasRestantes,
            validade
        };
    }
}

// SERVIÇO DE PERSONALIZAÇÃO
class ServicoPersonalizacao {
    configurar(
        produto: ProdutoPersonalizavel,
        opcoes: Map<string, any>
    ): void {
        const opcoesDisponiveis = produto.getOpcoesPersonalizacao();
        
        // Validar todas as opções
        for (const [id, valor] of opcoes) {
            const opcao = opcoesDisponiveis.find(op => op.id === id);
            if (!opcao) {
                throw new Error(`Opção ${id} não existe`);
            }
            
            // Validações específicas por tipo
            if (opcao.tipo === 'TEXTO' && typeof valor !== 'string') {
                throw new Error(`Opção ${opcao.nome} deve ser texto`);
            }
        }
        
        produto.aplicarPersonalizacao(opcoes);
    }
    
    previsualizar(produto: ProdutoPersonalizavel): PreviewPersonalizacao {
        const opcoes = produto.getPersonalizacaoAtual();
        const disponiveis = produto.getOpcoesPersonalizacao();
        
        return {
            produto: produto as Produto,
            personalizacoes: Array.from(opcoes.entries()).map(([id, valor]) => {
                const opcao = disponiveis.find(op => op.id === id);
                return {
                    nome: opcao?.nome || id,
                    valor
                };
            })
        };
    }
}

// Type guards para verificação de interface
function isProdutoEstocavel(produto: Produto): produto is Produto & ProdutoEstocavel {
    return 'getQuantidadeEstoque' in produto;
}

function isProdutoDigital(produto: Produto): produto is Produto & ProdutoDigitalInterface {
    return 'getUrlDownload' in produto;
}

function isProdutoEntregavel(produto: Produto): produto is Produto & ProdutoEntregavel {
    return 'calcularFrete' in produto;
}

function isAssinatura(produto: Produto): produto is Produto & AssinaturaInterface {
    return 'getPeriodoAssinatura' in produto;
}

function isProdutoPersonalizavel(produto: Produto): produto is Produto & ProdutoPersonalizavel {
    return 'getOpcoesPersonalizacao' in produto;
}

// TIPOS AUXILIARES
interface InfoFrete {
    valor: number;
    prazo: number;
    peso: number;
    dimensoes: Dimensoes;
}

interface StatusAcesso {
    temAcesso: boolean;
    diasRestantes: number;
    validade: Date;
}

interface PreviewPersonalizacao {
    produto: Produto;
    personalizacoes: Array<{
        nome: string;
        valor: any;
    }>;
}

// USO PRÁTICO COM TYPE GUARDS
class ProcessadorCheckout {
    processar(produto: Produto, quantidade: number = 1): void {
        console.log(`Processando: ${produto.getNome()}`);
        
        // Verifica estoque se for produto físico
        if (isProdutoEstocavel(produto)) {
            if (!produto.estaDisponivel(quantidade)) {
                throw new Error("Estoque insuficiente");
            }
            produto.reservarEstoque(quantidade);
        }
        
        // Verifica licença se for produto digital
        if (isProdutoDigital(produto)) {
            if (!produto.validarLicenca()) {
                throw new Error("Licença expirada");
            }
        }
        
        // Verifica se assinatura está ativa
        if (isAssinatura(produto)) {
            if (!produto.estaAtiva()) {
                throw new Error("Assinatura inativa");
            }
        }
        
        console.log(`Processamento concluído: R$ ${produto.getPreco()}`);
    }
}
```

### 4.3 Resultados do Refactoring

| Métrica | Antes (Interface Monolítica) | Depois (ISP Aplicado) | Melhoria |
|---------|------------------------------|------------------------|----------|
| Métodos por interface | 20+ | 3-5 | **75% redução** |
| Exceções UnsupportedOperation | 15/mês | 0/mês | **100% eliminação** |
| Linhas de código por classe | 300+ (70% exceções) | 100-150 (código útil) | **50% redução** |
| Clareza de API | Baixa | Alta | **Interfaces autodocumentadas** |
| Tempo para criar novo tipo | 4 horas | 1 hora | **75% mais rápido** |
| Complexidade de mock em testes | 20+ métodos | 3-5 métodos | **80% redução** |
| Satisfação do desenvolvedor | 3/10 | 9/10 | **200% melhoria** |

**Depoimento da equipe:**

> *"Antes, cada novo tipo de produto era uma dor de cabeça. Tínhamos que implementar 20 métodos, sendo que 15 só lançavam exceções. Agora, com interfaces segregadas, implementamos apenas o que faz sentido. Adicionamos produto de assinatura em 2 horas, quando antes levávamos 2 dias."* - Desenvolvedor Sênior

---

## 5. Abstrações Conceituais

### 5.1 ISP e Coesão de Interface

**Coesão de interface** = grau em que os métodos de uma interface pertencem juntos.

#### Alta Coesão (Desejável)

```java
// Interface com alta coesão - todos os métodos relacionados
interface Autenticavel {
    boolean autenticar(String usuario, String senha);
    void logout();
    boolean estaAutenticado();
}

// Todos os métodos tratam do mesmo conceito: autenticação
```

#### Baixa Coesão (Problemática)

```java
// Interface com baixa coesão - métodos não relacionados
interface ServicoUsuario {
    void salvarUsuario(Usuario usuario);        // Persistência
    boolean autenticar(String user, String pwd); // Autenticação
    void enviarEmail(String email, String msg);  // Notificação
    BigDecimal calcularSalario(Usuario usuario); // Cálculo
}

// Métodos não têm relação conceitual entre si
```

### 5.2 Princípio da Mínima Interface

**Menos é mais**: Interfaces devem expor o mínimo necessário.

```python
# ❌ Interface expõe detalhes de implementação
class Repositorio:
    def get_connection(self): pass
    def begin_transaction(self): pass
    def commit_transaction(self): pass
    def rollback_transaction(self): pass
    def execute_query(self, sql: str): pass
    def buscar_usuario(self, id: int): pass

# Expõe detalhes técnicos que cliente não deveria ver

# ✅ Interface minimalista - só o essencial
class RepositorioUsuario:
    def buscar(self, id: int) -> Usuario: pass
    def salvar(self, usuario: Usuario) -> None: pass
    def deletar(self, id: int) -> None: pass

# Cliente vê apenas o que precisa
```

### 5.3 ISP e Command/Query Separation

Segregar comandos (escrita) de queries (leitura):

```csharp
// Segregação por tipo de operação
public interface IRepositorioLeitura<T>
{
    T BuscarPorId(int id);
    IEnumerable<T> BuscarTodos();
}

public interface IRepositorioEscrita<T>
{
    void Criar(T entidade);
    void Atualizar(T entidade);
    void Deletar(int id);
}

// CQRS - Command Query Responsibility Segregation
public interface IQuery<TResult>
{
    TResult Executar();
}

public interface ICommand
{
    void Executar();
}

// Uso
public class BuscarUsuarioQuery : IQuery<Usuario>
{
    private readonly int _id;
    
    public BuscarUsuarioQuery(int id)
    {
        _id = id;
    }
    
    public Usuario Executar()
    {
        // Lógica de busca
        return null;
    }
}

public class CriarUsuarioCommand : ICommand
{
    private readonly Usuario _usuario;
    
    public CriarUsuarioCommand(Usuario usuario)
    {
        _usuario = usuario;
    }
    
    public void Executar()
    {
        // Lógica de criação
    }
}
```

### 5.4 Interface Pollution

**Poluição de interface** = força clientes a conhecer métodos irrelevantes.

```typescript
// ❌ Poluição: Cliente vê métodos que não usa
interface Animal {
    comer(): void;
    dormir(): void;
    voar(): void;      // Nem todos voam
    nadar(): void;     // Nem todos nadam
    botar_ovos(): void; // Nem todos botam ovos
}

class Cachorro implements Animal {
    comer(): void { /* ok */ }
    dormir(): void { /* ok */ }
    voar(): void { throw new Error(); }      // POLUIÇÃO
    nadar(): void { throw new Error(); }     // POLUIÇÃO
    botar_ovos(): void { throw new Error(); } // POLUIÇÃO
}

// Cliente forçado a lidar com métodos irrelevantes
function cuidarAnimal(animal: Animal) {
    animal.comer();
    animal.dormir();
    // Não deveria nem ver voar() se animal não voa!
}

// ✅ Sem poluição: Cliente vê apenas o relevante
interface Animal {
    comer(): void;
    dormir(): void;
}

interface Voador {
    voar(): void;
}

interface Nadador {
    nadar(): void;
}

class Cachorro implements Animal, Nadador {
    comer(): void { /* ok */ }
    dormir(): void { /* ok */ }
    nadar(): void { /* ok */ }
    // Não implementa voar() - método nem existe no contrato
}

function cuidarAnimal(animal: Animal) {
    animal.comer();
    animal.dormir();
    // Interface limpa - sem métodos irrelevantes
}

function treinarVoo(voador: Voador) {
    voador.voar();
    // Só aceita quem realmente voa
}
```

---

## 6. ISP e os Princípios SOLID

### 6.1 Relação com Outros Princípios

| Princípio | Relação com ISP | Exemplo |
|-----------|-----------------|---------|
| **S**RP | ISP é SRP aplicado a interfaces | Interface coesa = responsabilidade única |
| **O**CP | ISP facilita extensão sem modificação | Novas interfaces não quebram clientes |
| **L**SP | ISP reduz violações de LSP | Menos métodos = menos chance de violação |
| **D**IP | ISP cria abstrações melhores | Dependências específicas e focadas |

### 6.2 ISP + SRP: Interfaces Coesas

```ruby
# SRP diz: "Uma classe, uma responsabilidade"
# ISP diz: "Uma interface, um grupo coeso de métodos"

# ❌ Viola SRP E ISP
class GerenciadorUsuario
  def criar_usuario(dados); end
  def autenticar(email, senha); end
  def enviar_email(email, msg); end
  def gerar_relatorio; end
end

# ✅ Segue SRP E ISP
class CriadorUsuario
  def criar(dados); end
end

class Autenticador
  def autenticar(email, senha); end
end

class NotificadorEmail
  def enviar(email, mensagem); end
end

class GeradorRelatorio
  def gerar; end
end

# Cada classe/interface tem uma responsabilidade clara
```

### 6.3 ISP + LSP: Substituibilidade Segura

```kotlin
// LSP diz: "Subclasses devem ser substituíveis"
// ISP diz: "Não force implementações desnecessárias"

// ❌ Viola LSP porque viola ISP
interface Ave {
    fun voar()
    fun botar_ovos()
}

class Pinguim : Ave {
    override fun voar() {
        throw UnsupportedOperationException() // VIOLA LSP!
    }
    
    override fun botar_ovos() {
        // OK
    }
}

// ✅ Segue LSP E ISP
interface Ave {
    fun botar_ovos()
}

interface AveVoadora : Ave {
    fun voar()
}

class Pinguim : Ave {
    override fun botar_ovos() {
        // OK - implementa apenas o que faz sentido
    }
}

class Aguia : AveVoadora {
    override fun botar_ovos() { /* OK */ }
    override fun voar() { /* OK */ }
    // Aguia é substituível por AveVoadora
}

// Cliente usa interface apropriada
fun migrar_aves_voadoras(aves: List<AveVoadora>) {
    aves.forEach { it.voar() } // Seguro - todas voam
}
```
