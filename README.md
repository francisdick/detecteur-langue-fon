# Détecteur de langue : fɔ̀ngbè, français ou anglais ?

Un petit modèle de NLP qui lit une phrase et indique si elle est écrite en **fɔ̀ngbè (fon)**, en **français** ou en **anglais**.

C'est un projet d'apprentissage, construit étape par étape, de la collecte des données jusqu'à une démo en ligne.

> **État du projet :** phases 0 à 2 terminées ✅ · phase 3 (détecteur à base de règles) à venir

---

## Pourquoi ce projet ?

Le fɔ̀ngbè est parlé par plusieurs millions de personnes au Bénin, mais il reste très peu présent dans les outils numériques. Avant de traiter une langue (traduction, analyse de sentiment…), un système doit d'abord savoir **la reconnaître**, y compris dans la vraie vie : messages WhatsApp sans tons, phrases mêlant fon et français, mots isolés.

---

## Les données (`data/`)

| Fichier | Contenu |
|---|---|
| `phrases_propres.csv` | **Jeu utilisé pour l'entraînement** : 900 phrases (300 fon, 300 français, 300 anglais), vérifiées en phase 2 |
| `phrases.csv` | Jeu assemblé en phase 1, avant les vérifications de la phase 2 |
| `pieges.csv` | 22 phrases difficiles (mélanges fon-français, mots isolés, noms propres), réservées à l'évaluation : elles ne servent jamais à l'entraînement |
| `fon_zenodo_300.csv` | 300 phrases en fon relues et validées |
| `tatoeba_fr_300.csv` | 300 phrases en français relues et validées |
| `tatoeba_en_300.csv` | 300 phrases en anglais relues et validées |

Colonnes de `phrases.csv` et `phrases_propres.csv` : `texte`, `langue` (`fon`, `fr`, `en`), `source`.

### Phase 1 : collecte et préparation

1. **Collecte** : phrases tirées au hasard dans deux corpus ouverts (voir [SOURCES.md](SOURCES.md)).
2. **Relecture manuelle**, phrase par phrase :

   | Langue | Phrases relues | Rejetées | Validées |
   |---|---|---|---|
   | Fon | 347 | 47 | 300 |
   | Français | 334 | 34 | 300 |
   | Anglais | 364 | 64 | 300 |

   Motifs de rejet : fautes, phrases incompréhensibles, noms propres dominants, sujets sensibles, mélanges de langues.

   *Relecture du fon par un locuteur natif ; relecture du français et de l'anglais assistée par IA, puis validée manuellement.*
3. **Correction de caractères** : 35 phrases en fon utilisaient des caractères d'apparence identique mais différents (`ε` grec au lieu de `ɛ`, `ð` au lieu de `ɖ`). Elles ont été corrigées, et tout le texte a été normalisé en Unicode NFC.
4. **Versions « WhatsApp »** : 150 des 300 phrases en fon ont été converties **sans tons ni lettres spéciales** (`ɔ → o`, `ɛ → e`, `ɖ → d`), comme on écrit souvent sur un téléphone. Elles sont repérables par la source `zenodo_4432712_simplifie`. Sans elles, le modèle risquerait d'apprendre seulement que « ɔ = fon ».

### Phase 2 : exploration et vérifications

**Contrôles de qualité, tous réussis :**

| Contrôle | Résultat |
|---|---|
| Lignes vides | 0 |
| Doublons (en ignorant les majuscules) | 0 |
| Même phrase avec deux langues différentes | 0 |
| Caractères suspects restants (`ε`, `ð`, `Ð`) | 0 |
| Phrases pièges présentes dans le jeu d'entraînement | 0 |
| Équilibre des classes | 300 / 300 / 300 |

**Biais repérés**, c'est-à-dire des raccourcis que le modèle pourrait apprendre à la place de la langue :

| Biais | fon | français | anglais | Risque |
|---|---|---|---|---|
| Longueur moyenne | 20 caractères | 39 caractères | 37 caractères | « phrase courte = fon » |
| Phrases de moins de 15 caractères | 97 | 6 | 5 | idem |
| Majuscule en début de phrase | 0 % | 100 % | 99 % | « pas de majuscule = fon » |
| Ponctuation finale (`.` `!` `?`) | 0 % | 100 % | 99 % | « pas de ponctuation = fon » |
| Présence de `ɔ`, `ɛ` ou `ɖ` | 130 / 300 | 0 | 0 | « ɔ = fon », atténué par les versions WhatsApp |

Ces biais seront traités dans les phases suivantes :

- **Phase 3** : mesurer le score d'une simple règle « pas de ponctuation finale → fon » pour évaluer l'ampleur du biais.
- **Phase 4** : retirer la ponctuation et passer le texte en minuscules dans un prétraitement intégré au modèle. Le même traitement s'appliquera aux phrases saisies dans la démo.
- **Phase 5** : tester des phrases françaises et anglaises courtes, sans majuscule ni ponctuation, ainsi que des phrases fon longues.

**Point ouvert, non bloquant :** pour le ton montant, 23 phrases en fon utilisent la brève (`ă`, `ĭ`, `ŭ`, `ɔ̆`) et une seule le caron (`ǔ`). La forme correcte reste à confirmer auprès de locuteurs du fon. Les deux formes étant absentes du français et de l'anglais, ce point n'affecte pas la détection de langue. Le prétraitement de la phase 4 les ramènera à une forme unique.

---

## Structure du dépôt

```
detecteur-langue-fon/
├── data/                    # données (voir ci-dessus)
├── 00_bases_python.ipynb    # phase 0 : bases de Python
├── 01_collecte.ipynb        # phase 1 : collecte, relecture, assemblage
├── 02_exploration.ipynb     # phase 2 : exploration, contrôles, repérage des biais
├── SOURCES.md               # sources et licences des données
├── LICENSE                  # licence du code (MIT)
└── README.md
```

---

## Feuille de route

| Phase | Contenu | État |
|---|---|---|
| 0 | Bases de Python | ✅ |
| 1 | Collecte et validation des données | ✅ |
| 2 | Exploration, contrôles de qualité et repérage des biais (pandas) | ✅ |
| 3 | Un premier détecteur avec des règles écrites à la main (score de référence) | ⏳ |
| 4 | Un modèle d'apprentissage automatique (scikit-learn) | ⏳ |
| 5 | Analyse des erreurs, y compris sur les phrases pièges | ⏳ |
| 6 | Démo en ligne (Gradio, Hugging Face Spaces) | ⏳ |
| 7 | Bilan, limites et présentation du projet | ⏳ |

---

## Reproduire les résultats

- `01_collecte.ipynb` documente toute la collecte. Les séances 1 et 2 comprennent une **relecture manuelle** : leur résultat est publié dans `data/` (fichiers `*_300.csv`).
- La séance 3 (assemblage) est reproductible à partir de ces fichiers.
- `02_exploration.ipynb` est entièrement reproductible : il lit `data/phrases.csv` et `data/pieges.csv`, puis produit `data/phrases_propres.csv`.
- Les fichiers bruts de Tatoeba (`*.tsv.bz2`) ne sont pas publiés à cause de leur taille. Ils se téléchargent sur https://tatoeba.org/fr/downloads

Outils : Python, pandas, matplotlib, Google Colab.

---

## Licences

- **Code** (notebooks) : MIT, voir [LICENSE](LICENSE).
- **Données** (dossier `data/`) : [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
  Les phrases proviennent de corpus sous licences CC BY (voir [SOURCES.md](SOURCES.md)). La sélection, la relecture et les corrections sont l'œuvre de Francis Dick ACCROMBESSY. Toute réutilisation doit citer ce dépôt **et** les sources d'origine.

---

## Auteur

**Francis Dick ACCROMBESSY**, développeur Full Stack et technicien support IT, Bohicon (Bénin)
GitHub : [@francisdick](https://github.com/francisdick)
