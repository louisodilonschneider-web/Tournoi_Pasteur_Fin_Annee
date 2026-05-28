# 🏆 Tournois EPS — Collège Pasteur · Villemomble

Application web de gestion de tournois scolaires en temps réel.
Un seul fichier HTML pour gérer le **basket-ball** et le **football**, sur n'importe quel appareil.

---

## Sommaire

1. [Présentation](#présentation)
2. [Configuration Firebase](#configuration-firebase)
3. [Règles Firebase permanentes](#règles-firebase-permanentes)
4. [Lancer le site](#lancer-le-site)
5. [Partager avec les collègues](#partager-avec-les-collègues)
6. [Guide Basket-ball](#guide-basket-ball)
7. [Guide Football](#guide-football)
8. [Fonctionnalités communes](#fonctionnalités-communes)
9. [Structure du tournoi](#structure-du-tournoi)
10. [Structure des données Firebase](#structure-des-données-firebase)
11. [FAQ & Dépannage](#faq--dépannage)

---

## Présentation

Plusieurs enseignants peuvent gérer le tournoi simultanément depuis leurs appareils (PC, téléphone, tablette). Toute saisie est synchronisée en temps réel via Firebase Realtime Database — un score entré sur un téléphone apparaît immédiatement sur tous les autres écrans.

**Deux sports indépendants dans un seul fichier :**

| | Basket-ball 🏀 | Football ⚽ |
|---|---|---|
| Catégories | Benjamins + Minimes | Une seule catégorie |
| Équipes max | 8 par catégorie | 8 au total |
| Composition | Jusqu'à 6 joueurs, nom + classe libre | 5 slots fixes (6e, 5e, 4e, 3e + enseignant) |
| Couleur accent | Orange | Vert |

Les données des deux sports sont **totalement séparées** dans Firebase et n'interfèrent jamais.

---

## Configuration Firebase

> ✅ **Déjà intégrée dans le fichier HTML — rien à modifier.**

Projet Firebase : **tournoi-basket-pasteur**

```javascript
var firebaseConfig = {
  apiKey:            "AIzaSyCEDnS6Ji8x5Agmq_zWCpa3aYiMUeI_rcg",
  authDomain:        "tournoi-basket-pasteur.firebaseapp.com",
  databaseURL:       "https://tournoi-basket-pasteur-default-rtdb.europe-west1.firebasedatabase.app",
  projectId:         "tournoi-basket-pasteur",
  storageBucket:     "tournoi-basket-pasteur.firebasestorage.app",
  messagingSenderId: "663931206812",
  appId:             "1:663931206812:web:71489cb9ba386a5d998159"
};
```

---

## Règles Firebase permanentes

> ⚠️ **À vérifier si les données ne se sauvegardent plus.** Sans ces règles les écritures échouent silencieusement.

1. Aller sur [console.firebase.google.com](https://console.firebase.google.com)
2. Projet **tournoi-basket-pasteur** → **Build → Realtime Database → Règles**
3. Vérifier que le contenu est exactement :

```json
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
```

4. Si une date `"now < ..."` est présente (règles expirées), remplacer par le contenu ci-dessus et cliquer **Publier**.

> Ces règles n'ont **pas de date d'expiration** — elles restent actives indéfiniment.

---

## Lancer le site

### Option A — Ouverture locale (la plus simple)

Double-cliquez sur `tournois-eps.html`.

> Si le navigateur affiche une page blanche, utilisez l'option B.

### Option B — Serveur local

Si Python est installé, dans le dossier du fichier :

```bash
python -m http.server 8080
```

Puis ouvrir `http://localhost:8080/tournois-eps.html`.

---

## Partager avec les collègues

Pour que plusieurs enseignants accèdent au **même site depuis leurs téléphones**, le fichier doit être accessible en ligne.

### Via GitHub Pages (gratuit, recommandé)

1. Créer un compte sur [github.com](https://github.com)
2. Créer un **nouveau dépôt public**
3. Uploader `tournois-eps.html` dans ce dépôt
4. Aller dans **Settings → Pages → Source → main branch**
5. GitHub génère une URL : `https://votre-pseudo.github.io/nom-depot/tournois-eps.html`
6. Partager cette URL à tous les collègues

Chaque collègue ouvre l'URL sur son téléphone — toutes les saisies sont synchronisées en temps réel.

> **Indicateur de connexion** dans le header : point 🟢 vert = Firebase actif, données sauvegardées. Point 🔴 rouge = hors ligne, aucune donnée ne sera conservée.

---

## Guide Basket-ball 🏀

### Étape 1 — Sélectionner le sport

Sur l'écran d'accueil, cliquer sur la carte **Basket-ball 🏀**.

### Étape 2 — Créer une édition

Entrer l'année (ex: `2026`) dans la barre du haut → **+ Nouvelle édition**.

### Étape 3 — Inscrire les équipes

Onglet **Inscriptions** :

- Choisir la catégorie : **🔶 Benjamins** ou **🔷 Minimes**
- Renseigner le nom de l'équipe
- Choisir le type : Élèves ou Enseignants
- Ajouter jusqu'à **6 joueurs** : prénom/nom + classe individuelle (les élèves peuvent venir de classes différentes au sein d'une même équipe)
- Cliquer **✅ Enregistrer l'équipe**

L'équipe apparaît instantanément sur tous les appareils connectés.

Capacité : **8 équipes par catégorie** (typiquement 7 équipes élèves + 1 équipe enseignants).

### Étape 4 — Générer le tournoi

Une fois minimum 4 équipes inscrites par catégorie, cliquer **⚙️ Générer** dans la barre du haut.

- Les équipes sont réparties aléatoirement en **2 poules de 4**
- L'ordre des matchs est calculé par l'**algorithme du cercle** pour éviter qu'une même équipe joue deux matchs consécutifs

> ⚠️ La génération efface les poules existantes. Ne pas regénérer en cours de tournoi.

### Étape 5 — Phase de poules

Onglet **Poules** → choisir la catégorie :

- Cliquer **📝 Score** sur un match pour saisir le résultat
- Les champs affichent **0 par défaut** — un champ vide vaut 0
- La confirmation s'affiche **après** l'écriture Firebase effective
- Cliquer **✏️ Modifier** pour corriger un score déjà saisi
- Le classement et les statuts se mettent à jour en temps réel sur tous les écrans

**Classement :** victoire = 2 pts, défaite = 1 pt, égalité = 1 pt chacun.
Départage par différence de buts, puis buts marqués.

### Étape 6 — Tableau final

Généré **automatiquement** dès que tous les matchs de poule d'une catégorie sont terminés.

| Match | Adversaires | Demi-finale |
|---|---|---|
| QF1 | 1er Poule A vs 4ème Poule B | → SF1 |
| QF2 | 2ème Poule B vs 3ème Poule A | → SF1 |
| QF3 | 1er Poule B vs 4ème Poule A | → SF2 |
| QF4 | 2ème Poule A vs 3ème Poule B | → SF2 |

> Les 1ers de chaque poule sont dans des branches opposées — ils ne peuvent se rencontrer **qu'en finale**.

Chaque score saisi génère automatiquement le match suivant. Le nom du vainqueur remplace "À déterminer" immédiatement, sans recharger la page.

Cascade : QF terminés → **Demi-finales** créées → SF terminées → **Finale + Petite finale** créées.

### Étape 7 — Consolation (optionnel)

Onglet **Consolation**, si le créneau horaire le permet :

- **Quarts de consolation** : les 4 perdants des QF jouent entre eux (2 matchs)
- **Match 5e place** : vainqueurs des quarts de consolation
- **Match 7e place** : perdants des quarts de consolation

### Étape 8 — Palmarès et PDF

- Onglet **Palmarès** : classement de 🥇 à 8️⃣, se complète automatiquement
- Bouton **📥 PDF** : exporte un bilan complet (classements, résultats, palmarès)

---

## Guide Football ⚽

### Différences avec le basket

**Sélection :** cliquer sur la carte **Football ⚽** sur l'écran d'accueil.

**Pas d'onglets Benjamins/Minimes** — une seule liste d'équipes.

**Formulaire d'équipe spécifique — 5 slots fixes :**

| Slot | Champ prénom/nom | Champ complémentaire |
|---|---|---|
| **6ème** | Élève de 6ème | Lettre de classe (A, B, C…) |
| **5ème** | Élève de 5ème | Lettre de classe |
| **4ème** | Élève de 4ème | Lettre de classe |
| **3ème** | Élève de 3ème | Lettre de classe |
| **Enseignant** 👨‍🏫 | Prénom NOM | Matière enseignée |

> **Exemple :** élève de 5ème B → nom : `Martin Dupont`, lettre : `B` → affiché `5ème B`
> **Exemple :** enseignant → nom : `Mme Bernard`, matière : `Mathématiques`

Il n'y a pas de bouton "+ Joueur" en mode foot — la composition est fixe à 5 membres.

La suite du déroulement (génération, poules, tableau, consolation, palmarès, PDF) est **identique au basket**.

---

## Fonctionnalités communes

### Modifier une équipe

Sur chaque carte d'équipe, bouton **✏️** → modale pré-remplie avec tous les champs → modifier → **✅ Enregistrer**. Synchronisé en temps réel.

### Supprimer une équipe

Bouton **✕** sur la carte → confirmation demandée.

### Changer de sport

Bouton **← Sports** dans le header → retour à l'écran d'accueil pour choisir l'autre sport. Les données de chaque sport sont conservées indépendamment.

### Historique

Onglet **Historique** : liste de toutes les éditions passées pour le sport actuellement sélectionné. Cliquer sur une édition pour la recharger.

### Export PDF

Bouton **📥 PDF** dans la barre du haut — disponible à tout moment, même en cours de tournoi. Contient :

- Classements de poule (avec J/V/D/Pts/+-)
- Résultats de tous les matchs du tableau (QF, SF, Finale, Petite finale)
- Palmarès final

---

## Structure du tournoi

```
8 équipes
│
├── Poule A (4 équipes) — 6 matchs round-robin
└── Poule B (4 équipes) — 6 matchs round-robin
         │
         ▼  tous les matchs terminés → génération automatique
┌── QF1 : 1er A  vs 4ème B ──► Vainqueur → SF1  │ Perdant → Consolation
├── QF2 : 2ème B vs 3ème A ──► Vainqueur → SF1  │ Perdant → Consolation
├── QF3 : 1er B  vs 4ème A ──► Vainqueur → SF2  │ Perdant → Consolation
└── QF4 : 2ème A vs 3ème B ──► Vainqueur → SF2  │ Perdant → Consolation
         │
         ▼
SF1 : V(QF1) vs V(QF2) ──► Vainqueur → Finale     │ Perdant → Petite finale
SF2 : V(QF3) vs V(QF4) ──► Vainqueur → Finale     │ Perdant → Petite finale
         │
         ▼
🥇 Finale          → 1ère et 2ème place
🥉 Petite finale   → 3ème et 4ème place
5️⃣ Match 5e place → 5ème et 6ème place  (si consolation jouée)
7️⃣ Match 7e place → 7ème et 8ème place  (si consolation jouée)
```

**Ordre des matchs de poule — algorithme du cercle :**
Avec 4 équipes sur 1 terrain, 2 enchainements sont mathématiquement inévitables sur 6 matchs.
L'algorithme du cercle les répartit aussi équitablement que possible entre les équipes.

---

## Structure des données Firebase

```
tournaments/
│
├── basket/
│   └── {année}/
│       ├── meta/            { year, sport, createdAt, status }
│       ├── teams/
│       │   ├── benjamin/    { id, name, players:[{name, cls}] }
│       │   └── minime/      { id, name, players:[{name, cls}] }
│       ├── pools/
│       │   ├── benjamin/
│       │   │   ├── A/  { teams:[], matches:{ m0…m5: {teamA,teamB,scoreA,scoreB,done,order} } }
│       │   │   └── B/  (idem)
│       │   └── minime/  (idem)
│       ├── bracket/
│       │   ├── benjamin/
│       │   │   ├── qf/         { qf1, qf2, qf3, qf4 }
│       │   │   ├── sf/         { sf1, sf2 }
│       │   │   ├── final/      { teamA, teamB, scoreA, scoreB, done }
│       │   │   └── thirdPlace/ { teamA, teamB, scoreA, scoreB, done }
│       │   └── minime/  (idem)
│       └── consolation/
│           ├── benjamin/ { cqf1, cqf2, csf1, cpf }
│           └── minime/   (idem)
│
└── foot/
    └── {année}/
        ├── meta/        { year, sport, createdAt, status }
        ├── teams/       { id, name, players:[{name, cls, type, slotKey}] }
        ├── pools/
        │   ├── A/  { teams:[], matches:{m0…m5} }
        │   └── B/  (idem)
        ├── bracket/     { qf:{}, sf:{}, final:{}, thirdPlace:{} }
        └── consolation/ { cqf1, cqf2, csf1, cpf }
```

---

## FAQ & Dépannage

**Les scores disparaissent après rechargement**
→ Vérifier les règles Firebase (section dédiée). Le point 🔴 rouge dans le header confirme que les écritures échouent.

**Un collègue ne voit pas les mises à jour**
→ Vérifier qu'il a sélectionné le même sport ET la même édition (même année dans le menu déroulant).

**Aucun bouton ne répond / erreur en console**
→ Ouvrir la console (F12 → Console). Si `firebase is not defined` : un bloqueur de publicités bloque les CDN. Désactiver l'extension ou utiliser un autre navigateur.

**Modifier un score déjà saisi**
→ Bouton **✏️ Modifier** visible sur chaque match terminé, dans les onglets Poules et Tableau.

**Regénérer les poules (équipe désistée)**
→ Corriger les équipes dans Inscriptions, puis **⚙️ Générer**. ⚠️ Les scores existants sont effacés.

**Basculer entre basket et foot**
→ Bouton **← Sports** dans le header. Les deux tournois sont indépendants.

**Effacer toutes les données d'une édition**
→ Console Firebase → Realtime Database → naviguer jusqu'à `tournaments/basket/2026` ou `tournaments/foot/2026` → supprimer le nœud.

**Le PDF ne se génère pas**
→ Une connexion internet est nécessaire au premier chargement pour charger la librairie jsPDF. Si le site a déjà été ouvert, le PDF fonctionne normalement.

---

## Compatibilité navigateurs

| Navigateur | Support |
|---|---|
| Chrome 90+ | ✅ Complet |
| Firefox 90+ | ✅ Complet |
| Safari 15+ (iPhone/iPad) | ✅ Complet |
| Edge 90+ | ✅ Complet |
| Android Chrome | ✅ Responsive mobile |

---

*Application développée pour les tournois EPS scolaires*
*Collège Pasteur · Villemomble — Fichier unique, sans installation*
