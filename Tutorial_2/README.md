# 📱 Relatório de Projetos – Computação Móvel

**Course:** Computação Móvel  
**Student(s):** Bernardo Rocha – 15033  
**Date:** 2024/2025  
**Repository URL:** https://github.com/username/computacao-movel

---

## 📂 Estrutura do Repositório

```
.
├── Tuto2_kotlin       ← Exercícios avançados de Kotlin puro
├── CoolWeatherAPP     ← App Android de meteorologia em tempo real
└── AI                 ← App Android de galeria de imagens com favoritos
```

---
---

# 1️⃣ Tuto2 – Exercícios de Programação em Kotlin

---

## 1. Introduction

O projeto **Tuto2** foi desenvolvido no âmbito da unidade curricular de Computação Móvel com o objetivo de explorar e consolidar conceitos avançados da linguagem **Kotlin**. O problema central consistia em implementar, de raiz, quatro exercícios independentes que cobrem funcionalidades modernas do Kotlin frequentemente utilizadas no desenvolvimento Android profissional.

**Objetivos:**
- Compreender e aplicar Sealed Classes com pattern matching
- Implementar estruturas de dados genéricas e type-safe
- Construir pipelines de transformação de dados com Higher-Order Functions
- Utilizar sobrecarga de operadores para criar APIs expressivas e naturais

---

## 2. System Overview

O projeto é composto por quatro exercícios independentes, cada um focado num tema específico:

| Exercício | Tema | Descrição resumida |
|---|---|---|
| 1.1 | Event Log Processing | Sistema de eventos com Sealed Classes |
| 1.2 | Generic Type-Safe Cache | Cache genérica com HashMap |
| 1.3 | Configurable Data Pipeline | Pipeline de transformação com lambdas |
| 1.4 | 2D Vector Library | Biblioteca de vetores com operator overloading |

Todos os exercícios correm na **JVM** (sem dependências Android) e são executáveis diretamente no **IntelliJ IDEA** sem qualquer servidor ou serviço externo.

---

## 3. Architecture and Design

Sendo exercícios de linguagem pura, não foi aplicado um padrão arquitetural formal. No entanto, cada exercício segue princípios de design claros:

**Estrutura de pastas:**
```
Tuto2_kotlin
└── src/main/kotlin
    ├── cm/exe_1/exe_1.1.kt   ← Event Log Processing
    ├── cm/exe_2/exe_1.2.kt   ← Generic Type-Safe Cache
    ├── cm/exe_3/exe_1.3.kt   ← Configurable Data Pipeline
    └── cm/exe_4/exe_1.4.kt   ← 2D Vector Library
```

**Decisões de design:**
- **Sealed Classes** no exercício 1.1 para garantir exaustividade nas expressões `when` em tempo de compilação
- **Generics com upper bounds** (`K : Any`) no exercício 1.2 para garantir null-safety
- **Builder pattern com DSL** no exercício 1.3 para uma API declarativa e legível
- **Operator overloading + extension functions** no exercício 1.4 para uma sintaxe matemática natural

---

## 4. Implementation

### Exercício 1.1 – Event Log Processing

Sistema de processamento de eventos com **Sealed Classes**. Três tipos de eventos (`Login`, `Purchase`, `Logout`) são processados polimorficamente. Funções de extensão `filterByUser` e `totalSpent` operam diretamente sobre listas de eventos.

```kotlin
sealed class Event {
    data class Login(val username: String, val timestamp: Long) : Event()
    data class Purchase(val username: String, val amount: Double, val timestamp: Long) : Event()
    data class Logout(val username: String, val timestamp: Long) : Event()
}

fun processEvents(events: List<Event>, handler: (Event) -> Unit) {
    for (event in events) { handler(event) }
}
```

### Exercício 1.2 – Generic Type-Safe Cache

Classe `Cache<K, V>` genérica baseada em `HashMap` com operações de `put`, `get`, `evict`, `size`, `snapshot`, `getOrPut`, `transform` e `filterValues`.

```kotlin
class Cache<K : Any, V : Any> {
    private val store: MutableMap<K, V> = HashMap()

    fun getOrPut(key: K, default: () -> V): V { ... }
    fun transform(key: K, action: (V) -> V): Boolean { ... }
    fun filterValues(predicate: (V) -> Boolean): Map<K, V> { ... }
}
```

### Exercício 1.3 – Configurable Data Pipeline

Classe `Pipeline` com um builder DSL que encadeia estágios de transformação sequencialmente. Suporta `compose` (fusão de dois estágios) e `fork` (execução paralela).

```kotlin
val pipeline = buildPipeline {
    addStage("Trim")          { lines -> lines.map { it.trim() } }
    addStage("Filter errors") { lines -> lines.filter { "ERROR" in it } }
    addStage("Uppercase")     { lines -> lines.map { it.uppercase() } }
    addStage("Add index")     { lines -> lines.mapIndexed { i, l -> "${i+1}. $l" } }
}
```

### Exercício 1.4 – 2D Vector Library

`data class Vec2` com operadores `+`, `-`, `*`, `-` unário e `[]`. Implementa `Comparable<Vec2>` por magnitude. Extensão de `Double` para suportar `2.0 * v`.

```kotlin
data class Vec2(val x: Double, val y: Double) : Comparable<Vec2> {
    operator fun plus(other: Vec2)     = Vec2(x + other.x, y + other.y)
    operator fun times(scalar: Double) = Vec2(x * scalar, y * scalar)
    fun magnitude()  = sqrt(x * x + y * y)
    fun normalized() = Vec2(x / magnitude(), y / magnitude())
}

// Extensão: permite 2.0 * v
operator fun Double.times(v: Vec2) = Vec2(this * v.x, this * v.y)
```

---

## 5. Testing and Validation

Cada exercício inclui uma função `main` com casos de teste manuais:

| Exercício | Cenários testados |
|---|---|
| 1.1 | Filtro por utilizador, cálculo de total gasto, processamento de lista mista |
| 1.2 | Inserção, leitura, evição, `getOrPut` com chave existente e nova, `filterValues` |
| 1.3 | Pipeline completo com 4 estágios, `compose` de dois estágios, `fork` paralelo |
| 1.4 | Soma/subtração de vetores, multiplicação escalar, magnitude, normalização, ordenação |

**Limitações conhecidas:**
- Não existem testes unitários automatizados (JUnit/MockK)
- A Cache não é thread-safe (sem sincronização concorrente)
- O `fork` do Pipeline não executa verdadeiramente em paralelo

---

## 6. Usage Instructions

**Requisitos:** JDK 17+, IntelliJ IDEA, Gradle

```bash
# 1. Clonar o repositório
git clone https://github.com/username/repo.git

# 2. Abrir no IntelliJ IDEA
# File → Open → selecionar a pasta Tuto2_kotlin

# 3. Executar cada exercício individualmente
# Navegar até ao ficheiro desejado e clicar em Run 'MainKt'
```

---

## 7. Prompting Strategy

A IA foi consultada pontualmente em momentos específicos:

- **Builder DSL:** *"Como implementar um builder DSL em Kotlin com um bloco lambda receiver?"* → obteve-se um exemplo base adaptado para o Pipeline
- **Generics:** *"Porque é que `K : Any` é necessário num HashMap genérico em Kotlin?"* → esclareceu a relação com null-safety
- **Operator overloading:** *"Como extender o operador `*` no tipo Double para aceitar um Vec2?"* → confirmou a sintaxe da extension function com `operator`

Os prompts foram sempre direcionados a **conceitos específicos**, nunca à geração direta de soluções completas.

---

## 8. Autonomous Agent Workflow

A IA não foi utilizada de forma autónoma ou agêntica neste projeto. O uso foi exclusivamente consultivo — semelhante a uma pesquisa avançada — para esclarecer dúvidas pontuais sobre sintaxe e semântica do Kotlin. Todo o código foi escrito, estruturado e testado manualmente pelo estudante.

---

## 9. Verification of AI-Generated Artifacts

Os excertos consultados foram sempre:
1. **Analisados e compreendidos** antes de qualquer utilização
2. **Adaptados** ao contexto específico de cada exercício
3. **Testados manualmente** através das funções `main` de cada ficheiro
4. **Revistos** para garantir conformidade com os requisitos do enunciado

Nenhum bloco de código foi copiado diretamente sem compreensão e modificação.

---

## 10. Human vs AI Contribution

| Componente | Humano | IA |
|---|---|---|
| Design dos exercícios | ✅ | ❌ |
| Implementação das Sealed Classes | ✅ | ❌ |
| Estrutura da Cache genérica | ✅ | ❌ |
| Builder DSL do Pipeline | ✅ | Consulta de sintaxe |
| Operator overloading Vec2 | ✅ | Confirmação de sintaxe |
| Casos de teste | ✅ | ❌ |

---

## 11. Ethical and Responsible Use

O uso de IA foi transparente, limitado e responsável. As ferramentas foram utilizadas como apoio ao estudo e não como substituto do raciocínio próprio. Todos os conceitos utilizados foram compreendidos pelo estudante. Não foram gerados conteúdos enganosos nem foram omitidas contribuições da IA.

---

## 12. Version Control and Commit History

O projeto foi versionado com **Git**, com commits regulares ao longo do desenvolvimento:
- Commits iniciais com a estrutura base de cada exercício
- Commits intermédios à medida que cada funcionalidade era implementada
- Commits finais com casos de teste e ajustes de código

O histórico reflete trabalho contínuo e incremental, não apenas commits finais.

---

## 13. Difficulties and Lessons Learned

- **Generics com null-safety:** A restrição `K : Any` foi inicialmente confusa mas revelou-se essencial para evitar chaves `null` na cache
- **Builder DSL:** A sintaxe de lambda receivers (`block: Pipeline.() -> Unit`) exigiu pesquisa adicional mas o resultado final é muito mais expressivo
- **Comparable via magnitude:** Compreender que `compareTo` deve ser consistente com `equals` levou a repensar a implementação da `data class`

**Insight principal:** O Kotlin oferece ferramentas poderosas para criar APIs expressivas que parecem extensões naturais da própria linguagem.

---

## 14. Future Improvements

- Adicionar testes unitários automatizados com **JUnit 5** e **MockK**
- Implementar **TTL (Time To Live)** nas entradas da Cache
- Adicionar thread-safety à Cache com **ConcurrentHashMap**
- Suporte a execução assíncrona dos estágios do Pipeline com **Coroutines**
- Expandir `Vec2` para **Vec3** com produto vetorial e projeção

---

## 15. AI Usage Disclosure (Mandatory)

| Ferramenta | Como foi utilizada |
|---|---|
| **Claude (Anthropic)** | Consultas sobre sintaxe Kotlin: generics, DSL, operator overloading |
| **ChatGPT (OpenAI)** | Esclarecimento conceptual sobre Sealed Classes e Comparable |

---
---

# 2️⃣ CoolWeatherApp – Aplicação Android de Meteorologia

---

## 1. Introduction

O **CoolWeatherApp** foi desenvolvido com o objetivo de criar uma aplicação Android funcional que apresente dados meteorológicos em tempo real. O problema central era integrar uma API pública de meteorologia num contexto Android, gerir permissões do sistema operativo e apresentar a informação de forma adaptativa consoante as condições climáticas e o período do dia.

**Objetivos:**
- Consumir a API REST Open-Meteo para obter dados meteorológicos em tempo real
- Implementar o padrão arquitetural MVVM com LiveData
- Gerir permissões de localização GPS no Android
- Criar interfaces adaptativas para diferentes orientações e tamanhos de ecrã

---

## 2. System Overview

O CoolWeatherApp é uma aplicação Android single-activity que:

- Deteta automaticamente a localização do utilizador via **GPS**
- Consome a **API Open-Meteo** (gratuita, sem chave) para obter temperatura, vento, pressão e condição meteorológica
- Apresenta um **ícone dinâmico** e um **fundo adaptativo** consoante a condição WMO e o período do dia (dia/noite)
- Suporta **tablets** e diferentes orientações com layouts alternativos

**Casos de uso principais:**
- Utilizador abre a app → GPS deteta localização → dados carregam automaticamente
- Utilizador insere coordenadas manualmente → atualiza os dados com o botão
- App adapta o fundo visual conforme dia/noite e portrait/landscape

---

## 3. Architecture and Design

A aplicação segue o padrão **MVVM (Model-View-ViewModel)**:

```
CoolWeatherAPP
└── app/src/main/java/dam_A15033/coolweatherapp
    ├── MainActivity.kt       ← View: UI, permissões, observação de LiveData
    ├── WeatherViewModel.kt   ← ViewModel: lógica de negócio, chamadas à API
    └── WeatherData.kt        ← Model: estruturas de dados, enum WMO_WeatherCode
```

**Justificação das decisões de design:**
- **MVVM com LiveData** – Separa a lógica de negócio da UI, permitindo que a Activity sobreviva a rotações de ecrã sem perder dados
- **Thread manual para a API** – A chamada HTTP é feita numa Thread separada para não bloquear a UI thread
- **Enum WMO_WeatherCode** – Mapeia todos os códigos meteorológicos WMO para nomes de drawables, centralizando a lógica de apresentação visual num único local

---

## 4. Implementation

**WeatherData.kt** – Modelos de resposta da API e enum WMO:

```kotlin
enum class WMO_WeatherCode(val code: Int, val imageName: String) {
    CLEAR_SKY(0, "clear_sky"),
    MAINLY_CLEAR(1, "mainly_clear"),
    PARTLY_CLOUDY(2, "partly_cloudy"),
    // ... +25 condições meteorológicas
}
```

**WeatherViewModel.kt** – Construção do URL e exposição de dados via LiveData:

```kotlin
val reqString = buildString {
    append("https://api.open-meteo.com/v1/forecast?")
    append("latitude=${lat}&longitude=${long}&")
    append("current_weather=true&")
    append("hourly=temperature_2m,weathercode,pressure_msl,windspeed_10m&")
    append("timezone=auto")
}
```

**MainActivity.kt** – Cálculo do período do dia e adaptação do fundo:

```kotlin
private fun calculateDay(time: String): Boolean {
    val hour = time.split("T")[1].substring(0, 2).toInt()
    return hour in 6..18
}
```

A interface apresenta: campos de latitude/longitude (auto-preenchidos via GPS), temperatura, velocidade e direção do vento, pressão atmosférica, ícone e fundo dinâmico.

---

## 5. Testing and Validation

| Cenário | Resultado |
|---|---|
| GPS disponível → coordenadas preenchidas automaticamente | ✅ |
| GPS indisponível → fallback para Lisboa (38.76, -9.12) | ✅ |
| Período diurno (6h–18h) → fundo de dia | ✅ |
| Período noturno → fundo de noite | ✅ |
| Rotação do ecrã → dados preservados via ViewModel | ✅ |
| Tablet (sw600dp) → layout alternativo carregado | ✅ |
| Mais de 25 condições WMO diferentes testadas | ✅ |

**Limitações conhecidas:**
- Sem feedback visual ao utilizador quando a API falha (sem rede)
- A Thread manual não tem mecanismo de cancelamento
- Não existe cache local (cada abertura faz uma nova chamada à API)

---

## 6. Usage Instructions

**Requisitos:** Android Studio, Android SDK 24+, dispositivo/emulador com acesso à internet

```bash
# 1. Clonar o repositório
git clone https://github.com/username/repo.git

# 2. Abrir no Android Studio
# File → Open → selecionar a pasta CoolWeatherAPP

# 3. Sincronizar o Gradle e executar
# Run → Run 'app'
```

> ⚠️ Aceitar a permissão de localização quando solicitada para ativar o GPS automático.

---

## 7. Prompting Strategy

A IA foi consultada em momentos específicos:

- **Permissões GPS:** *"Como pedir permissão de localização em runtime no Android com ActivityResultLauncher?"* → forneceu o padrão correto para API 23+
- **LiveData com Thread:** *"Como atualizar LiveData a partir de uma Thread secundária?"* → esclareceu o uso de `postValue` vs `setValue`
- **Layouts adaptativos:** *"Como criar um layout alternativo para tablets no Android?"* → confirmou a convenção de pasta `res/layout-sw600dp/`

---

## 8. Autonomous Agent Workflow

A IA não foi utilizada de forma autónoma. Contribuiu para o planeamento ao ajudar a clarificar a arquitetura MVVM e o fluxo de dados (API → ViewModel → LiveData → UI). O desenvolvimento, a integração e os testes foram inteiramente realizados pelo estudante.

---

## 9. Verification of AI-Generated Artifacts

Todos os padrões sugeridos foram:
1. Testados no emulador Android antes de integração
2. Adaptados ao modelo de dados específico da Open-Meteo
3. Verificados em rotação de ecrã
4. Validados para diferentes versões de Android (API 24+)

---

## 10. Human vs AI Contribution

| Componente | Humano | IA |
|---|---|---|
| Design da arquitetura MVVM | ✅ | ❌ |
| Integração com Open-Meteo API | ✅ | ❌ |
| Enum WMO_WeatherCode completo | ✅ | ❌ |
| Lógica dia/noite | ✅ | ❌ |
| Layouts portrait/landscape/tablet | ✅ | ❌ |
| Permissões GPS em runtime | ✅ | Consulta de padrão |
| Uso de `postValue` no LiveData | ✅ | Esclarecimento |

---

## 11. Ethical and Responsible Use

A API Open-Meteo é gratuita e open-source, utilizada de acordo com os seus termos de serviço. Não são recolhidos dados dos utilizadores além das coordenadas usadas para a consulta meteorológica, que não são persistidas nem partilhadas.

---

## 12. Version Control and Commit History

O projeto foi desenvolvido com commits incrementais que refletem trabalho contínuo:
- Setup inicial e dependências Gradle
- Implementação dos modelos de dados (`WeatherData.kt`)
- Implementação do ViewModel e chamada à API
- Integração do GPS e permissões em runtime
- Layouts adaptativos (landscape, tablet sw600dp)
- Ajustes visuais e correção de bugs

---

## 13. Difficulties and Lessons Learned

- **`setValue` vs `postValue`:** Atualizar LiveData numa Thread secundária exige `postValue`; usar `setValue` causava crash — erro identificado e corrigido rapidamente
- **Layouts adaptativos:** A convenção de pastas do Android (`sw600dp`, `land`) exigiu pesquisa inicial mas revelou-se um sistema elegante e poderoso
- **Códigos WMO:** Mapear todos os 30+ códigos para drawables foi trabalhoso mas necessário para uma experiência visual completa e coerente

---

## 14. Future Improvements

- Migrar a Thread manual para **Kotlin Coroutines** com `viewModelScope`
- Adicionar **previsão a 7 dias** usando os dados `hourly` já disponíveis na API
- Integrar **geocoding** (Open-Meteo) para pesquisa por nome de cidade
- Implementar **notificações** de condições adversas via WorkManager
- Adicionar **widget** para o ecrã inicial do Android
- Cache local com **Room Database** para uso offline

---

## 15. AI Usage Disclosure (Mandatory)

| Ferramenta | Como foi utilizada |
|---|---|
| **Claude (Anthropic)** | Esclarecimento sobre `postValue` vs `setValue`; padrão de permissões GPS em runtime |
| **ChatGPT (OpenAI)** | Consulta sobre layouts adaptativos (`sw600dp`) no Android |

---
---

# 3️⃣ CoolImageFeed – Galeria de Imagens com Favoritos

---

## 1. Introduction

O **CoolImageFeed** é o projeto mais completo da unidade curricular, desenvolvido com o objetivo de criar uma aplicação Android moderna que integre múltiplas tecnologias em simultâneo. O problema central consistia em construir uma galeria de imagens com paginação infinita, persistência local, sistema de favoritos e cache inteligente, seguindo as boas práticas de arquitetura Android contemporânea.

**Objetivos:**
- Consumir a API Picsum Photos com Retrofit e Moshi
- Implementar persistência local com Room Database e cache automática
- Usar Kotlin Coroutines e StateFlow para programação reativa
- Implementar paginação infinita e sistema de favoritos persistente

---

## 2. System Overview

O CoolImageFeed é uma aplicação Android com duas Activities que permite:

- Navegar por um **feed infinito de imagens** carregadas da API Picsum Photos
- **Marcar imagens como favoritas** (limite de 5, persistido no Room)
- Consultar favoritos num **Bottom Sheet deslizante**
- Ver **detalhes de cada imagem** (autor, dimensões, URL original)
- Usar a app **offline** graças à cache local em Room (até 50 imagens)
- **Atualizar** o feed com gesto de pull-to-refresh

**Fluxo de dados:**
```
API (Picsum) → Retrofit → Repository → Room DB → StateFlow → ViewModel → UI
```

---

## 3. Architecture and Design

A aplicação segue rigorosamente o padrão **MVVM com Repository Pattern**:

```
CoolImageFeed
└── app/src/main/java/com/example/coolimagefeed
    ├── MainActivity.kt                      ← View principal
    ├── ImageDetailsActivity.kt              ← View de detalhe
    ├── api/
    │   ├── PicsumApi.kt                     ← Interface Retrofit
    │   └── NetworkModule.kt                 ← Configuração OkHttp/Retrofit
    ├── db/
    │   ├── AppDatabase.kt                   ← Base de dados Room
    │   ├── ImageDao.kt                      ← Queries DAO
    │   └── ImageEntity.kt                   ← Entidade Room
    ├── models/
    │   └── ImageItem.kt                     ← Modelo da API
    ├── repository/
    │   └── ImageRepository.kt               ← Camada de dados
    ├── viewmodel/
    │   └── MainViewModel.kt                 ← ViewModel com StateFlow
    └── ui/
        ├── ImageAdapter.kt                  ← Adapter principal com DiffUtil
        ├── FavoritesAdapter.kt              ← Adapter dos favoritos
        └── FavoritesBottomSheetFragment.kt  ← Bottom sheet de favoritos
```

**Decisões de design:**
- **Repository Pattern** – Abstrai a origem dos dados (API vs Room), permitindo mudar a fonte sem afetar o ViewModel
- **StateFlow em vez de LiveData** – Mais adequado para Coroutines; suporta operadores como `stateIn` e integra melhor com o ciclo de vida
- **Room com auto-manutenção de cache** – As queries SQL gerem automaticamente os limites (50 imagens / 5 favoritos), sem lógica extra no ViewModel
- **ViewBinding** – Elimina `findViewById` e garante null-safety nas referências de UI

---

## 4. Implementation

**Interface Retrofit:**
```kotlin
interface PicsumApi {
    @GET("v2/list")
    suspend fun getImages(
        @Query("page") page: Int,
        @Query("limit") limit: Int
    ): List<ImageItem>
}
```

**Room com gestão automática de cache:**
```kotlin
@Query("SELECT * FROM images WHERE isFavorite = 0 ORDER BY timestamp DESC LIMIT 50")
fun getCachedImages(): Flow<List<ImageEntity>>

@Query("DELETE FROM images WHERE isFavorite = 0 AND id NOT IN " +
       "(SELECT id FROM images WHERE isFavorite = 0 ORDER BY timestamp DESC LIMIT 50)")
suspend fun maintainCacheLimit()
```

**ViewModel com StateFlow e paginação:**
```kotlin
val cachedImages: StateFlow<List<ImageEntity>> = repository.cachedImages
    .stateIn(viewModelScope, SharingStarted.Lazily, emptyList())

fun loadNextPage() {
    currentPage++
    loadImages()
}

fun toggleFavorite(image: ImageEntity) {
    viewModelScope.launch { repository.toggleFavorite(image) }
}
```

A **paginação infinita** é implementada com `RecyclerView.OnScrollListener` que deteta quando o utilizador se aproxima do fim da lista e chama `loadNextPage()` automaticamente.

---

## 5. Testing and Validation

| Cenário | Resultado |
|---|---|
| Feed carrega imagens da API na abertura | ✅ |
| Scroll até ao fim → nova página carrega automaticamente | ✅ |
| Marcar favorito → persiste após fechar e reabrir a app | ✅ |
| Limite de 5 favoritos respeitado | ✅ |
| Cache de 50 imagens mantida automaticamente pelo DAO | ✅ |
| Modo offline → imagens da cache apresentadas | ✅ |
| Pull-to-refresh → feed reinicia na página 1 | ✅ |
| Bottom Sheet abre com lista de favoritos atualizada | ✅ |
| Tap numa imagem → abre ImageDetailsActivity com dados corretos | ✅ |

**Limitações conhecidas:**
- Sem feedback visual de erro quando a API falha sem cache disponível
- O limite de 5 favoritos é fixo (não configurável pelo utilizador)
- Não é possível fazer download das imagens para a galeria do dispositivo

---

## 6. Usage Instructions

**Requisitos:** Android Studio Hedgehog+, Android SDK 26+, internet (1ª utilização)

```bash
# 1. Clonar o repositório
git clone https://github.com/username/repo.git

# 2. Abrir no Android Studio
# File → Open → selecionar a pasta AI

# 3. Sincronizar Gradle e executar
# Run → Run 'app'
```

**Interações principais:**
- 📜 Scroll para baixo → carrega mais imagens automaticamente
- ⭐ Botão estrela → adicionar/remover favorito (máx. 5)
- 📋 Botão favoritos → abre Bottom Sheet com favoritos guardados
- 🔍 Toque numa imagem → abre detalhes completos (autor, dimensões, URL)
- 🔄 Pull-to-refresh → reinicia o feed desde a página 1

---

## 7. Prompting Strategy

A IA foi utilizada de forma estruturada em fases específicas do desenvolvimento:

- **Planeamento:** *"Qual a melhor forma de implementar paginação infinita com RecyclerView em Android com MVVM?"* → orientou para `OnScrollListener` com lógica no ViewModel
- **Implementação:** *"Como converter um Flow do Room num StateFlow no ViewModel?"* → forneceu o padrão `stateIn` com `SharingStarted.Lazily`
- **Debug:** *"Porque é que o RecyclerView não atualiza quando o StateFlow emite novos valores?"* → identificou a necessidade de usar `DiffUtil` no Adapter

Os prompts evoluíram de genéricos para específicos à medida que o projeto avançava.

---

## 8. Autonomous Agent Workflow

A IA contribuiu de forma consultiva nas fases de planeamento e debug. O fluxo típico foi:

1. **Estudante** define o requisito (ex: paginação infinita)
2. **IA** sugere abordagem (ex: `OnScrollListener` + threshold de scroll)
3. **Estudante** implementa, testa e ajusta ao contexto
4. **IA** apoia no debug se surgem problemas inesperados

A IA não gerou código completo de forma autónoma em nenhuma fase do projeto.

---

## 9. Verification of AI-Generated Artifacts

Todos os padrões sugeridos pela IA foram verificados através de:
- **Testes manuais** em emulador (Pixel 6, API 34)
- **Verificação de comportamento offline** (modo avião ativado)
- **Testes de stress** (scroll rápido, marcação consecutiva de favoritos)
- **Confirmação de persistência** (fechar e reabrir a app)
- **Análise do Logcat** para confirmar ausência de crashes e erros

---

## 10. Human vs AI Contribution

| Componente | Humano | IA |
|---|---|---|
| Arquitetura MVVM + Repository | ✅ | ❌ |
| Configuração Retrofit + Moshi | ✅ | ❌ |
| Esquema Room + queries DAO | ✅ | ❌ |
| Lógica de cache automática (SQL) | ✅ | ❌ |
| Sistema de favoritos | ✅ | ❌ |
| Paginação infinita | ✅ | Sugestão de abordagem |
| StateFlow com `stateIn` | ✅ | Esclarecimento de sintaxe |
| DiffUtil no Adapter | ✅ | Identificação do problema |
| Bottom Sheet de favoritos | ✅ | ❌ |
| ImageDetailsActivity | ✅ | ❌ |

---

## 11. Ethical and Responsible Use

A API Picsum Photos é gratuita e pública, utilizada dentro dos seus termos de uso. As imagens são de domínio público. A aplicação não recolhe dados pessoais dos utilizadores. O limite de favoritos e de cache foi implementado conscientemente para evitar uso excessivo de armazenamento no dispositivo.

---

## 12. Version Control and Commit History

O projeto foi desenvolvido com commits regulares e incrementais que refletem trabalho contínuo:
- Setup do projeto e dependências Gradle (Retrofit, Room, Glide, Coroutines)
- Implementação dos modelos de dados e interface Retrofit
- Configuração do Room Database, DAO e entidades
- Implementação do Repository e ViewModel com StateFlow
- UI principal com RecyclerView, DiffUtil e paginação
- Sistema de favoritos e Bottom Sheet
- ImageDetailsActivity e navegação entre ecrãs
- Pull-to-refresh e ajustes finais de UX

---

## 13. Difficulties and Lessons Learned

- **StateFlow vs LiveData:** A transição para StateFlow com Coroutines exigiu compreender `stateIn`, `SharingStarted` e o ciclo de vida do `viewModelScope` — mais complexo inicialmente mas muito mais poderoso e expressivo
- **DiffUtil no RecyclerView:** Sem DiffUtil a lista piscava a cada atualização; a implementação resolveu o problema e melhorou significativamente a fluidez da UI
- **Room com Flow:** Perceber que o Room com `Flow` emite automaticamente quando os dados mudam (sem polling) foi um dos maiores insights do projeto
- **Cache via SQL:** Implementar a manutenção da cache diretamente nas queries DAO resultou num código mais limpo e eficiente do que fazê-lo no ViewModel

---

## 14. Future Improvements

- Migrar para **Jetpack Paging 3** para paginação mais robusta com estados nativos de loading/error
- Implementar **pesquisa e filtros** por autor ou dimensões
- Adicionar **download de imagens** para a galeria do dispositivo
- Integrar o **Android Share Sheet** para partilha de imagens
- Suporte a **Dark Mode** com Material You / Dynamic Colors
- Adicionar **testes instrumentados** com Espresso e Room in-memory database
- Tornar o **limite de favoritos configurável** nas definições da app

---

## 15. AI Usage Disclosure (Mandatory)

| Ferramenta | Como foi utilizada |
|---|---|
| **Claude (Anthropic)** | Sugestão de abordagem para paginação infinita; esclarecimento de `stateIn` com StateFlow |
| **ChatGPT (OpenAI)** | Identificação do problema de atualização do RecyclerView sem DiffUtil |

---
---

# 📚 Conhecimentos Adquiridos

Ao longo do desenvolvimento destes projetos foram adquiridos conhecimentos em:

* **Kotlin avançado** – Sealed Classes, Generics, Operator Overloading, Coroutines, Flow, StateFlow
* **Arquitetura Android** – MVVM, ViewModel, LiveData, StateFlow, Repository Pattern
* **Persistência de dados** – Room Database, DAO, Entities, gestão automática de cache
* **Comunicação com APIs** – Retrofit, Moshi, Gson, REST APIs públicas
* **UI Android** – RecyclerView, DiffUtil, ViewBinding, BottomSheetDialogFragment, layouts adaptativos
* **Boas práticas** – Separação de camadas, código limpo, reutilização de componentes, commits incrementais

---

# 🏁 Conclusão Geral

O desenvolvimento destes três projetos ao longo da unidade curricular de **Computação Móvel** permitiu uma progressão clara e gradual no domínio do ecossistema Kotlin/Android.

O **Tuto2** estabeleceu uma base sólida na linguagem Kotlin, explorando conceitos como Sealed Classes, Generics e Operator Overloading que são fundamentais para escrever código idiomático e expressivo. Estes padrões revelaram-se diretamente aplicáveis nos projetos Android seguintes.

O **CoolWeatherApp** introduziu o desenvolvimento Android real, com consumo de APIs externas, gestão de permissões do sistema operativo e a implementação do padrão MVVM com LiveData. A necessidade de suportar múltiplas orientações e tamanhos de ecrã demonstrou a importância de um design de UI cuidado e adaptativo.

O **CoolImageFeed** representou o projeto mais completo e exigente, integrando a totalidade dos conceitos abordados: arquitetura MVVM com StateFlow e Coroutines, persistência local com Room, comunicação REST com Retrofit, paginação infinita e gestão de estado de UI. A combinação de todos estes elementos num produto coeso evidenciou a maturidade adquirida ao longo da unidade curricular.

Em suma, os projetos evoluíram de exercícios de linguagem para aplicações Android funcionais com arquitetura moderna, demonstrando a capacidade de aplicar boas práticas de engenharia de software num contexto móvel real.

---

## 👨‍💻 Autor

**Bernardo Rocha – 15033**

Estudante de Engenharia Informática  
Unidade Curricular: Computação Móvel

---

## 📄 Licença

Projetos desenvolvidos para **fins académicos**.
