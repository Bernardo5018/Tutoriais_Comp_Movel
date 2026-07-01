# 📱 Relatório de Projetos – Computação Móvel

**Course:** Computação Móvel  
**Student(s):** Bernardo Rocha – 15033  
**Date:** 2025/2026  
**Repository URL:** https://github.com/Bernardo5018/Tutoriais_Comp_Movel

---
## 1. Introduction to the work
O Find My Trail foi concebido como uma solução tecnológica premium destinada a ciclistas de
BTT, caminhantes e entusiastas de desportos ao ar livre. O projeto nasceu da necessidade de
unificar a descoberta de rotas, o registo de atividade física e a segurança num único
ecossistema focado na experiência do utilizador.
O principal diferencial da aplicação prende-se com uma navegação visualmente apelativa e
imersiva. A exploração de trilhos é feita através de um mapa interativo nativo (Google Maps),
suportado por um carrossel de fotografias de alta resolução, permitindo uma descoberta
orgânica das rotas.
Adicionalmente, a aplicação foi desenvolvida sob um modelo de negócio Freemium. A versão
base é gratuita e oferece todas as funcionalidades de registo e gestão de perfil. No entanto,
para garantir a monetização e a viabilidade do projeto (perspetivando uma base de 10 milhões
de utilizadores), introduziu-se uma funcionalidade PRO ("Heatmap") por 1€ mensal. Esta
funcionalidade permite aos subscritores visualizarem as zonas de maior afluência e os "trilhos
secretos" mais percorridos pela comunidade, aliando um modelo de negócio sustentável a
uma ferramenta altamente desejada pelo público-alvo.
## 2. The entire application development process
O desenvolvimento da aplicação seguiu uma metodologia iterativa e estritamente centrada no
utilizador, dividida nas quatro grandes fases estipuladas no guião da disciplina:
- Concept Phase (Fase de Conceito): O projeto iniciou-se com sessões
de brainstorming para definir a proposta de valor. Elaborou-se o Application Concept
Document (ACD), onde se estabeleceu o público-alvo, os requisitos técnicos e o
modelo de negócio base. O conceito do "Mapa de Calor" pago foi imediatamente
delineado como a Key Feature para atrair capital.
- Pre-Production Phase (Fase de Pré-Produção): Esta fase serviu para estruturar a
engenharia do projeto. Adotou-se o padrão de arquitetura MVVM (Model-View-
ViewModel) com linguagem Kotlin. Definiu-se o modelo de dados em ambiente Cloud
(Firebase Firestore) e realizaram-se os primeiros testes de usabilidade em
papel/wireframes com 4 utilizadores reais, cujos comentários foram vitais para
simplificar a navegação inicial.
- Production Phase (Fase de Produção): Entrou-se na codificação pesada.
Os layouts XML começaram a ganhar vida com recurso aos componentes de Material
Design. Estabeleceu-se a ligação oficial ao Google Firebase (para a Autenticação segura
por Email/Password e armazenamento remoto de dados) e à Google Maps API.
Procedeu-se a uma segunda fase de avaliação de usabilidade ("Production
Evaluation"), onde se concluiu que os utilizadores preferiam menus inferiores para
facilitar o uso do telemóvel com uma só mão.

- Post-Production Phase (Fase de Pós-Produção e Mercado): A aplicação recebeu o
seu polimento final ("Pre-market version"). Implementou-se a Bottom Navigation View,
adicionaram-se funcionalidades de Inteligência Artificial (um "AI Coach" para dar dicas
automáticas baseadas nas métricas), e integrou-se um sistema multi-idioma
(Português, Inglês e Espanhol) operável em tempo real. Uma avaliação final a 6 novos
utilizadores validou que o produto está totalmente pronto para o mercado.

## 3. Initial wire-frame diagrams
 AS IMAGENS ESTÃO NO PDF

## 4. Initial mock-ups
 AS IMAGENS ESTÃO NO PDF










## 5. Final screenshots
 AS IMAGENS ESTÃO NO PDF











## 6. Final full entity-association diagram of database
 AS IMAGENS ESTÃO NO PDF



















## 7. Final full data-base schema (no data)
A aplicação utiliza o Firebase Firestore, uma base de dados NoSQL orientada a documentos,
garantindo sincronização em tempo real e modo offline. O esquema estrutural final é o
seguinte:
- Collection trails (Rotas Globais Partilhadas por todos)
o id (String)
o title (String)
o description (String)
o imageUrl (String)
o distanceKm (Number / Double)
o elevationGain (Number / Long)
o difficulty (String: "EASY", "MODERATE", "HARD")
- Collection users (Dados Privados de cada Utilizador)
o Document {uid} (Chave Primária, gerada pelo Firebase Auth)
▪ name (String)
▪ email (String)
▪ isPro (Boolean)
▪ avatar (String)
▪ updatedAt (Timestamp)
▪ Sub-collection favorites (Trilhos que o utilizador marcou com a estrela)
▪ Document {trailId} (Replicação dos metadados do trilho para
acesso rápido)
▪ savedAt (Timestamp)
▪ Sub-collection trips (Histórico de Voltas/Treinos do utilizador)
▪ Document {tripId}
▪ id (String)
▪ title (String)
▪ distanceKm (Number / Double)
▪ durationMinutes (Number / Long)
▪ dateMillis (Timestamp)



## 8. Final full UML class diagram
 AS IMAGENS ESTÃO NO PDF


## 9. Results obtained
Os resultados obtidos no final do ciclo de desenvolvimento foram excecionais. A
aplicação Find My Trail demonstrou um desempenho bom, cumprindo os rigorosos requisitos
iniciais propostos para o semestre. No campo técnico, a adoção de tecnologias assíncronas
(Kotlin Coroutines e Flow) garantiu que a Interface (UI) nunca sofresse bloqueios durante a
comunicação com a rede. O sistema de autenticação Cloud provou ser impenetrável e rápido,
enquanto a gestão de carregamento assíncrono de fotografias (via biblioteca Glide) manteve o
uso da memória RAM estável mesmo em scrolls infinitos. Do ponto de vista do mercado, as três
rondas de testes de usabilidade evidenciaram que o público-alvo (Mountain Bikers) se
apaixonou pelo conceito visual fluído e revelou intenção de aderir à subscrição PRO de 1€,
validando formalmente o modelo de negócio proposto.

## 10. Discussion of the most important issues
Durante as fases de Produção e Pós-Produção, a equipa deparou-se com alguns desafios
técnicos arquiteturais que exigiram estudo aprofundado:
- Gestão de Memória com Imagens Pesadas (OOM - Out of Memory): Inicialmente,
carregar dezenas de fotografias 4K do Unsplash diretamente nas listas (RecyclerViews)
causou lentidão e quebras de memória. Solução: Integração da biblioteca Glide para
efetuar redimensionamento automático (centerCrop()), caching em disco e reciclagem
de bitmaps.
- Navegação e Usabilidade em Ecrãs Grandes: Os primeiros protótipos forçavam o
utilizador a navegar por menus localizados no topo do ecrã, o que foi criticado na
Avaliação de Usabilidade por ser pouco ergonómico. Solução: A navegação foi
totalmente reconstruída, adotando um BottomNavigationView standard do Material
Design, facilitando o acesso instantâneo a todos os módulos da app com apenas o
polegar.

- Sincronização de Estado Assíncrona: Gerir quando os dados locais (Preferências de
Dark Mode ou Idioma, via DataStore) divergiam dos dados remotos do servidor
Firebase. Solução: Implementou-se um fluxo unificado (Repository Pattern) onde a
Base de Dados atua como "Single Source of Truth", e a interface reage apenas à
subscrição de alterações (Flow).

## 11. Conclusions
O desenvolvimento do "Find My Trail" foi uma jornada incrivelmente rica que consolidou na
prática todos os conhecimentos de Computação Móvel e Engenharia de Software abordados
em aula. A aplicação não é apenas um "trabalho académico"; evoluiu para um produto polido,
com "Look & Feel" profissional, totalmente Market-Ready. Cumpre as mais recentes diretrizes
do Google Material Design, integra as APIs de ponta (Firebase, Google Maps e Inteligência
Artificial) e prova que, mais importante do que programar linhas de código, é fundamental ouvir
o feedback dos utilizadores e pivotar a navegação quando necessário. A validação do modelo
de negócio pelos próprios testadores deixa em aberto um forte potencial para publicação real
da aplicação na Google Play Store num futuro muito próximo.
