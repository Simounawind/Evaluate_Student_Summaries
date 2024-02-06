# Evaluation_Student_Summaries
Tâche d'évaluation data basée sur les données de compétition CommonLit - Evaluate Student Summaries de Kaggle



Site competition: https://www.kaggle.com/competitions/commonlit-evaluate-student-summaries



## 1️⃣ Défi

1. Pour un même prompt, comparer deux réponses différentes. En définissant l'une des réponses comme étant une "bonne réponse" (R\*) et analyser les similitudes et les différences entre R et R\*.
2. Évaluation du contenu combiné : en combinant les deux réponses, créer une nouvelle réponse et l'évaluer.



## 2️⃣ Résumé de tâche

**L’objectif** est : pour un prompt et deux réponses données R et R* où R* est une **« bonne réponse »**, est-ce que « R est aussi bonne que R* » ?

> ⬆️ Comparaison

Plus **précisément** : 
1. Ont-elles des points communs ?  `analyse`
2. Des différences ? `analyse`
3. Elles se complètent ? En les combinant, on obtiendrait une réponse encore meilleure ?  `génération+évaluation`

> - Analyser le problème, proposer une solution et l’implémenter.
> - Pas besoin de chercher à faire le meilleur modèle possible (ni de tester des dizaines de modèles)
> - Se Concentrer sur l’analyse du problème, la proposition d’une solution pertinente et la qualité du code Python d’implémentation de la solution.





## 3️⃣ Solution

### 1. EDA

- Utilisation de l'Exploratory data analysis (EDA)

```txt
Statistiques de score de content :
count    7165.000000
mean       -0.014853
std         1.043569
min        -1.729859
25%        -0.799545
50%        -0.093814
75%         0.499660
max         3.900326
```



### 2. Ingénierie des caractéristiques

1) Fusionner deux DataFrames.

2) Utiliser la fonction `feature_engineering` pour ajouter plus de colonnes de caractéristiques au DataFrame fusionné, y compris :

```txt
1. longueur de chaque réponse
2. longueur de chaque prompt
3. ratio de réponse/prompt
4. longueur de vocabulaire (mots uniques)
5. word_overlap
6. bigram_overlap
7. trigram_overlap
8. quotes_num
9. jaccard_similarity
10. semantic_similarity
```

| index | student_id   | prompt_id | text                                              | content  |                                       prompt_text |                                   prompt_question | semantic_similarity | reponse_longueur | prompt_longueur | rep/prompt_ratio | vocabulary_richness | mot_overlap | citations | bigramme_overlap | trigramme_overlap | jaccard_similarité |
| ----- | ------------ | --------- | ------------------------------------------------- | -------- | ------------------------------------------------: | ------------------------------------------------: | ------------------: | ---------------: | --------------: | ---------------: | ------------------: | ----------: | --------: | ---------------: | ----------------: | -----------------: |
| 0     | 000e8c3c7ddb | 814d6b    | the third wave was an experimentto see how peo... | 0.205683 | background \r\nthe third wave experiment took ... | summarize how the third wave developed over su... |            0.658571 |               71 |             673 |         0.105498 |                  49 |          22 |         0 |                7 |                 2 |           0.083893 |

3). Évaluation de la corrélation

- Graphiques de dispersion

  ![image-20240206232614872](../../../Library/Mobile Documents/iCloud~md~obsidian/Documents/学习笔记/总附件/image-20240206232614872.png)

- Matrice de corrélation

![image-20240206232727210](../../../Library/Mobile Documents/iCloud~md~obsidian/Documents/学习笔记/总附件/image-20240206232727210.png)

### 3. Classification de la qualité

Selon la distribution statistique des scores de `content` et la nature réelle des données, les réponses sont classées comme suit :

1. mauvaises réponses : n <= -1
2. réponses moyennes : -1 < n < 1
3. bonnes réponses : 1 <= n <= 3
4. excellentes réponses : n > 3



Sélectionner aléatoirement 2 réponses au même prompt, R et R\*, en stipulant que R* doit être une <u>bonne</u> ou <u>excellente</u> réponse, conformément aux exigences de la tâche.

L'analyse de ces deux réponses se base sur les caractéristiques obtenues.

En outre, on peut aussi obtenir la similarité entre deux réponses en construisant un vecteur avec les caractéristiques susmentionnées



### 4. Génération de nouveau texte

Considérer cette question : *"La combinaison des réponses R et R\* serait-elle une meilleure réponse ?"*

Pour obtenir R et R*, une intervention humaine ou l'aide de l'apprentissage en profondeur est nécessaire pour combiner et produire une **nouvelle réponse**.

Pour cela, nous nous connectons à l'API `openai` et spécifions le modèle `gpt3.5 turbo-0301` pour la génération de texte.



### 5. Évaluation du nouveau texte

Deux manières d'évaluer le nouveau texte :

1. Construire des caractéristiques pour ce texte et le comparer aux bonnes réponses.
2. Construire un modèle prédictif pour prédire et évaluer les scores.



### 6. Modèle pour la régression

- XGbooster
- TF-IDF
