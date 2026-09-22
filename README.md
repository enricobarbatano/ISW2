# ISW2 - Bug Prediction, Software Testing e Refactoring su Apache Storm

## Descrizione

Questo repository raccoglie gli artefatti prodotti per il progetto di **Ingegneria del Software 2 (ISW2)**, svolto sul progetto open source **Apache Storm**.

Il lavoro studia la relazione tra **code smell** e **difettosita del software** attraverso quattro attivita principali:

1. costruzione di un dataset evolutivo a livello classe-release;
2. addestramento e valutazione di modelli di Machine Learning per la bug prediction;
3. what-if analysis sull'impatto teorico della rimozione dei code smell;
4. software testing e refactoring automatizzato mediante Large Language Models, con particolare riferimento a Microsoft Copilot.

La versione corrente del repository include anche:

- il **dataset finale etichettato** utilizzato per l'addestramento e la valutazione;
- il **modello predittivo definito con WEKA**;
- la relazione completa del progetto;
- il report dedicato al software testing;
- i riferimenti al fork di Apache Storm utilizzato durante la sperimentazione.

## Repository collegato ad Apache Storm

Le attivita sperimentali di testing e refactoring sono state eseguite su un fork di Apache Storm:

- **Fork:** <https://github.com/enricobarbatano/storm.git>
- **Branch sperimentale:** `milestone4-refactoring`

Apache Storm e distribuito con licenza Apache License 2.0. Il codice sorgente originale e la relativa licenza rimangono quelli del progetto Apache Storm.

## Contenuto del repository

La struttura puo variare in base al nome assegnato al file del modello WEKA. Una possibile organizzazione e la seguente:

```text
.
├── README.md
├── LICENSE
├── Enrico_Barbatano_Code_Smell_Difettosita_Apache_Storm.pdf
├── sezione_testing_ISW2.pdf
├── dataset_labeled.csv
├── model/
│   └── bug_prediction_random_forest.model
└── classi_selezionate.txt
```

> Se il modello WEKA ha un nome differente, aggiornare il percorso mostrato nell'albero e nella sezione dedicata.

## Artefatti principali

### `Enrico_Barbatano_Code_Smell_Difettosita_Apache_Storm.pdf`

Relazione principale del progetto. Descrive la costruzione del dataset, il labeling dei difetti, il confronto tra classificatori, la what-if analysis e la valutazione del refactoring automatizzato.

### `sezione_testing_ISW2.pdf`

Report dedicato al software testing e alla quality assurance. Include:

- Category Partition;
- Boundary Value Analysis;
- Control-Flow Testing;
- coverage con JaCoCo;
- Mutation Testing con PIT;
- generazione automatica con Randoop;
- sperimentazione con EvoSuite;
- generazione di test tramite LLM;
- validazione delle varianti rifattorizzate;
- integrazione CI/CT tramite GitHub Actions.

### `dataset_labeled.csv`

Dataset evolutivo finale. Ogni riga rappresenta una coppia **classe-release** di Apache Storm ed e associata a metriche software e all'etichetta target `buggy`.

Le colonne del dataset sono:

```text
project
release
class
loc
churn
loc_added
fan_out
revisions
max_churn
max_loc_added
avg_churn
avg_loc_added
fan_in_total
loc_touched_total
revisions_density
ns
age
weighted_age
nauth
nfix
ndev
bug_density
fix
code_smells
buggy
```

L'etichetta `buggy` assume i valori:

- `YES`: la classe e considerata difettosa nella release;
- `NO`: la classe non e considerata difettosa nella release.

I percorsi delle classi presenti nel CSV utilizzano il separatore `\` derivato dall'ambiente Windows. Su Linux, se tali percorsi vengono impiegati direttamente per accedere al filesystem, potrebbe essere necessario convertirli nel separatore `/` oppure gestirli tramite API portabili come `Path` in Java.

### Modello WEKA

Il repository include il modello di bug prediction addestrato tramite **WEKA**. Il classificatore selezionato e:

```text
RandomForest
Feature Selection: no
Bilanciamento: SMOTE
```

La configurazione e stata scelta come miglior compromesso tra capacita discriminativa e riconoscimento della classe minoritaria. Le prestazioni ottenute nella valutazione sono:

| Metrica | Valore |
|---|---:|
| Recall | 0,577 |
| Precision | 0,806 |
| AUC | 0,958 |
| Kappa | 0,649 |

Il file del modello consente di riutilizzare il classificatore senza ripetere l'intero addestramento, a condizione che le nuove istanze rispettino lo stesso schema, lo stesso ordine degli attributi e lo stesso preprocessing utilizzato durante la sperimentazione.

## Obiettivi

Gli obiettivi principali del progetto sono:

- costruire un dataset evolutivo a livello classe-release per Apache Storm;
- associare a ogni istanza una label `buggy` mediante Jira, SZZ e Proportion Total;
- raccogliere metriche strutturali, storiche, evolutive e di debito tecnico;
- analizzare la relazione tra code smell e difettosita;
- confrontare differenti classificatori di Machine Learning;
- gestire lo sbilanciamento della classe target tramite SMOTE;
- selezionare e salvare un modello predittivo tramite WEKA;
- stimare con una what-if analysis l'impatto teorico dell'azzeramento dei code smell;
- progettare suite di test manuali e automatiche;
- valutare l'efficacia del refactoring generato da Microsoft Copilot;
- verificare compilabilita, stabilita, coverage e mutation score delle varianti rifattorizzate.

## Metodologia

Il progetto e organizzato in quattro milestone.

### Milestone 1 - Dataset Creation

E stato costruito un dataset evolutivo nel quale ogni istanza rappresenta una coppia classe-release.

Le principali attivita sono state:

- selezione delle release di Apache Storm;
- estrazione dei ticket Bug da Jira;
- collegamento tra ticket e commit Git;
- identificazione delle versioni di introduzione e correzione dei difetti;
- applicazione di SZZ per individuare i bug-introducing changes;
- utilizzo di Proportion Total nei casi in cui SZZ o Jira non fornivano informazioni sufficienti;
- estrazione delle classi Java, incluse le classi generated presenti nelle release;
- calcolo delle metriche per ogni coppia classe-release;
- conteggio dei code smell tramite analisi statica;
- assegnazione della label `buggy`.

Per mitigare lo snoring, sono state considerate le prime 12 release valide sulle 36 censite.

### Milestone 2 - Bug Prediction con WEKA

Sono stati confrontati tre classificatori:

- RandomForest;
- NaiveBayes;
- IBk.

La sperimentazione ha considerato:

- configurazioni con e senza Feature Selection;
- configurazioni con e senza SMOTE;
- normalizzazione delle feature numeriche;
- rimozione degli attributi identificativi prima dell'addestramento;
- 10-times 10-fold cross-validation.

La Feature Selection e stata eseguita con `CfsSubsetEval` e `BestFirst`. Il sottoinsieme ottenuto comprendeva:

```text
loc
loc_added
fan_out
revisions_density
age
bug_density
code_smells
```

Il miglior compromesso complessivo e risultato **RandomForest senza Feature Selection e con SMOTE**. Il modello finale e stato esportato da WEKA e aggiunto al repository.

### Milestone 3 - What-if Analysis

La what-if analysis ha stimato la variazione delle predizioni in uno scenario controfattuale privo di code smell.

Sono stati costruiti quattro dataset:

- **Dataset A:** dataset originale completo;
- **Dataset B+:** istanze con almeno un code smell;
- **Dataset B:** copia di B+ con `code_smells = 0`;
- **Dataset C:** istanze naturalmente prive di smell.

Con il classificatore principale, le predizioni buggy sono passate da 547 in B+ a 525 in B, con una riduzione netta di 22 predizioni, pari al 4,02% delle predizioni positive iniziali in B+.

Questo risultato rappresenta la risposta del modello a uno scenario artificiale. Non dimostra che 22 difetti reali sarebbero stati certamente evitati.

### Milestone 4 - Software Testing e Refactoring Automatizzato

La fase finale ha valutato Microsoft Copilot su due classi con caratteristiche opposte:

- `RedisKeyValueStateIterator.java`, classe compatta e focalizzata;
- `StatsUtil.java`, God Class utility molto estesa e complessa.

Per ogni classe sono state prodotte quattro varianti mediante prompting incrementale:

- **C1:** sorgente originale e report dei code smell;
- **C2:** C1 piu test black-box progettati tramite Category Partition;
- **C3:** C2 piu test Control-Flow e risultati JaCoCo;
- **C4:** C3 piu risultati PIT e test progettati sui mutanti sopravvissuti.

Tutte le varianti finali compilano e superano la suite standard disponibile. Per `RedisKeyValueStateIterator` l'unico smell iniziale e stato eliminato. Per `StatsUtil`, i code smell sono passati da 265 a 19 in C1, 45 in C2, 19 in C3 e 19 in C4.

## Software Testing

La strategia di testing e stata sviluppata in modo incrementale:

1. progettazione black-box mediante Category Partition;
2. applicazione della Boundary Value Analysis;
3. Adequacy Growth mediante Control-Flow Testing;
4. rafforzamento degli oracoli tramite PIT Mutation Testing;
5. generazione automatica con Randoop;
6. tentativo di integrazione di EvoSuite;
7. generazione di suite JUnit 4 tramite LLM;
8. validazione continua con GitHub Actions.

Per `RedisKeyValueStateIterator`, la suite manuale completa ha raggiunto il 100% di Instruction Coverage e il 100% di Mutation Coverage. Per `StatsUtil`, la complessita della classe e degli input basati su mappe annidate e oggetti Thrift ha limitato i risultati degli strumenti automatici.

Sulle varianti di `StatsUtil`, il risultato migliore della suite LLM e stato ottenuto su C2:

| Metrica | C2 con test LLM |
|---|---:|
| Instruction Coverage | 78% |
| Branch Coverage | 64% |
| Mutation Score | 51% |

Randoop ha ottenuto il proprio risultato migliore su C1, con il 17% di Instruction Coverage, l'11% di Branch Coverage e il 6% di Mutation Score. EvoSuite non e stato incluso nel confronto finale perche i test generati non erano integrabili nell'ambiente Java 11/17 utilizzato da Apache Storm 2.7.1.

## Risultati principali

- 12 release analizzate su 36 censite;
- 9.864 coppie classe-release;
- 713 istanze buggy, pari al 7,23%;
- 9.151 istanze non buggy, pari al 92,77%;
- 1.132 ticket collegati correttamente ai commit Git su 1.193 ticket validi;
- 597 classi buggy con almeno un code smell;
- miglior modello: RandomForest senza Feature Selection e con SMOTE;
- AUC del modello: 0,958;
- Kappa: 0,649;
- riduzione controfattuale netta: 22 predizioni buggy;
- eliminazione dell'unico smell di `RedisKeyValueStateIterator`;
- riduzione degli smell di `StatsUtil` da 265 a un intervallo compreso tra 19 e 45;
- compilazione e superamento della suite standard per tutte le varianti finali.

## Utilizzo del dataset e del modello

### Apertura del dataset in WEKA

1. Avviare WEKA Explorer.
2. Aprire la sezione **Preprocess**.
3. Caricare `dataset_labeled.csv`.
4. Verificare che `buggy` sia riconosciuto come attributo nominale.
5. Impostare `buggy` come classe target.
6. Rimuovere gli attributi identificativi `project`, `release` e `class` prima dell'addestramento o della predizione.

### Caricamento del modello

In WEKA Explorer:

1. aprire la sezione **Classify**;
2. selezionare **More options**;
3. utilizzare **Load model**;
4. scegliere il file `.model` presente nel repository;
5. caricare un dataset compatibile con gli attributi attesi dal modello.

### Requisiti di compatibilita

Per utilizzare correttamente il modello, il dataset di input deve rispettare:

- gli stessi nomi degli attributi;
- lo stesso ordine degli attributi;
- lo stesso tipo numerico o nominale;
- la stessa classe target;
- lo stesso preprocessing;
- l'assenza degli attributi identificativi esclusi durante il training.

## Strumenti utilizzati

- Java
- Maven
- Apache Storm
- Jira
- Git e GitHub
- SonarCloud
- WEKA
- SMOTE
- JaCoCo
- PIT Mutation Testing
- Randoop
- EvoSuite
- Microsoft Copilot
- GitHub Actions

## Continuous Integration e Continuous Testing

Nel fork di Apache Storm e stata configurata una pipeline GitHub Actions per automatizzare build e test.

A ogni push sul branch `milestone4-refactoring`, il workflow esegue i test dei moduli coinvolti, con particolare attenzione a:

- `storm-server`;
- `storm-redis`.

La pipeline e stata utilizzata per verificare progressivamente le suite manuali, i test generati automaticamente e le varianti rifattorizzate.

## Limiti dello studio

I risultati devono essere interpretati considerando alcune minacce alla validita:

- la cross-validation non preserva l'ordine temporale delle release;
- SZZ dipende dalla corretta identificazione dei fix commit;
- Proportion Total introduce una stima nei ticket incompleti;
- la what-if analysis azzera solo `code_smells`, lasciando invariate le altre metriche;
- la valutazione del refactoring riguarda soltanto due classi;
- la generazione tramite LLM non e deterministica;
- EvoSuite non e risultato integrabile nell'ambiente di progetto utilizzato.

## Licenza

Questo repository contiene documentazione e artefatti accademici prodotti dall'autore.

Apache Storm e distribuito con licenza Apache License 2.0. La licenza del presente repository non modifica la licenza del progetto Apache Storm o del fork collegato.

## Finalita

Il repository e stato creato con finalita:

- accademiche;
- documentali;
- sperimentali;
- di portfolio personale.

## Autore

**Enrico Barbatano**
