**Métricas de Escalabilidade:**

| Aspecto | Sem SRP | Com SRP | Impacto |
|---------|---------|---------|---------|
| Tempo para adicionar feature | 2-3 dias | 4-8 horas | **4x mais rápido** |
| Onboarding de novos devs | 4 semanas | 1 semana | **75% redução** |
| Capacidade de paralelização | Baixa (20%) | Alta (80%) | **300% aumento** |
| Reuso de código | 15% | 60% | **300% aumento** |
| Linhas afetadas por bug fix | 200-500 | 20-50 | **90% redução** |

#### Exemplo: Microsserviços Escaláveis

```java
// Cada microsserviço segue SRP em nível de sistema

// SERVIÇO: Catálogo de Produtos
@RestController
@RequestMapping("/api/produtos")
public class ProdutoController {
    private final CatalogoProdutos catalogo;
    
    @GetMapping("/{id}")
    public ResponseEntity<Produto> buscar(@PathVariable String id) {
        return ResponseEntity.ok(catalogo.buscar(id));
    }
}

// SERVIÇO: Gestão de Pedidos
@RestController
@RequestMapping("/api/pedidos")
public class PedidoController {
    private final ServicoPedido servico;
    
    @PostMapping
    public ResponseEntity<Pedido> criar(@RequestBody PedidoRequest request) {
        return ResponseEntity.ok(servico.criar(request));
    }
}

// SERVIÇO: Processamento de Pagamentos
@RestController
@RequestMapping("/api/pagamentos")
public class PagamentoController {
    private final ProcessadorPagamento processador;
    
    @PostMapping
    public ResponseEntity<ResultadoPagamento> processar(
            @RequestBody PagamentoRequest request) {
        return ResponseEntity.ok(processador.processar(request));
    }
}

// Cada serviço pode escalar independentemente
// Catálogo: 5 instâncias
// Pedidos: 10 instâncias (mais demanda)
// Pagamentos: 3 instâncias
```

### 8.4 Análise Quantitativa de Impacto

#### Estudo de Caso Real: Refactoring de E-commerce

**Projeto:** Sistema de e-commerce com 3 anos de desenvolvimento  
**Equipe:** 15 desenvolvedores  
**Código:** 250.000 linhas

**Fase 1: Antes da Aplicação do SRP**
```java
// Estatísticas do código
public class AnaliseCodigo {
    // Classe principal: OrderManager
    // - 2.847 linhas
    // - 87 métodos
    // - 42 dependências diretas
    // - Complexidade ciclomática: 156
    
    // Métricas de qualidade
    // - Cobertura de testes: 23%
    // - Bugs por sprint: 18
    // - Tempo médio de correção: 6 horas
    // - Dívida técnica: 45 dias
    // - Satisfação do dev: 4/10
}
```

**Fase 2: Após 6 Meses de Aplicação do SRP**
```java
public class ResultadosRefactoring {
    // Classe principal agora dividida em:
    // - OrderService (120 linhas)
    // - OrderValidator (80 linhas)
    // - OrderCalculator (95 linhas)
    // - OrderRepository (60 linhas)
    // - OrderNotifier (75 linhas)
    // - OrderLogger (45 linhas)
    
    // Métricas de qualidade
    // - Cobertura de testes: 87% (+277%)
    // - Bugs por sprint: 4 (-78%)
    // - Tempo médio de correção: 1.5 horas (-75%)
    // - Dívida técnica: 8 dias (-82%)
    // - Satisfação do dev: 8.5/10 (+112%)
    
    // Métricas de produtividade
    // - Velocidade do time: +65%
    // - Features entregues por sprint: +45%
    // - Retrabalho: -70%
    // - Conflitos de merge: -85%
}
```

#### ROI (Return on Investment) do SRP

```java
public class CalculadoraROI {
    public static void calcularROI() {
        // INVESTIMENTO
        int horasRefactoring = 480; // 3 meses, 2 devs
        double custoHoraDev = 50.0;
        double investimentoTotal = horasRefactoring * custoHoraDev;
        // Total: R$ 24.000
        
        // RETORNO (por ano)
        double economiaBugs = 18 * 4 * 12 * custoHoraDev; // R$ 43.200
        double economiaManutencao = 10 * 52 * custoHoraDev; // R$ 26.000
        double ganhoVelocidade = 8 * 52 * custoHoraDev; // R$ 20.800
        double retornoTotal = economiaBugs + economiaManutencao + ganhoVelocidade;
        // Total: R$ 90.000
        
        double roi = ((retornoTotal - investimentoTotal) / investimentoTotal) * 100;
        System.out.println("ROI: " + roi + "%"); // ROI: 275%
        System.out.println("Payback: " + (investimentoTotal / (retornoTotal / 12)) + " meses");
        // Payback: 3.2 meses
    }
}
```

### 8.5 Síntese do Impacto

```
┌────────────────────────────────────────────────────────────┐
│          BENEFÍCIOS DO SRP - VISÃO CONSOLIDADA             │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  🔧 MANUTENIBILIDADE                                       │
│     ├─ Mudanças isoladas e seguras                        │
│     ├─ Menor risco de regressão                           │
│     ├─ Código autodocumentado                             │
│     └─ Facilita debugging                                 │
│                                                             │
│  ✅ TESTABILIDADE                                          │
│     ├─ Testes unitários simples                           │
│     ├─ Mocks fáceis de criar                              │
│     ├─ Alta cobertura alcançável                          │
│     └─ Testes rápidos e confiáveis                        │
│                                                             │
│  📈 ESCALABILIDADE                                         │
│     ├─ Desenvolvimento paralelo                           │
│     ├─ Crescimento orgânico                               │
│     ├─ Onboarding facilitado                              │
│     └─ Redução de conflitos                               │
│                                                             │
│  🔄 REUSABILIDADE                                          │
│     ├─ Componentes especializados                         │
│     ├─ Fácil composição                                   │
│     ├─ DRY (Don't Repeat Yourself)                        │
│     └─ Biblioteca de componentes                          │
│                                                             │
│  🎯 CLAREZA E COMPREENSÃO                                  │
│     ├─ Intenção explícita                                 │
│     ├─ Menos complexidade cognitiva                       │
│     ├─ Nomes descritivos possíveis                        │
│     └─ Estrutura lógica clara                             │
│                                                             │
│  💰 ECONOMIA                                               │
│     ├─ Menor custo de manutenção                          │
│     ├─ Menos bugs em produção                             │
│     ├─ Maior produtividade                                │
│     └─ ROI positivo em curto prazo                        │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

---

## 📚 Referências e Recursos

### Livros Fundamentais
- **"Clean Code"** - Robert C. Martin (Uncle Bob)
- **"Agile Software Development, Principles, Patterns, and Practices"** - Robert C. Martin
- **"Design Patterns: Elements of Reusable Object-Oriented Software"** - Gang of Four
- **"Refactoring: Improving the Design of Existing Code"** - Martin Fowler

### Artigos e Papers
- "The Single Responsibility Principle" - Robert C. Martin
- "On the Criteria To Be Used in Decomposing Systems into Modules" - David Parnas (1972)

### Ferramentas de Análise
```java
// Ferramentas para detectar violações do SRP

// 1. SonarQube - Análise de complexidade
// Identifica classes com muitas responsabilidades
// Métricas: complexidade ciclomática, acoplamento

// 2. JDepend - Análise de dependências
// Calcula métricas de acoplamento e coesão

// 3. PMD - Detector de code smells
// Regras: God Class, Long Method, Feature Envy

// 4. Checkstyle - Verificação de padrões
// Configura limites de linhas por classe/método

// Exemplo de configuração PMD
public class ConfiguracaoPMD {
    // pmd-rules.xml
    /*
    <rule name="GodClass">
        <properties>
            <property name="methods" value="20" />
            <property name="lines" value="200" />
        </properties>
    </rule>
    */
}
```

---

## 🎓 Conclusão

O **Princípio da Responsabilidade Única** não é apenas uma diretriz técnica, mas uma filosofia de design que permeia todos os aspectos do desenvolvimento de software de qualidade. Sua aplicação consistente resulta em:

1. **Código mais limpo e profissional**
2. **Equipes mais produtivas e satisfeitas**
3. **Software mais robusto e confiável**
4. **Menor custo total de propriedade**
5. **Maior agilidade para mudanças de negócio**

### Recomendações Práticas

```java
public class GuiaAplicacaoPratica {
    
    // 1. Comece pequeno
    public void iniciar() {
        // Não refatore tudo de uma vez
        // Aplique SRP em código novo primeiro
        // Refatore gradualmente código legado
    }
    
    // 2. Use revisão de código
    public void revisarCodigo() {
        // Pergunta-chave: "Esta classe tem mais de uma razão para mudar?"
        // Se sim, considere refatorar
    }
    
    // 3. Automatize verificações
    public void automatizar() {
        // Configure ferramentas de análise estática
        // Defina limites razoáveis (ex: 200 linhas por classe)
        // Integre ao CI/CD
    }
    
    // 4. Eduque a equipe
    public void educar() {
        // Sessões de code review focadas em SOLID
        // Pair programming para disseminar conhecimento
        // Documentação de padrões do projeto
    }
    
    // 5. Meça e melhore
    public void medir() {
        // Acompanhe métricas de qualidade
        // Celebre melhorias
        // Ajuste processo baseado em dados
    }
}
```

### Chamada para Ação

> **"O código que escrevemos hoje é o legado que deixamos para o amanhã."**

Aplique o SRP não por obrigação, mas por **profissionalismo** e **respeito** aos futuros mantenedores do código (que podem ser você mesmo daqui 6 meses).

---

## 📞 Contato e Contribuições

Este documento é um **guia vivo** e pode ser atualizado com novos exemplos, casos de uso e melhores práticas.

**Contribua:**
- Compartilhe seus casos de sucesso na aplicação do SRP
- Sugira novos exemplos práticos
- Reporte padrões e anti-padrões que encontrou

---

**Versão:** 1.0  
**Última Atualização:** Setembro 2025

---

## 🔍 Apêndice A: Checklist de Revisão SRP

Use este checklist durante code reviews para garantir aderência ao SRP:

```java
public class ChecklistSRP {
    
    // ✅ CRITÉRIOS DE CONFORMIDADE
    
    public boolean verificarConformidade(Classe classe) {
        return verificarNome(classe)
            && verificarTamanho(classe)
            && verificarCoesao(classe)
            && verificarDependencias(classe)
            && verificarMotivosParaMudar(classe);
    }
    
    // 1. NOME DA CLASSE
    private boolean verificarNome(Classe classe) {
        // ✅ Nome descreve UMA responsabilidade
        // ✅ Não contém "E", "Gerenciador", "Controlador", "Utilitário"
        // ❌ NomeClasseQueGerenciaEValida
        // ✅ ValidadorUsuario, GeradorRelatorio
        return true;
    }
    
    // 2. TAMANHO
    private boolean verificarTamanho(Classe classe) {
        // ✅ Menos de 200 linhas (ideal: 50-150)
        // ✅ Menos de 10 métodos públicos
        // ✅ Menos de 5 dependências
        return classe.getLinhas() < 200 
            && classe.getMetodosPublicos() < 10
            && classe.getDependencias().size() < 5;
    }
    
    // 3. COESÃO
    private boolean verificarCoesao(Classe classe) {
        // ✅ Todos os métodos usam os mesmos atributos
        // ✅ Métodos colaboram entre si
        // ❌ Métodos independentes que não se relacionam
        return calcularCoesao(classe) > 0.7; // 70%+ de coesão
    }
    
    // 4. DEPENDÊNCIAS
    private boolean verificarDependencias(Classe classe) {
        // ✅ Dependências do mesmo domínio/contexto
        // ❌ Dependências de múltiplos contextos (DB, Email, File, HTTP)
        Set<String> contextos = classe.getDependencias()
            .stream()
            .map(d -> d.getContexto())
            .collect(Collectors.toSet());
        return contextos.size() <= 2;
    }
    
    // 5. MOTIVOS PARA MUDAR
    private boolean verificarMotivosParaMudar(Classe classe) {
        // Perguntas chave:
        // - Quantos stakeholders solicitariam mudanças nesta classe?
        // - Mudança no formato de saída afeta esta classe?
        // - Mudança na persistência afeta esta classe?
        // - Mudança na regra de negócio afeta esta classe?
        // 
        // ✅ Apenas 1 resposta "sim" = SRP correto
        // ❌ 2+ respostas "sim" = viola SRP
        return contarMotivosParaMudar(classe) == 1;
    }
    
    private double calcularCoesao(Classe classe) {
        // LCOM (Lack of Cohesion in Methods)
        // Quanto menor, melhor a coesão
        return 0.8; // Implementação simplificada
    }
    
    private int contarMotivosParaMudar(Classe classe) {
        return 1; // Implementação simplificada
    }
}
```

---

## 🎯 Apêndice B: Padrões de Design e SRP

O SRP é a base para diversos padrões de design:

### B.1 Strategy Pattern

```java
// O Strategy Pattern naturalmente segue SRP
// Cada estratégia tem UMA responsabilidade

public interface EstrategiaDesconto {
    double calcular(double valor);
}

// Responsabilidade: Desconto para clientes VIP
public class DescontoVIP implements EstrategiaDesconto {
    @Override
    public double calcular(double valor) {
        return valor * 0.20; // 20% de desconto
    }
}

// Responsabilidade: Desconto para primeira compra
public class DescontoPrimeiraCompra implements EstrategiaDesconto {
    @Override
    public double calcular(double valor) {
        return valor * 0.10; // 10% de desconto
    }
}

// Responsabilidade: Desconto sazonal
public class DescontoBlackFriday implements EstrategiaDesconto {
    @Override
    public double calcular(double valor) {
        return valor * 0.50; // 50% de desconto
    }
}

// Contexto que usa a estratégia
public class CalculadoraPreco {
    private EstrategiaDesconto estrategia;
    
    public CalculadoraPreco(EstrategiaDesconto estrategia) {
        this.estrategia = estrategia;
    }
    
    public double calcularPrecoFinal(double precoBase) {
        double desconto = estrategia.calcular(precoBase);
        return precoBase - desconto;
    }
    
    public void setEstrategia(EstrategiaDesconto estrategia) {
        this.estrategia = estrategia;
    }
}

// Uso
public class ExemploStrategy {
    public static void main(String[] args) {
        double preco = 1000.0;
        
        // Cliente VIP
        CalculadoraPreco calc = new CalculadoraPreco(new DescontoVIP());
        System.out.println("Preço VIP: " + calc.calcularPrecoFinal(preco));
        
        // Black Friday
        calc.setEstrategia(new DescontoBlackFriday());
        System.out.println("Preço Black Friday: " + calc.calcularPrecoFinal(preco));
    }
}
```

### B.2 Observer Pattern

```java
// Observer Pattern com SRP: cada observador tem uma responsabilidade

public interface Observer {
    void atualizar(Evento evento);
}

// Responsabilidade: Enviar notificações por email
public class EmailObserver implements Observer {
    private final ServicoEmail servicoEmail;
    
    public EmailObserver(ServicoEmail servicoEmail) {
        this.servicoEmail = servicoEmail;
    }
    
    @Override
    public void atualizar(Evento evento) {
        if (evento.getTipo().equals("PEDIDO_CRIADO")) {
            servicoEmail.enviar(
                evento.getDestinatario(),
                "Pedido Confirmado",
                "Seu pedido foi criado com sucesso!"
            );
        }
    }
}

// Responsabilidade: Registrar logs
public class LogObserver implements Observer {
    private final Logger logger;
    
    public LogObserver(Logger logger) {
        this.logger = logger;
    }
    
    @Override
    public void atualizar(Evento evento) {
        logger.info(String.format(
            "Evento: %s | Timestamp: %s",
            evento.getTipo(),
            evento.getTimestamp()
        ));
    }
}

// Responsabilidade: Atualizar métricas
public class MetricasObserver implements Observer {
    private final ServicoMetricas metricas;
    
    public MetricasObserver(ServicoMetricas metricas) {
        this.metricas = metricas;
    }
    
    @Override
    public void atualizar(Evento evento) {
        metricas.incrementar("eventos." + evento.getTipo().toLowerCase());
    }
}

// Subject
public class PublicadorEventos {
    private final List<Observer> observers = new ArrayList<>();
    
    public void adicionarObserver(Observer observer) {
        observers.add(observer);
    }
    
    public void removerObserver(Observer observer) {
        observers.remove(observer);
    }
    
    public void notificar(Evento evento) {
        for (Observer observer : observers) {
            observer.atualizar(evento);
        }
    }
}
```

### B.3 Factory Pattern

```java
// Factory Pattern com SRP: fábrica tem APENAS responsabilidade de criação

// Responsabilidade: Criar processadores de pagamento
public class ProcessadorPagamentoFactory {
    
    public ProcessadorPagamento criar(String tipo) {
        switch (tipo.toUpperCase()) {
            case "CARTAO":
                return criarProcessadorCartao();
            case "BOLETO":
                return criarProcessadorBoleto();
            case "PIX":
                return criarProcessadorPix();
            default:
                throw new IllegalArgumentException("Tipo inválido: " + tipo);
        }
    }
    
    private ProcessadorPagamento criarProcessadorCartao() {
        GatewayCartao gateway = new GatewayCartao("api-key-123");
        ValidadorCartao validador = new ValidadorCartao();
        return new ProcessadorCartao(gateway, validador);
    }
    
    private ProcessadorPagamento criarProcessadorBoleto() {
        GeradorBoleto gerador = new GeradorBoleto();
        return new ProcessadorBoleto(gerador);
    }
    
    private ProcessadorPagamento criarProcessadorPix() {
        GeradorQRCode gerador = new GeradorQRCode();
        return new ProcessadorPix(gerador);
    }
}

// Cada processador tem SUA responsabilidade
public interface ProcessadorPagamento {
    ResultadoPagamento processar(double valor);
}

public class ProcessadorCartao implements ProcessadorPagamento {
    private final GatewayCartao gateway;
    private final ValidadorCartao validador;
    
    public ProcessadorCartao(GatewayCartao gateway, ValidadorCartao validador) {
        this.gateway = gateway;
        this.validador = validador;
    }
    
    @Override
    public ResultadoPagamento processar(double valor) {
        // Apenas lógica de cartão
        return gateway.cobrar(valor);
    }
}
```

### B.4 Decorator Pattern

```java
// Decorator Pattern com SRP: cada decorador adiciona UMA funcionalidade

// Interface base
public interface Relatorio {
    String gerar();
}

// Implementação concreta: Responsabilidade = gerar conteúdo base
public class RelatorioVendas implements Relatorio {
    private final List<Venda> vendas;
    
    public RelatorioVendas(List<Venda> vendas) {
        this.vendas = vendas;
    }
    
    @Override
    public String gerar() {
        StringBuilder sb = new StringBuilder();
        sb.append("RELATÓRIO DE VENDAS\n");
        for (Venda venda : vendas) {
            sb.append(venda.toString()).append("\n");
        }
        return sb.toString();
    }
}

// Decorador abstrato
public abstract class RelatorioDecorator implements Relatorio {
    protected Relatorio relatorio;
    
    public RelatorioDecorator(Relatorio relatorio) {
        this.relatorio = relatorio;
    }
}

// Responsabilidade: Adicionar cabeçalho
public class ComCabecalho extends RelatorioDecorator {
    
    public ComCabecalho(Relatorio relatorio) {
        super(relatorio);
    }
    
    @Override
    public String gerar() {
        return gerarCabecalho() + relatorio.gerar();
    }
    
    private String gerarCabecalho() {
        return "═══════════════════════════════\n" +
               "   EMPRESA XYZ - " + LocalDate.now() + "\n" +
               "═══════════════════════════════\n\n";
    }
}

// Responsabilidade: Adicionar rodapé
public class ComRodape extends RelatorioDecorator {
    
    public ComRodape(Relatorio relatorio) {
        super(relatorio);
    }
    
    @Override
    public String gerar() {
        return relatorio.gerar() + gerarRodape();
    }
    
    private String gerarRodape() {
        return "\n═══════════════════════════════\n" +
               "Gerado automaticamente em " + LocalDateTime.now() +
               "\n═══════════════════════════════";
    }
}

// Responsabilidade: Adicionar assinatura digital
public class ComAssinatura extends RelatorioDecorator {
    private final ServicoAssinatura servicoAssinatura;
    
    public ComAssinatura(Relatorio relatorio, ServicoAssinatura servico) {
        super(relatorio);
        this.servicoAssinatura = servico;
    }
    
    @Override
    public String gerar() {
        String conteudo = relatorio.gerar();
        String assinatura = servicoAssinatura.assinar(conteudo);
        return conteudo + "\n\nAssinatura Digital: " + assinatura;
    }
}

// Uso: Composição de decoradores
public class ExemploDecorator {
    public static void main(String[] args) {
        List<Venda> vendas = obterVendas();
        
        // Relatório simples
        Relatorio relatorio = new RelatorioVendas(vendas);
        
        // Adicionar cabeçalho e rodapé
        relatorio = new ComCabecalho(relatorio);
        relatorio = new ComRodape(relatorio);
        
        // Adicionar assinatura
        ServicoAssinatura servico = new ServicoAssinatura();
        relatorio = new ComAssinatura(relatorio, servico);
        
        System.out.println(relatorio.gerar());
    }
    
    private static List<Venda> obterVendas() {
        return Arrays.asList(
            new Venda("001", 100.0),
            new Venda("002", 250.0)
        );
    }
}
```

---

## 📊 Apêndice C: Métricas de Código

### C.1 Ferramentas de Medição

```java
// Exemplo de configuração para análise estática

// pom.xml (Maven)
/*
<plugin>
    <groupId>org.sonarsource.scanner.maven</groupId>
    <artifactId>sonar-maven-plugin</artifactId>
    <version>3.9.1.2184</version>
</plugin>

<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-pmd-plugin</artifactId>
    <version>3.15.0</version>
    <configuration>
        <rulesets>
            <ruleset>custom-pmd-rules.xml</ruleset>
        </rulesets>
    </configuration>
</plugin>
*/

// custom-pmd-rules.xml
/*
<?xml version="1.0"?>
<ruleset name="Custom Rules">
    
    <!-- Limitar tamanho de classes -->
    <rule ref="category/java/design.xml/ExcessiveClassLength">
        <properties>
            <property name="minimum" value="200" />
        </properties>
    </rule>
    
    <!-- Limitar número de métodos -->
    <rule ref="category/java/design.xml/TooManyMethods">
        <properties>
            <property name="maxmethods" value="10" />
        </properties>
    </rule>
    
    <!-- Detectar God Classes -->
    <rule ref="category/java/design.xml/GodClass" />
    
    <!-- Limitar complexidade ciclomática -->
    <rule ref="category/java/design.xml/CyclomaticComplexity">
        <properties>
            <property name="methodReportLevel" value="10" />
        </properties>
    </rule>
    
    <!-- Detectar acoplamento excessivo -->
    <rule ref="category/java/design.xml/CouplingBetweenObjects">
        <properties>
            <property name="threshold" value="5" />
        </properties>
    </rule>
    
</ruleset>
*/
```

### C.2 Script de Análise Customizado

```java
import java.io.File;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.*;
import java.util.stream.Collectors;

/**
 * Analisador de conformidade com SRP
 */
public class AnalisadorSRP {
    
    private static final int MAX_LINHAS = 200;
    private static final int MAX_METODOS = 10;
    private static final int MAX_DEPENDENCIAS = 5;
    
    public static void main(String[] args) throws Exception {
        String diretorio = args[0];
        List<RelatorioClasse> relatorios = analisarDiretorio(diretorio);
        
        gerarRelatorio(relatorios);
    }
    
    public static List<RelatorioClasse> analisarDiretorio(String dir) 
            throws Exception {
        List<RelatorioClasse> relatorios = new ArrayList<>();
        
        Files.walk(Path.of(dir))
            .filter(p -> p.toString().endsWith(".java"))
            .forEach(p -> {
                try {
                    RelatorioClasse rel = analisarArquivo(p.toFile());
                    relatorios.add(rel);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            });
        
        return relatorios;
    }
    
    public static RelatorioClasse analisarArquivo(File arquivo) 
            throws Exception {
        List<String> linhas = Files.readAllLines(arquivo.toPath());
        
        String nomeClasse = extrairNomeClasse(linhas);
        int totalLinhas = contarLinhasCodigo(linhas);
        int totalMetodos = contarMetodos(linhas);
        int totalDependencias = contarDependencias(linhas);
        
        boolean conformeSRP = totalLinhas <= MAX_LINHAS
            && totalMetodos <= MAX_METODOS
            && totalDependencias <= MAX_DEPENDENCIAS;
        
        return new RelatorioClasse(
            nomeClasse,
            arquivo.getName(),
            totalLinhas,
            totalMetodos,
            totalDependencias,
            conformeSRP
        );
    }
    
    private static String extrairNomeClasse(List<String> linhas) {
        return linhas.stream()
            .filter(l -> l.contains("class ") || l.contains("interface "))
            .findFirst()
            .map(l -> l.replaceAll(".*(?:class|interface)\\s+(\\w+).*", "$1"))
            .orElse("Unknown");
    }
    
    private static int contarLinhasCodigo(List<String> linhas) {
        return (int) linhas.stream()
            .filter(l -> !l.trim().isEmpty())
            .filter(l -> !l.trim().startsWith("//"))
            .filter(l -> !l.trim().startsWith("/*"))
            .filter(l -> !l.trim().startsWith("*"))
            .count();
    }
    
    private static int contarMetodos(List<String> linhas) {
        return (int) linhas.stream()
            .filter(l -> l.matches(".*\\s+(public|private|protected)\\s+.*\\(.*\\).*"))
            .filter(l -> !l.contains("class "))
            .count();
    }
    
    private static int contarDependencias(List<String> linhas) {
        return (int) linhas.stream()
            .filter(l -> l.trim().startsWith("import "))
            .filter(l -> !l.contains("java.lang"))
            .count();
    }
    
    public static void gerarRelatorio(List<RelatorioClasse> relatorios) {
        System.out.println("\n╔═══════════════════════════════════════════════════════╗");
        System.out.println("║          RELATÓRIO DE ANÁLISE SRP                     ║");
        System.out.println("╚═══════════════════════════════════════════════════════╝\n");
        
        long totalClasses = relatorios.size();
        long classesConformes = relatorios.stream()
            .filter(r -> r.conformeSRP)
            .count();
        
        double percentualConformidade = (double) classesConformes / totalClasses * 100;
        
        System.out.printf("Total de Classes: %d\n", totalClasses);
        System.out.printf("Classes Conformes: %d (%.1f%%)\n", 
            classesConformes, percentualConformidade);
        System.out.printf("Classes Não Conformes: %d (%.1f%%)\n\n", 
            totalClasses - classesConformes, 
            100 - percentualConformidade);
        
        System.out.println("Classes que violam SRP:");
        System.out.println("─".repeat(80));
        
        relatorios.stream()
            .filter(r -> !r.conformeSRP)
            .sorted(Comparator.comparingInt(r -> -r.totalLinhas))
            .forEach(r -> {
                System.out.printf("%-40s | Linhas: %4d | Métodos: %2d | Deps: %2d\n",
                    r.nomeClasse,
                    r.totalLinhas,
                    r.totalMetodos,
                    r.totalDependencias
                );
                
                if (r.totalLinhas > MAX_LINHAS) {
                    System.out.println("  ⚠️  Excede limite de linhas");
                }
                if (r.totalMetodos > MAX_METODOS) {
                    System.out.println("  ⚠️  Muitos métodos");
                }
                if (r.totalDependencias > MAX_DEPENDENCIAS) {
                    System.out.println("  ⚠️  Muitas dependências");
                }
                System.out.println();
            });
    }
    
    static class RelatorioClasse {
        String nomeClasse;
        String nomeArquivo;
        int totalLinhas;
        int totalMetodos;
        int totalDependencias;
        boolean conformeSRP;
        
        public RelatorioClasse(String nomeClasse, String nomeArquivo,
                              int totalLinhas, int totalMetodos,
                              int totalDependencias, boolean conformeSRP) {
            this.nomeClasse = nomeClasse;
            this.nomeArquivo = nomeArquivo;
            this.totalLinhas = totalLinhas;
            this.totalMetodos = totalMetodos;
            this.totalDependencias = totalDependencias;
            this.conformeSRP = conformeSRP;
        }
    }
}
```

---

## 🎓 Apêndice D: Exercícios Práticos

### Exercício 1: Identificar Violações

```java
// Analise a classe abaixo e identifique as violações do SRP
public class GerenciadorUsuario {
    private Connection conexao;
    
    public void criarUsuario(String nome, String email, String senha) {
        // Validação
        if (!email.contains("@")) {
            throw new IllegalArgumentException("Email inválido");
        }
        
        // Hash de senha
        String senhaHash = BCrypt.hashpw(senha, BCrypt.gensalt());
        
        // Persistência
        try {
            PreparedStatement stmt = conexao.prepareStatement(
                "INSERT INTO usuarios (nome, email, senha) VALUES (?, ?, ?)"
            );
            stmt.setString(1, nome);
            stmt.setString(2, email);
            stmt.setString(3, senhaHash);
            stmt.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
        
        // Email de boas-vindas
        enviarEmail(email, "Bem-vindo!", "Obrigado por se cadastrar!");
        
        // Log
        System.out.println("Usuário " + nome + " criado em " + new Date());
        
        // Auditoria
        registrarAuditoria("CREATE_USER", nome);
    }
    
    private void enviarEmail(String para, String assunto, String corpo) {
        // Implementação SMTP
    }
    
    private void registrarAuditoria(String acao, String detalhes) {
        // Gravar em arquivo de auditoria
    }
}

// TAREFA: Refatore esta classe aplicando o SRP
// Quantas classes você criaria? Quais seriam suas responsabilidades?
```

### Exercício 2: Refatorar God Object

```java
// Refatore este "God Object" aplicando SRP
public class SistemaEcommerce {
    // ... imagine 1000+ linhas de código aqui
    
    public void processarCompra(Carrinho carrinho, String formaPagamento) {
        // Validações
        // Cálculos
        // Estoque
        // Pagamento
        // Notificações
        // Relatórios
    }
}

// TAREFA: Desenhe a arquitetura refatorada
// Quais classes você criaria?
// Como elas se relacionariam?
```

---

## 🏆 Palavras Finais

O **Princípio da Responsabilidade Única** é mais que uma técnica — é uma mentalidade que transforma a forma como pensamos sobre código. Cada classe, cada método, cada função deve ter um propósito claro e bem definido.

**Lembre-se:**
- ✅ Classes pequenas e focadas são mais fáceis de entender
- ✅ Responsabilidades únicas facilitam testes
- ✅ Código coeso é código profissional
- ✅ SRP é a base para todos os outros princípios SOLID

### Citação Final

> *"Fazer as coisas certas é mais importante do que fazer as coisas rapidamente. E fazer as coisas certas começa com o design correto."* — Robert C. Martin

---

**📖 Continue Aprendendo:**
- Explore os outros princípios SOLID
- Pratique refatoração regularmente
- Compartilhe conhecimento com sua equipe
- Revise código pensando em SRP

**💡 Próximos Passos:**
1. Aplique SRP no seu próximo commit
2. Revise código existente buscando violações
3. Configure ferramentas de análise estática
4. Ensine SRP para um colega

---

**Licença:** MIT  
**Autor:** Guia Colaborativo  
**Contribuições:** Bem-vindas via pull request

---

*"Clean code always looks like it was written by someone who cares."* — Robert C. Martin

═══════════════════════════════════════════════════════════════════════════

**FIM DO DOCUMENTO**# Princípio da Responsabilidade Única (SRP)
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
// ❌ Violando o SRP
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
    public void salvar() throws SQLException {
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

```python
class ProcessadorPedido:
    def processar_pedido(self, pedido):
        # Validação
        if not pedido.items:
            raise ValueError("Pedido vazio")
        
        # Cálculo
        total = 0
        for item in pedido.items:
            total += item.preco * item.quantidade
        
        # Desconto
        if total > 1000:
            total *= 0.9
        
        # Imposto
        total *= 1.15
        
        # Persistência
        conn = sqlite3.connect('pedidos.db')
        cursor = conn.cursor()
        cursor.execute(
            "INSERT INTO pedidos (cliente_id, total) VALUES (?, ?)",
            (pedido.cliente_id, total)
        )
        conn.commit()
        conn.close()
        
        # Notificação
        enviar_email(pedido.cliente.email, f"Pedido confirmado: R$ {total}")
        
        # Log
        print(f"Pedido {pedido.id} processado com sucesso")
        
        return total
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

## 5. Abstrações Conceituais

### 5.1 SRP e Acoplamento (Coupling)

O **acoplamento** mede o grau de interdependência entre módulos. O SRP reduz o acoplamento ao isolar responsabilidades.

#### Tipos de Acoplamento

```
┌─────────────────────────────────────────────────────────┐
│  Níveis de Acoplamento (do pior para o melhor)         │
├─────────────────────────────────────────────────────────┤
│  1. Acoplamento de Conteúdo    (Content Coupling)      │
│     └─ Uma classe modifica dados internos de outra     │
│                                                          │
│  2. Acoplamento Comum          (Common Coupling)        │
│     └─ Múltiplas classes compartilham dados globais    │
│                                                          │
│  3. Acoplamento de Controle    (Control Coupling)       │
│     └─ Uma classe controla o fluxo de outra            │
│                                                          │
│  4. Acoplamento de Dados       (Data Coupling)          │
│     └─ Classes compartilham apenas dados simples       │
│                                                          │
│  5. Acoplamento de Mensagem    (Message Coupling) ✅    │
│     └─ Classes se comunicam via interfaces/mensagens   │
└─────────────────────────────────────────────────────────┘
```

#### Exemplo: Reduzindo Acoplamento com SRP

```java
// ❌ Alto Acoplamento (viola SRP)
public class ProcessadorPagamento {
    public void processar(Pedido pedido) throws Exception {
        // Acoplamento direto com banco de dados
        Connection conn = DriverManager.getConnection(
            "jdbc:mysql://localhost:3306/db", "root", "password"
        );
        PreparedStatement stmt = conn.prepareStatement(
            "UPDATE pedidos SET status = ? WHERE id = ?"
        );
        
        // Acoplamento direto com API externa
        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create("https://api.pagamento.com/charge"))
            .POST(HttpRequest.BodyPublishers.ofString(
                "{\"amount\": " + pedido.getTotal() + "}"
            ))
            .build();
        client.send(request, HttpResponse.BodyHandlers.ofString());
        
        // Acoplamento direto com email
        Properties props = new Properties();
        props.put("mail.smtp.host", "smtp.gmail.com");
        Session session = Session.getInstance(props);
        MimeMessage message = new MimeMessage(session);
        message.setSubject("Pagamento processado: " + pedido.getId());
        Transport.send(message);
        
        // Acoplamento direto com log
        FileWriter fw = new FileWriter("pagamentos.log", true);
        fw.write(LocalDateTime.now() + ": Pedido " + pedido.getId() + " processado\n");
        fw.close();
    }
}

// ✅ Baixo Acoplamento (segue SRP)
public interface PedidoRepository {
    void atualizarStatus(String pedidoId, String status);
}

public interface PaymentGateway {
    ResultadoPagamento cobrar(double valor);
}

public interface Notificador {
    void enviarConfirmacao(Pedido pedido);
}

public interface Logger {
    void info(String mensagem);
}

public class ProcessadorPagamento {
    private final PedidoRepository repositorio;
    private final PaymentGateway gateway;
    private final Notificador notificador;
    private final Logger logger;
    
    public ProcessadorPagamento(
            PedidoRepository repositorio,
            PaymentGateway gateway,
            Notificador notificador,
            Logger logger) {
        this.repositorio = repositorio;
        this.gateway = gateway;
        this.notificador = notificador;
        this.logger = logger;
    }
    
    public ResultadoPagamento processar(Pedido pedido) {
        ResultadoPagamento resultado = gateway.cobrar(pedido.getTotal());
        
        if (resultado.isSucesso()) {
            repositorio.atualizarStatus(pedido.getId(), "PAGO");
            notificador.enviarConfirmacao(pedido);
            logger.info("Pagamento processado: " + pedido.getId());
        }
        
        return resultado;
    }
}
```

**Benefícios:**
- Dependências explícitas (via construtor)
- Fácil substituição de implementações (mock para testes)
- Cada dependência tem responsabilidade única
- Menor impacto de mudanças

### 5.2 SRP e Modularidade

A **modularidade** é a capacidade de decompor um sistema em módulos independentes e intercambiáveis.

#### Princípios de Modularidade Reforçados pelo SRP

```
┌──────────────────────────────────────────────────────┐
│              CARACTERÍSTICAS MODULARES                │
├──────────────────────────────────────────────────────┤
│                                                       │
│  🔒 ENCAPSULAMENTO                                   │
│     └─ Cada módulo esconde detalhes de implementação│
│                                                       │
│  🔌 INTERFACES CLARAS                                │
│     └─ Contratos bem definidos entre módulos        │
│                                                       │
│  ♻️  REUSABILIDADE                                    │
│     └─ Módulos podem ser usados em contextos diversos│
│                                                       │
│  🧩 COMPOSIÇÃO                                        │
│     └─ Módulos podem ser combinados de várias formas│
│                                                       │
│  🔄 SUBSTITUIBILIDADE                                │
│     └─ Módulos podem ser trocados sem impacto global│
│                                                       │
└──────────────────────────────────────────────────────┘
```

#### Exemplo: Sistema Modular com SRP

```java
// Módulo de Autenticação
public interface Autenticador {
    Usuario autenticar(Credenciais credenciais);
}

public class AutenticadorJWT implements Autenticador {
    private final UsuarioRepository repository;
    private final GeradorToken geradorToken;
    
    public Usuario autenticar(Credenciais credenciais) {
        Usuario usuario = repository.buscarPorEmail(credenciais.email());
        if (verificarSenha(usuario, credenciais.senha())) {
            String token = geradorToken.gerar(usuario);
            return usuario.comToken(token);
        }
        throw new AutenticacaoException("Credenciais inválidas");
    }
}

// Módulo de Autorização
public interface Autorizador {
    boolean temPermissao(Usuario usuario, String recurso, String acao);
}

public class AutorizadorRBAC implements Autorizador {
    private final PermissaoRepository repository;
    
    public boolean temPermissao(Usuario usuario, String recurso, String acao) {
        Set<String> papeis = usuario.getPapeis();
        return repository.buscarPermissoes(papeis)
            .stream()
            .anyMatch(p -> p.permite(recurso, acao));
    }
}

// Módulo de Validação
public interface Validador<T> {
    ResultadoValidacao validar(T entidade);
}

public class ValidadorUsuario implements Validador<Usuario> {
    private final List<Regra<Usuario>> regras;
    
    public ResultadoValidacao validar(Usuario usuario) {
        List<String> erros = new ArrayList<>();
        
        for (Regra<Usuario> regra : regras) {
            if (!regra.avaliar(usuario)) {
                erros.add(regra.getMensagemErro());
            }
        }
        
        return erros.isEmpty() 
            ? ResultadoValidacao.sucesso()
            : ResultadoValidacao.falha(erros);
    }
}

// Composição Modular
public class ServicoUsuario {
    private final Autenticador autenticador;
    private final Autorizador autorizador;
    private final Validador<Usuario> validador;
    private final UsuarioRepository repository;
    
    public Usuario criar(DadosUsuario dados, Usuario criador) {
        // Autorização
        if (!autorizador.temPermissao(criador, "usuarios", "criar")) {
            throw new AutorizacaoException("Sem permissão");
        }
        
        // Validação
        Usuario novoUsuario = Usuario.de(dados);
        ResultadoValidacao resultado = validador.validar(novoUsuario);
        if (!resultado.valido()) {
            throw new ValidacaoException(resultado.getErros());
        }
        
        // Persistência
        return repository.salvar(novoUsuario);
    }
}
```

### 5.3 SRP e Coesão

**Coesão** é a medida de quão fortemente relacionados os elementos de um módulo estão.

#### Níveis de Coesão (do pior para o melhor)

| Tipo | Descrição | Exemplo | Avaliação |
|------|-----------|---------|-----------|
| **Coincidental** | Elementos agrupados arbitrariamente | `Utilidades` com métodos aleatórios | ❌ Muito ruim |
| **Lógica** | Elementos relacionados por categoria geral | `Validadores` misturando diferentes contextos | ❌ Ruim |
| **Temporal** | Elementos que executam no mesmo momento | `Inicializador` que faz tudo no startup | ⚠️ Questionável |
| **Procedural** | Elementos que seguem uma sequência | `Pipeline` de processamento | ⚠️ Aceitável |
| **Comunicacional** | Elementos que operam nos mesmos dados | `GerenciadorProduto` | ✅ Bom |
| **Sequencial** | Saída de um é entrada de outro | `ProcessadorPedido` em pipeline | ✅ Muito bom |
| **Funcional** | Elementos contribuem para uma única tarefa | `CalculadoraDesconto` | ✅ Excelente |

```csharp
// ❌ Coesão Coincidental (viola SRP)
public class Utilidades
{
    public static string FormatarCPF(string cpf) { }
    public static void EnviarEmail(string destinatario) { }
    public static decimal CalcularJuros(decimal valor) { }
    public static bool ValidarJSON(string json) { }
}

// ✅ Coesão Funcional (segue SRP)
public class FormatadorDocumento
{
    public string FormatarCPF(string cpf) 
    {
        return $"{cpf.Substring(0,3)}.{cpf.Substring(3,3)}." +
               $"{cpf.Substring(6,3)}-{cpf.Substring(9,2)}";
    }
    
    public string FormatarCNPJ(string cnpj) { }
}

public class CalculadoraFinanceira
{
    public decimal CalcularJurosSimples(decimal valor, decimal taxa, int meses)
    {
        return valor * taxa * meses / 100;
    }
    
    public decimal CalcularJurosCompostos(decimal valor, decimal taxa, int meses)
    {
        return valor * Math.Pow(1 + taxa / 100, meses) - valor;
    }
}
```

---

## 6. SRP e os Princípios SOLID

### 6.1 Visão Geral da Interdependência

```
        ┌─────────────────────────────────────┐
        │   SOLID PRINCIPLES                  │
        └─────────────────────────────────────┘
                       │
        ┌──────────────┴──────────────┐
        │                             │
        ▼                             ▼
┌──────────────┐              ┌──────────────┐
│     SRP      │◄─────────────┤     OCP      │
│  (Foundation)│              │              │
└──────┬───────┘              └──────┬───────┘
       │                             │
       │         ┌───────────────────┘
       │         │
       ▼         ▼
┌──────────────────────┐
│        LSP           │
│                      │
└──────┬───────────────┘
       │
       │  ┌───────────────┐
       └──┤     ISP       │
          └───────┬───────┘
                  │
                  ▼
          ┌───────────────┐
          │     DIP       │
          └───────────────┘
```

### 6.2 SRP como Base para OCP (Open/Closed Principle)

**OCP**: "Entidades de software devem estar abertas para extensão, mas fechadas para modificação."

O SRP é **pré-requisito** para o OCP:

```java
// ❌ Viola SRP e OCP
public class ProcessadorPagamento {
    public ResultadoPagamento processar(Pedido pedido, String tipo) {
        if (tipo.equals("cartao")) {
            // Lógica cartão
            validarCartao();
            return cobrarCartao();
        } else if (tipo.equals("boleto")) {
            // Lógica boleto
            return gerarBoleto();
        } else if (tipo.equals("pix")) {
            // Lógica PIX
            return gerarQRCode();
        }
        throw new IllegalArgumentException("Tipo de pagamento inválido");
        // Adicionar novo método = modificar esta classe (viola OCP)
    }
    
    private void validarCartao() { /* ... */ }
    private ResultadoPagamento cobrarCartao() { return null; }
    private ResultadoPagamento gerarBoleto() { return null; }
    private ResultadoPagamento gerarQRCode() { return null; }
}

// ✅ Segue SRP e OCP
public interface MetodoPagamento {
    ResultadoPagamento processar(double valor);
}

public class PagamentoCartao implements MetodoPagamento {
    @Override
    public ResultadoPagamento processar(double valor) {
        // Apenas responsabilidade de cartão
        return cobrarCartao(valor);
    }
    
    private ResultadoPagamento cobrarCartao(double valor) {
        return new ResultadoPagamento(true, "Cartão processado");
    }
}

public class PagamentoBoleto implements MetodoPagamento {
    @Override
    public ResultadoPagamento processar(double valor) {
        // Apenas responsabilidade de boleto
        return gerarBoleto(valor);
    }
    
    private ResultadoPagamento gerarBoleto(double valor) {
        return new ResultadoPagamento(true, "Boleto gerado");
    }
}

public class PagamentoPix implements MetodoPagamento {
    @Override
    public ResultadoPagamento processar(double valor) {
        // Apenas responsabilidade de PIX
        return gerarQRCode(valor);
    }
    
    private ResultadoPagamento gerarQRCode(double valor) {
        return new ResultadoPagamento(true, "QR Code gerado");
    }
}

// Adicionar novo método = criar nova classe (segue OCP)
public class PagamentoCriptomoeda implements MetodoPagamento {
    @Override
    public ResultadoPagamento processar(double valor) {
        return processarBlockchain(valor);
    }
    
    private ResultadoPagamento processarBlockchain(double valor) {
        return new ResultadoPagamento(true, "Transação blockchain");
    }
}

public class ProcessadorPagamento {
    private final MetodoPagamento metodo;
    
    public ProcessadorPagamento(MetodoPagamento metodo) {
        this.metodo = metodo;
    }
    
    public ResultadoPagamento processar(Pedido pedido) {
        return metodo.processar(pedido.getTotal());
    }
}

// Classe auxiliar
public class ResultadoPagamento {
    private final boolean sucesso;
    private final String mensagem;
    
    public ResultadoPagamento(boolean sucesso, String mensagem) {
        this.sucesso = sucesso;
        this.mensagem = mensagem;
    }
    
    public boolean isSucesso() { return sucesso; }
    public String getMensagem() { return mensagem; }
}
```

**Como SRP habilita OCP:**
1. Classes com responsabilidade única são naturalmente **extensíveis**
2. Não há "lógica condicional" que precise ser modificada
3. Novas funcionalidades = novas classes, não modificação de existentes

### 6.3 SRP e ISP (Interface Segregation Principle)

**ISP**: "Clientes não devem ser forçados a depender de interfaces que não usam."

O SRP em classes leva ao ISP em interfaces:

```java
// ❌ Interface "gorda" (viola ISP e SRP)
public interface Trabalhador {
    void trabalhar();
    void comer();
    void dormir();
    void receberSalario();
    void pedirFerias();
    void fazerRelatorio();
}

// ✅ Interfaces segregadas (segue ISP e SRP)
public interface Trabalhavel {
    void trabalhar();
}

public interface Alimentavel {
    void comer();
}

public interface Descansavel {
    void dormir();
}

public interface Remuneravel {
    void receberSalario();
}

// Classes implementam apenas o que precisam
public class Robo implements Trabalhavel {
    // Robô não come, não dorme, não recebe salário
    public void trabalhar() { }
}

public class Funcionario implements Trabalhavel, Alimentavel, 
                                     Descansavel, Remuneravel {
    public void trabalhar() { }
    public void comer() { }
    public void dormir() { }
    public void receberSalario() { }
}
```

### 6.4 SRP e LSP (Liskov Substitution Principle)

**LSP**: "Subtipos devem ser substituíveis por seus tipos base."

SRP facilita LSP ao evitar classes com múltiplas responsabilidades conflitantes:

```java
// ❌ Viola LSP (e SRP)
public class Passaro {
    public void voar() {
        System.out.println("Voando...");
    }
    
    public void nadar() {
        System.out.println("Nadando...");
    }
}

public class Pinguim extends Passaro {
    @Override
    public void voar() {
        // Pinguim não voa! Viola LSP
        throw new UnsupportedOperationException("Pinguins não voam!");
    }
}

// ✅ Segue LSP e SRP
public abstract class Passaro {
    public abstract void mover();
}

public abstract class PassaroVoador extends Passaro {
    @Override
    public void mover() {
        voar();
    }
    
    public void voar() {
        System.out.println("Voando...");
    }
}

public abstract class PassaroNadador extends Passaro {
    @Override
    public void mover() {
        nadar();
    }
    
    public void nadar() {
        System.out.println("Nadando...");
    }
}

public class Aguia extends PassaroVoador {
    @Override
    public void voar() {
        System.out.println("Águia voando alto...");
    }
}

public class Pinguim extends PassaroNadador {
    @Override
    public void nadar() {
        System.out.println("Pinguim nadando...");
    }
}
```

### 6.5 SRP e DIP (Dependency Inversion Principle)

**DIP**: "Dependa de abstrações, não de implementações concretas."

SRP + DIP = arquitetura altamente desacoplada:

```java
// ❌ Viola SRP e DIP
public class PedidoService {
    public void processarPedido(Pedido pedido) throws Exception {
        // Dependência concreta de MySQL
        Connection conn = DriverManager.getConnection(
            "jdbc:mysql://localhost/dbname", "user", "pass"
        );
        PreparedStatement stmt = conn.prepareStatement(
            "INSERT INTO pedidos ..."
        );
        stmt.executeUpdate();
        conn.close();
        
        // Dependência concreta de SMTP
        Properties props = new Properties();
        props.put("mail.smtp.host", "smtp.gmail.com");
        Session session = Session.getInstance(props);
        MimeMessage message = new MimeMessage(session);
        Transport.send(message);
    }
}

// ✅ Segue SRP e DIP
public interface PedidoRepository {
    void salvar(Pedido pedido) throws Exception;
}

public interface Notificador {
    void enviar(String destinatario, String mensagem) throws Exception;
}

public class PedidoService {
    private final PedidoRepository repository;  // Abstração
    private final Notificador notificador;      // Abstração
    
    public PedidoService(PedidoRepository repository, Notificador notificador) {
        this.repository = repository;
        this.notificador = notificador;
    }
    
    public void processarPedido(Pedido pedido) throws Exception {
        // Depende de abstrações, não implementações
        repository.salvar(pedido);
        notificador.enviar(
            pedido.getCliente().getEmail(),
            "Pedido confirmado"
        );
    }
}

// Implementações concretas
public class MySQLPedidoRepository implements PedidoRepository {
    private final Connection db;
    
    public MySQLPedidoRepository(Connection db) {
        this.db = db;
    }
    
    @Override
    public void salvar(Pedido pedido) throws SQLException {
        PreparedStatement stmt = db.prepareStatement(
            "INSERT INTO pedidos (cliente_id, total) VALUES (?, ?)"
        );
        stmt.setInt(1, pedido.getClienteId());
        stmt.setDouble(2, pedido.getTotal());
        stmt.executeUpdate();
    }
}

public class EmailNotificador implements Notificador {
    private final String smtpHost;
    
    public EmailNotificador(String smtpHost) {
        this.smtpHost = smtpHost;
    }
    
    @Override
    public void enviar(String destinatario, String mensagem) throws Exception {
        // Implementação SMTP
        Properties props = new Properties();
        props.put("mail.smtp.host", smtpHost);
        Session session = Session.getInstance(props);
        
        MimeMessage message = new MimeMessage(session);
        message.setRecipients(Message.RecipientType.TO, destinatario);
        message.setText(mensagem);
        
        Transport.send(message);
    }
}
```

---

## 7. Armadilhas Comuns

### 7.1 Over-Engineering: Separação Excessiva

**Problema**: Criar classes demais, tornando o código difícil de navegar.

```ruby
# ❌ Exagero na separação
class UsuarioNomeValidator
  def validar(nome)
    nome.length >= 3
  end
end

class UsuarioEmailValidator
  def validar(email)
    email.include?('@')
  end
end

class UsuarioIdadeValidator
  def validar(idade)
    idade >= 18
  end
end

class UsuarioCPFValidator
  def validar(cpf)
    cpf.length == 11
  end
end

# Agora temos 4 classes para validação simples!

# ✅ Equilíbrio adequado
class UsuarioValidator
  def validar(usuario)
    erros = []
    erros << "Nome muito curto" if usuario.nome.length < 3
    erros << "Email inválido" unless usuario.email.include?('@')
    erros << "Idade mínima 18 anos" if usuario.idade < 18
    erros << "CPF inválido" if usuario.cpf.length != 11
    erros
  end
end
```

**Regra de Ouro**: Separe quando as validações tiverem **motivos diferentes para mudar** (ex: regras de negócio vs formato técnico).

### 7.2 Dificuldade em Definir "Responsabilidade"

**Problema**: Ambiguidade sobre o que constitui "uma responsabilidade".

#### Framework para Definir Responsabilidade

```
┌────────────────────────────────────────────────────────┐
│  PERGUNTAS PARA IDENTIFICAR RESPONSABILIDADE           │
├────────────────────────────────────────────────────────┤
│                                                         │
│  1️⃣  Quem solicitaria mudanças nesta classe?          │
│     ├─ Um stakeholder? ✅ Uma responsabilidade         │
│     └─ Múltiplos? ❌ Múltiplas responsabilidades      │
│                                                         │
│  2️⃣  Posso descrever a classe em uma frase sem "e"?   │
│     ├─ "Valida usuários" ✅                            │
│     └─ "Valida E salva E notifica" ❌                  │
│                                                         │
│  3️⃣  Os métodos operam nos mesmos dados?              │
│     ├─ Sim, alta coesão ✅                             │
│     └─ Não, baixa coesão ❌                            │
│                                                         │
│  4️⃣  Mudanças em um método afetam outros?             │
│     ├─ Sim, acoplamento interno ⚠️                     │
│     └─ Não, independentes ✅                           │
│                                                         │
│  5️⃣  Posso testar cada método isoladamente?           │
│     ├─ Sim ✅                                          │
│     └─ Não, dependências complexas ❌                  │
│                                                         │
└────────────────────────────────────────────────────────┘
```

### 7.3 SRP em Arquiteturas em Camadas

**Desafio**: Aplicar SRP em sistemas com múltiplas camadas (Presentation, Business, Data).

```
┌─────────────────────────────────────────────────────────┐
│            ARQUITETURA EM CAMADAS + SRP                 │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  ┌────────────────────────────────────────────┐        │
│  │  PRESENTATION LAYER                         │        │
│  │  ├─ UserController (HTTP)                  │        │
│  │  ├─ UserPresenter (Formatação)             │        │
│  │  └─ UserViewModel (DTO)                    │        │
│  └────────────────────────────────────────────┘        │
│                      │                                   │
│                      ▼                                   │
│  ┌────────────────────────────────────────────┐        │
│  │  APPLICATION LAYER                          │        │
│  │  ├─ CreateUserUseCase                      │        │
│  │  ├─ AuthenticateUserUseCase                │        │
│  │  └─ UpdateUserProfileUseCase               │        │
│  └────────────────────────────────────────────┘        │
│                      │                                   │
│                      ▼                                   │
│  ┌────────────────────────────────────────────┐        │
│  │  DOMAIN LAYER                               │        │
│  │  ├─ User (Entity)                           │        │
│  │  ├─ UserValidator (Business Rules)         │        │
│  │  └─ UserRepository (Interface)             │        │
│  └────────────────────────────────────────────┘        │
│                      │                                   │
│                      ▼                                   │
│  ┌────────────────────────────────────────────┐        │
│  │  INFRASTRUCTURE LAYER                       │        │
│  │  ├─ MySQLUserRepository (Implementation)   │        │
│  │  ├─ EmailService                            │        │
│  │  └─ CacheService                            │        │
│  └────────────────────────────────────────────┘        │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

**Cada camada tem responsabilidade única:**
- **Presentation**: Interface com usuário
- **Application**: Orquestração de casos de uso
- **Domain**: Lógica de negócio
- **Infrastructure**: Detalhes técnicos

### 7.4 SRP em Microsserviços

**Desafio**: Definir boundaries de responsabilidade em arquitetura distribuída.

```
🏢 Sistema de E-commerce (Microsserviços)

┌──────────────┐  ┌──────────────┐  ┌──────────────┐
│   Catálogo   │  │   Pedidos    │  │  Pagamento   │
│   Service    │  │   Service    │  │   Service    │
│              │  │              │  │              │
│ - Produtos   │  │ - Criar      │  │ - Processar  │
│ - Categorias │  │ - Atualizar  │  │ - Validar    │
│ - Busca      │  │ - Cancelar   │  │ - Estornar   │
└──────────────┘  └──────────────┘  └──────────────┘
       │                  │                  │
       └──────────────────┴──────────────────┘
                          │
              ┌───────────┴───────────┐
              │   Message Bus (Kafka) │
              └───────────────────────┘
```

**Princípio**: Cada microsserviço = Uma responsabilidade de negócio (Bounded Context do DDD).

### 7.5 Classes Utilitárias e SRP

**Problema comum**: Classes `Utils` que violam SRP.

```java
// ❌ Classe Utils genérica (viola SRP)
public class Utils {
    public static String formatarCPF(String cpf) {
        return cpf.substring(0, 3) + "." + 
               cpf.substring(3, 6) + "." + 
               cpf.substring(6, 9) + "-" + 
               cpf.substring(9, 11);
    }
    
    public static boolean validarEmail(String email) {
        return email.contains("@");
    }
    
    public static int calcularIdade(LocalDate dataNascimento) {
        return Period.between(dataNascimento, LocalDate.now()).getYears();
    }
    
    public static String gerarHash(String senha) {
        return BCrypt.hashpw(senha, BCrypt.gensalt());
    }
    
    public static String converterParaJSON(Object obj) {
        return new Gson().toJson(obj);
    }
}

// ✅ Classes especializadas (segue SRP)
public class FormatadorDocumento {
    public String formatarCPF(String cpf) {
        return cpf.substring(0, 3) + "." + 
               cpf.substring(3, 6) + "." + 
               cpf.substring(6, 9) + "-" + 
               cpf.substring(9, 11);
    }
    
    public String formatarCNPJ(String cnpj) {
        return cnpj.substring(0, 2) + "." +
               cnpj.substring(2, 5) + "." +
               cnpj.substring(5, 8) + "/" +
               cnpj.substring(8, 12) + "-" +
               cnpj.substring(12, 14);
    }
}

public class ValidadorEmail {
    private static final Pattern EMAIL_PATTERN = 
        Pattern.compile("^[^@]+@[^@]+\\.[^@]+$");
    
    public boolean validar(String email) {
        return EMAIL_PATTERN.matcher(email).matches();
    }
}

public class CalculadoraIdade {
    public int calcular(LocalDate dataNascimento) {
        return Period.between(dataNascimento, LocalDate.now()).getYears();
    }
}

public class GeradorHash {
    public String gerar(String senha) {
        return BCrypt.hashpw(senha, BCrypt.gensalt());
    }
    
    public boolean verificar(String senha, String hash) {
        return BCrypt.checkpw(senha, hash);
    }
}

public class ConversorJSON {
    private final Gson gson = new Gson();
    
    public String paraJSON(Object obj) {
        return gson.toJson(obj);
    }
    
    public <T> T deJSON(String json, Class<T> classe) {
        return gson.fromJson(json, classe);
    }
}
```

---

## 8. Impacto na Qualidade do Software

### 8.1 Manutenibilidade

**Antes do SRP:**
```
Mudança solicitada: "Alterar formato do relatório de PDF para Excel"

Impacto:
├─ Modificar classe GerenciadorRelatorio (250 linhas)
├─ Risco de quebrar cálculos (mesma classe)
├─ Risco de quebrar persistência (mesma classe)
├─ Necessário testar TUDO novamente
└─ Tempo estimado: 8 horas
```

**Depois do SRP:**
```
Mudança solicitada: "Alterar formato do relatório de PDF para Excel"

Impacto:
├─ Criar nova classe: ExcelReportFormatter (50 linhas)
├─ Implementar interface ReportFormatter
├─ Trocar dependência no container DI
├─ Testar apenas novo formatter
└─ Tempo estimado: 2 horas
```

**Métricas de Manutenibilidade:**

| Métrica | Sem SRP | Com SRP | Melhoria |
|---------|---------|---------|----------|
| Tempo médio para correção de bug | 4h | 1h | **75%** |
| Linhas alteradas por mudança | 120 | 25 | **79%** |
| Risco de regressão | Alto | Baixo | **Significativa** |
| Facilidade de entendimento (1-10) | 4 | 8 | **100%** |

### 8.2 Testabilidade

```java
// ❌ Difícil de testar (viola SRP)
public class ServicoUsuario {
    public void criarUsuario(String nome, String email) throws Exception {
        // Lógica de validação
        if (nome == null || nome.isEmpty()) {
            throw new IllegalArgumentException("Nome inválido");
        }
        
        // Acesso direto ao banco
        Connection conn = DriverManager.getConnection(
            "jdbc:mysql://localhost:3306/db", "root", "password"
        );
        PreparedStatement stmt = conn.prepareStatement(
            "INSERT INTO usuarios (nome, email) VALUES (?, ?)"
        );
        stmt.setString(1, nome);
        stmt.setString(2, email);
        stmt.executeUpdate();
        conn.close();
        
        // Envio de email
        Properties props = new Properties();
        props.put("mail.smtp.host", "smtp.gmail.com");
        Session session = Session.getInstance(props);
        MimeMessage message = new MimeMessage(session);
        message.setSubject("Bem-vindo!");
        Transport.send(message);
        
        // Log
        FileWriter fw = new FileWriter("log.txt", true);
        fw.write("Usuário " + nome + " criado\n");
        fw.close();
    }
}

// Para testar: precisa de banco real, servidor SMTP, sistema de arquivos

// ✅ Fácil de testar (segue SRP)
public interface Validador {
    boolean validar(Usuario usuario);
}

public interface UsuarioRepository {
    void salvar(Usuario usuario);
}

public interface Notificador {
    void enviarBoasVindas(Usuario usuario);
}

public interface Logger {
    void info(String mensagem);
}

public class ServicoUsuario {
    private final Validador validador;
    private final UsuarioRepository repository;
    private final Notificador notificador;
    private final Logger logger;
    
    public ServicoUsuario(
            Validador validador,
            UsuarioRepository repository,
            Notificador notificador,
            Logger logger) {
        this.validador = validador;
        this.repository = repository;
        this.notificador = notificador;
        this.logger = logger;
    }
    
    public void criarUsuario(String nome, String email) {
        Usuario usuario = new Usuario(nome, email);
        
        if (!validador.validar(usuario)) {
            throw new IllegalArgumentException("Usuário inválido");
        }
        
        repository.salvar(usuario);
        notificador.enviarBoasVindas(usuario);
        logger.info("Usuário " + nome + " criado");
    }
}

// TESTE UNITÁRIO
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;
import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

public class ServicoUsuarioTest {
    
    @Test
    public void deveLancarExcecao_QuandoNomeInvalido() {
        // Arrange
        Validador validadorMock = mock(Validador.class);
        when(validadorMock.validar(any(Usuario.class))).thenReturn(false);
        
        ServicoUsuario servico = new ServicoUsuario(
            validadorMock,
            mock(UsuarioRepository.class),
            mock(Notificador.class),
            mock(Logger.class)
        );
        
        // Act & Assert
        assertThrows(IllegalArgumentException.class, () -> {
            servico.criarUsuario("", "teste@email.com");
        });
    }
    
    @Test
    public void deveSalvarUsuario_QuandoDadosValidos() {
        // Arrange
        Validador validadorMock = mock(Validador.class);
        when(validadorMock.validar(any(Usuario.class))).thenReturn(true);
        
        UsuarioRepository repositoryMock = mock(UsuarioRepository.class);
        Notificador notificadorMock = mock(Notificador.class);
        Logger loggerMock = mock(Logger.class);
        
        ServicoUsuario servico = new ServicoUsuario(
            validadorMock,
            repositoryMock,
            notificadorMock,
            loggerMock
        );
        
        // Act
        servico.criarUsuario("João Silva", "joao@email.com");
        
        // Assert
        verify(repositoryMock, times(1)).salvar(any(Usuario.class));
        verify(notificadorMock, times(1)).enviarBoasVindas(any(Usuario.class));
        verify(loggerMock, times(1)).info(contains("João Silva"));
    }
    
    @Test
    public void naoDeveEnviarEmail_QuandoValidacaoFalhar() {
        // Arrange
        Validador validadorMock = mock(Validador.class);
        when(validadorMock.validar(any(Usuario.class))).thenReturn(false);
        
        Notificador notificadorMock = mock(Notificador.class);
        
        ServicoUsuario servico = new ServicoUsuario(
            validadorMock,
            mock(UsuarioRepository.class),
            notificadorMock,
            mock(Logger.class)
        );
        
        // Act
        try {
            servico.criarUsuario("", "teste@email.com");
        } catch (IllegalArgumentException e) {
            // Esperado
        }
        
        // Assert
        verify(notificadorMock, never()).enviarBoasVindas(any(Usuario.class));
    }
}
```

**Benefícios dos Testes com SRP:**
- ✅ Testes executam em **milissegundos** (sem I/O real)
- ✅ Cada componente testado **isoladamente**
- ✅ Mocks simples e diretos
- ✅ Testes **determinísticos** (sem dependências externas)
- ✅ Cobertura de código **acima de 90%**

### 8.3 Escalabilidade

#### Cenário: Sistema crescendo de 10 para 100 funcionalidades

**Sem SRP (God Object):**
```
Classe Principal:
├─ 5.000 linhas de código
├─ 150 métodos
├─ 30 desenvolvedores editando simultaneamente
├─ Conflitos de merge: 15 por semana
├─ Build time: 15 minutos
└─ Impossível paralelizar desenvolvimento
```

**Com SRP (Classes Coesas):**
```
50 Classes Especializadas:
├─ Média de 100 linhas por classe
├─ 3 métodos por classe
├─ 1-2 desenvolvedores por classe
├─ Conflitos de merge: 1 por semana
├─ Build time: 3 minutos
└─ Desenvolvimento totalmente paralelo
```