# Détecteur de langue : fɔ̀ngbè, français ou anglais ?

Un petit modèle de NLP qui lit une phrase et indique si elle est écrite en **fɔ̀ngbè (fon)**, en **français** ou en **anglais**.

C'est un projet d'apprentissage, construit étape par étape, de la collecte des données jusqu'à une démo en ligne. Il est mené en parallèle du projet communautaire **FonNLP** de Bénin IA Community (BAIC).

> **État du projet :** phase 1 (collecte des données) terminée ✅ · phase 2 (exploration) à venir

---

## Pourquoi ce projet ?

Le fɔ̀ngbè est parlé par plusieurs millions de personnes au Bénin, mais il reste très peu présent dans les outils numériques. Avant de traiter une langue (traduction, analyse de sentiment…), un système doit d'abord savoir **la reconnaître**, y compris dans la vraie vie : messages WhatsApp sans tons, phrases mêlant fon et français, mots isolés.

---

## Les données (`data/`)

| Fichier | Contenu |
|---|---|
| `phrases.csv` | **Jeu final** : 900 phrases (300 fon, 300 français, 300 anglais) |
| `pieges.csv` | 22 phrases difficiles (mélanges fon-français, mots isolés, noms propres), réservées à l'évaluation |
| `fon_zenodo_300.csv` | 300 phrases en fon relues et validées |
| `tatoeba_fr_300.csv` | 300 phrases en français relues et validées |
| `tatoeba_en_300.csv` | 300 phrases en anglais relues et validées |

Colonnes de `phrases.csv` : `texte`, `langue` (`fon`, `fr`, `en`), `source`.

### Comment les données ont été préparées

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
4. **Versions « WhatsApp »** : 150 des 300 phrases en fon ont été converties **sans tons ni lettres spéciales** (`ɔ → o`, `ɛ → e`, `ɖ → d`), comme on écrit souvent sur un téléphone. Sans cela, le modèle risquerait d'apprendre seulement que « ɔ = fon ».

---

## Structure du dépôt

```
detecteur-langue-fon/
├── data/                   # données (voir ci-dessus)
├── 00_bases_python.ipynb   # phase 0 : bases de Python
├── 01_collecte.ipynb       # phase 1 : collecte, relecture, assemblage
├── SOURCES.md              # sources et licences des données
├── LICENSE                 # licence du code (MIT)
└── README.md
```

---

## Feuille de route

| Phase | Contenu | État |
|---|---|---|
| 0 | Bases de Python | ✅ |
| 1 | Collecte et validation des données | ✅ |
| 2 | Exploration et nettoyage avec pandas | ⏳ |
| 3 | Un premier détecteur avec des règles écrites à la main | ⏳ |
| 4 | Un modèle d'apprentissage automatique (scikit-learn) | ⏳ |
| 5 | Évaluation, y compris sur les phrases pièges | ⏳ |
| 6 | Démo en ligne (Gradio, Hugging Face Spaces) | ⏳ |

---

## Reproduire les résultats

- `01_collecte.ipynb` documente toute la collecte. Les séances 1 et 2 comprennent une **relecture manuelle** : leur résultat est publié dans `data/` (fichiers `*_300.csv`).
- La séance 3 (assemblage) est reproductible à partir de ces fichiers.
- Les fichiers bruts de Tatoeba (`*.tsv.bz2`) ne sont pas publiés à cause de leur taille. Ils se téléchargent sur https://tatoeba.org/fr/downloads

Outils : Python, pandas, Google Colab.

---

## Licences

- **Code** (notebooks) : MIT — voir [LICENSE](LICENSE).
- **Données** (dossier `data/`) : [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
  Les phrases proviennent de corpus sous licences CC BY (voir [SOURCES.md](SOURCES.md)) ; la sélection, la relecture et les corrections sont l'œuvre de Francis Dick ACCROMBESSY. Toute réutilisation doit citer ce dépôt **et** les sources d'origine.

---

## Auteur

**Francis Dick ACCROMBESSY** — Développeur Full Stack et technicien support IT, Bohicon (Bénin)
GitHub : [@francisdick](https://github.com/francisdick)
