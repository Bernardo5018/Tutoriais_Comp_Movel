# 📱 Relatório de Projetos – Computação Móvel

**Course:** Computação Móvel  
**Student(s):** Bernardo Rocha – 15033  
**Date:** 2024/2025  
**Repository URL:** https://github.com/Bernardo5018/Tutoriais_Comp_Movel

---
## 1. Introduction

Este relatório descreve dois projetos desenvolvidos no âmbito da unidade curricular de **Computação Móvel**, no curso de **Engenharia Informática**, durante o ano letivo 2024-2025.

O objetivo da unidade curricular é desenvolver competências no desenvolvimento de aplicações móveis e na linguagem **Kotlin**, explorando desde conceitos fundamentais até arquiteturas e ferramentas modernas do ecossistema Android e JVM.

Os dois projetos abordados são:

* **CoolWeatherApp** – aplicação Android que consome uma API meteorológica em tempo real, construída com Jetpack Compose e arquitetura MVVM.
* **GreetingProcessor** – projeto Kotlin JVM que implementa processamento de anotações em tempo de compilação com KAPT e KotlinPoet, gerando código automaticamente.

Ambos os projetos têm como objetivo aprofundar o conhecimento prático em Kotlin, seguindo boas práticas de arquitetura de software e explorando ferramentas modernas do ecossistema.

---

## 2. System Overview

### CoolWeatherApp

O **CoolWeatherApp** é uma aplicação Android que permite ao utilizador consultar dados meteorológicos em tempo real para qualquer localização do mundo. O utilizador pode introduzir coordenadas manualmente, selecionar um ponto no mapa através do **Google Maps**, ou aceder a localizações favoritas guardadas.

**Principais funcionalidades:**
* Consulta de dados meteorológicos em tempo real via API Open-Meteo
* Seleção de localização por coordenadas ou por mapa interativo
* Gestão de localizações favoritas
* Interface adaptativa para orientação portrait e landscape
* Ícones meteorológicos dinâmicos (dia/noite) com base no código WMO

### GreetingProcessor

O **GreetingProcessor** é um projeto Kotlin JVM multi-módulo que demonstra a geração automática de código em tempo de compilação através de **annotation processing** com KAPT. O sistema processa anotações personalizadas (`@Greeting` e `@Extract`) e gera automaticamente classes Kotlin completas durante a compilação.

**Principais funcionalidades:**
* Definição de anotações personalizadas com retenção `SOURCE`
* Geração automática de classes wrapper com mensagens de saudação (`GreetingProcessor`)
* Geração automática de classes extratoras com expressões regulares (`RegexProcessor`)
* Estrutura multi-módulo (annotations, processor, app)

---

## 3. Architecture and Design

### CoolWeatherApp – Arquitetura MVVM

A aplicação segue a arquitetura **MVVM (Model–View–ViewModel)**, recomendada pelo Google para desenvolvimento Android moderno, com separação clara de responsabilidades entre as camadas:

```
┌─────────────────────────────────────┐
│           UI Layer (View)           │
│   WeatherScreen.kt (Jetpack Compose)│
│   LocationPickerActivity.kt         │
└────────────────┬────────────────────┘
                 │ observa StateFlow
┌────────────────▼────────────────────┐
│         ViewModel Layer             │
│   WeatherViewModel.kt               │
│   WeatherUIState (StateFlow)        │
└────────────────┬────────────────────┘
                 │ chama suspend fun
┌────────────────▼────────────────────┐
│           Data Layer                │
│   WeatherApiClient.kt (Ktor)        │
│   WeatherData.kt (modelos)          │
└─────────────────────────────────────┘
```

**Estrutura de pastas:**
```
CoolWeatherAPP_tutorial_3
└── app/src/main/java/dam_A15033/coolweatherapp
    ├── MainActivity.kt
    ├── data
    │   ├── WeatherApiClient.kt
    │   └── WeatherData.kt
    ├── ui
    │   ├── WeatherScreen.kt
    │   ├── WeatherCodeMapper.kt
    │   └── LocationPickerActivity.kt
    └── viewmodel
        └── WeatherViewModel.kt
```

**Decisões de design:**
* Uso de **StateFlow** em vez de LiveData para reatividade mais moderna e suporte nativo a Compose
* **Ktor** como cliente HTTP em vez de Retrofit, por ser mais idiomático em Kotlin puro
* **Kotlinx Serialization** para deserialização do JSON da API, sem necessidade de Gson
* Separação da `LocationPickerActivity` como Activity independente para manter a `MainActivity` coesa

---

### GreetingProcessor – Arquitetura Multi-Módulo

O projeto segue uma arquitetura de **três módulos Gradle** com dependências unidirecionais:

```
┌──────────────┐
│  annotations │  Define @Greeting e @Extract
└──────┬───────┘
       │ depende de
┌──────▼───────┐
│  processor   │  Implementa GreetingProcessor e RegexProcessor
└──────┬───────┘
       │ depende de (kapt)
┌──────▼───────┐
│     app      │  Usa as anotações; recebe o código gerado
└──────────────┘
```

**Estrutura de pastas:**
```
GreetingProcessor
├── annotations/src/main/kotlin/annotations
│   ├── Greeting.kt
│   └── Extract.kt
├── processor/src/main/kotlin/processor
│   ├── GreetingProcessor.kt
│   └── RegexProcessor.kt
└── app/src/main/kotlin/app
    ├── MyClass.kt
    ├── DataProcessor.kt
    └── Main.kt
```

**Decisões de design:**
* Separação do módulo `annotations` do `processor` para que o módulo `app` não precise de ter o processador no classpath em runtime
* Uso de **KotlinPoet** para construção de código de forma tipada e segura, em vez de concatenação de strings
* **AutoService** para registo automático dos processadores no `META-INF/services`, evitando configuração manual
* Retenção `SOURCE` nas anotações, pois estas não precisam de existir em runtime

---

## 4. Implementation

### CoolWeatherApp

**`WeatherApiClient.kt`** – cliente HTTP singleton baseado em Ktor. Constrói a URL de pedido com os parâmetros de latitude, longitude e campos pretendidos, e deserializa a resposta JSON para o modelo `WeatherData`.

```kotlin
suspend fun getWeather(lat: Float, lon: Float): WeatherData? {
    val reqString = buildString {
        append("https://api.open-meteo.com/v1/forecast?")
        append("latitude=$lat&longitude=$lon&")
        append("current_weather=true&")
        append("hourly=temperature_2m,weathercode,pressure_msl,windspeed_10m")
    }
    return try {
        client.get(reqString).body()
    } catch (e: Exception) {
        e.printStackTrace()
        null
    }
}
```

**`WeatherData.kt`** – modelos de dados serializáveis que mapeiam diretamente a resposta JSON da API Open-Meteo:

```kotlin
@Serializable
data class WeatherData(
    val latitude: Float,
    val longitude: Float,
    val current_weather: CurrentWeather,
    val hourly: Hourly
)
```

**`WeatherViewModel.kt`** – gere o estado da aplicação com `StateFlow` e expõe um `WeatherUIState` imutável. A função `fetchWeather()` corre numa coroutine do `viewModelScope`:

```kotlin
data class WeatherUIState(
    val latitude: Float = 38.7169f,
    val longitude: Float = -9.1399f,
    val temperature: Float = 0f,
    val windspeed: Float = 0f,
    val winddirection: Int = 0,
    val weathercode: Int = 0,
    val seaLevelPressure: Float = 0f,
    val time: String = ""
)
```

**`WeatherScreen.kt`** – interface declarativa em Jetpack Compose, organizada em componentes reutilizáveis (`HeaderCard`, `CoordinatesCard`, `FavoritesRow`, `WeatherInfoCard`). Deteta a orientação do dispositivo e renderiza layouts distintos:

```kotlin
if (configuration.orientation == Configuration.ORIENTATION_LANDSCAPE) {
    LandscapeWeatherUI(...)
} else {
    PortraitWeatherUI(...)
}
```

**`WeatherCodeMapper.kt`** – mapeia códigos WMO para recursos de drawable, distinguindo dia e noite com base na hora atual:

```kotlin
fun getWeatherIcon(code: Int, isNight: Boolean): Int {
    return when (code) {
        0 -> if (isNight) R.drawable.clear_night else R.drawable.clear_day
        1 -> if (isNight) R.drawable.mostly_clear_night else R.drawable.mostly_clear_day
        3 -> R.drawable.cloudy
        45, 48 -> R.drawable.fog
        61, 63, 65 -> R.drawable.rain
        95, 96, 99 -> R.drawable.tstorm
        else -> R.drawable.cloudy
    }
}
```

**`LocationPickerActivity.kt`** – Activity secundária com um mapa Google Maps interativo. O utilizador clica num ponto do mapa e as coordenadas são devolvidas à `MainActivity` via `Intent`.

---

### GreetingProcessor

**`Greeting.kt` e `Extract.kt`** – anotações personalizadas com retenção `SOURCE` e alvo `FUNCTION`:

```kotlin
@Target(AnnotationTarget.FUNCTION)
@Retention(AnnotationRetention.SOURCE)
annotation class Greeting(val message: String)

@Target(AnnotationTarget.FUNCTION)
@Retention(AnnotationRetention.SOURCE)
annotation class Extract(val regex: String)
```

**`GreetingProcessor.kt`** – agrupa os métodos anotados por classe e gera uma classe wrapper com KotlinPoet. Cada método gerado imprime a mensagem antes de delegar ao método original:

```kotlin
@AutoService(Processor::class)
@SupportedAnnotationTypes("annotations.Greeting")
class GreetingProcessor : AbstractProcessor() {
    override fun process(
        annotations: MutableSet<out TypeElement>,
        roundEnv: RoundEnvironment
    ): Boolean {
        for (element in roundEnv.getElementsAnnotatedWith(Greeting::class.java)) {
            if (element is ExecutableElement) {
                val enclosingClass = element.enclosingElement as TypeElement
                classMethodMap.computeIfAbsent(enclosingClass) { mutableListOf() }.add(element)
            }
        }
        for ((classElement, methods) in classMethodMap) {
            generateKotlinWrapperClass(classElement, methods)
        }
        return true
    }
}
```

**`RegexProcessor.kt`** – gera uma subclasse concreta da classe abstrata original. Para cada método anotado, implementa a extração com a expressão regular definida:

```kotlin
val methodBuilder = FunSpec.builder(methodName)
    .addModifiers(KModifier.OVERRIDE)
    .returns(String::class.asClassName().copy(nullable = true))
    .addStatement("val match = Regex(%S).find(input)", regex)
    .addStatement("return match?.groupValues?.get(1)")
```

**`Main.kt` (app)** – demonstra o uso das classes geradas automaticamente:

```kotlin
fun main() {
    val input = "Name: John Address: 123 Street"
    val extractor = DataProcessorExtractor(input)
    println("Name: ${extractor.getName()}")      // Name: John
    println("Address: ${extractor.getAddress()}") // Address: 123 Street
}
```

---

## 5. Testing and Validation

### CoolWeatherApp

A validação da aplicação foi realizada de forma manual através de testes funcionais no emulador Android e em dispositivo físico:

| Cenário de Teste | Resultado |
|---|---|
| Carregar dados meteorológicos para Lisboa (coordenadas padrão) | ✅ Dados carregados corretamente |
| Inserir coordenadas manualmente e atualizar | ✅ Interface atualizada com novos dados |
| Selecionar localização no Google Maps | ✅ Coordenadas devolvidas e dados atualizados |
| Guardar e aceder a localização favorita | ✅ Chip adicionado e selecionável |
| Rotacionar o dispositivo (portrait ↔ landscape) | ✅ Layout adapta-se corretamente |
| Sem ligação à internet | ⚠️ Aplicação não apresenta dados; sem mensagem de erro explícita |
| Coordenadas inválidas (fora do intervalo) | ⚠️ API devolve erro; aplicação não apresenta feedback ao utilizador |

**Limitações conhecidas:**
* Não existe cache local; sem internet a aplicação não apresenta dados
* Não há validação dos campos de coordenadas antes de efetuar o pedido à API
* As localizações favoritas são perdidas ao fechar a aplicação (sem persistência)

---

### GreetingProcessor

A validação do processador foi feita compilando o módulo `app` e verificando que:

| Cenário de Teste | Resultado |
|---|---|
| Compilar `app` com `@Greeting` em `MyClass` | ✅ Classe `MyClassWrapper` gerada corretamente |
| Compilar `app` com `@Extract` em `DataProcessor` | ✅ Classe `DataProcessorExtractor` gerada corretamente |
| Executar `Main.kt` com `DataProcessorExtractor` | ✅ Extração de nome e endereço correta |
| Aplicar `@Greeting` a método sem anotação prévia | ✅ Processador ignora sem erro |
| Regex inválida em `@Extract` | ⚠️ Erro apenas em runtime, não em compilação |

**Limitações conhecidas:**
* Não existe validação das expressões regulares em tempo de compilação
* Suporte limitado a métodos sem parâmetros
* Sem testes automatizados ao processador

---

## 6. Usage Instructions

### CoolWeatherApp

**Requisitos:**
* Android Studio Hedgehog ou superior
* Android SDK 24+
* Chave de API do Google Maps configurada no `AndroidManifest.xml`
* Ligação à internet

**Configuração e execução:**

1. Clonar o repositório e abrir a pasta `CoolWeatherAPP_tutorial_3` no Android Studio
2. Adicionar a chave da Google Maps API no ficheiro `AndroidManifest.xml`:
```xml
<meta-data
    android:name="com.google.android.geo.API_KEY"
    android:value="SUA_CHAVE_AQUI"/>
```
3. Sincronizar o projeto com Gradle (`File > Sync Project with Gradle Files`)
4. Executar a aplicação num emulador ou dispositivo físico com Android 7.0+
5. Na interface, inserir coordenadas ou usar o botão do mapa para selecionar uma localização
6. Pressionar o botão de pesquisa para carregar os dados meteorológicos

---

### GreetingProcessor

**Requisitos:**
* IntelliJ IDEA (Community ou Ultimate)
* JDK 17 ou superior
* Gradle 8+

**Configuração e execução:**

1. Clonar o repositório e abrir a pasta `GreetingProcessor` no IntelliJ IDEA
2. Aguardar a sincronização do projeto Gradle
3. Para compilar e executar o módulo `app`:
```bash
./gradlew :app:run
```
4. O código gerado pelo processador pode ser encontrado em:
```
app/build/generated/source/kapt/main/
```
5. Para limpar e recompilar desde o início:
```bash
./gradlew clean :app:build
```

---

# Autonomous Software Engineering Sections

## 7. Prompting Strategy

Durante o desenvolvimento, as ferramentas de IA foram utilizadas pontualmente para apoio técnico em áreas específicas, nomeadamente na configuração de dependências Gradle e na compreensão da API KotlinPoet.

**Exemplos de prompts utilizados:**

* *"Como configurar o Ktor com Kotlinx Serialization no build.gradle.kts para Android?"* — usado para obter a configuração correta das dependências do cliente HTTP, dado que a documentação oficial tem exemplos dispersos.

* *"Como usar KotlinPoet para gerar uma função que overrides um método abstrato com parâmetros?"* — usado para compreender a API do KotlinPoet na geração de funções com modificadores `override`.

* *"Qual a diferença entre KAPT e KSP e como registar um processador com AutoService?"* — usado para esclarecer conceitos antes de iniciar a implementação do `GreetingProcessor`.

Os prompts foram sempre orientados a **conceitos e configurações**, nunca à geração direta de lógica de negócio ou das funcionalidades principais da aplicação. As respostas foram analisadas criticamente e adaptadas ao contexto específico de cada projeto.

---

## 8. Autonomous Agent Workflow

As ferramentas de IA não foram utilizadas como agentes autónomos nestes projetos. O fluxo de desenvolvimento foi maioritariamente humano, com IA a desempenhar um papel de **consulta pontual**, semelhante à consulta de documentação ou Stack Overflow.

O processo de desenvolvimento seguiu o fluxo típico:

```
Análise do problema
      ↓
Pesquisa de documentação (oficial + IA pontual)
      ↓
Implementação manual no Android Studio / IntelliJ
      ↓
Teste no emulador / execução local
      ↓
Correção de erros e refatoração
      ↓
Validação final
```

A IA não foi utilizada para planeamento arquitetural, decisões de design, escrita de testes, debugging ou documentação.

---

## 9. Verification of AI-Generated Artifacts

Os excertos de código sugeridos por ferramentas de IA foram sempre verificados antes de serem integrados no projeto, através dos seguintes métodos:

* **Leitura e compreensão** – o código sugerido foi lido e compreendido linha a linha antes de ser utilizado, garantindo que a lógica estava correta e adequada ao contexto.
* **Consulta da documentação oficial** – as sugestões relacionadas com bibliotecas (Ktor, KotlinPoet, KAPT) foram validadas contra a documentação oficial respetiva.
* **Compilação e execução** – todo o código foi compilado e executado localmente para verificar o comportamento real, não assumindo que o código sugerido estava correto à partida.
* **Adaptação ao contexto** – nenhum excerto foi utilizado diretamente sem adaptação; foram sempre ajustados aos modelos de dados, nomes de variáveis e estrutura do projeto.

Não foram detetados erros graves introduzidos por código gerado por IA, embora algumas sugestões de configuração Gradle tenham necessitado de ajuste de versões de dependências.

---

## 10. Human vs AI Contribution

| Componente | Autoria |
|---|---|
| Arquitetura MVVM e estrutura de pastas | Humano |
| Modelos de dados (`WeatherData.kt`) | Humano |
| Lógica do ViewModel e StateFlow | Humano |
| Interface Jetpack Compose (`WeatherScreen.kt`) | Humano |
| Mapeamento de ícones meteorológicos | Humano |
| Integração Google Maps | Humano |
| Definição das anotações `@Greeting` e `@Extract` | Humano |
| Lógica dos processadores KAPT | Humano |
| Geração de código com KotlinPoet | Humano (com apoio pontual de IA para sintaxe da API) |
| Configuração Gradle (dependências) | Humano (com apoio pontual de IA para versões) |
| Testes e validação | Humano |


---

## 11. Ethical and Responsible Use

A utilização de ferramentas de IA nestes projetos foi feita de forma consciente e responsável:

* **Transparência** – o uso de IA está documentado neste relatório, identificando claramente as áreas em que foi utilizada.
* **Responsabilidade** – todo o código submetido foi compreendido, verificado e é da responsabilidade do autor, independentemente da origem da sugestão.
* **Sem plágio** – a IA não foi utilizada para gerar soluções completas para os exercícios, mas apenas como ferramenta de apoio ao estudo e à compreensão de APIs.
* **Sem envio de dados sensíveis** – não foram enviados para ferramentas de IA dados pessoais, chaves de API ou código proprietário.
* **Limitações reconhecidas** – reconhece-se que as ferramentas de IA podem gerar código desatualizado ou incorreto, pelo que toda a informação foi verificada.

---

# Development Process

## 12. Version Control and Commit History

O controlo de versões foi realizado com **Git**, com commits regulares ao longo do desenvolvimento de cada projeto, refletindo a progressão incremental do trabalho.

**Padrão de commits utilizado:**

```
feat: add WeatherApiClient with Ktor HTTP client
feat: implement WeatherViewModel with StateFlow
feat: add Jetpack Compose WeatherScreen UI
feat: add portrait and landscape layout support
feat: integrate Google Maps location picker
fix: correct weathercode mapping for fog conditions
feat: add favorites row with chip components
refactor: extract WeatherCodeMapper to separate file

feat: setup multi-module Gradle project structure
feat: define @Greeting and @Extract annotations
feat: implement GreetingProcessor with KotlinPoet
feat: implement RegexProcessor for @Extract annotation
feat: add AutoService registration for processors
test: validate generated classes in app module
```

Os commits foram realizados por funcionalidade completa, garantindo que o histórico reflete o progresso real do desenvolvimento e não apenas estados finais.

---

## 13. Difficulties and Lessons Learned

### CoolWeatherApp

**Dificuldades encontradas:**

* **Configuração do Ktor com Kotlinx Serialization** – a integração do motor HTTP do Ktor com o plugin de serialização no Android exigiu configuração específica do `HttpClient` com o `ContentNegotiation` plugin, o que não é imediatamente óbvio pela documentação.

* **Gestão do estado com StateFlow e Compose** – garantir que a interface reage corretamente às mudanças de estado sem recomposições desnecessárias exigiu compreensão do ciclo de vida dos composables e do uso correto de `collectAsState()`.

* **Layout adaptativo portrait/landscape** – implementar dois layouts distintos que partilham os mesmos dados mas têm estruturas visuais diferentes obrigou a refatorar os componentes Compose para serem suficientemente modulares e reutilizáveis.

* **Integração do Google Maps com Compose** – a `LocationPickerActivity` usa a API tradicional do Maps SDK, que não é nativamente compatível com Compose, exigindo o uso de uma Activity separada com comunicação via `ActivityResultLauncher`.

**Lições aprendidas:**

* A arquitetura MVVM, quando bem implementada, simplifica significativamente a manutenção e a testabilidade do código.
* Jetpack Compose exige uma mudança de mentalidade em relação ao desenvolvimento Android tradicional com XML, mas resulta em código mais conciso e expressivo.
* A gestão de estado reativo com StateFlow é mais poderosa e segura do que abordagens imperativas.

---

### GreetingProcessor

**Dificuldades encontradas:**

* **Compreensão da API de annotation processing** – a API `javax.annotation.processing` é verbosa e pouco intuitiva, especialmente no que diz respeito à navegação entre `TypeElement`, `ExecutableElement` e `PackageElement`.

* **Configuração do projeto multi-módulo com KAPT** – garantir que o módulo `processor` era aplicado como processador KAPT do módulo `app` (e não como dependência de runtime) exigiu configuração cuidada do `build.gradle.kts`.

* **Geração de código com KotlinPoet** – a API do KotlinPoet tem uma curva de aprendizagem considerável, em particular para gerar código com modificadores `override`, parâmetros de construtor, e instruções complexas com `addStatement`.

* **Registo automático com AutoService** – perceber que o `AutoService` gera o ficheiro `META-INF/services` automaticamente (e que sem ele o processador não é descoberto pelo compilador) foi um ponto de confusão inicial.

**Lições aprendidas:**

* O processamento de anotações é uma técnica poderosa para eliminar boilerplate e impor padrões de forma automática, mas tem uma curva de aprendizagem elevada.
* A separação em módulos distintos (annotations, processor, app) é essencial para evitar dependências circulares e manter o processador fora do classpath de runtime.
* KotlinPoet é uma biblioteca elegante que torna a geração de código muito mais segura e legível do que a manipulação direta de strings.

---

## 14. Future Improvements

### CoolWeatherApp

* **Persistência de favoritos** com Room Database ou DataStore, para manter as localizações guardadas entre sessões.
* **Previsão para vários dias** aproveitando os dados horários já devolvidos pela API Open-Meteo.
* **Notificações meteorológicas** com WorkManager para alertar sobre condições adversas em background.
* **Geocodificação por nome** permitindo pesquisar localizações por texto em vez de coordenadas.
* **Testes unitários e de interface** com MockK e Compose UI Testing.
* **Modo offline** com cache dos últimos dados obtidos.

### GreetingProcessor

* **Migração para KSP** (Kotlin Symbol Processing) para tempos de compilação mais rápidos.
* **Suporte a propriedades e classes** como alvos das anotações.
* **Validação de regex em compilação** com mensagens de erro claras via `processingEnv.messager`.
* **Testes automatizados ao processador** com a biblioteca `compile-testing` do Google.
* **Suporte a métodos com parâmetros** no `RegexProcessor`.
* **Nova anotação `@Log`** para geração automática de logging estruturado.

---

## 15. AI Usage Disclosure (Mandatory)

| Ferramenta | Utilização | Áreas |
|---|---|---|
| ChatGPT (GPT-4) | Consulta pontual | Configuração de dependências Gradle; sintaxe da API KotlinPoet; diferenças entre KAPT e KSP |

---

# 👨‍💻 Autor

**Bernardo Rocha – 15033**

Estudante de Engenharia Informática
Unidade Curricular: Computação Móvel
Ano Letivo: 2024-2025

---

# 📄 Licença

Projetos desenvolvidos para **fins académicos**.
