# 📺 Écran d'accueil — Collège Joliot Curie

Affichage dynamique diffusé via **MagicInfo** sur l'écran du hall d'entrée.
Hébergé sur **GitHub Pages**, mis à jour sans manipulation technique.

---

## Structure du repo

```
Ecran_College/
├── ecran_joliot_curie.html        Page affichée sur l'écran
├── admin_joliot_curie.html        Interface d'administration (usage local)
├── data.json                      Mis à jour automatiquement par la page admin
├── meteo.json                     Mis à jour automatiquement (GitHub Action)
├── meteo.yml                      Copie locale du workflow (ne pas toucher)
├── logo.png                       Logo du collège
└── .github/workflows/meteo.yml    GitHub Action météo (ne pas toucher)
```

---

## Mettre à jour l'écran chaque semaine

**Tout se fait via la page d'administration** `admin_joliot_curie.html`. Il n'y a pas besoin de modifier `data.json` à la main ni de toucher GitHub directement.

### 1. Ouvrir la page admin

Ouvrir `admin_joliot_curie.html` **localement** dans un navigateur (double-clic sur le fichier). Elle ne nécessite pas de serveur web.

### 2. Configurer la connexion GitHub (première fois uniquement)

En bas de la page, renseigner la section **Connexion GitHub** :

- **Propriétaire du repo** : nom du compte ou de l'organisation GitHub
- **Nom du repo** : nom exact du dépôt (ex. `Ecran_College`)
- **Token GitHub** : un token *fine-grained* avec droit d'écriture sur le repo (`github_pat_…`)

Ces informations sont sauvegardées dans le navigateur (localStorage) — elles n'ont pas à être ressaisies à chaque fois. Le token n'est jamais envoyé ailleurs que vers GitHub.

Cliquer sur **🔍 Tester la connexion** pour vérifier. Une fois validée, la page charge automatiquement les données actuelles depuis GitHub.

### 3. Modifier les contenus

L'interface propose une section par zone de l'écran :

| Section | Correspond à |
|---|---|
| Semaine & Période | Type A/B + dates affichées |
| Informations — Carrousel central | Les grandes cartes défilantes (`actus`) |
| Agenda | Les événements de la colonne droite |
| Menus de la semaine | Le menu du jour (par onglet de date) |
| Bandeau défilant | Les messages du ticker |
| Citations | Les citations en rotation |

Un **aperçu en temps réel** (colonne de droite) reflète les modifications au fur et à mesure.

### 4. Publier

Cliquer sur le bouton **📤 Publier sur GitHub** en haut de la page. La page admin envoie le `data.json` généré directement via l'API GitHub — un commit est créé automatiquement.

L'écran se met à jour dans les **15 minutes** (rafraîchissement automatique).

---

## Structure de `data.json` (référence)

> Ce fichier est généré automatiquement par la page admin. Cette section sert de référence en cas d'édition manuelle d'urgence.

### Semaine A/B

```json
"semaine": {
  "type": "B",
  "periode": "7 au 11 avril 2026"
}
```

### Carrousel d'infos (`actus`)

```json
"actus": [
  {
    "emoji": "🎭",
    "tag": "Spectacle",
    "couleur": "purple",
    "texte": "Spectacle de fin d'année — 6ème",
    "detail": "Vendredi 10 avril · Salle polyvalente",
    "date": "Cette semaine"
  }
]
```

**Couleurs disponibles** : `blue` `red` `green` `orange` `purple`

### Agenda

```json
"agenda": [
  {
    "label": "Lun 7/4\nmatin",
    "couleur": "blue",
    "nom": "Conseil de classe 3ème",
    "lieu": "Salle des professeurs",
    "semaine_prochaine": false
  }
]
```

Mettre `"semaine_prochaine": true` pour les événements de la semaine suivante — ils apparaissent en grisé sous la ligne de séparation.

### Bandeau défilant (`ticker`)

```json
"ticker": [
  "Bienvenue au Collège Joliot Curie ! 👋",
  "📅 Semaine B — du 7 au 11 avril 2026",
  "🎭 Vendredi 10 avril : Spectacle de fin d'année"
]
```

Chaque élément devient un message séparé par un `•` dans le bandeau.

### Menus

Les menus sont indexés par date au format `AAAA-MM-JJ`. Il n'y a pas de menu le mercredi ni le week-end.

```json
"menus": {
  "2026-04-07": {
    "entree":  ["Salade de tomates"],
    "plat":    ["Steak haché", "Galette végétarienne <span class=\"vege-tag\">Végé</span>"],
    "accomp":  ["Haricots verts <span class=\"bio-tag\">BIO</span>"],
    "fromage": ["Yaourt nature"],
    "dessert": ["Compote de pommes"]
  }
}
```

**Tags disponibles dans les plats :**
- `<span class="bio-tag">BIO</span>` → badge vert BIO
- `<span class="vege-tag">Végé</span>` → badge orange Végé
- `<span class="bio-tag">AOC</span>` → badge vert AOC

Laisser un tableau vide `[]` pour une catégorie absente du jour — elle ne s'affiche pas.

### Citations

```json
"citations": [
  {
    "texte": "Une belle citation.",
    "auteur": "Son auteur"
  }
]
```

---

## Météo

La météo fonctionne en deux temps :

- **GitHub Action** : récupère les données depuis **Open-Meteo** (sans clé API, RGPD ✅) toutes les **30 minutes** et met à jour `meteo.json` automatiquement — rien à faire manuellement.
- **Écran** : recharge `meteo.json` toutes les **5 minutes** via `XMLHttpRequest`.

**Source :** Open-Meteo, coordonnées configurées dans `.github/workflows/meteo.yml`.

---

## Correction de l'heure (MagicInfo)

MagicInfo ne gère pas automatiquement le changement heure été/hiver.

Dans `ecran_joliot_curie.html`, deux variables contrôlent le décalage :

```js
var HEURE_ETE_MS    = 3600000; // 1h — passer à 0 en heure d'hiver
var AVANCE_ECOLE_MS =  180000; // 3 min — décalage fixe du poste, ne pas toucher
```

**Chaque année :**
- **Dernier dimanche de mars** → vérifier que `HEURE_ETE_MS = 3600000`
- **Dernier dimanche d'octobre** → passer `HEURE_ETE_MS = 0`

---

## Ce qui s'affiche sur l'écran

| Zone | Source | Fréquence de MAJ |
|---|---|---|
| Heure & date | Calculé en temps réel | — |
| Météo aujourd'hui + demain | `meteo.json` (GitHub Action) | Toutes les 5 min (écran) / 30 min (Action) |
| Semaine A/B | `data.json` | À chaque chargement |
| Carrousel d'infos | `data.json` → `actus` | À chaque chargement |
| Agenda | `data.json` → `agenda` | À chaque chargement |
| Menu du jour | `data.json` → `menus` | À chaque chargement |
| Bandeau défilant | `data.json` → `ticker` | À chaque chargement |
| Citations | `data.json` → `citations` | Rotation toutes les 13s |

L'écran recharge `data.json` toutes les **15 minutes**. Une mise à jour sur GitHub est visible à l'écran au bout de 15 minutes maximum.

---

## Compatibilité MagicInfo

- Résolution fixe **1920 × 1080**
- JavaScript **ES5** (pas de `const`, `let`, arrow functions, `async/await`)
- Pas de `backdrop-filter` (non supporté)
- Polices chargées depuis Google Fonts (`Nunito`, `Righteous`)
- Météo et données via `XMLHttpRequest` (pas de `fetch`)
