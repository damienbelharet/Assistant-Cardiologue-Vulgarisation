# Contexte projet — Site « Lis un cœur » (à donner à une IA agent)

Tu interviens sur un site web pédagogique déjà bien avancé. Lis ce document en entier
avant toute modification : il décrit le but, les contraintes **non négociables**, l'état
actuel, et les conventions à respecter. Ne casse pas l'existant ; respecte le style et
l'architecture déjà en place.

---

## 1. Le projet en deux phrases

« Lis un cœur » est un site interactif support d'un **atelier (calibré ~3 h)** mené par deux
étudiants de l'ENSEIRB-MATMECA (Damien Belharet & Dima Husseini) pour faire découvrir les
**télécommunications et le traitement du signal** à **2 lycéens de seconde** (sans aucune
connaissance en code). Le fil rouge : « Aujourd'hui vous devenez ingénieurs biomédicaux ;
votre mission : construire l'outil qui lit un cœur et détecte une maladie. » On apprend le
signal, le bruit, les filtres, l'ECG, puis on diagnostique.

Les élèves **ne codent jamais** : ils cliquent sur des boutons et bougent des curseurs.

**Retour terrain (important).** Le site a déjà été présenté à plusieurs groupes de seconde.
En pratique, la partie atelier-sur-site dure **entre 2 h et 2 h 30** selon les groupes
(questions, rythme), soit plutôt ~2 h. Il reste donc ~1 h, aujourd'hui occupée par une
discussion sur l'orientation qu'on souhaite **alléger**. **Objectif des prochaines
évolutions : enrichir le site (surtout via de nouveaux modules bonus) pour qu'il occupe
davantage la séance.** Les élèves ont particulièrement apprécié les modules bonus (voir §9).

---

## 2. Contraintes techniques NON NÉGOCIABLES

- **100 % statique, hors-ligne, sans installation.** Le site doit tourner en ouvrant les
  fichiers en local (`file:///…`), sans serveur, sans build, sans accès réseau (le wifi de
  la salle peut tomber). Aucune dépendance CDN : tout est dans le dossier (polices, Plotly).
- **Site désormais AUSSI hébergé en ligne (URL publique).** Les élèves peuvent y revenir de
  chez eux. ⚠️ Cela ne relâche PAS la contrainte ci-dessus : le build reste statique /
  hors-ligne / `file://`. Concrètement, **les nouveaux bonus doivent être des simulations
  pédagogiques** (pas de vrai matériel ni d'API à permission), exactement comme « La photo du
  son » (aucune vraie FFT) ou « La montre du sang » (PPG simulé). En particulier, le bonus
  « Paiement sans contact » ne doit **pas** dépendre du Web NFC (HTTPS + Chrome Android
  uniquement) : c'est une **animation expliquant le principe**, pas un accès matériel.
- **Pas de framework.** HTML/CSS/JavaScript « vanilla ». Pas de React, pas de bundler, pas
  de npm dans le livrable. Le JS existant mélange `var`/`function` et `const`/`let` ; reste
  dans cet esprit simple et lisible, sans étape de compilation.
- **Pas de `localStorage`** pour la progression : on utilise **`sessionStorage`** (la
  progression se réinitialise volontairement à la fermeture de l'onglet).
- **Compatibilité Firefox `file://`** : attention aux comportements liés à l'origine `null`
  (voir la règle des badges, §6).

---

## 3. Pile technique

- HTML/CSS/JS natifs.
- **Plotly** (`lib/plotly-basic.min.js`, embarqué localement) pour les graphes du Labo.
- **Canvas 2D** maison pour l'oscilloscope temps réel de l'acquisition et le spectrogramme.
- Polices **auto-hébergées** dans `fonts/` : **Bricolage Grotesque** (titres) et
  **Atkinson Hyperlegible** (texte courant).
- Une feuille de style commune `style.css` + des `<style>` propres à chaque page pour le
  spécifique.

---

## 4. Arborescence des fichiers

```text
index.html        Accueil / mission + progression (badges, diplôme, QTE)
signal.html       Module « C'est quoi un signal ? »      (badge : signal)
coeur.html        Module « Le cœur et l'ECG »            (badge : coeur_ecg)
ecouter.html      Module « Écouter le cœur » (Oreille d'or)
labo.html         Module « Le Labo ECG »                 (badge : labo)   ← module central
pathologies.html  Module « Détective des pathologies »   (badge : pathologies)
metiers.html      Module « Les métiers de l'ingénieur »
spectro.html      Bonus « La photo du son »
montre.html       Bonus « La montre du sang » (PPG simulé)
piano.html        Bonus « Piano Anti-Parasite »
reels.html        Bonus « Coulisses Matlab » (3 vrais cœurs)
progression.js    Gestionnaire de badges (sessionStorage) + moteur de sons + confettis
signaux.js        Bibliothèque partagée : maths du signal/ECG + câblage des jeux de signal.html
style.css         Habillage commun (thème « papier ECG »)
fonts/            Polices woff2 (Bricolage, Atkinson)
lib/              plotly-basic.min.js
```

Note : `acquisition.html` a été **fusionné dans labo.html** (étape 1 « Capter ») et peut être
supprimé.

---

## 5. Système de design (à réutiliser tel quel)

Variables CSS définies dans `:root` de `style.css` :

- `--papier` #fbfaf6 (fond), `--encre` #16302a (texte), `--encre-doux` #4b6058
- `--signal` #0e9f6e (vert, couleur « signal »), `--signal-fonce` #0b7a55
- `--battement` #e11d48 (rouge, couleur « cœur/pic R »), `--attention` #d97706
- `--carte` #ffffff, `--bord` #e3e7e2, `--rayon` 16px, `--ombre` (ombre douce)
- `--titre` = Bricolage (titres en gras), `--texte` = Atkinson (corps)
- Fond du `body` : léger quadrillage façon papier d'électrocardiogramme.

Classes communes : `.barre` (nav du haut), `.page` (conteneur centré), `.mission`
(bandeau vert du role-play), `.carte` (carte blanche), `.bouton` (+ `.bouton.secondaire`),
`.message` (+ `.bon` / `.pas-encore`), `.etapes .pastille` (barre d'étapes), `.cache`
(masquer), `.pied` (pied de page).

**Règle de placement du style** : ce qui est spécifique à une page va dans son `<style>` ;
ce qui est réutilisable (ex. les sous-listes dépliables) va dans `style.css`, dans un bloc
commenté. Les icônes sont des emojis (cohérent avec tout le site).

---

## 6. Progression & badges — RÈGLE CRITIQUE

`progression.js` expose : `validerModule(id)`, `moduleValide(id)`, `modulesValides()`,
`tousLesBadges()`, `reinitialiserTout()`, `afficherToastBadge()`, `lancerConfettis()`.
Clé `sessionStorage` : `"lisuncoeur_badges"`. Quatre badges **obligatoires** :
`signal` 🌊, `coeur_ecg` 🫀, `labo` 🔬, `pathologies` 🩺.

Le badge d'un module est attribué **à l'intérieur du module**, à la fin, par
`validerModule("…")`. Pour fiabiliser l'affichage sur l'accueil en `file://`, l'accueil
lit aussi un paramètre d'URL `index.html?badge=<id>` et valide le badge correspondant.

⚠️ **RÈGLE À NE JAMAIS ENFREINDRE** : le paramètre `?badge=<id>` ne doit figurer **que** sur
le bouton « Retour à l'accueil pour voir ton badge » qui apparaît **une fois le module
terminé**. Tous les liens de navigation « quitter / Accueil / logo » pointent vers
`index.html` **nu** (sans paramètre). Sinon, revenir à l'accueil en cours de route attribue
le badge prématurément (bug déjà rencontré et corrigé). `signal.html` est le modèle correct.

Note : les **modules bonus sont optionnels** — ils ne conditionnent pas le diplôme (qui
dépend des 4 badges obligatoires). Un bonus peut avoir son propre badge décoratif (ex.
« curieux » pour la photo du son), sans entrer dans le compte des 4.

---

## 7. État actuel, module par module

### index.html — Accueil
- Bandeau mission + liste des modules + section « Ta progression » (barre + 4 cartes de
  badges + bouton diplôme + confettis quand les 4 badges sont obtenus).
- La pastille **« Le Labo ECG » est dépliable** : au clic, elle révèle une **sous-liste**
  des 4 mini-parties (Capter / Nettoyer / Zoomer / Repérer) qui pointent vers
  `labo.html?etape=0..3`.
- **Verrouillage** : tant que le badge `labo` n'est pas obtenu, seule « Capter » est
  cliquable ; les autres sous-étapes sont grisées avec un 🔒. Une fois le badge obtenu,
  tout se déverrouille (mode révision). Motif réutilisable : `.pastille-expandable` >
  `.pastille-tete` + `.sous-liste` > `.sous-liste-inner`, et `.sous-item.verrou`.
- Carte/arbre du parcours : nœuds principaux + branches bonus, animations de tracé des
  câbles (sons `trace`), flux lumineux continu sur les chemins validés.

### signal.html — « C'est quoi un signal ? » (badge : signal) — COMPLET
Page la plus riche. Contient : un jeu « Tinder des signaux » (objet vs signal, swipe), un
bac à sable amplitude/fréquence avec son, un défi de synchronisation, la lecture de la
période sur le graphe, une démo d'**échantillonnage** (signal discret), et un module
**spectre** (voix grave / aigus / bruit blanc). Badge « Maître des Ondes ».

### coeur.html — « Le cœur et l'ECG » (badge : coeur_ecg) — COMPLET
Anatomie simplifiée → ondes P, QRS, T → synchronisation cœur + ECG en direct. Badge
« Explorateur Anatomique ».

### ecouter.html — « Écouter le cœur » (Oreille d'or) — COMPLET
Explication du son du cœur en deux étapes. Montre les différentes pathologies en temps réel.

### labo.html — « Le Labo ECG » (badge : labo) — MODULE CENTRAL, COMPLET
Barre d'étapes à **4 étapes verrouillées linéairement** (on ne saute pas une étape non
faite ; on peut revenir en arrière sur une étape déjà faite ; tout se déverrouille une fois
le badge obtenu). Deep-link `?etape=N` (borné au niveau débloqué).

1. **Capter** — oscilloscope canvas temps réel + sélecteur de capteur (électrodes hôpital
   vs montre de sport) + « machine à bruit » (respiration → dérive lente, mouvement →
   sursauts, secteur 50 Hz → grésillement) + jauge de qualité. But : montrer d'où vient la
   saleté. Bouton « Envoyer l'enregistrement au labo ».
2. **Nettoyer** — réception animée du signal (Plotly), puis console à 2 curseurs
   (anti-tangage / anti-grésillement) + jauge de qualité avec un « point d'équilibre »
   (sweet spot 40-60/40-60). Le bouton « Valider » disparaît après validation ; reste
   « Passer au zoom ».
3. **Zoomer** — trouver la bonne largeur d'affichage (ni trop large ni trop serré).
4. **Repérer** — l'élève clique lui-même les pics R sur le graphe ; vérification ; puis
   calcul du **BPM** et **diagnostic** ; attribution du badge.

Détails d'implémentation : `FE = 200` Hz, signal de 24 s. Variables d'état clés dans le
script inline : `etapeActuelle`, `etapeMax` (verrouillage), `filtreValide`, `propre`
(signal nettoyé), `plageActuelle` (zoom). Fonction de navigation : `allerEtape(n)` (refuse
`n > etapeMax`), `majPastilles()` met à jour actif/fait/verrou. L'oscilloscope est isolé
dans une IIFE exposant `window.__oscillo = { start, stop, resize }`.

### pathologies.html — « Détective des pathologies » (badge : pathologies) — COMPLET
3 mini-parties (barre d'étapes linéaire, comme le Labo) :
1. **Comprendre la mécanique** : animation d'un cœur (SVG/emoji qui bat) + bascule entre
   Rythme Normal, Tachycardie, Bradycardie, Fibrillation, Extrasystole, avec le signal ECG
   théorique en dessous.
2. **Le jeu du diagnostic (style Tinder)** : 5-6 patients max, mini-profil textuel, signal
   ECG qui défile, 4 choix de diagnostic. Mécanique humoristique (le patient râle si erreur,
   message de prévention si juste).
3. **Le Patient Mystère (boss de fin)** : transition dramatique, signal brut ultra-sali ;
   l'élève filtre, zoome, clique les pics R, pose le diagnostic. Réussir attribue le 4ᵉ
   badge obligatoire `pathologies` et déclenche le diplôme.

### metiers.html — « Les métiers de l'ingénieur » — COMPLET
Deux étapes : (1) **Ingénieur Réseaux & Télécoms** — choix de la qualité réseau
(Edge / 4G / Fibre), l'ECG arrive troué ou intact ; (2) **Ingénieur Génie Logiciel** —
construction animée de l'interface (HTML → CSS → JavaScript, le cœur se met à battre).

### spectro.html — « La photo du son » (Bonus) — COMPLET
Page bonus centrée sur la lecture du spectrogramme. 3 mini-parties : (1) lire la photo du
son (3 sons : grave, aigu, sirène) ; (2) la signature du bruit (ligne 50 Hz + filtre) ;
(3) trouve l'intrus (jeu). Technique : **aucune vraie FFT** — la « photo » est dessinée sur
Canvas à partir d'un modèle, pour rester léger et 100 % hors-ligne.

### montre.html — « La montre du sang » (Bonus) — COMPLET
Deux sections : taper « photopléthysmographie » lettre par lettre pour « activer le
capteur », puis simulation LED verte + vaisseau + signal PPG. **Simulation** (pas de capteur
réel).

### piano.html — « Piano Anti-Parasite » (Bonus) — COMPLET
Clavier (note = fréquence) puis console anti-parasite : un morceau parasité, un curseur de
**fréquence de coupure** à régler pour retirer le parasite sans abîmer la musique ; spectre
animé en temps réel.

### reels.html — « Coulisses Matlab » (Bonus) — COMPLET
Trois vrais signaux ECG bruts filtrés à la main (sain, FA, FV), avec le décrochage de la
fibrillation ventriculaire visible (~213 s). Conclusion : le filtrage rend la lecture
possible, le zoom fait passer de « on voit des pics » à « on lit la forme d'un battement ».

---

## 8. API de signaux.js (bibliothèque partagée)

Maths du signal/ECG (haut du fichier) :
- `genererECG({ bpm, duree, fe, graine })` → objet ECG (axe temps `.t` + échantillons),
  un battement = ondes P-QRS-T générées par `unBattement`/`cloche`, hasard reproductible
  via `creerHasard(graine)`.
- `ajouterBruit(signal, reglages)` → signal bruité (dérive, secteur, etc.).
- `filtrer(valeurs, fe, { derive, lissage })` → signal filtré (correction de dérive +
  lissage `"aucun"|"leger"|"fort"`).
- `detecterPicsR(valeurs, fe)` → indices des pics R.
- `calculerBPM(picsIndices, fe)` → fréquence cardiaque.
- `diagnostiquer(bpm)` → `{ etat, texte, couleur }` (normal / tachy / brady…).
- `trouverSommetProche(valeurs, index, demiFenetre)` → cale un clic sur le pic le plus proche.
- `evaluerSelection(picsVrais, picsChoisis, fe, tolerance)` → `{ total, trouves, manques, faux }`.

⚠️ Le bas de `signaux.js` contient aussi des helpers **spécifiques à signal.html** (jeu de
swipe, audio, spectre/animation). Ne casse pas ces parties en touchant à la lib.

Note pour l'agent : pour des rythmes lents (brady), rapides (tachy), chaotiques
(fibrillation) ou avec anomalies isolées (extrasystoles), utilise ou étends `genererECG`.

---

## 9. Idée directrice à poursuivre & TODO

**Objectif.** Enrichir le site avec **4 nouveaux modules bonus** pour étoffer l'atelier (les
élèves adorent les bonus, et on veut occuper davantage la séance — cf. §1). **Règle de
placement commune** : un bonus se débloque **après une section obligatoire** (même logique
que les bonus existants, en branche depuis le parcours), sauf mention contraire ci-dessous.
Tous restent **optionnels** (ne conditionnent pas le diplôme) et **simulés** (cf. §2).

Répartition, placement et intentions :

- **Réduction de bruit** — *Dima (HD)*. Placement : **juste après `signal.html`**
  (« C'est quoi un signal ? »). Idée : montrer/entendre comment on retire le bruit d'un son
  pour ne garder que l'utile. ⚠️ Veiller à un **angle distinct** de la section « bruit »
  déjà présente dans `signal.html` et du bonus « Piano Anti-Parasite » (éviter le doublon ;
  ici se concentrer sur le *débruitage* d'un son réel, comparaison avant/après).
- **Paiement sans contact** — *Dima (HD)*. Placement : **juste après `metiers.html`**
  (« Les métiers de l'ingénieur »). Idée : illustrer un échange d'information à très courte
  distance (principe NFC) comme exemple de télécom du quotidien. ⚠️ **Simulation uniquement**
  (pas de Web NFC, pas de permission) : animation du principe carte ↔ terminal.
- **Sonar & Radar** — *Damien*. Placement : **déblocage caché (Easter Egg) sur l'accueil `index.html`** (ex: zone cliquable invisible dans un coin qui plonge l'écran dans le noir). 
  Le module se déroule en 2 phases :
  - **Phase 1 (Sonar)** : Écran noir, la souris crée un halo lumineux. L'élève trouve une console, lance des pings sonores, et calcule la distance (d = v*t/2) d'un sous-marin mobile en fonction du temps de retour de l'écho.
  - **Phase 2 (Radar)** : Le décor s'éclaire (océan). Le radar balaie la zone en continu. Un graphique Plotly trace la vitesse du sous-marin en temps réel. L'élève pose des obstacles (filets) : la distance entre deux pings change, la vitesse chute sur le graphique.
  ⚠️ **Contrainte de développement** : Ne traiter QUE ce module. Ignorer les modules de Dima (Réduction de bruit, Paiement sans contact).
- **Types de filtres et leurs effets sur un son** — *Damien*. Placement : déblocage par court-circuit (easter egg) depuis le module Piano Anti-Parasite existant. (À développer plus tard, ne pas s'en occuper pour l'instant).

**Contraintes pour TOUS les nouveaux bonus** : statique / hors-ligne / `file://`,
`sessionStorage`, **règle des badges (§6)**, simulations pédagogiques (pas de matériel/API à
permission), langage seconde + tutoiement + ton « fait par des étudiants », **aucune
manipulation de code par l'élève**, réutilisation du design system (§5).

**À rediscuter (Damien)** : la mécanique de déblocage « spéciale » de **Radar** et
**Types de filtres** (pas une simple branche après un nœud) — concept à arrêter avant
implémentation.

---

## 10. Conventions à respecter quand tu modifies

- Reste **statique / hors-ligne / `file://`** ; pas de réseau, pas de build, pas de framework.
- `sessionStorage` (jamais `localStorage`) pour la progression.
- **Règle des badges** (§6) : liens de nav → `index.html` nu ; `?badge=<id>` seulement sur
  le bouton de fin de module.
- Réutilise les variables et classes de `style.css` ; mets le spécifique dans le `<style>`
  de la page, le réutilisable dans `style.css` (bloc commenté).
- Verrouillage linéaire des étapes par défaut ; déverrouillage complet une fois le badge du
  module obtenu (mode révision) — cohérent avec ce qui est fait dans le Labo.
- Public = lycéens de seconde : langage simple, tutoiement, ton « fait par des étudiants »,
  zéro jargon non expliqué, aucune manipulation de code par l'élève.
- Teste tes modifications dans le navigateur (oscilloscope animé, curseurs réactifs,
  verrouillage, badge attribué uniquement en fin de parcours).

## 11. Dernières implémentations : Audio & Visual FX (Game Feel)

### 1. Moteur Audio (`progression.js`)
* **Centralisation :** Le dictionnaire `SONS` pointe désormais vers les assets locaux définitifs (`pop`, `erreur`, `succes`, `mini_succes`, `swipe`, `badge`, `trace`, `victoire_module`, `diplome`).
* **Validation :** Remplacement de l'ancien son de succès par `badge.mp3` (effet "Level Up" de 2-3s) lors du déblocage d'un module.

### 2. Intégration sonore par module
* **Mini-jeu Tinder (`signal.html` & `signaux.js`) :** Câblage millimétré de `swipe.mp3` à la prise en main des cartes, `mini_succes.mp3` ou `erreur.mp3` à la validation, et `succes.mp3` à l'écran de score final.
* **Anatomie & ECG (`coeur.html`) :** "Pops" d'interface très doux pour l'exploration anatomique. Bip de moniteur médical synchronisé dynamiquement avec le passage sur le complexe QRS lors de l'animation de lecture.
* **Carte interactive (`index.html`) :** Synchronisation du sifflement électrique (`trace.mp3`) pendant l'animation de tracé des câbles. Ajout des sons d'explosions (`victoire_module`) et de pop d'interface sur le déverrouillage des branches principales ET des branches bonus.

### 3. Effets Visuels (Carte de progression)
* **Énergie continue :** Ajout d'une animation CSS (`flux-circulation`) sur les SVG des chemins validés. Les câbles verts font transiter des pointillés lumineux continus (`#7effc5`) pour simuler un circuit vivant.
* **Fade-in organique :** L'apparition des flux continus se fait avec une transition d'opacité de 1.5s (effet de chauffe) pour éviter un "spawn" brutal des particules.
* **Interactivité du background (En cours) :** Préparation d'un `<canvas>` en `z-index: -1` sur l'accueil pour générer un halo lumineux au survol de la souris et une onde de choc (sonar) au clic.


## Séquence Finale : Le QTE du Diplôme (Game Design)

### Concept & Objectif
La remise du diplôme n'est pas une simple redirection de page. Elle prend la forme d'un mini-jeu de type "Quick Time Event" (QTE) directement sur la page d'accueil. L'étudiant doit "compiler" et "forger" numériquement son diplôme via une série d'interactions d'arcade avant d'appeler l'animateur.

### Architecture
- **L'outil Admin (`diplome.html`) :** Reste intact. C'est le template "hors-ligne" utilisé par les animateurs pour imprimer les vrais diplômes papier (A4 Paysage) en amont ou pendant l'atelier.
- **L'interface Élève (`index.html`) :** Importe et adapte les variables CSS dorées et le design de `diplome.html` pour créer un rendu premium sur la carte finale.

### Déroulé technique du mini-jeu (dans `index.html`)
La fonction `debloquerDiplome()` déclenche la chorégraphie suivante :
1. **Synchronisation (Timing) :** Une jauge oscillante. L'utilisateur doit cliquer au bon moment. Action : Joue `trace.mp3` et révèle le fond ECG du diplôme.
2. **Compilation (Mashing) :** L'utilisateur doit cliquer 6 fois très rapidement. Action : Joue `pop.mp3` à chaque clic et affiche les 6 badges de compétences en transparence.
3. **Scellage (Hold) :** L'utilisateur doit maintenir le clic enfoncé pendant 1.5s pour remplir une barre de pression. Action : Joue `diplome.mp3`, appelle `lancerConfettis()` et révèle la carte finale.

### Rendu Final
Le diplôme apparaît avec un champ `contenteditable` ou `<input>` pour que l'élève tape son prénom (pure satisfaction visuelle). Il est accompagné d'un Call-To-Action (CTA) clignotant lui demandant de lever la main pour qu'un animateur vienne lui remettre la version papier officielle.
