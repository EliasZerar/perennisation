# VR-AI
## Un atelier pour développer son esprit critique face aux images générées par IA

VR-AI est un projet pédagogique conçu par 10 étudiants en BUT Métiers du Multimédia et de l'Internet à l'IUT de Marne-la-Vallée.
L'objectif était de créer un atelier interactif autour des coulisses du numérique, l'animer devant un vrai public, puis mettre toute la démarche en ligne pour que n'importe qui puisse le reproduire.

## Stack technique

Site statique en HTML / CSS / JavaScript vanilla, sans framework ni étape de build. Le contenu de chaque page n'est pas écrit en dur dans le HTML : il est stocké dans des fichiers JSON et injecté au chargement par `scripts/content.js`.

## Lancer le projet en local

Aucune installation n'est nécessaire, mais le site doit être servi par un serveur local (et non ouvert directement en double-cliquant sur le fichier, sinon les `fetch()` vers les JSON sont bloqués par le navigateur).

- Avec l'extension VS Code **Live Server**, ou le serveur intégré de WebStorm/PhpStorm.
- Ou en ligne de commande, depuis la racine du projet :
  ```bash
  python3 -m http.server 8000
  ```
  puis ouvrir `http://localhost:8000/index.html`.

⚠️ Si le site est déployé dans un sous-dossier (par ex. `https://utilisateur.github.io/nom-du-repo/`), ne jamais écrire de chemins relatifs en dur (`../assets/...`) dans le JS ou le HTML généré dynamiquement : utiliser systématiquement `window.resolveSitePath('assets/...')`, qui recalcule le bon chemin selon l'endroit où le site est réellement hébergé.

## Structure du projet

```
.
├── index.html, a-propos.html, ...   # une page HTML "coquille" par page du site
├── data/
│   ├── nav.json                     # structure du menu de navigation
│   ├── search-data.json             # index utilisé par la recherche
│   └── pages/
│       └── <pageId>.json            # contenu de chaque page (un fichier = une page)
├── scripts/
│   ├── content.js                   # charge nav.json + data/pages/<pageId>.json et génère le HTML
│   ├── search.js                    # recherche dans search-data.json
│   ├── theme-toggle.js              # mode clair / sombre
│   ├── os-shortcuts.js              # adaptation des raccourcis selon l'OS détecté
│   ├── copy.js                      # bouton "copier" des copy-box
│   └── ellipses.js                  # menus "..." (actions contextuelles)
├── styles/
│   ├── variables.css                # couleurs, espacements (--spacing-*), rayons
│   ├── layout.css                   # structure générale, sidebar, .content, breakpoints
│   ├── table.css, header.css, ...   # un fichier par composant
│   └── styles.css                   # point d'entrée qui importe les autres fichiers
└── assets/
    ├── svg/                         # icônes
    ├── img/                         # images des pages
    └── download/                    # fichiers proposés au téléchargement (kit téléchargeable)
```

## Comment fonctionne une page

Chaque page HTML (ex. `objectifs.html`) est une coquille quasi vide avec un `<body data-page="objectifs">`. Au chargement, `content.js` :

1. lit l'attribut `data-page` pour savoir quel JSON charger ;
2. va chercher `data/nav.json` (menu) et `data/pages/objectifs.json` (contenu) ;
3. transforme chaque bloc du JSON en HTML grâce à un `renderer` dédié à son `type`.

Types de blocs disponibles dans `data/pages/*.json` :

| type | usage |
|---|---|
| `h1`, `h2`, `h3` | titres |
| `p` | paragraphe |
| `ul`, `ol` | listes |
| `hr` | séparateur |
| `alertInfo`, `alertError` | encarts d'alerte |
| `verbatim` | citation |
| `copyBox` | bloc de texte avec bouton "copier" |
| `table` | tableau (colonnes "Visualiser"/"Télécharger" générées automatiquement si la cellule contient `{ "path": "..." }`) |
| `card` | carte avec icône, titre, texte, lien |
| `flexBox` | deux colonnes de blocs côte à côte |
| `introImg`, `img_content` | bloc titre+image / image avec légende |
| `youtube` | vidéo intégrée, avec transcription dépliable optionnelle |
| `steps` | liste d'étapes numérotées |

Pour ajouter une page : créer `nouvelle-page.html` (copier une page existante en changeant `data-page`), créer `data/pages/nouvelle-page.json` avec un tableau de blocs, puis ajouter une entrée dans `data/nav.json`.

## Conventions de style

- Toutes les dimensions sont en `rem` (base 16px), définies via les variables `--spacing-*` de `styles/variables.css`.
- Les breakpoints desktop sont gérés en `min-width` (1440px, 1920px) dans `styles/layout.css`, en plus du breakpoint mobile `max-width: 768px`.
- Pour cibler une page précise en CSS, utiliser `body[data-page="..."]` (la valeur correspond à l'attribut `data-page` du `<body>` de la page HTML).

## Auteurs

Projet réalisé par 10 étudiants en BUT Métiers du Multimédia et de l'Internet, IUT de Marne-la-Vallée.