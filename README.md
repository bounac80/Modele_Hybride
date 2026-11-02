# Modèles hybrides : MC + IA

Support d'une présentation sur les **modèles hybrides** (modèle de connaissance + modèle guidé par les données) et tutoriel numérique associé, construit sur le cas test du bioréacteur fed-batch de Psichogios et Ungar (1992).

Présentation donnée au LRGP le 19 novembre 2026.

**Notations utilisées partout :**
- **MC** : modèle de connaissance (physique, bilans, lois connues)
- **IA** : modèle guidé par les données (réseau de neurones)

## Contenu du dépôt

| Fichier | Contenu |
|---|---|
| `presentation/Modeles_hybrides_LRGP_2026.pdf` | Diapositives de la présentation (43 pages) |
| `notebooks/Synthese_modeles_hybrides.ipynb` | Notebook autonome : cas test, IA pure, hybride série, taille du jeu, architectures, risques, conclusion (parties 1 à 7) |
| `notebooks/Partie8_PINN.ipynb` | Notebook autonome : PINN écrit à la main et comparaison avec les autres modèles (partie 8) |
| `requirements.txt` | Dépendances Python |
| `LICENSE` | Licence MIT (code) |

Les notebooks sont fournis **avec leurs sorties** (figures et tableaux) : ils s'affichent directement sur GitHub, sans rien exécuter.

## Le cas test

Un bioréacteur fed-batch dont les **bilans de matière sont connus**, mais dont la **cinétique de croissance μ(S) est supposée inconnue**. La « vraie » cinétique est une loi de Haldane (inhibition par le substrat), avec un maximum de μ ≈ 0,238 h⁻¹ pour S = 1 g/L. Le modèle ne connaît pas cette formule : il doit la retrouver à partir de données bruitées (σ = 0,1 g/L).

Le tutoriel compare, sur le même jeu d'apprentissage et les mêmes tests (interpolation et extrapolation) :

1. un MC seul (Monod : bilans + cinétique approchée),
2. une IA seule (réseau de neurones, sans physique),
3. un hybride série (l'IA fournit μ, injecté dans les bilans du MC),
4. d'autres architectures hybrides (série A, B, B′ et parallèle C),
5. un PINN.

## Les parties du tutoriel

| Partie | Thème | Notebook |
|---|---|---|
| 1 | Le cas test : bilans connus, cinétique de Haldane inconnue | Synthèse |
| 2 | Données pseudo-expérimentales bruitées ; pourquoi l'estimation directe de μ par différences finies est inexploitable | Synthèse |
| 3 | IA pure (boîte noire, sans physique) : bonne erreur à un pas, mauvaise simulation libre, pas de conservation de la matière | Synthèse |
| 4 | Hybride série : un réseau fournit μ, intégré aux bilans analytiques, entraîné par rétropropagation à travers la physique | Synthèse |
| 5 | Effet de la taille du jeu d'apprentissage | Synthèse |
| 6 | Comparaison des architectures hybrides (série A, B, B′ ; parallèle C) | Synthèse |
| 7 | Robustesse : erreurs de structure du MC, domaine non appris, bruit élevé | Synthèse |
| 8 | PINN : un réseau de trajectoire par essai et un réseau cinétique μ(S) partagé | Partie8_PINN |

Les notebooks utilisent les mêmes notations que la présentation : **MC** pour le modèle de connaissance et **IA** pour le modèle guidé par les données (notés FPM et DDM dans Shah et al. 2025). Les architectures y sont nommées *série IA→MC* (l'IA fournit un terme au MC), *série MC→IA* (l'IA corrige la sortie du MC) et *parallèle* (l'IA prédit l'écart).

Tout est écrit en **Python avec NumPy, SciPy et Matplotlib uniquement** : les réseaux de neurones et la rétropropagation sont programmés à la main, et les gradients sont vérifiés par différences finies. Aucune bibliothèque d'apprentissage profond n'est nécessaire. Les notebooks régénèrent eux-mêmes toutes leurs données (aucun fichier externe).

## Installation et exécution

```bash
git clone https://github.com/bounac80/Modele_Hybride.git
cd Modele_Hybride

python -m venv .venv
source .venv/bin/activate        # Windows : .venv\Scripts\activate
pip install -r requirements.txt

jupyter lab
```

Ouvrir ensuite les notebooks du dossier `notebooks/`.

| Notebook | Durée d'exécution indicative |
|---|---|
| `Synthese_modeles_hybrides.ipynb` | environ 5 minutes ; pour une exécution rapide, mettre `N_SESS = 3` dans la première cellule de code |
| `Partie8_PINN.ipynb` | environ 7 minutes (5 initialisations du PINN, environ 1 minute chacune) |

Des versions détaillées de chaque partie du tutoriel existent : contacter l'auteur (voir ci-dessous) si besoin.

Les durées sont mesurées sur 2 cœurs CPU. Les notebooks ont été exécutés de bout en bout, sans erreur, avec Python 3.13, NumPy 2.5.3, SciPy 1.18.1 et Matplotlib 3.11.2. Les résultats sont déterministes (graines fixées).

## Références

**Article de référence de la présentation**
- Shah, P., Pahari, S., Bhavsar, R., Kwon, J. S.-I. (2025). *Hybrid modeling of first-principles and machine learning: A step-by-step tutorial review for practical implementation.* Computers & Chemical Engineering, 194, 108926. https://doi.org/10.1016/j.compchemeng.2024.108926

**Cas test et premiers modèles hybrides**
- Psichogios, D. C., Ungar, L. H. (1992). *A hybrid neural network-first principles approach to process modeling.* AIChE Journal, 38(10), 1499-1511.
- Su, H.-T., Bhat, N., Minderman, P., McAvoy, T. (1993). *Integrating neural networks with first principles models for dynamic modeling.* In: Dynamics and Control of Chemical Reactors, Distillation Columns and Batch Processes, Elsevier, 327-332.
- Bastin, G., Dochain, D. (1990). *On-line Estimation and Adaptive Control of Bioreactors.* Elsevier, Amsterdam (cinétique de Haldane du cas test).
- Thompson, M. L., Kramer, M. A. (1994). *Modeling chemical processes using prior knowledge and neural networks.* AIChE Journal, 40(8), 1328-1340.

**Architectures série et parallèle, domaine de validité**
- Sitapure, N., Kwon, J. S.-I. (2023). *Introducing hybrid modeling with time-series-transformers: A comparative study of series and parallel approach in batch crystallization.* Industrial & Engineering Chemistry Research, 62(49), 21278-21291.
- von Stosch, M., Oliveira, R., Peres, J., de Azevedo, S. F. (2014). *Hybrid semi-parametric modeling in process systems engineering: Past, present and future.* Computers & Chemical Engineering, 60, 86-101.
- Kahrs, O., Marquardt, W. (2007). *The validity domain of hybrid models and its application in process optimization.* Chemical Engineering and Processing: Process Intensification, 46(11), 1054-1066.

**PINN**
- Raissi, M., Perdikaris, P., Karniadakis, G. E. (2019). *Physics-informed neural networks: A deep learning framework for solving forward and inverse problems involving nonlinear partial differential equations.* Journal of Computational Physics, 378, 686-707.

**Cinétiques de croissance**
- Monod, J. (1949). *The growth of bacterial cultures.* Annual Review of Microbiology, 3, 371-394.
- Andrews, J. F. (1968). *A mathematical model for the continuous culture of microorganisms utilizing inhibitory substrates.* Biotechnology and Bioengineering, 10(6), 707-723.
- Haldane, J. B. S. (1930). *Enzymes.* Longmans, Green and Co., Londres.

## Licences

- **Code** (notebooks et scripts) : licence [MIT](LICENSE).
- **Diapositives** (texte et schémas originaux) : [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.fr). Les logos institutionnels (LRGP, UNYS, CNRS, Université de Lorraine), la photographie de couverture (© Vincent Damarin) et les éléments repris de publications tierces restent soumis à leurs droits propres et ne sont pas couverts par cette licence.

## Auteur

**Roda Bounaceur**
Université de Lorraine, CNRS, LRGP, F-54000 Nancy, France
Courriel : roda.bounaceur@univ-lorraine.fr
Plateformes en ligne : https://thermokinetics-lrgp.com
GitHub : [@bounac80](https://github.com/bounac80)
