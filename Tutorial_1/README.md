# 📱 Relatório de Projetos – Computação Móvel

**Course:** Computação Móvel  
**Student(s):** Bernardo Rocha – 15033  
**Date:** 2024/2025  
**Repository URL:** https://github.com/Bernardo5018/Tutoriais_Comp_Movel

---

## 1. Introduction

Este relatório documenta o conjunto de trabalhos práticos desenvolvidos no âmbito da unidade curricular de **Computação Móvel**, do curso de Engenharia Informática, utilizando **Kotlin** e **Android Studio**.

O objetivo central da unidade curricular é dotar os estudantes de competências sólidas no desenvolvimento de aplicações para dispositivos móveis. Os projetos foram organizados de forma progressiva: os primeiros focam-se nos fundamentos da plataforma Android, o TP1 aprofunda a linguagem Kotlin pura, e o RickAndMortyExplorer integra todas as competências numa aplicação completa com arquitetura moderna.

**Objetivos gerais:**
- Compreender a estrutura e o ciclo de vida de aplicações Android
- Desenvolver interfaces gráficas com os componentes do Android SDK
- Explorar a linguagem Kotlin nos paradigmas OOP, funcional e assíncrono
- Consumir APIs REST externas com Retrofit e coroutines
- Implementar a arquitetura MVVM com ViewModel e LiveData
- Aplicar boas práticas de separação de responsabilidades e manutenibilidade

---

## 2. System Overview

O repositório contém quatro projetos distintos mas complementares, cada um abordando uma camada diferente do desenvolvimento Android e da linguagem Kotlin.

| # | Projeto | Tipo | Tecnologias Principais |
|---|---------|------|------------------------|
| 1 | HelloWorld | Aplicação Android base | Kotlin, ConstraintLayout, Activity |
| 2 | HelloWorldOptional | Aplicação Android | Kotlin, Build API, EditText |
| 3 | RickAndMortyExplorer | Aplicação Android avançada | MVVM, Retrofit, Navigation, Coil |
| 4 | TP1 | Exercícios Kotlin (JVM) | Kotlin, POO, Coleções, Exceções |

**HelloWorld** — Primeira aplicação Android com uma única Activity, layout responsivo e CalendarView interativo. Serve como ponto de entrada para compreender a estrutura de um projeto Android Studio.

**HelloWorldOptional** — Extensão do HelloWorld que recolhe e apresenta em tempo de execução as propriedades técnicas do dispositivo via classe `Build` do Android SDK.

**RickAndMortyExplorer** — Aplicação completa que consome a [Rick and Morty API](https://rickandmortyapi.com/) pública para listar e detalhar personagens. Implementa arquitetura MVVM com Retrofit, Coil e Navigation Component.

**TP1** — Três exercícios Kotlin independentes (arrays, calculadora, sequências lazy) e um sistema de biblioteca virtual orientado a objetos.

---

## 3. Architecture and Design

### HelloWorld e HelloWorldOptional

Seguem a arquitetura mínima de uma aplicação Android: uma única `Activity` com layout XML associado. A escolha de `ConstraintLayout` permite posicionamento relativo dos componentes sem hierarquias aninhadas, resultando em melhor performance de renderização.

### RickAndMortyExplorer – MVVM

O projeto implementa o padrão **Model-View-ViewModel**, recomendado pela Google para Android moderno:

| Camada | Responsabilidade |
|--------|-----------------|
| **Model** | `CharacterRepository`, `RickAndMortyApi`, `CharacterModels` |
| **ViewModel** | `CharacterViewModel` com LiveData para gestão de estado |
| **View** | `CharacterListFragment`, `CharacterDetailFragment`, `CharacterAdapter` |

O fluxo de dados é unidirecional: a View observa o ViewModel (LiveData), o ViewModel delega ao Repository, e o Repository comunica com a API via Retrofit. Este design facilita os testes unitários pois cada camada pode ser testada de forma isolada.

### TP1 – Design Orientado a Objetos

A biblioteca virtual aplica os princípios SOLID. A classe abstrata `Book` define o contrato comum, enquanto `DigitalBook` e `PhysicalBook` adicionam propriedades específicas. O princípio Open/Closed é respeitado: é possível adicionar novos tipos de livro sem modificar a classe `Library`.

**Justificação das principais decisões:**

| Decisão | Justificação |
|---------|-------------|
| MVVM | Separação clara entre UI e lógica; LiveData gere o ciclo de vida automaticamente |
| Retrofit | API declarativa type-safe, integração nativa com coroutines e Gson |
| Navigation Component | Gestão do backstack simplificada; animações e passagem de argumentos |
| Coil | Biblioteca de imagens moderna em Kotlin com suporte nativo a coroutines |
| ViewBinding | Acesso type-safe aos componentes de layout, elimina `findViewById` |

---

## 4. Implementation

### 4.1 HelloWorld

A implementação resume-se à configuração do modo Edge-to-Edge e ao tratamento dos insets do sistema:

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_main)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }
        println(localClassName + getString(R.string.activity_oncreate_msg))
    }
}
```

### 4.2 HelloWorldOptional

Recolhe propriedades do dispositivo via `Build` e injeta no `EditText`:

```kotlin
val informacoes =
    "Manufacturer: " + Build.MANUFACTURER + "\n" +
    "Model: "        + Build.MODEL        + "\n" +
    "SDK: "          + Build.VERSION.SDK_INT + "\n" +
    "Version: "      + Build.VERSION.RELEASE + "\n"
editText.setText(informacoes)
```

### 4.3 RickAndMortyExplorer

**Interface Retrofit:**
```kotlin
interface RickAndMortyApi {
    @GET("character")
    suspend fun getCharacters(@Query("page") page: Int = 1): Response<CharacterResponse>

    @GET("character/{id}")
    suspend fun getCharacter(@Path("id") id: Int): Response<Character>
}
```

**ViewModel com estado reativo:**
```kotlin
fun loadCharacters(page: Int = 1) {
    viewModelScope.launch {
        _isLoading.value = true
        try {
            val response = repository.getCharacters(page)
            if (response.isSuccessful) _characters.value = response.body()?.results
            else _error.value = "Error: ${response.code()}"
        } catch (e: Exception) {
            _error.value = e.message
        } finally {
            _isLoading.value = false
        }
    }
}
```

**Navegação entre ecrãs:**
```kotlin
val bundle = Bundle().apply { putInt("characterId", character.id) }
findNavController().navigate(R.id.action_list_to_detail, bundle)
```

### 4.4 TP1

**Exercício 1 – Três abordagens para quadrados perfeitos:**
```kotlin
val a = IntArray(50) { i -> (i + 1) * (i + 1) }         // IntArray com lambda
val b = (1..50).map { it * it }.toIntArray()              // Programação funcional
val c = Array(50) { i -> (i + 1) * (i + 1) }             // Array genérico
```

**Exercício 2 – Calculadora com `when` e exceções:**
```kotlin
val resultado: Any = when (op) {
    "+", "-", "*", "/" -> { /* operações aritméticas com Double */ }
    "&&", "||"         -> { /* operações booleanas */            }
    "shl", "shr"       -> { /* deslocamento de bits em Int */    }
    else -> throw IllegalArgumentException("Operador '$op' não reconhecido.")
}
```

**Exercício 3 – Sequência lazy com `generateSequence`:**
```kotlin
val alturas = generateSequence(100.0 * 0.6) { it * 0.6 }
    .filter { it >= 1.0 }
    .take(15)
    .toList()
```

**Biblioteca Virtual – Classe abstrata `Book`:**
```kotlin
abstract class Book(val title: String, val author: String, val publicationYear: Int) {
    var availableCopies: Int = 0
        set(value) {
            if (value < 0) println("Warning: Cannot have negative copies!")
            else { field = value; if (field == 0) println("Warning: Book is now out of stock!") }
        }
    val yearCategory: String
        get() = when {
            publicationYear < 1980        -> "Classic"
            publicationYear in 1980..2010 -> "Modern"
            else                          -> "Contemporary"
        }
    abstract fun getStorageInfo(): String
}
```

---

## 5. Testing and Validation

Os projetos foram validados através de testes manuais em emulador (AVD) e dispositivo físico, dado o âmbito académico dos trabalhos.

### HelloWorld e HelloWorldOptional
- Verificação do arranque da aplicação sem crash
- Confirmação da mensagem de log no Logcat com o nome correto da Activity
- Teste em orientação portrait e landscape
- Validação dos dados do `HelloWorldOptional` em emuladores com diferentes versões de Android (API 28, 33, 34)

### RickAndMortyExplorer
- Carregamento da lista com ligação à internet ativa
- Estado de loading: `ProgressBar` visível durante o pedido, oculto após resposta
- Teste de erro de rede: desligar a ligação e verificar Toast com mensagem de erro
- Navegação lista → detalhe e retrocesso
- Rotação do dispositivo: ViewModel retém os dados sem refazer pedidos à API

### TP1
- Exercício 1: comparação dos três arrays — produzem resultados idênticos
- Exercício 2: todos os operadores suportados com valores válidos; operador inválido (exceção apanhada); divisão por zero
- Exercício 3: nenhum valor inferior a 1.0; contagem correta de elementos
- Biblioteca virtual: empréstimo com e sem stock disponível; devolução; pesquisa por autor

### Limitações Conhecidas

| Limitação | Descrição |
|-----------|-----------|
| Sem testes automáticos | Nenhum projeto inclui testes JUnit ou Espresso |
| Paginação incompleta | O Explorer carrega apenas a primeira página (20 personagens) |
| Biblioteca sem persistência | Os dados da biblioteca virtual perdem-se ao terminar a execução |
| Sem mock de rede | Não foram usados MockWebServer para simular respostas da API |

---

## 6. Usage Instructions

### Requisitos
- **Android Studio** Hedgehog (2023.1.1) ou superior
- **JDK** 17 ou superior / **Android SDK** API 28 (mínimo), API 34 (target)
- **IntelliJ IDEA** — necessário para o TP1 (projeto Maven/Kotlin JVM)
- **Ligação à internet** — necessária para o RickAndMortyExplorer

### HelloWorld e HelloWorldOptional
1. Clonar o repositório: `git clone https://github.com/username/computacao-movel.git`
2. Abrir `HelloWorld/` ou `HelloWorldOptional/` no Android Studio
3. Aguardar a sincronização do Gradle
4. Selecionar um AVD ou ligar um dispositivo físico com USB Debugging ativo
5. Clicar em **Run ▶**

### RickAndMortyExplorer
1. Abrir `RickAndMortyExplorer/` no Android Studio
2. Aguardar o download das dependências (Retrofit, Coil, Navigation)
3. Garantir que o emulador/dispositivo tem acesso à internet
4. Executar com **Run ▶** — clicar num personagem abre o ecrã de detalhe

### TP1
1. Abrir `TP1/` no IntelliJ IDEA — deteta automaticamente o `pom.xml`
2. Para os exercícios: correr `cm/exer_1/exer_1.kt`, `exer_2.kt`, `exer_3.kt`
3. Para a biblioteca virtual: correr `cm/virtual_library/Main.kt`

---

## 7. Prompting Strategy

Durante o desenvolvimento foi utilizado o **Claude (Anthropic)** como assistente de IA. A estratégia de prompting evoluiu ao longo dos projetos, tornando-se progressivamente mais estruturada e eficaz.

**Abordagens utilizadas:**

**Prompts contextuais com código** — em vez de perguntas genéricas, os prompts incluíam o código existente e especificavam o problema concreto:
```
"Tenho este CharacterViewModel. O _isLoading nunca passa a false quando ocorre
uma exceção. Como corrigir com try/catch/finally dentro de viewModelScope?"
```

**Prompts de explicação progressiva** — para compreender conceitos novos:
```
"Explica a diferença entre MutableLiveData e LiveData num ViewModel Android,
com exemplo de como expor dados à Fragment de forma segura."
```

**Prompts de revisão** — o código era submetido para revisão após implementação:
```
"Revê este CharacterRepository. O tratamento de erro com Response<T>
do Retrofit está correto? Há melhorias a fazer?"
```

**Lições aprendidas:**
- Prompts específicos com código concreto produzem respostas mais úteis do que perguntas abstratas
- Indicar a versão das bibliotecas evita sugestões com APIs desatualizadas
- Pedir ao modelo que explique o raciocínio facilita a aprendizagem real
- Dividir problemas complexos em subproblemas resulta em respostas mais focadas

---

## 8. Autonomous Agent Workflow

O assistente de IA foi integrado em múltiplas fases do ciclo de desenvolvimento:

| Fase | Contribuição do Agente |
|------|----------------------|
| Planeamento | Comparação de arquiteturas (MVVM vs MVP), decisões de design |
| Implementação | Geração de boilerplate, dúvidas de API Android |
| Debugging | Diagnóstico de erros, sugestões de correção |
| Testes | Sugestão de casos de teste e cenários de fronteira |
| Documentação | Estruturação do relatório, revisão de conteúdo |

**Contribuições concretas no RickAndMortyExplorer:**
- Geração do esqueleto inicial do `RetrofitInstance` com configuração correta do `GsonConverterFactory`
- Definição da estrutura das data classes alinhada com o JSON da API
- Implementação da lógica de navegação com Navigation Component
- Resolução de um bug onde as imagens Coil não respeitavam os cantos arredondados

---

## 9. Verification of AI-Generated Artifacts

Todo o código sugerido pelo assistente foi sujeito a verificação antes de ser integrado. A política adotada foi: **nunca aceitar código sem o compreender e validar**.

**Processo de revisão:**
- Leitura linha a linha de todos os snippets antes de os copiar
- Comparação com a documentação oficial (developer.android.com, kotlinlang.org) em caso de dúvida
- Verificação de compatibilidade com as versões de bibliotecas usadas
- Teste de execução imediato após integração

**Análise estática:**
- Utilização do *Inspect Code* do Android Studio para detetar code smells
- Revisão dos warnings do compilador Kotlin (deprecated APIs, nullable types)
- Verificação das sugestões de lint sobre boas práticas Android

**Casos de correção identificados:**
- Sugestão inicial com `Glide` → substituído por `Coil` (mais idiomático em Kotlin)
- `RetrofitInstance` não-singleton → corrigido para `object` Kotlin
- Uso de `GlobalScope` → corrigido para `viewModelScope` para respeitar o ciclo de vida

---

## 10. Human vs AI Contribution

| Componente | Desenvolvimento Humano | Assistência de IA |
|-----------|----------------------|------------------|
| HelloWorld / HelloWorldOptional | Implementação completa | Esclarecimento de dúvidas pontuais |
| Arquitetura MVVM | Decisão e estruturação final | Comparação de alternativas |
| RetrofitInstance | Integração e configuração | Sugestão do esqueleto inicial |
| CharacterViewModel | Implementação e correção de bugs | Sugestão da estrutura LiveData |
| CharacterAdapter (RecyclerView) | Implementação completa | Sugestão do padrão ViewHolder |
| Navegação (Navigation Component) | Configuração do NavGraph | Esclarecimento da passagem de argumentos |
| TP1 – Exercícios 1, 2, 3 | Implementação completa | Revisão e feedback |
| TP1 – Biblioteca Virtual | Design OOP e implementação | Revisão da hierarquia de classes |
| Layouts XML | Implementação completa | Sem assistência |
| Testes e Validação | Execução manual completa | Sugestão de casos de teste |
| Relatório | Escrita e revisão final | Estruturação e revisão de conteúdo |

Estima-se que cerca de **80% do código final é de autoria humana** — escrito, compreendido e testado pelo estudante. Os restantes 20% foram gerados ou sugeridos pela IA e posteriormente revistos, corrigidos e integrados de forma consciente.

---

## 11. Ethical and Responsible Use

**Riscos identificados e como foram geridos:**

**Dependência excessiva** — O maior risco no contexto académico é substituir a aprendizagem pela geração automática. Para mitigar, foi adotada a política de nunca aceitar código sem o compreender — qualquer sugestão foi acompanhada de uma explicação solicitada ao próprio Claude.

**Alucinações e código incorreto** — Os modelos de linguagem podem gerar código plausível mas incorreto com APIs recentes. Foram identificadas duas situações: uma importação inexistente numa versão de Coil e um padrão de coroutine deprecated. Ambas foram detetadas na compilação e corrigidas com consulta à documentação oficial.

**Sugestões desproporcionadas** — O Claude tende a sugerir soluções mais complexas do que o necessário (ex: sugeriu injeção de dependências com Hilt para o HelloWorld). As sugestões foram sempre filtradas com senso crítico.

**Medidas adotadas:**
- A IA foi utilizada como ferramenta de apoio à aprendizagem, nunca como substituto
- Todo o código final foi escrito, compreendido e testado pelo estudante
- As sugestões foram sempre validadas contra documentação oficial
- O uso da IA foi documentado de forma transparente neste relatório

---

## 12. Version Control and Commit History

O repositório foi gerido com **Git**, com commits frequentes que refletem o trabalho contínuo ao longo do semestre. A política adotada foi de commits atómicos: cada commit corresponde a uma funcionalidade ou correção específica.

**Convenção de mensagens de commit:**

| Prefixo | Significado |
|---------|-------------|
| `feat:` | Nova funcionalidade |
| `fix:` | Correção de bug |
| `refactor:` | Refatoração sem mudança de comportamento |
| `docs:` | Alterações em documentação |
| `style:` | Formatação sem mudanças de lógica |

**Exemplos de commits representativos:**
```
feat: initial HelloWorld project with ConstraintLayout and CalendarView
feat: add Build info display to HelloWorldOptional
feat: setup Retrofit with RickAndMorty API and Gson converter
feat: implement CharacterViewModel with LiveData loading/error states
feat: add RecyclerView with CharacterAdapter and ViewHolder pattern
fix: coroutine scope changed from GlobalScope to viewModelScope
feat: implement CharacterDetailFragment with Coil image loading
feat: add TP1 exercises - arrays, calculator, generateSequence
feat: implement virtual library OOP hierarchy with Book/DigitalBook/PhysicalBook
docs: add README with full project documentation
```

O histórico de commits reflete trabalho contínuo ao longo do semestre, não apenas commits finais antes da entrega.

---

## 13. Difficulties and Lessons Learned

### Principais Dificuldades

**Ciclo de vida dos Fragments** — A maior dificuldade no RickAndMortyExplorer foi compreender como o ciclo de vida dos Fragments interage com o ViewModel. Inicialmente, os dados eram recarregados da API sempre que o Fragment era recriado (ex: rotação de ecrã). A solução foi perceber que o ViewModel sobrevive às recreações e que o Observer deve ser configurado com `viewLifecycleOwner` em vez de `this`.

**Coroutines e Threading** — A transição para programação assíncrona exigiu uma mudança de paradigma. O erro mais comum foi aceder à rede na main thread (`NetworkOnMainThreadException`). Compreender que `suspend functions` e `viewModelScope.launch` garantem execução na IO thread foi uma aprendizagem fundamental.

**Gestão de dependências com Gradle** — A primeira configuração do Gradle com Retrofit, Coil e Navigation Component gerou conflitos de versões. Foi necessário alinhar as versões das bibliotecas com a versão do Android Gradle Plugin utilizada.

**Lazy evaluation no TP1** — A lógica de `generateSequence` com avaliação lazy não é imediatamente intuitiva. Compreender que a sequência não é calculada de uma vez, mas elemento a elemento conforme necessário, foi um conceito que exigiu experimentação.

### Lições Aprendidas

- A arquitetura MVVM, apesar de parecer excessiva para projetos pequenos, torna o código significativamente mais fácil de manter e depurar à medida que o projeto cresce
- Kotlin é uma linguagem expressiva que recompensa o investimento inicial: features como data classes, extension functions e coroutines reduzem muito o boilerplate em comparação com Java
- Começar pela documentação oficial (developer.android.com) antes de procurar soluções em fóruns evita aprender padrões desatualizados
- Testes manuais estruturados (lista de cenários a verificar) são mais eficazes do que testes ad-hoc

---

## 14. Future Improvements

### 1️⃣ Hello World

- Adicionar um botão que altere a mensagem apresentada dinamicamente ao ser clicado, introduzindo interatividade básica
- Substituir o `CalendarView` por um `DatePickerDialog` mais moderno e personalizável
- Implementar suporte a **Dark Mode** com temas alternativos em `themes.xml`
- Adicionar animações de entrada nos elementos de layout com `MotionLayout` ou `ObjectAnimator`
- Explorar o modo landscape já incluído no projeto com um layout diferenciado para orientação horizontal

---

### 2️⃣ HelloWorld Optional

- Adicionar um botão de **cópia para a área de transferência** para permitir partilhar as informações do dispositivo facilmente
- Incluir informações adicionais como resolução do ecrã (`DisplayMetrics`), memória RAM disponível (`ActivityManager`) e espaço de armazenamento livre
- Apresentar os dados num `RecyclerView` em vez de um `EditText`, com cada campo numa linha formatada individualmente
- Adicionar a possibilidade de **exportar as informações para um ficheiro de texto** através do sistema de ficheiros Android
- Implementar atualização automática de campos que mudam em tempo real, como a memória disponível

---

### 3️⃣ Rick and Morty Explorer

- Implementar **paginação infinita** com `Paging 3` para carregar mais personagens à medida que o utilizador faz scroll
- Adicionar **filtros e pesquisa** por nome, estado (vivo/morto) e espécie diretamente na lista
- Implementar **cache local** com `Room` (base de dados SQLite) para permitir o uso offline da aplicação
- Migrar o `CharacterDetailFragment` para usar um `ViewModel` próprio com injeção de dependências via **Hilt**
- Adicionar animações de transição partilhadas (`Shared Element Transition`) entre a lista e o detalhe para uma experiência mais fluída
- Implementar **testes unitários** para o `CharacterViewModel` e `CharacterRepository` com `JUnit` e `MockK`
- Suportar a listagem de **episódios** em que cada personagem aparece, já disponíveis no modelo de dados (`episode: List<String>`)
- Adicionar suporte a **modo offline** com indicador de conectividade em tempo real

---

### 4️⃣ TP1 – Biblioteca Virtual

- Persistir os dados da biblioteca em ficheiro JSON ou base de dados **SQLite**, eliminando a perda de estado entre execuções
- Adicionar um **menu interativo em consola** com opções numeradas para o utilizador gerir a biblioteca sem precisar editar o código
- Implementar o conceito de **empréstimo com prazo** — associar uma data de devolução a cada empréstimo e calcular penalizações por atraso
- Criar uma interface **Android** para a biblioteca virtual, reutilizando as classes do domínio (`Book`, `Library`) como camada de modelo
- Adicionar **testes unitários** com `JUnit` para validar as operações de empréstimo, devolução e pesquisa
- Expandir a hierarquia de livros com novos tipos como `AudioBook` ou `Magazine`, aplicando o princípio Open/Closed
- Implementar **pesquisa por múltiplos critérios** (título, ano, categoria) com filtros combináveis usando a API de coleções Kotlin

---

## 15. AI Usage Disclosure (Mandatory)

| Campo | Detalhe |
|-------|---------|
| **Ferramenta utilizada** | Claude (Anthropic) — claude.ai |
| **Como foi utilizada** | Apoio ao planeamento de arquitetura, revisão de código, debugging, estruturação da documentação e deste relatório |
| **Secções com contribuição de IA** | Secções 7, 8, 9, 10, 11 descrevem em detalhe o uso |
| **Responsabilidade** | Todo o conteúdo deste relatório e do repositório foi revisto, compreendido e validado pelo estudante. O estudante assume plena responsabilidade por todo o conteúdo submetido |
| **Código gerado por IA** | Estimativa de ~20% do código foi sugerido pela IA e posteriormente revisto e integrado de forma consciente |
| **Código de autoria humana** | Estimativa de ~80% — escrito, compreendido e testado pelo estudante |

A utilização do assistente de IA foi feita de forma transparente, ética e como ferramenta de aprendizagem — nunca como substituto do trabalho e do raciocínio próprio. Qualquer sugestão gerada pela IA foi verificada, testada e só integrada após compreensão plena pelo estudante.

---

## ✅ Conclusion

Este repositório documenta a evolução progressiva ao longo da unidade curricular de **Computação Móvel**, partindo do zero absoluto até à construção de uma aplicação Android com arquitetura moderna e integração de API REST.

Os primeiros dois projetos — **HelloWorld** e **HelloWorldOptional** — serviram como ponto de entrada no ecossistema Android, permitindo familiarizar com conceitos essenciais como o ciclo de vida de uma `Activity`, a organização de recursos em XML e a estrutura de um projeto Gradle. Apesar da sua simplicidade, foram determinantes para compreender como o sistema Android funciona por baixo.

O **TP1** consolidou os fundamentos da linguagem Kotlin fora do contexto Android, explorando paradigmas funcionais, tratamento de erros e programação orientada a objetos. A biblioteca virtual em particular demonstrou como aplicar corretamente conceitos como herança, polimorfismo e encapsulamento numa solução coerente e extensível.

O projeto **RickAndMortyExplorer** representou o salto qualitativo mais significativo, exigindo a integração de múltiplas tecnologias num sistema bem estruturado: comunicação assíncrona com uma API REST via Retrofit e coroutines, gestão de estado reativa com ViewModel e LiveData, navegação entre ecrãs com Navigation Component, e apresentação eficiente de listas com RecyclerView. A adoção da arquitetura MVVM desde o início garantiu uma separação clara de responsabilidades, tornando o código mais testável e fácil de manter.

No global, estes projetos estabeleceram uma base sólida em desenvolvimento Android moderno com Kotlin, cobrindo tanto os fundamentos da plataforma como padrões de arquitetura utilizados na indústria. As melhorias futuras identificadas em cada projeto apontam caminhos concretos para aprofundar os conhecimentos adquiridos — nomeadamente persistência de dados, testes automatizados, injeção de dependências e otimização de performance.

---

## 👨‍💻 Autor

**Bernardo Rocha – 15033**

Estudante de Engenharia Informática
Unidade Curricular: Computação Móvel

---

## 📄 Licença

Projetos desenvolvidos para **fins académicos**.
