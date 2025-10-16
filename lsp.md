# Princípio da Substituição de Liskov (LSP)
## Liskov Substitution Principle - Guia Completo

![SOLID Principles](https://img.shields.io/badge/SOLID-LSP-blue)
![Clean Code](https://img.shields.io/badge/Clean%20Code-Best%20Practices-green)

---

## 📋 Sumário

1. [Definição Abrangente do LSP](#1-definição-abrangente-do-lsp)
2. [Diretrizes de Implementação](#2-diretrizes-de-implementação)
3. [Exemplos Práticos e Use Cases](#3-exemplos-práticos-e-use-cases)
4. [Caso Real: Refactoring de Hierarquia de Formas Geométricas](#4-caso-real-refactoring-de-hierarquia-de-formas-geométricas)
5. [Abstrações Conceituais](#5-abstrações-conceituais)
6. [LSP e os Princípios SOLID](#6-lsp-e-os-princípios-solid)
7. [Armadilhas Comuns](#7-armadilhas-comuns)
8. [Impacto na Qualidade do Software](#8-impacto-na-qualidade-do-software)

---

## 1. Definição Abrangente do LSP

### 1.1 O Conceito Fundamental

> **"Objetos de uma superclasse devem ser substituíveis por objetos de suas subclasses sem quebrar a aplicação."** - Barbara Liskov

O Princípio da Substituição de Liskov estabelece que se uma classe S é um subtipo de T, então objetos do tipo T podem ser **substituídos por objetos do tipo S** sem alterar as propriedades desejáveis do programa (correção, execução de tarefas, etc.).

### 1.2 Entendendo "Substituibilidade"

**Substituibilidade** significa que:
- Um subtipo deve honrar o **contrato comportamental** estabelecido pelo tipo base
- Clientes que usam a classe base não devem **saber** que estão usando um subtipo
- O comportamento do programa deve permanecer **correto e previsível** após a substituição

**Exemplo conceitual:**

```
❌ Viola LSP:
   class Retangulo { setLargura(), setAltura() }
   class Quadrado extends Retangulo { /* sobrescreve para manter largura = altura */ }
   // Um Quadrado NÃO pode substituir um Retangulo sem quebrar expectativas
   
✅ Segue LSP:
   interface Forma { calcularArea() }
   class Retangulo implements Forma { }
   class Quadrado implements Forma { }
   // Ambos são substituíveis onde Forma é esperado
```

### 1.3 Por Que o LSP é Importante?

| Problema sem LSP | Benefício com LSP |
|------------------|-------------------|
| Necessidade de verificar tipos em runtime (instanceof) | Polimorfismo puro e confiável |
| Comportamentos inesperados ao usar subclasses | Comportamento previsível e consistente |
| Testes quebram ao introduzir novas subclasses | Testes robustos independentes de implementação |
| Acoplamento com implementações específicas | Desacoplamento através de abstrações |
| Código defensivo cheio de validações | Código limpo e confiante |

### 1.4 A Definição Formal de Barbara Liskov

Na definição original (1987), Liskov e Wing estabeleceram que um tipo S é subtipo de T se:

1. **Pré-condições não podem ser fortalecidas** no subtipo
2. **Pós-condições não podem ser enfraquecidas** no subtipo
3. **Invariantes** da superclasse devem ser preservadas no subtipo
4. **A regra do histórico** (propriedades sobre objetos mutáveis)

Traduzindo para a prática:

```
Pré-condição: O que deve ser verdade ANTES de executar um método
Pós-condição: O que deve ser verdade DEPOIS de executar um método
Invariante: O que deve ser SEMPRE verdade sobre o objeto
```

**Exemplo:**

```java
// Classe base estabelece contrato
class ContaBancaria {
    protected double saldo;
    
    // Pré-condição: valor > 0
    // Pós-condição: saldo aumenta em 'valor'
    public void depositar(double valor) {
        if (valor <= 0) {
            throw new IllegalArgumentException("Valor deve ser positivo");
        }
        saldo += valor;
    }
}

// ❌ VIOLA LSP: Fortalece pré-condição
class ContaPremium extends ContaBancaria {
    @Override
    public void depositar(double valor) {
        // Pré-condição mais forte: valor > 100 (era > 0)
        if (valor <= 100) {
            throw new IllegalArgumentException("Depósito mínimo: R$ 100");
        }
        saldo += valor;
    }
}

// ✅ SEGUE LSP: Mantém ou enfraquece pré-condição
class ContaComBonus extends ContaBancaria {
    @Override
    public void depositar(double valor) {
        // Mesma pré-condição (valor > 0)
        if (valor <= 0) {
            throw new IllegalArgumentException("Valor deve ser positivo");
        }
        // Pós-condição mais forte: adiciona bônus (mais do que prometido)
        saldo += valor + (valor * 0.01); // 1% de bônus
    }
}
```

---

## 2. Diretrizes de Implementação

### 2.1 Identificando Violações do LSP

#### Sinais de Alerta (Code Smells)

1. **Verificações de tipo com instanceof**: `if (obj instanceof ClasseEspecifica)`
2. **Métodos vazios ou com exceções em subclasses**: `throw new UnsupportedOperationException()`
3. **Sobrescrita que altera semântica**: método faz algo diferente do esperado
4. **Necessidade de conhecer a subclasse**: código cliente precisa saber qual implementação está usando
5. **Condições especiais para subclasses**: `if (this.getClass() == Subclasse.class)`

#### Técnica: Teste de Substituibilidade

Pergunte: **"Se eu substituir a classe base pela subclasse, o código cliente continua funcionando corretamente?"**

```
Cenário: Sistema de gestão de veículos

Teste de substituição:

void calcularImposto(Veiculo veiculo) {
    double valor = veiculo.calcularValor();
    return valor * 0.05;
}

❌ Viola LSP:
   class Bicicleta extends Veiculo {
       // Bicicleta não tem motor, mas é forçada a implementar métodos de motor
       double calcularConsumo() { throw new UnsupportedOperationException(); }
   }
   // Cliente quebra ao receber Bicicleta

✅ Segue LSP:
   interface Veiculo { double calcularValor(); }
   class VeiculoMotorizado implements Veiculo, Motorizado { }
   class Bicicleta implements Veiculo { }
   // Qualquer Veiculo funciona em calcularImposto()
```

### 2.2 Regras para Preservar Substituibilidade

#### 2.2.1 Regra das Pré-condições

**Pré-condições não podem ser fortalecidas nas subclasses.**

```java
// Base
class Autenticador {
    // Pré-condição: senha não null
    public boolean autenticar(String usuario, String senha) {
        if (senha == null) throw new IllegalArgumentException();
        return validarCredenciais(usuario, senha);
    }
}

// ❌ VIOLA: Pré-condição mais forte
class AutenticadorSeguro extends Autenticador {
    @Override
    public boolean autenticar(String usuario, String senha) {
        // Exige senha com no mínimo 8 caracteres (mais restritivo!)
        if (senha == null || senha.length() < 8) {
            throw new IllegalArgumentException();
        }
        return validarCredenciais(usuario, senha);
    }
}

// ✅ CORRETO: Mesma ou mais fraca pré-condição
class AutenticadorTolerante extends Autenticador {
    @Override
    public boolean autenticar(String usuario, String senha) {
        // Aceita senha null (converte para string vazia)
        if (senha == null) senha = "";
        return validarCredenciais(usuario, senha);
    }
}
```

#### 2.2.2 Regra das Pós-condições

**Pós-condições não podem ser enfraquecidas nas subclasses.**

```java
// Base
class RepositorioProduto {
    // Pós-condição: retorna produto ou lança exceção
    public Produto buscar(Long id) {
        Produto produto = database.buscar(id);
        if (produto == null) {
            throw new ProdutoNaoEncontradoException(id);
        }
        return produto;
    }
}

// ❌ VIOLA: Pós-condição mais fraca
class RepositorioProdutoCache extends RepositorioProduto {
    @Override
    public Produto buscar(Long id) {
        Produto produto = cache.buscar(id);
        // Retorna null em vez de lançar exceção (mais fraco!)
        return produto; // pode ser null
    }
}

// ✅ CORRETO: Mesma ou mais forte pós-condição
class RepositorioProdutoOtimizado extends RepositorioProduto {
    @Override
    public Produto buscar(Long id) {
        Produto produto = buscarComCache(id);
        if (produto == null) {
            throw new ProdutoNaoEncontradoException(id);
        }
        // Pós-condição adicional: produto está em cache
        cache.salvar(id, produto);
        return produto;
    }
}
```

#### 2.2.3 Regra dos Invariantes

**Invariantes da classe base devem ser mantidos nas subclasses.**

```java
// Base estabelece invariante: saldo >= 0
class ContaBancaria {
    protected double saldo;
    
    // Invariante: saldo nunca pode ser negativo
    public void sacar(double valor) {
        if (saldo - valor < 0) {
            throw new SaldoInsuficienteException();
        }
        saldo -= valor;
    }
    
    protected void setSaldo(double saldo) {
        if (saldo < 0) {
            throw new IllegalArgumentException("Saldo não pode ser negativo");
        }
        this.saldo = saldo;
    }
}

// ❌ VIOLA: Quebra invariante
class ContaEspecial extends ContaBancaria {
    @Override
    public void sacar(double valor) {
        // Permite saldo negativo (quebra invariante!)
        saldo -= valor;
    }
}

// ✅ CORRETO: Mantém invariante com comportamento diferente
class ContaComLimite extends ContaBancaria {
    private double limite = 1000;
    
    @Override
    public void sacar(double valor) {
        // Mantém invariante: saldo efetivo >= 0
        if (saldo + limite - valor < 0) {
            throw new SaldoInsuficienteException();
        }
        saldo -= valor;
        // saldo pode ser negativo, mas não excede o limite
    }
    
    public double getSaldoEfetivo() {
        return saldo; // pode ser negativo, mas cliente sabe disso
    }
}
```

### 2.3 Design by Contract

O LSP está intimamente ligado ao conceito de **Design by Contract** (DbC) de Bertrand Meyer:

```
Contrato = Pré-condições + Pós-condições + Invariantes

Regra de Ouro do LSP:
- Cliente obedece pré-condições → Classe garante pós-condições
- Subclasse deve honrar o mesmo contrato (ou mais generoso)
```

**Exemplo completo:**

```java
/**
 * Contrato da interface ProcessadorPagamento:
 * 
 * Pré-condições:
 * - valor > 0
 * - dadosPagamento não null
 * 
 * Pós-condições:
 * - Se sucesso: retorna ResultadoPagamento com transacaoId
 * - Se falha: lança PagamentoException com motivo claro
 * - Nunca retorna null
 * 
 * Invariantes:
 * - Estado interno consistente antes e depois da operação
 */
interface ProcessadorPagamento {
    ResultadoPagamento processar(double valor, DadosPagamento dados) 
        throws PagamentoException;
}

// ✅ Implementação que respeita o contrato
class ProcessadorCartao implements ProcessadorPagamento {
    @Override
    public ResultadoPagamento processar(double valor, DadosPagamento dados) 
        throws PagamentoException {
        
        // Valida pré-condições (pode ser mais tolerante, nunca mais restritivo)
        if (valor <= 0) {
            throw new IllegalArgumentException("Valor deve ser positivo");
        }
        if (dados == null) {
            throw new IllegalArgumentException("Dados não podem ser null");
        }
        
        try {
            // Processa pagamento
            String transacaoId = gateway.processar(valor, dados.getCartao());
            
            // Garante pós-condição: nunca retorna null
            return new ResultadoPagamento(true, transacaoId);
            
        } catch (GatewayException e) {
            // Garante pós-condição: lança exceção em caso de falha
            throw new PagamentoException("Falha no gateway: " + e.getMessage(), e);
        }
    }
}
```

---

## 3. Exemplos Práticos e Use Cases

### 3.1 O Clássico Problema Retângulo-Quadrado

Este é o exemplo mais famoso de violação do LSP.

#### ❌ Violando o LSP

```java
public class Retangulo {
    protected int largura;
    protected int altura;
    
    public void setLargura(int largura) {
        this.largura = largura;
    }
    
    public void setAltura(int altura) {
        this.altura = altura;
    }
    
    public int getArea() {
        return largura * altura;
    }
    
    public int getLargura() {
        return largura;
    }
    
    public int getAltura() {
        return altura;
    }
}

public class Quadrado extends Retangulo {
    @Override
    public void setLargura(int largura) {
        // PROBLEMA: Altera ambos para manter largura = altura
        this.largura = largura;
        this.altura = largura;
    }
    
    @Override
    public void setAltura(int altura) {
        // PROBLEMA: Altera ambos para manter largura = altura
        this.largura = altura;
        this.altura = altura;
    }
}

// Código cliente que QUEBRA com Quadrado
public class TesteGeometria {
    public static void testarRetangulo(Retangulo r) {
        r.setLargura(5);
        r.setAltura(4);
        
        // Expectativa: área = 20
        assert r.getArea() == 20 : "Área deveria ser 20";
        // ❌ FALHA com Quadrado: área = 16 (4x4)
        
        assert r.getLargura() == 5 : "Largura deveria ser 5";
        // ❌ FALHA com Quadrado: largura = 4
    }
    
    public static void main(String[] args) {
        Retangulo retangulo = new Retangulo();
        testarRetangulo(retangulo); // ✅ PASSA
        
        Retangulo quadrado = new Quadrado();
        testarRetangulo(quadrado); // ❌ QUEBRA!
    }
}
```

**Análise do Problema:**

1. **Violação do contrato**: `Retangulo` estabelece que `setLargura()` muda apenas a largura
2. **Quadrado quebra a expectativa**: ao mudar largura, também muda altura
3. **Clientes surpreendidos**: código que funciona com `Retangulo` falha com `Quadrado`

#### ✅ Seguindo o LSP

**Solução 1: Composição em vez de Herança**

```java
// Interface comum
public interface Forma {
    int calcularArea();
    int calcularPerimetro();
}

// Retângulo com comportamento independente
public class Retangulo implements Forma {
    private int largura;
    private int altura;
    
    public Retangulo(int largura, int altura) {
        this.largura = largura;
        this.altura = altura;
    }
    
    public void setLargura(int largura) {
        this.largura = largura;
    }
    
    public void setAltura(int altura) {
        this.altura = altura;
    }
    
    @Override
    public int calcularArea() {
        return largura * altura;
    }
    
    @Override
    public int calcularPerimetro() {
        return 2 * (largura + altura);
    }
    
    public int getLargura() { return largura; }
    public int getAltura() { return altura; }
}

// Quadrado com comportamento próprio
public class Quadrado implements Forma {
    private int lado;
    
    public Quadrado(int lado) {
        this.lado = lado;
    }
    
    public void setLado(int lado) {
        this.lado = lado;
    }
    
    @Override
    public int calcularArea() {
        return lado * lado;
    }
    
    @Override
    public int calcularPerimetro() {
        return 4 * lado;
    }
    
    public int getLado() { return lado; }
}

// Código cliente funciona com ambos
public class CalculadoraGeometrica {
    public static int calcularAreaTotal(List<Forma> formas) {
        return formas.stream()
            .mapToInt(Forma::calcularArea)
            .sum();
    }
    
    public static void main(String[] args) {
        List<Forma> formas = Arrays.asList(
            new Retangulo(5, 4),
            new Quadrado(3),
            new Retangulo(10, 2)
        );
        
        int areaTotal = calcularAreaTotal(formas);
        System.out.println("Área total: " + areaTotal); // 20 + 9 + 20 = 49
    }
}
```

**Solução 2: Imutabilidade**

```java
// Formas imutáveis eliminam o problema de mutação inconsistente
public abstract class FormaImutavel {
    public abstract int getArea();
    public abstract int getPerimetro();
}

public class RetanguloImutavel extends FormaImutavel {
    private final int largura;
    private final int altura;
    
    public RetanguloImutavel(int largura, int altura) {
        this.largura = largura;
        this.altura = altura;
    }
    
    // Retorna NOVO objeto em vez de modificar
    public RetanguloImutavel comLargura(int novaLargura) {
        return new RetanguloImutavel(novaLargura, this.altura);
    }
    
    public RetanguloImutavel comAltura(int novaAltura) {
        return new RetanguloImutavel(this.largura, novaAltura);
    }
    
    @Override
    public int getArea() {
        return largura * altura;
    }
    
    @Override
    public int getPerimetro() {
        return 2 * (largura + altura);
    }
}

public class QuadradoImutavel extends FormaImutavel {
    private final int lado;
    
    public QuadradoImutavel(int lado) {
        this.lado = lado;
    }
    
    public QuadradoImutavel comLado(int novoLado) {
        return new QuadradoImutavel(novoLado);
    }
    
    @Override
    public int getArea() {
        return lado * lado;
    }
    
    @Override
    public int getPerimetro() {
        return 4 * lado;
    }
}
```

### 3.2 Sistema de Aves: Quando Herança Falha

#### ❌ Violando o LSP

```java
public class Ave {
    protected String nome;
    protected double peso;
    
    public void comer() {
        System.out.println(nome + " está comendo");
    }
    
    public void voar() {
        System.out.println(nome + " está voando");
    }
    
    public void fazerSom() {
        System.out.println(nome + " está fazendo som");
    }
}

public class Pardal extends Ave {
    public Pardal() {
        this.nome = "Pardal";
    }
    
    // Tudo funciona normalmente
}

public class Pinguim extends Ave {
    public Pinguim() {
        this.nome = "Pinguim";
    }
    
    @Override
    public void voar() {
        // ❌ PROBLEMA: Pinguim não voa!
        throw new UnsupportedOperationException("Pinguins não voam!");
    }
}

public class Avestruz extends Ave {
    public Avestruz() {
        this.nome = "Avestruz";
    }
    
    @Override
    public void voar() {
        // ❌ PROBLEMA: Avestruz não voa!
        throw new UnsupportedOperationException("Avestruzes não voam!");
    }
}

// Código cliente QUEBRA
public class SimuladorVoo {
    public static void simularVoo(List<Ave> aves) {
        for (Ave ave : aves) {
            try {
                ave.voar(); // ❌ Lança exceção para Pinguim e Avestruz!
            } catch (UnsupportedOperationException e) {
                System.err.println("Erro: " + e.getMessage());
            }
        }
    }
    
    public static void main(String[] args) {
        List<Ave> aves = Arrays.asList(
            new Pardal(),
            new Pinguim(),  // ❌ Quebra a substituibilidade
            new Avestruz()  // ❌ Quebra a substituibilidade
        );
        
        simularVoo(aves);
    }
}
```

#### ✅ Seguindo o LSP

```java
// Interface base comum
public interface Ave {
    void comer();
    void fazerSom();
    String getNome();
}

// Interface para capacidade específica
public interface AveVoadora extends Ave {
    void voar();
    double getAltitudeMaxima();
}

// Interface para capacidade de natação
public interface AveNadadora extends Ave {
    void nadar();
    double getVelocidadeNatacao();
}

// Implementações específicas
public class Pardal implements AveVoadora {
    private String nome = "Pardal";
    
    @Override
    public void comer() {
        System.out.println(nome + " está comendo sementes");
    }
    
    @Override
    public void fazerSom() {
        System.out.println(nome + " está cantando");
    }
    
    @Override
    public void voar() {
        System.out.println(nome + " está voando graciosamente");
    }
    
    @Override
    public double getAltitudeMaxima() {
        return 100.0; // metros
    }
    
    @Override
    public String getNome() {
        return nome;
    }
}

public class Pinguim implements AveNadadora {
    private String nome = "Pinguim";
    
    @Override
    public void comer() {
        System.out.println(nome + " está comendo peixes");
    }
    
    @Override
    public void fazerSom() {
        System.out.println(nome + " está grasnando");
    }
    
    @Override
    public void nadar() {
        System.out.println(nome + " está nadando rapidamente");
    }
    
    @Override
    public double getVelocidadeNatacao() {
        return 36.0; // km/h
    }
    
    @Override
    public String getNome() {
        return nome;
    }
}

public class Pato implements AveVoadora, AveNadadora {
    private String nome = "Pato";
    
    @Override
    public void comer() {
        System.out.println(nome + " está comendo");
    }
    
    @Override
    public void fazerSom() {
        System.out.println(nome + " está grasnando");
    }
    
    @Override
    public void voar() {
        System.out.println(nome + " está voando");
    }
    
    @Override
    public double getAltitudeMaxima() {
        return 500.0;
    }
    
    @Override
    public void nadar() {
        System.out.println(nome + " está nadando");
    }
    
    @Override
    public double getVelocidadeNatacao() {
        return 5.0;
    }
    
    @Override
    public String getNome() {
        return nome;
    }
}

// Código cliente SEGURO e CORRETO
public class SimuladorAves {
    public static void alimentarAves(List<Ave> aves) {
        for (Ave ave : aves) {
            ave.comer(); // ✅ Funciona com TODAS as aves
        }
    }
    
    public static void simularVoo(List<AveVoadora> avesVoadoras) {
        for (AveVoadora ave : avesVoadoras) {
            ave.voar(); // ✅ Só recebe aves que realmente voam
        }
    }
    
    public static void simularNatacao(List<AveNadadora> avesNadadoras) {
        for (AveNadadora ave : avesNadadoras) {
            ave.nadar(); // ✅ Só recebe aves que realmente nadam
        }
    }
    
    public static void main(String[] args) {
        // Todas as aves podem comer
        List<Ave> todasAves = Arrays.asList(
            new Pardal(),
            new Pinguim(),
            new Pato()
        );
        alimentarAves(todasAves); // ✅ Funciona perfeitamente
        
        // Apenas aves voadoras
        List<AveVoadora> avesVoadoras = Arrays.asList(
            new Pardal(),
            new Pato()
            // Pinguim não está aqui - sistema de tipos previne erro!
        );
        simularVoo(avesVoadoras); // ✅ Seguro
        
        // Apenas aves nadadoras
        List<AveNadadora> avesNadadoras = Arrays.asList(
            new Pinguim(),
            new Pato()
        );
        simularNatacao(avesNadadoras); // ✅ Seguro
    }
}
```

**Benefícios desta abordagem:**

✅ **Tipo-seguro**: Compilador previne erros  
✅ **Substituibilidade garantida**: Cada interface tem um contrato claro  
✅ **Flexibilidade**: Aves podem ter múltiplas capacidades (Pato)  
✅ **Princípio ISP**: Interfaces segregadas e focadas  
✅ **Sem exceções em runtime**: Impossível chamar voar() em Pinguim

---

## 4. Caso Real: Refactoring de Hierarquia de Formas Geométricas

### 4.1 O Problema Original

Uma empresa de software CAD tinha uma hierarquia complexa de formas geométricas que violava constantemente o LSP, causando bugs em produção.

#### ❌ Código Original (Problemático)

```java
public abstract class Forma {
    protected Point posicao;
    protected Color cor;
    
    public abstract double calcularArea();
    public abstract double calcularPerimetro();
    public abstract void desenhar(Graphics g);
    public abstract void redimensionar(double fator);
    public abstract void rotacionar(double graus);
    
    // Métodos específicos que nem todas as formas suportam
    public void setRaio(double raio) {
        throw new UnsupportedOperationException("Forma não possui raio");
    }
    
    public void setLargura(double largura) {
        throw new UnsupportedOperationException("Forma não possui largura");
    }
    
    public void setAltura(double altura) {
        throw new UnsupportedOperationException("Forma não possui altura");
    }
}

public class Circulo extends Forma {
    private double raio;
    
    @Override
    public void setRaio(double raio) {
        this.raio = raio;
    }
    
    @Override
    public void setLargura(double largura) {
        // ❌ PROBLEMA: O que fazer aqui?
        this.raio = largura / 2;
    }
    
    @Override
    public void redimensionar(double fator) {
        this.raio *= fator;
    }
    
    @Override
    public void rotacionar(double graus) {
        // ❌ PROBLEMA: Círculo não muda ao rotacionar!
        // Não faz nada, mas cliente espera mudança
    }
    
    @Override
    public double calcularArea() {
        return Math.PI * raio * raio;
    }
    
    @Override
    public double calcularPerimetro() {
        return 2 * Math.PI * raio;
    }
    
    @Override
    public void desenhar(Graphics g) {
        g.drawOval((int)posicao.x, (int)posicao.y, (int)(raio*2), (int)(raio*2));
    }
}

public class Linha extends Forma {
    private Point pontoFinal;
    
    @Override
    public double calcularArea() {
        // ❌ PROBLEMA: Linha não tem área!
        return 0; // Valor sem sentido
    }
    
    @Override
    public double calcularPerimetro() {
        // ❌ PROBLEMA: Linha não tem perímetro!
        return calcularComprimento();
    }
    
    @Override
    public void redimensionar(double fator) {
        // ❌ PROBLEMA: O que significa "redimensionar" uma linha?
        double comprimento = calcularComprimento();
        double novoComprimento = comprimento * fator;
        // Altera ponto final mantendo direção...
    }
    
    private double calcularComprimento() {
        double dx = pontoFinal.x - posicao.x;
        double dy = pontoFinal.y - posicao.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
    
    @Override
    public void desenhar(Graphics g) {
        g.drawLine((int)posicao.x, (int)posicao.y, 
                   (int)pontoFinal.x, (int)pontoFinal.y);
    }
}

// Código cliente CHEIO de verificações
public class EditorCAD {
    private List<Forma> formas = new ArrayList<>();
    
    public void aplicarTransformacao(Forma forma, String tipo, double valor) {
        // ❌ Código defensivo por causa de LSP violado
        if (tipo.equals("raio")) {
            if (forma instanceof Circulo || forma instanceof Esfera) {
                forma.setRaio(valor);
            } else {
                System.err.println("Forma não suporta raio");
            }
        } else if (tipo.equals("largura")) {
            if (forma instanceof Retangulo || forma instanceof Cubo) {
                forma.setLargura(valor);
            } else if (forma instanceof Circulo) {
                // Tratamento especial para círculo
                forma.setRaio(valor / 2);
            } else {
                System.err.println("Forma não suporta largura");
            }
        }
        // ... mais casos especiais
    }
    
    public double calcularAreaTotal() {
        double total = 0;
        for (Forma forma : formas) {
            if (!(forma instanceof Linha)) { // ❌ Verificação de tipo
                total += forma.calcularArea();
            }
        }
        return total;
    }
    
    public void rotacionarTodas(double graus) {
        for (Forma forma : formas) {
            if (!(forma instanceof Circulo) && !(forma instanceof Esfera)) {
                // ❌ Só rotaciona se não for circular
                forma.rotacionar(graus);
            }
        }
    }
}
```

**Problemas identificados:**

| Problema | Impacto | Frequência |
|----------|---------|------------|
| UnsupportedOperationException em runtime | Crashes em produção | 12 bugs/mês |
| Métodos sem sentido (área de linha) | Resultados incorretos | 8 bugs/mês |
| Verificações instanceof por todo código | Código frágil e acoplado | 40+ ocorrências |
| Comportamentos inconsistentes | Usuários confusos | 15 tickets/mês |
| Impossível adicionar novas formas | Desenvolvimento bloqueado | 3 semanas/feature |

### 4.2 Refactoring: Aplicando LSP

#### Passo 1: Segregar Interfaces (ISP + LSP)

```java
// Interface base: o que TODAS as formas têm
public interface ElementoGrafico {
    void desenhar(Graphics g);
    void mover(Point novaPosicao);
    Point getPosicao();
    Color getCor();
    void setCor(Color cor);
}

// Capacidade: Formas que têm área
public interface FormaComArea extends ElementoGrafico {
    double calcularArea();
}

// Capacidade: Formas que têm perímetro
public interface FormaComPerimetro extends ElementoGrafico {
    double calcularPerimetro();
}

// Capacidade: Formas que podem ser redimensionadas
public interface Redimensionavel {
    void redimensionar(double fator);
    Dimensoes getDimensoes();
}

// Capacidade: Formas que podem rotacionar
public interface Rotacionavel {
    void rotacionar(double graus);
    double getAngulo();
}

// Classes auxiliares
public class Point {
    public final double x;
    public final double y;
    
    public Point(double x, double y) {
        this.x = x;
        this.y = y;
    }
}

public class Dimensoes {
    public final double largura;
    public final double altura;
    
    public Dimensoes(double largura, double altura) {
        this.largura = largura;
        this.altura = altura;
    }
}
```

#### Passo 2: Implementações Consistentes

```java
// Círculo: tem área, perímetro, redimensionável, mas NÃO rotacionável
public class Circulo implements FormaComArea, FormaComPerimetro, Redimensionavel {
    private Point posicao;
    private double raio;
    private Color cor;
    
    public Circulo(Point posicao, double raio, Color cor) {
        this.posicao = posicao;
        this.raio = raio;
        this.cor = cor;
    }
    
    @Override
    public double calcularArea() {
        return Math.PI * raio * raio;
    }
    
    @Override
    public double calcularPerimetro() {
        return 2 * Math.PI * raio;
    }
    
    @Override
    public void redimensionar(double fator) {
        if (fator <= 0) {
            throw new IllegalArgumentException("Fator deve ser positivo");
        }
        this.raio *= fator;
    }
    
    @Override
    public Dimensoes getDimensoes() {
        double diametro = raio * 2;
        return new Dimensoes(diametro, diametro);
    }
    
    @Override
    public void desenhar(Graphics g) {
        g.setColor(cor);
        g.drawOval((int)(posicao.x - raio), (int)(posicao.y - raio), 
                   (int)(raio * 2), (int)(raio * 2));
    }
    
    @Override
    public void mover(Point novaPosicao) {
        this.posicao = novaPosicao;
    }
    
    @Override
    public Point getPosicao() { return posicao; }
    
    @Override
    public Color getCor() { return cor; }
    
    @Override
    public void setCor(Color cor) { this.cor = cor; }
    
    // Método específico de círculo
    public double getRaio() { return raio; }
    public void setRaio(double raio) { this.raio = raio; }
}

// Retângulo: tem área, perímetro, redimensionável E rotacionável
public class Retangulo implements FormaComArea, FormaComPerimetro, 
                                   Redimensionavel, Rotacionavel {
    private Point posicao;
    private double largura;
    private double altura;
    private double angulo;
    private Color cor;
    
    public Retangulo(Point posicao, double largura, double altura, Color cor) {
        this.posicao = posicao;
        this.largura = largura;
        this.altura = altura;
        this.angulo = 0;
        this.cor = cor;
    }
    
    @Override
    public double calcularArea() {
        return largura * altura;
    }
    
    @Override
    public double calcularPerimetro() {
        return 2 * (largura + altura);
    }
    
    @Override
    public void redimensionar(double fator) {
        if (fator <= 0) {
            throw new IllegalArgumentException("Fator deve ser positivo");
        }
        this.largura *= fator;
        this.altura *= fator;
    }
    
    @Override
    public void rotacionar(double graus) {
        this.angulo = (this.angulo + graus) % 360;
    }
    
    @Override
    public double getAngulo() {
        return angulo;
    }
    
    @Override
    public Dimensoes getDimensoes() {
        return new Dimensoes(largura, altura);
    }
    
    @Override
    public void desenhar(Graphics g) {
        Graphics2D g2d = (Graphics2D) g;
        AffineTransform old = g2d.getTransform();
        
        g2d.rotate(Math.toRadians(angulo), posicao.x, posicao.y);
        g2d.setColor(cor);
        g2d.drawRect((int)posicao.x, (int)posicao.y, (int)largura, (int)altura);
        
        g2d.setTransform(old);
    }
    
    @Override
    public void mover(Point novaPosicao) {
        this.posicao = novaPosicao;
    }
    
    @Override
    public Point getPosicao() { return posicao; }
    
    @Override
    public Color getCor() { return cor; }
    
    @Override
    public void setCor(Color cor) { this.cor = cor; }
}

// Linha: elemento gráfico com comprimento, redimensionável E rotacionável
public class Linha implements ElementoGrafico, Redimensionavel, Rotacionavel {
    private Point inicio;
    private Point fim;
    private double angulo;
    private Color cor;
    
    public Linha(Point inicio, Point fim, Color cor) {
        this.inicio = inicio;
        this.fim = fim;
        this.cor = cor;
        this.angulo = calcularAnguloInicial();
    }
    
    // Linha tem comprimento, não área ou perímetro
    public double calcularComprimento() {
        double dx = fim.x - inicio.x;
        double dy = fim.y - inicio.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
    
    @Override
    public void redimensionar(double fator) {
        if (fator <= 0) {
            throw new IllegalArgumentException("Fator deve ser positivo");
        }
        
        double comprimento = calcularComprimento();
        double novoComprimento = comprimento * fator;
        
        double dx = fim.x - inicio.x;
        double dy = fim.y - inicio.y;
        double fatorReal = novoComprimento / comprimento;
        
        this.fim = new Point(
            inicio.x + dx * fatorReal,
            inicio.y + dy * fatorReal
        );
    }
    
    @Override
    public void rotacionar(double graus) {
        this.angulo = (this.angulo + graus) % 360;
        
        double comprimento = calcularComprimento();
        double radianos = Math.toRadians(angulo);
        
        this.fim = new Point(
            inicio.x + comprimento * Math.cos(radianos),
            inicio.y + comprimento * Math.sin(radianos)
        );
    }
    
    @Override
    public double getAngulo() {
        return angulo;
    }
    
    @Override
    public Dimensoes getDimensoes() {
        double comprimento = calcularComprimento();
        return new Dimensoes(comprimento, 0);
    }
    
    @Override
    public void desenhar(Graphics g) {
        g.setColor(cor);
        g.drawLine((int)inicio.x, (int)inicio.y, (int)fim.x, (int)fim.y);
    }
    
    @Override
    public void mover(Point novaPosicao) {
        double dx = fim.x - inicio.x;
        double dy = fim.y - inicio.y;
        this.inicio = novaPosicao;
        this.fim = new Point(novaPosicao.x + dx, novaPosicao.y + dy);
    }
    
    @Override
    public Point getPosicao() { return inicio; }
    
    @Override
    public Color getCor() { return cor; }
    
    @Override
    public void setCor(Color cor) { this.cor = cor; }
    
    private double calcularAnguloInicial() {
        double dx = fim.x - inicio.x;
        double dy = fim.y - inicio.y;
        return Math.toDegrees(Math.atan2(dy, dx));
    }
}
```

#### Passo 3: Cliente Limpo e Tipo-Seguro

```java
public class EditorCAD {
    private List<ElementoGrafico> elementos = new ArrayList<>();
    
    public void adicionarElemento(ElementoGrafico elemento) {
        elementos.add(elemento);
    }
    
    // ✅ Método tipo-seguro: só aceita formas com área
    public double calcularAreaTotal(List<FormaComArea> formas) {
        return formas.stream()
            .mapToDouble(FormaComArea::calcularArea)
            .sum();
    }
    
    // ✅ Método tipo-seguro: só aceita formas rotacionáveis
    public void rotacionarElementos(List<Rotacionavel> rotacionaveis, double graus) {
        rotacionaveis.forEach(r -> r.rotacionar(graus));
    }
    
    // ✅ Método tipo-seguro: funciona com TODOS os elementos
    public void moverTodos(Point offset) {
        for (ElementoGrafico elemento : elementos) {
            Point posicaoAtual = elemento.getPosicao();
            elemento.mover(new Point(
                posicaoAtual.x + offset.x,
                posicaoAtual.y + offset.y
            ));
        }
    }
    
    // ✅ Método tipo-seguro: só aceita redimensionáveis
    public void ampliar(List<Redimensionavel> redimensionaveis, double fator) {
        redimensionaveis.forEach(r -> r.redimensionar(fator));
    }
    
    // ✅ Filtragem tipo-segura
    public List<FormaComArea> obterFormasComArea() {
        return elementos.stream()
            .filter(e -> e instanceof FormaComArea)
            .map(e -> (FormaComArea) e)
            .collect(Collectors.toList());
    }
    
    public List<Rotacionavel> obterRotacionaveis() {
        return elementos.stream()
            .filter(e -> e instanceof Rotacionavel)
            .map(e -> (Rotacionavel) e)
            .collect(Collectors.toList());
    }
    
    public void desenharTodos(Graphics g) {
        elementos.forEach(e -> e.desenhar(g));
    }
}

// Uso tipo-seguro
public class AplicacaoCAD {
    public static void main(String[] args) {
        EditorCAD editor = new EditorCAD();
        
        // Adicionar diferentes tipos de elementos
        Circulo circulo = new Circulo(new Point(100, 100), 50, Color.RED);
        Retangulo retangulo = new Retangulo(new Point(200, 100), 80, 60, Color.BLUE);
        Linha linha = new Linha(new Point(50, 50), new Point(150, 150), Color.BLACK);
        
        editor.adicionarElemento(circulo);
        editor.adicionarElemento(retangulo);
        editor.adicionarElemento(linha);
        
        // ✅ Operações tipo-seguras
        List<FormaComArea> formasComArea = editor.obterFormasComArea();
        double areaTotal = editor.calcularAreaTotal(formasComArea);
        System.out.println("Área total: " + areaTotal);
        
        // ✅ Rotacionar apenas os rotacionáveis (Retangulo e Linha)
        List<Rotacionavel> rotacionaveis = editor.obterRotacionaveis();
        editor.rotacionarElementos(rotacionaveis, 45);
        
        // ✅ Mover todos (todos implementam ElementoGrafico)
        editor.moverTodos(new Point(10, 10));
    }
}
```

### 4.3 Resultados do Refactoring

| Métrica | Antes | Depois | Melhoria |
|---------|-------|--------|----------|
| Exceções em runtime | 12/mês | 0/mês | **100% eliminadas** |
| Bugs de comportamento | 8/mês | 1/mês | **87% redução** |
| Uso de instanceof | 40+ | 0 (apenas em casting seguro) | **Eliminado** |
| Tempo para nova forma | 3 semanas | 2 dias | **91% mais rápido** |
| Linhas de código cliente | ~500 | ~200 | **60% redução** |
| Cobertura de testes | 55% | 94% | **71% aumento** |
| Satisfação do usuário | 6.2/10 | 9.1/10 | **47% melhoria** |

---

## 5. Abstrações Conceituais

### 5.1 LSP e Covariância/Contravariância

O LSP está relacionado aos conceitos de **covariância** e **contravariância** em tipos de retorno e parâmetros.

#### 5.1.1 Covariância em Tipos de Retorno

**Regra**: Subtipos podem retornar tipos **mais específicos** (subtipos).

```java
class Animal {
    public Animal reproduzir() {
        return new Animal();
    }
}

class Cachorro extends Animal {
    @Override
    public Cachorro reproduzir() { // ✅ Covariante: retorna tipo mais específico
        return new Cachorro();
    }
}

// Cliente feliz
Animal animal = new Cachorro();
Animal filhote = animal.reproduzir(); // ✅ Funciona
Cachorro filhoteCachorro = ((Cachorro) animal).reproduzir(); // ✅ Tipo correto
```

#### 5.1.2 Contravariância em Parâmetros

**Regra**: Subtipos devem aceitar tipos **mais genéricos** (supertipos) ou iguais.

```java
class ProcessadorAnimal {
    public void processar(Animal animal) {
        System.out.println("Processando animal: " + animal);
    }
}

class ProcessadorCachorro extends ProcessadorAnimal {
    @Override
    public void processar(Animal animal) { // ✅ Aceita Animal (mesmo tipo ou mais genérico)
        if (animal instanceof Cachorro) {
            System.out.println("Processamento especial para cachorro");
        } else {
            super.processar(animal);
        }
    }
    
    // ❌ VIOLA LSP se fosse assim:
    // public void processar(Cachorro cachorro) { 
    //     // Mais restritivo que o tipo base!
    // }
}
```

### 5.2 Relação com Design by Contract

| Elemento do Contrato | Regra LSP | Exemplo |
|---------------------|-----------|---------|
| **Pré-condições** | Não podem ser fortalecidas | Se base aceita valor > 0, subtipo não pode exigir > 100 |
| **Pós-condições** | Não podem ser enfraquecidas | Se base garante retorno não-null, subtipo também deve |
| **Invariantes** | Devem ser preservadas | Se base mantém saldo >= 0, subtipo também deve |
| **Exceções** | Só podem lançar as mesmas ou subtipos | Se base lança IOException, subtipo não pode lançar Exception |

```java
class ServicoBase {
    // Contrato: pré-condição (id != null), pós-condição (retorna ou lança exceção)
    public Usuario buscar(Long id) throws UsuarioNaoEncontradoException {
        if (id == null) {
            throw new IllegalArgumentException("ID não pode ser null");
        }
        Usuario usuario = database.buscar(id);
        if (usuario == null) {
            throw new UsuarioNaoEncontradoException(id);
        }
        return usuario;
    }
}

// ✅ CORRETO: Mantém contrato
class ServicoCache extends ServicoBase {
    @Override
    public Usuario buscar(Long id) throws UsuarioNaoEncontradoException {
        // Mesma pré-condição (ou mais fraca)
        if (id == null) {
            throw new IllegalArgumentException("ID não pode ser null");
        }
        
        // Busca no cache primeiro
        Usuario usuario = cache.buscar(id);
        if (usuario == null) {
            usuario = database.buscar(id);
            if (usuario != null) {
                cache.salvar(usuario);
            }
        }
        
        // Mesma pós-condição: retorna ou lança exceção
        if (usuario == null) {
            throw new UsuarioNaoEncontradoException(id);
        }
        return usuario;
    }
}

// ❌ VIOLA: Enfraquece pós-condição
class ServicoTolerante extends ServicoBase {
    @Override
    public Usuario buscar(Long id) {
        if (id == null) return null; // Retorna null em vez de lançar exceção!
        Usuario usuario = database.buscar(id);
        return usuario; // Pode retornar null, violando pós-condição!
    }
}
```

---

## 6. LSP e os Princípios SOLID

### 6.1 Relação com Outros Princípios

| Princípio | Relação com LSP | Exemplo |
|-----------|-----------------|---------|
| **S**RP | Classes coesas têm contratos claros, facilitando LSP | Uma classe `Calculadora` com responsabilidade única tem contrato previsível |
| **O**CP | LSP garante que extensões sejam verdadeiramente substituíveis | Se violar LSP, não é extensão real (não é aberta) |
| **I**SP | Interfaces pequenas são mais fáceis de implementar corretamente | Interface `Voador` é mais fácil de cumprir contrato que `Ave` |
| **D**IP | Abstrações dependem de contratos cumpridos (LSP) | Depender de `Repositorio` só funciona se implementações cumprem contrato |

### 6.2 LSP Como Guardião do Polimorfismo

**LSP é essencial para que polimorfismo funcione corretamente:**

```java
// Sem LSP: polimorfismo quebrado
List<Forma> formas = Arrays.asList(new Circulo(), new Linha());
for (Forma forma : formas) {
    forma.calcularArea(); // ❌ Linha lança exceção ou retorna valor sem sentido
}

// Com LSP: polimorfismo confiável
List<FormaComArea> formas = Arrays.asList(new Circulo(), new Retangulo());
for (FormaComArea forma : formas) {
    forma.calcularArea(); // ✅ Funciona corretamente para todas
}
```

---

## 7. Armadilhas Comuns

### 7.1 Herança "IS-A" vs "BEHAVES-LIKE-A"

**Problema**: Usar herança baseada em classificação conceitual em vez de comportamento.

```java
// ❌ "IS-A" conceitual, mas não comportamental
class Quadrado extends Retangulo {
    // Conceitualmente, quadrado É UM retângulo
    // Mas comportamentalmente, NÃO se comporta como retângulo
    // (setLargura afeta altura também)
}

// ✅ "BEHAVES-LIKE-A" comportamental
interface Forma {
    double calcularArea();
}

class Retangulo implements Forma { /* ... */ }
class Quadrado implements Forma { /* ... */ }
// Ambos SE COMPORTAM como Forma, mas não há herança entre eles
```

**Regra**: Use herança apenas quando houver **substituibilidade comportamental**, não apenas classificação conceitual.

### 7.2 Exceções que Violam o Contrato

**Problema**: Lançar exceções não declaradas ou mais genéricas.

```java
interface Repositorio<T> {
    T buscar(Long id) throws EntidadeNaoEncontradaException;
}

// ❌ VIOLA: Lança exceção mais genérica
class RepositorioImpl<T> implements Repositorio<T> {
    @Override
    public T buscar(Long id) throws Exception { // ❌ Exception > EntidadeNaoEncontradaException
        // ...
    }
}

// ❌ VIOLA: Lança exceção não declarada
class RepositorioImpl2<T> implements Repositorio<T> {
    @Override
    public T buscar(Long id) {
        if (id == null) {
            throw new NullPointerException(); // ❌ Não declarada no contrato
        }
        // ...
    }
}

// ✅ CORRETO: Mesma exceção ou subtipo
class RepositorioImpl3<T> implements Repositorio<T> {
    @Override
    public T buscar(Long id) throws EntidadeNaoEncontradaException {
        // Pode lançar subtipo de EntidadeNaoEncontradaException
        if (id == null) {
            throw new IllegalArgumentException("ID não pode ser null");
        }
        T entidade = database.buscar(id);
        if (entidade == null) {
            throw new EntidadeNaoEncontradaException(id);
        }
        return entidade;
    }
}
```

### 7.3 Métodos que Fazem "Nada"

**Problema**: Implementar métodos vazios ou que não fazem o esperado.

```java
interface Persistivel {
    void salvar();
    void deletar();
}

// ❌ VIOLA: Método vazio sem razão
class EntidadeReadOnly implements Persistivel {
    @Override
    public void salvar() {
        // Não faz nada - viola expectativa
    }
    
    @Override
    public void deletar() {
        // Não faz nada - viola expectativa
    }
}

// ✅ CORRETO: Segregar interfaces
interface Consultavel {
    Entidade buscar(Long id);
}

interface Persistivel extends Consultavel {
    void salvar();
    void deletar();
}

class EntidadeReadOnly implements Consultavel {
    @Override
    public Entidade buscar(Long id) {
        return database.buscar(id);
    }
    // Não precisa implementar salvar/deletar
}
```

### 7.4 Sobrescrita que Altera Semântica

**Problema**: Método sobrescrito faz algo completamente diferente.

```java
class Cache {
    protected Map<String, Object> dados = new HashMap<>();
    
    public void adicionar(String chave, Object valor) {
        dados.put(chave, valor);
    }
    
    public Object buscar(String chave) {
        return dados.get(chave);
    }
}

// ❌ VIOLA: Muda semântica completamente
class CacheLimitado extends Cache {
    private int limite = 100;
    
    @Override
    public void adicionar(String chave, Object valor) {
        if (dados.size() >= limite) {
            // ❌ Remove itens aleatoriamente (cliente não espera isso!)
            String chaveAleatoria = dados.keySet().iterator().next();
            dados.remove(chaveAleatoria);
        }
        dados.put(chave, valor);
    }
}

// ✅ CORRETO: Mantém semântica, adiciona comportamento
class CacheLRU extends Cache {
    private LinkedHashMap<String, Object> dadosLRU = 
        new LinkedHashMap<>(100, 0.75f, true) {
            protected boolean removeEldestEntry(Map.Entry eldest) {
                return size() > 100;
            }
        };
    
    @Override
    public void adicionar(String chave, Object valor) {
        // Mantém semântica: sempre adiciona
        // Mas usa estratégia LRU (cliente OK com isso)
        dadosLRU.put(chave, valor);
    }
    
    @Override
    public Object buscar(String chave) {
        return dadosLRU.get(chave);
    }
}
```

---

## 8. Impacto na Qualidade do Software

### 8.1 Métricas de Qualidade

| Métrica | Sem LSP | Com LSP | Impacto |
|---------|---------|---------|---------|
| **Confiabilidade** | Baixa (surpresas em runtime) | Alta (comportamento previsível) | ✅ **90% menos exceções** |
| **Manutenibilidade** | Difícil (código defensivo) | Fácil (confiança no polimorfismo) | ✅ **Código 50% menor** |
| **Testabilidade** | Complexa (casos especiais) | Simples (testes por contrato) | ✅ **70% menos testes** |
| **Reusabilidade** | Baixa (verificações de tipo) | Alta (substituibilidade) | ✅ **Componentes reutilizáveis** |
| **Extensibilidade** | Arriscada (quebra clientes) | Segura (contratos garantidos) | ✅ **Zero regressões** |

### 8.2 Impacto no Desenvolvimento

#### 8.2.1 Confiança no Código

```
Timeline: Adicionar nova subclasse

❌ Sem LSP:
Dia 1: Criar subclasse
Dia 2: Descobrir que quebra código cliente
Dia 3-4: Adicionar verificações de tipo em 15 lugares
Dia 5: Refazer testes
Dia 6: Testar edge cases
Dia 7: Bug em produção
Total: 7 dias + bug

✅ Com LSP:
Dia 1: Criar subclasse respeitando contrato
Dia 2: Testes passam automaticamente
Dia 3: Deploy seguro
Total: 3 dias, zero bugs
```

#### 8.2.2 Redução de Código Defensivo

**Sem LSP:**
```java
public void processar(Forma forma) {
    if (forma instanceof Circulo) {
        // Tratamento especial para círculo
    } else if (forma instanceof Linha) {
        // Tratamento especial para linha
    } else if (forma instanceof Retangulo) {
        // Tratamento especial para retângulo
    }
    // Código cresce com cada nova forma
}
```

**Com LSP:**
```java
public void processar(FormaComArea forma) {
    double area = forma.calcularArea(); // ✅ Funciona com qualquer FormaComArea
    // Código simples e estável
}
```

### 8.3 Benefícios de Longo Prazo

#### 8.3.1 Evolução Segura

Sistemas que seguem LSP podem **evoluir sem quebrar**:

```java
// Código cliente escrito em 2020
public double calcularTotal(List<Desconto> descontos, double valor) {
    return descontos.stream()
        .mapToDouble(d -> d.calcular(valor))
        .sum();
}

// Novo desconto adicionado em 2025
class DescontoCriptomoeda implements Desconto {
    public double calcular(double valor) {
        return valor * 0.05;
    }
}

// ✅ Código de 2020 funciona perfeitamente com desconto de 2025!
// Nenhuma modificação necessária
```

#### 8.3.2 Documentação Viva

Contratos bem definidos servem como **documentação executável**:

```java
/**
 * Contrato ProcessadorPagamento:
 * 
 * TODAS as implementações devem:
 * - Aceitar valor > 0
 * - Retornar ResultadoPagamento não-null
 * - Lançar PagamentoException em caso de falha
 * - Preservar idempotência (processar 2x = processar 1x)
 * 
 * Clientes podem confiar que QUALQUER ProcessadorPagamento
 * seguirá estas regras.
 */
interface ProcessadorPagamento {
    ResultadoPagamento processar(double valor, DadosPagamento dados)
        throws PagamentoException;
}
```

---

## Conclusão

O **Liskov Substitution Principle** é fundamental para criar **hierarquias de tipos confiáveis** e garantir que o **polimorfismo funcione corretamente**. Quando seguimos LSP:

✅ **Confiabilidade**: Substituições funcionam sem surpresas  
✅ **Previsibilidade**: Comportamento consistente em toda hierarquia  
✅ **Simplicidade**: Menos código defensivo, mais confiança  
✅ **Manutenibilidade**: Mudanças localizadas, sem efeitos colaterais  
✅ **Extensibilidade**: Novas subclasses funcionam automaticamente

### Checklist: Você está seguindo LSP?

- [ ] Subclasses podem substituir superclasses **sem quebrar o código cliente**?
- [ ] Seus métodos sobrescritos **mantêm as pré-condições** ou as enfraquecem?
- [ ] Seus métodos sobrescritos **mantêm as pós-condições** ou as fortalecem?
- [ ] **Invariantes** da classe base são preservados nas subclasses?
- [ ] Evita lançar **exceções não declaradas** no tipo base?
- [ ] Não usa **instanceof** para tratar subclasses de forma especial?
- [ ] Pode escrever **testes genéricos** que funcionam para todas as subclasses?

### Quando Aplicar LSP?

✅ **Design de hierarquias**: Sempre que usar herança ou interfaces  
✅ **APIs públicas**: Contratos devem ser respeitados por todas implementações  
✅ **Frameworks**: Pontos de extensão devem garantir substituibilidade  
✅ **Bibliotecas**: Usuários devem confiar em abstrações

### Sinais de Violação

❌ **Código cliente verifica tipos** com instanceof  
❌ **Métodos lançam UnsupportedOperationException**  
❌ **Documentação diz "exceto para subclasse X"**  
❌ **Testes quebram ao adicionar nova subclasse**  
❌ **Métodos sobrescritos fazem algo completamente diferente**

### Próximos Passos

Continue sua jornada no SOLID aprendendo sobre:
- **I - Interface Segregation Principle**: Como criar interfaces focadas que facilitam LSP
- **D - Dependency Inversion Principle**: Como depender de abstrações estáveis

**Lembre-se:** LSP não é sobre "é um" (IS-A), mas sobre **"se comporta como"** (BEHAVES-LIKE-A). Use herança apenas quando houver **verdadeira substituibilidade comportamental**.

---

> *"Se parece com um pato, grasna como um pato, mas precisa de baterias — você provavelmente tem a abstração errada."* - Analogia do LSP

**Gostou deste guia?** Contribua com exemplos, correções ou traduções no GitHub!

📚 **Referências:**
- Liskov, Barbara; Wing, Jeannette. "A Behavioral Notion of Subtyping" (1994)
- Martin, Robert C. "Agile Software Development: Principles, Patterns, and Practices"
- Bloch, Joshua. "Effective Java" - Item 18: Favor composition over inheritance
- Meyer, Bertrand. "Object-Oriented Software Construction" - Design by Contract
