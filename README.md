# 📺 Écran d'accueil — Collège Joliot Curie

Affichage dynamique diffusé via **MagicInfo** sur l'écran du hall d'entrée.
Hébergé sur **GitHub Pages**, mis à jour sans manipulation technique.

---

## Structure du repo

```
Ecran_College/
├── ecran_joliot_curie.html        Page affichée sur l'écran
├── admin_joliot_curie.html        Interface d'administration (usage LOCAL, hors dépôt)
├── data.json                      Mis à jour automatiquement par la page admin
├── meteo.json                     Mis à jour automatiquement (GitHub Action)
├── images/                        Images du carrousel et du plein écran
├── logo.png                       Logo du collège
└── .github/workflows/
    ├── meteo.yml                  Action météo (ne pas toucher)
    └── cleanup-images.yml         Nettoyage des images inutilisées (ne pas toucher)
```

> ⚠️ **Le dépôt est public.** Tout ce qui est publié (`data.json`, images, pages
> de PDF converties) est accessible à qui connaît l'adresse. Ne rien y déposer
> de nominatif (noms d'élèves, courriels, documents internes).

---

## Mettre à jour l'écran chaque semaine

**Tout se fait via la page d'administration** `admin_joliot_curie.html`. Il n'y a
pas besoin de modifier `data.json` à la main ni de toucher GitHub directement.

### 1. Ouvrir la page admin

Ouvrir `admin_joliot_curie.html` **localement** dans un navigateur (double-clic
sur le fichier). Elle ne nécessite pas de serveur web. **Elle n'est pas
publiée sur le dépôt** : elle reste sur le poste.

### 2. Configurer la connexion GitHub (première fois uniquement)

En bas à droite, renseigner la section **Connexion GitHub** :

- **Propriétaire du repo** : nom du compte ou de l'organisation GitHub
- **Nom du repo** : nom exact du dépôt (ex. `Ecran_College`)
- **Token GitHub** : un token *fine-grained* avec droit d'écriture sur le repo (`github_pat_…`)

Ces informations sont sauvegardées dans le navigateur (localStorage) — elles
n'ont pas à être ressaisies à chaque fois. Le token n'est jamais envoyé
ailleurs que vers GitHub. **Ne pas utiliser la page admin sur un poste
partagé** : le token reste enregistré dans le navigateur.

Cliquer sur **🔍 Tester la connexion** pour vérifier. Au chargement suivant,
la page récupère automatiquement les données actuelles depuis GitHub.

### 3. Modifier les contenus

La page est organisée en **cinq onglets**, dans l'ordre du travail
hebdomadaire :

| Onglet | Contient | Ce qu'on y fait |
|---|---|---|
| 🗓️ **Ma semaine** | Semaine & Période, Basculement | Régler le lundi de la semaine, le type A/B, puis basculer vers la semaine suivante |
| 📢 **Contenus** | Carrousel, Agenda, Bandeau, Citations | Tout ce qui s'affiche dans le corps de l'écran |
| 🍽️ **Menus** | Import PDF, Menus par jour | Déposer le PDF Elior, puis relire jour par jour |
| 🖼️ **Plein écran** | Images / PDF / messages | Ce qui recouvre l'écran à intervalle régulier |
| ⚙️ **Réglages** | Publications programmées, GitHub, JSON | À régler une fois pour toutes |

Chaque onglet affiche un **compteur** (nombre d'infos, de jours de menus
remplis, d'éléments plein écran actifs) : on voit ce qui est fait sans ouvrir.

**Le lundi de la semaine se saisit à un seul endroit**, dans *Ma semaine*. Il
remplit automatiquement la période affichée, le type A/B (si le calcul
automatique est activé) et les jours de menus. Si vous choisissez un autre jour
que le lundi, la date est recalée sur le lundi de la même semaine.

Un **aperçu en temps réel** (colonne de droite) reste visible quel que soit
l'onglet, avec un encart **état de publication** qui indique s'il reste des
modifications non publiées et si une publication est programmée.

Si des champs manquent, la bannière rouge en haut liste les problèmes :
**cliquer sur une ligne amène directement à l'endroit concerné**.

### 4. Publier

Cliquer sur **🚀 Publier l'écran** en haut de la page. La page admin envoie le
`data.json` généré via l'API GitHub — un commit est créé automatiquement.

L'écran recharge `data.json` **toutes les 3 minutes** : une mise à jour est
visible au bout de 3 minutes maximum.

### 5. Publications programmées

Dans **⚙️ Réglages**, on peut préparer un contenu et demander qu'il parte à une
date et une heure précises. Deux règles :

- **La publication programmée est prioritaire.** Tant qu'il en existe une à
  venir, le bouton *Publier* n'envoie rien : il propose de **reporter vos
  modifications en cours dans la publication programmée**, pour qu'elles
  partent à l'heure prévue plutôt que d'être perdues. On peut aussi le faire à
  tout moment via le bouton *⟳ Mettre à jour le contenu* de la chronologie.
- **Si le poste est éteint à l'heure prévue, la publication part plus tard.**
  Elle est renvoyée à la prochaine ouverture de la page admin, avec la mention
  « envoyée avec retard ». Elle n'est marquée *publiée* qu'une fois GitHub
  ayant confirmé : en cas d'échec (réseau, token expiré, GitHub indisponible),
  elle reste en attente et un nouvel essai a lieu automatiquement.

⚠️ La page admin doit donc être **ouverte** pour qu'une publication programmée
parte : elle s'exécute dans le navigateur, pas sur un serveur.

---

## Le plein écran (interstitiels)

Permet de faire passer, par-dessus l'écran d'accueil, une ou plusieurs
**images**, **pages de PDF** ou **messages**, à intervalle régulier.

Dans l'admin, section **🖼️ Plein écran** :

1. Cocher **Activer l'affichage plein écran**.
2. Régler **toutes les N minutes** : c'est le temps d'écran d'accueil entre
   deux passages.
3. Ajouter des éléments (image, PDF ou message). Pour chacun :
   - **Durée d'affichage** : de 3 à 180 secondes,
   - **Mode d'affichage** : `Tout l'écran` ou `Panneau par-dessus`
     (l'écran d'accueil reste visible autour),
   - **Cadrage** (images) : `Image entière` ou `Remplit l'écran`,
   - **Titre / sous-titre** facultatifs, affichés en surimpression,
   - **Texte défilant** (messages) : le texte monte de bas en haut,
   - **Fenêtre de dates** facultative : l'élément ne s'affiche qu'entre ces
     deux dates, et s'efface tout seul ensuite,
   - **Élément actif** : décocher pour le mettre de côté sans le supprimer.

Un **passage** affiche tous les éléments actifs à la suite, chacun pendant sa
propre durée, puis rend la main à l'écran d'accueil. Le résumé sous les
réglages indique la durée totale d'un passage.

### Le cas des PDF

MagicInfo n'affiche pas les PDF de façon fiable. La page admin **convertit
donc chaque page du PDF en image** au moment de l'ajout, et n'envoie que des
images à l'écran. Un PDF de 3 pages crée 3 éléments, que l'on peut réordonner,
désactiver ou supprimer individuellement.

---

## Structure de `data.json` (référence)

> Ce fichier est généré automatiquement par la page admin. Cette section sert
> de référence en cas d'édition manuelle d'urgence.

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
    "date": "Cette semaine",
    "image": "images/20260410_0900_affiche.jpg",
    "imagePosition": "50.0% 40.0%",
    "imageScale": 1
  }
]
```

**Couleurs disponibles** : `blue` `red` `green` `orange` `purple`
`image`, `imagePosition` et `imageScale` sont facultatifs (carte texte sinon).

### Plein écran (`plein_ecran`)

```json
"plein_ecran": {
  "actif": true,
  "intervalle": 10,
  "items": [
    {
      "type": "image",
      "src": "images/20260410_0900_fs_affiche_p1.jpg",
      "titre": "Portes ouvertes",
      "detail": "Samedi 14 mars · 9h-12h",
      "cadrage": "contain",
      "duree": 20,
      "taille": "plein",
      "actif": true,
      "debut": "",
      "fin": "2026-03-14"
    },
    {
      "type": "texte",
      "titre": "Rappel",
      "texte": "Les emplois du temps définitifs seront distribués lundi.",
      "defilant": false,
      "duree": 15,
      "taille": "panneau",
      "actif": true
    }
  ]
}
```

| Champ | Valeurs | Rôle |
|---|---|---|
| `actif` (global) | `true` / `false` | Coupe tout le plein écran |
| `intervalle` | minutes | Temps d'écran d'accueil entre deux passages |
| `type` | `image` / `texte` | Nature de l'élément |
| `duree` | secondes | Temps d'affichage de cet élément |
| `taille` | `plein` / `panneau` | Tout l'écran, ou encart par-dessus |
| `cadrage` | `contain` / `cover` | Image entière, ou remplit en recadrant |
| `defilant` | `true` / `false` | Texte qui monte de bas en haut |
| `debut` / `fin` | `AAAA-MM-JJ` | Fenêtre d'affichage (vide = sans limite) |
| `actif` (élément) | `true` / `false` | Masque l'élément sans le supprimer |

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

Mettre `"semaine_prochaine": true` pour les événements de la semaine suivante —
ils apparaissent en grisé sous la ligne de séparation.

### Bandeau défilant (`ticker`)

```json
"ticker": [
  "Bienvenue au Collège Joliot Curie ! 👋",
  "📅 Semaine B — du 7 au 11 avril 2026"
]
```

Chaque élément devient un message séparé par un `•` dans le bandeau.

### Menus

Les menus sont indexés par date au format `AAAA-MM-JJ`. Il n'y a pas de menu le
mercredi ni le week-end.

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

Laisser un tableau vide `[]` pour une catégorie absente du jour — elle ne
s'affiche pas.

### Citations

```json
"citations": [
  { "texte": "Une belle citation.", "auteur": "Son auteur" }
]
```

---

## Météo

La météo fonctionne en deux temps :

- **GitHub Action** : récupère les données depuis **Open-Meteo** (sans clé API,
  RGPD ✅) toutes les **30 minutes** et met à jour `meteo.json` automatiquement.
- **Écran** : recharge `meteo.json` toutes les **5 minutes** via
  `XMLHttpRequest`. En cas d'échec, un seul réessai est programmé à 2 minutes.

**Source :** Open-Meteo, coordonnées configurées dans `.github/workflows/meteo.yml`.

⚠️ `FALLBACK_WCODE` / `FALLBACK_TEMP` dans `ecran_joliot_curie.html` sont des
valeurs **de secours et de test** (par défaut : pluie, 14 °C). Elles ne servent
qu'à animer le fond quand `meteo.json` est injoignable — la météo affichée est
alors fausse. Passer `FALLBACK_WCODE` à `0` pour un fond neutre.

---

## Nettoyage des images

L'Action `cleanup-images.yml` tourne chaque lundi à 3h et supprime de `images/`
les fichiers de plus de 3 semaines **qui ne sont plus référencés dans
`data.json`**. Une image encore affichée (carrousel ou plein écran) est
conservée quel que soit son âge.

---

## Correction de l'heure (MagicInfo)

MagicInfo ne gère pas automatiquement le changement heure été/hiver.

Dans `ecran_joliot_curie.html`, deux variables contrôlent le décalage :

```js
var HEURE_ETE_MS    = 3600000; // 1h — passer à 0 en heure d'hiver
var AVANCE_ECOLE_MS =   60000; // 1 min — décalage fixe du poste, ne pas toucher
```

**Chaque année :**
- **Dernier dimanche de mars** → vérifier que `HEURE_ETE_MS = 3600000`
- **Dernier dimanche d'octobre** → passer `HEURE_ETE_MS = 0`

---

## Ce qui s'affiche sur l'écran

| Zone | Source | Fréquence de MAJ |
|---|---|---|
| Heure & date | Calculé en temps réel | — |
| Météo aujourd'hui + demain | `meteo.json` (GitHub Action) | 5 min (écran) / 30 min (Action) |
| Semaine A/B | `data.json` | À chaque chargement |
| Carrousel d'infos | `data.json` → `actus` | Rotation toutes les 6 s |
| Plein écran | `data.json` → `plein_ecran` | Selon `intervalle` |
| Agenda | `data.json` → `agenda` | À chaque chargement |
| Menu du jour | `data.json` → `menus` | Recalculé toutes les heures |
| Bandeau défilant | `data.json` → `ticker` | À chaque chargement |
| Citations | `data.json` → `citations` | Rotation toutes les 13 s |

L'écran recharge `data.json` toutes les **3 minutes**. Le carrousel n'est
reconstruit que si le contenu a réellement changé : un rafraîchissement à vide
n'interrompt plus la rotation en cours.

---

## Compatibilité MagicInfo

- Résolution fixe **1920 × 1080**
- JavaScript **ES5** (pas de `const`, `let`, arrow functions, `async/await`,
  `includes()`, `padStart()`, template literals)
- Pas de `backdrop-filter` (non supporté)
- Pas de PDF affiché directement (converti en images côté admin)
- Polices chargées depuis Google Fonts (`Nunito`, `Righteous`)
- Météo et données via `XMLHttpRequest` (pas de `fetch`)
