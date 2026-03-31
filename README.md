# 📺 Écran d'accueil — Collège Joliot Curie

Affichage dynamique diffusé via **MagicInfo** sur l'écran du hall d'entrée.
Hébergé sur **GitHub Pages**, mis à jour sans manipulation technique.

---

## Structure du repo

```
Ecran_College/
├── ecran_joliot_curie.html        Page affichée sur l'écran
├── data.json                      ← À modifier chaque semaine
├── meteo.json                     Mis à jour automatiquement (GitHub Action)
├── logo.png                       Logo du collège
└── .github/workflows/meteo.yml    GitHub Action météo (ne pas toucher)
```

---

## Mettre à jour l'écran chaque semaine

**Tout se fait dans `data.json` uniquement.** C'est le seul fichier à modifier.

### 1. Ouvrir `data.json` sur GitHub

Aller sur la page du repo → cliquer sur `data.json` → icône crayon ✏️ (Edit this file).

### 2. Changer la semaine A/B

```json
"semaine": {
  "type": "B",
  "periode": "7 au 11 avril 2026"
}
```

Ce champ met à jour automatiquement tous les badges « Semaine A/B » visibles à l'écran.

### 3. Mettre à jour les informations (carrousel)

La section `actus` alimente les grandes cartes défilantes au centre de l'écran.

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

### 4. Mettre à jour l'agenda (colonne droite)

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

### 5. Mettre à jour le bandeau défilant (ticker)

```json
"ticker": [
  "Bienvenue au Collège Joliot Curie ! 👋",
  "📅 Semaine B — du 7 au 11 avril 2026",
  "🎭 Vendredi 10 avril : Spectacle de fin d'année"
]
```

Chaque élément du tableau devient un message séparé par un `•` dans le bandeau.

### 6. Mettre à jour les menus

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

### 7. Sauvegarder

En bas de la page d'édition GitHub → **Commit changes** → message type `📅 Mise à jour semaine B` → **Commit changes**.

L'écran se met à jour dans les **15 minutes** (rafraîchissement automatique).

---

## Météo

La météo est récupérée automatiquement depuis **Open-Meteo** (sans clé API, RGPD ✅) toutes les **30 minutes** via une GitHub Action.

Le fichier `meteo.json` est mis à jour et committé automatiquement — il n'y a rien à faire manuellement.

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
| Météo aujourd'hui + demain | `meteo.json` (GitHub Action) | Toutes les 30 min |
| Semaine A/B | `data.json` | À chaque chargement |
| Carrousel d'infos | `data.json` → `actus` | À chaque chargement |
| Agenda | `data.json` → `agenda` | À chaque chargement |
| Menu du jour | `data.json` → `menus` | À chaque chargement |
| Bandeau défilant | `data.json` → `ticker` | À chaque chargement |
| Citation | `data.json` → `citation` | Rotation toutes les 13s |

L'écran recharge `data.json` toutes les **15 minutes**. Une mise à jour sur GitHub est visible à l'écran au bout de 15 minutes maximum.

---

## Compatibilité MagicInfo

- Résolution fixe **1920 × 1080**
- JavaScript **ES5** (pas de `const`, `let`, arrow functions, `async/await`)
- Pas de `backdrop-filter` (non supporté)
- Polices chargées depuis Google Fonts (`Nunito`, `Righteous`)
- Météo et données via `XMLHttpRequest` (pas de `fetch`)
