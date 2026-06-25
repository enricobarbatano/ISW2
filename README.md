# ISW2 – Bug Prediction e Refactoring su Apache Storm

## Descrizione

Questo repository raccoglie la documentazione prodotta per il progetto di **Ingegneria del Software 2 (ISW2)**, svolto sul progetto open source **Apache Storm**.

Il lavoro analizza la relazione tra **code smell** e **difettosità del software**, utilizzando Apache Storm come sistema oggetto di studio. Il progetto include inoltre una sezione dedicata al **software testing** e al **refactoring automatizzato tramite Large Language Models**, con particolare riferimento a Microsoft Copilot.

Questo repository ha finalità documentale: non contiene l'intero codice sorgente di Apache Storm, ma raccoglie la relazione, la sezione di testing e l'elenco delle classi selezionate per l'analisi e il refactoring.

---

## Collegamento con Apache Storm

Il progetto è associato a un fork di Apache Storm utilizzato per le attività sperimentali, in particolare per la milestone dedicata al refactoring e al testing.

Fork di riferimento:

```text
https://github.com/enricobarbatano/storm.git
```

Branch utilizzato per lo studio:

```text
milesto4-refactoring
```

Apache Storm è un progetto open source distribuito con licenza **Apache License 2.0**. Questo repository contiene solo documentazione prodotta a fini accademici; il codice sorgente originale e la relativa licenza restano quelli del progetto Apache Storm.

---

## Contenuto del repository

Il repository contiene:

```text
.
├── README.md
├── LICENSE
├── Bug Prediction.docx
├── sezione swtesting_ISW2.pdf
└── classe.txt.txt
```

### File principali

- `Bug Prediction.docx`  
  Relazione principale del progetto ISW2, dedicata alla costruzione del dataset, alla defect prediction, alla what-if analysis e al refactoring automatizzato.

- `sezione swtesting_ISW2.pdf`  
  Documento relativo alla parte di software testing, con progettazione delle suite di test, analisi di coverage, mutation testing, generazione automatica dei test e validazione delle varianti rifattorizzate.

- `classe.txt.txt`  
  File contenente le classi di Apache Storm selezionate per la fase di testing e refactoring.

---

## Classi analizzate

Le classi selezionate per la fase di testing e refactoring sono:

```text
external/storm-redis/src/main/java/org/apache/storm/redis/state/RedisKeyValueStateIterator.java
storm-server/src/main/java/org/apache/storm/stats/StatsUtil.java
```

Le due classi rappresentano scenari opposti:

- **RedisKeyValueStateIterator.java**  
  Classe di dimensioni ridotte, usata per valutare il comportamento degli strumenti di testing e refactoring su un caso localizzato.

- **StatsUtil.java**  
  Classe utility molto estesa e complessa, utilizzata per valutare i limiti degli strumenti automatici e degli LLM su codice legacy con elevato debito tecnico.

---

## Obiettivi del progetto

Gli obiettivi principali del lavoro sono:

- costruire un dataset evolutivo a livello classe-release per Apache Storm;
- etichettare le classi come buggy o non buggy tramite SZZ e Proportion Total;
- analizzare la relazione tra code smell e difettosità;
- confrontare diversi classificatori di Machine Learning per la defect prediction;
- stimare, tramite what-if analysis, l'impatto teorico della rimozione dei code smell;
- valutare l'efficacia del refactoring automatizzato tramite Microsoft Copilot;
- progettare e validare suite di test manuali e automatiche;
- confrontare refactoring localizzati e refactoring su classi complesse.

---

## Metodologia

Il progetto è stato strutturato in quattro milestone principali.

### Milestone 1 – Dataset Creation

In questa fase è stato costruito un dataset evolutivo del progetto Apache Storm.  
Per ogni coppia classe-release sono state raccolte metriche software relative a dimensione, complessità, churn, storia dei difetti, autori, revisioni e code smell.

Il labeling delle classi è stato effettuato utilizzando:

- dati provenienti da Jira;
- algoritmo SZZ per individuare i bug-introducing changes;
- tecnica Proportion Total come fallback per stimare la versione di introduzione del difetto.

### Milestone 2 – Classifiers

In questa fase sono stati confrontati diversi classificatori per predire la difettosità delle classi:

- RandomForest;
- NaiveBayes;
- IBk.

Sono state valutate configurazioni con e senza feature selection, e con gestione dello sbilanciamento tramite SMOTE.  
Il miglior modello individuato è stato **RandomForest senza feature selection e con balancing**, utilizzato poi come classificatore di riferimento nelle analisi successive.

### Milestone 3 – What-if Analysis

La what-if analysis è stata utilizzata per stimare quante classi potenzialmente buggy sarebbero state evitate in uno scenario controfattuale privo di code smell.

Sono stati costruiti quattro dataset:

- **Dataset A:** dataset originale completo;
- **Dataset B+:** classi con almeno un code smell;
- **Dataset B:** scenario controfattuale con code smell impostati a zero;
- **Dataset C:** classi già prive di smell.

L'analisi ha stimato una riduzione contenuta ma misurabile della difettosità predetta in assenza di code smell.

### Milestone 4 – Automated Refactoring

La fase finale ha valutato l'efficacia di Microsoft Copilot nel refactoring automatico di classi reali di Apache Storm.

Per ogni classe sono state generate quattro varianti tramite prompting incrementale:

- **C1:** codice originale e report SonarCloud;
- **C2:** C1 più test black-box basati su Category Partition;
- **C3:** C2 più test di control-flow;
- **C4:** C3 più risultati di mutation testing.

Il risultato ha mostrato un comportamento molto diverso tra la classe piccola e la classe grande: il refactoring è risultato efficace e stabile su RedisKeyValueStateIterator, mentre su StatsUtil le varianti prodotte non sono risultate compilabili.

---

## Software Testing

La parte di software testing ha incluso:

- progettazione manuale dei test tramite **Category Partition**;
- analisi dell'adeguatezza tramite **JaCoCo**;
- crescita della suite tramite criteri di **control-flow**;
- valutazione tramite **Mutation Testing** con PIT;
- generazione automatica dei test con **Randoop**;
- tentativo di generazione tramite **EvoSuite**;
- generazione di test tramite **LLM**;
- validazione delle varianti rifattorizzate.

Per la classe RedisKeyValueStateIterator, le suite LLM hanno raggiunto risultati molto elevati, fino al 100% di instruction coverage e mutation score. Per StatsUtil, invece, la complessità della classe ha limitato l'efficacia degli strumenti automatici.

---

## Risultati principali

I risultati principali del progetto sono:

- dataset finale composto da **9.864 istanze**;
- circa **7,2%** di istanze buggy;
- circa **65,2%** di istanze con almeno un code smell;
- prevalenza di classi buggy tra le classi smelly;
- miglior classificatore: **RandomForest senza feature selection con SMOTE**;
- AUC del miglior modello pari a circa **0.958**;
- riduzione stimata tramite what-if analysis pari a **22 classi buggy potenzialmente prevenute**;
- refactoring completamente riuscito sulla classe RedisKeyValueStateIterator;
- riduzione statica degli smell su StatsUtil, ma fallimento della compilazione delle varianti rifattorizzate.

---

## Strumenti utilizzati

- Java
- Maven
- Apache Storm
- Jira
- Git/GitHub
- SonarCloud
- WEKA
- JaCoCo
- PIT Mutation Testing
- Randoop
- EvoSuite
- Microsoft Copilot
- GitHub Actions

---

## Continuous Integration

Nel fork di Apache Storm è stata configurata una pipeline di **GitHub Actions** per automatizzare build e test.  
La pipeline esegue i test rilevanti sui moduli coinvolti nello studio, con particolare attenzione a `storm-server` e `storm-redis`.

---

## Note sulla licenza

Questo repository contiene documentazione accademica prodotta dall'autore.

Il progetto Apache Storm originale è distribuito con licenza **Apache License 2.0**.  
La licenza presente in questo repository si applica esclusivamente alla documentazione contenuta qui e non modifica in alcun modo la licenza del progetto Apache Storm o del relativo fork.

---

## Finalità

Il repository è stato creato con finalità:

- accademiche;
- documentali;
- sperimentali;
- di portfolio personale.

---

## Autore

**Enrico Barbatano**
