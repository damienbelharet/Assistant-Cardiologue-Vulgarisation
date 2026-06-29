# ❤️ Lis un cœur

### Mission ECG — En quête au cœur du signal

> Un atelier interactif pour faire découvrir les télécommunications et le traitement du signal à des lycéens de seconde, à travers l'électrocardiogramme (ECG).

Projet de stage — **ENSEIRB-MATMECA**, Département Télécommunications.
Site en ligne : **[ecg.damienbelharet.com](https://ecg.damienbelharet.com)**

---

## 🎯 De quoi s'agit-il ?

Nous sommes un binôme d'étudiants de l'ENSEIRB-MATMECA. Pendant notre stage, nous accueillons des lycéens de seconde pour leur faire découvrir notre école, les télécommunications et le traitement du signal.

Pour rendre ça concret, on s'appuie sur un projet réalisé cette année : **l'analyse automatique de l'ECG**, c'est-à-dire l'enregistrement de l'activité électrique du cœur. L'idée est de montrer comment un ordinateur arrive à « lire » un cœur et à repérer une maladie, et d'expliquer que c'est exactement la même logique que dans les télécoms : **capter une information, la nettoyer, en extraire le sens.**

Ce dépôt contient le **site web interactif** qui sert de support à l'atelier.

---

## 🚀 Ouvrir le site

Aucune installation nécessaire.

- **En ligne :** [ecg.damienbelharet.com](https://ecg.damienbelharet.com)
- **En local :** double-cliquez sur `index.html` (testé sur Firefox et Chrome).

Le site est **100 % statique et fonctionne hors-ligne** : aucun serveur, aucun `npm install`, aucune dépendance réseau. Toutes les polices et bibliothèques sont incluses dans le dépôt. C'est volontaire : l'atelier doit pouvoir tourner même si le wifi de la salle tombe.

---

## 📋 Le cadre en bref

| Élément | Détail |
|---|---|
| **Public** | 2 lycéens de seconde par session (aucune connaissance en informatique ni en programmation) |
| **Durée** | 1 bloc de 3 h |
| **Lieu** | Salle informatique de l'école (un ordinateur par élève) |
| **Support principal** | Ce site web interactif |
| **Contrainte importante** | Les élèves ne codent pas : ils cliquent sur des boutons et bougent des curseurs |
| **Matériel physique** | Une montre connectée reliée à un téléphone, pour la démo en direct. Le reste de l'atelier utilise des signaux déjà enregistrés. |

---

## 🏆 Objectifs pédagogiques

À la fin de la session, chaque lycéen doit :

- avoir compris ce qu'est **un signal** et ce qu'est **le bruit** ;
- avoir compris **à quoi sert un filtre** (nettoyer un signal) ;
- savoir qu'un **ECG est un signal**, composé d'ondes caractéristiques (les ondes **P, QRS, T**) ;
- avoir réussi, **par lui-même**, à transformer un signal cardiaque « sale » en un diagnostic ;
- repartir avec **l'envie d'en savoir plus** sur nos métiers.

---

## 🧵 Le fil rouge : « Devenez ingénieurs biomédicaux »

> « Aujourd'hui, vous devenez ingénieurs biomédicaux. Votre mission : construire l'outil qui lit un cœur et détecte une maladie. Tout ce qu'on apprend sert à cette mission. »

Chaque notion (signal, bruit, filtre, cœur) est une **pièce que les élèves débloquent** sur une carte de progression, pour accomplir leur mission finale : diagnostiquer un patient et obtenir leur diplôme.

---

## 🌐 Structure du site

Le parcours principal suit le déroulé de l'atelier — une idée par page :

| Page | Rôle |
|---|---|
| `index.html` | Accueil, carte de progression, badges, diplôme final |
| `signal.html` | Module 1 — C'est quoi un signal ? (amplitude, fréquence, période, bruit, échantillonnage) |
| `coeur.html` | Module 2 — Le cœur et l'ECG (anatomie, ondes P-QRS-T) |
| `ecouter.html` | Module 3 — Écouter le cœur (signaux réels, premières pathologies) |
| `labo.html` | Module 4 — Le Labo ECG : capter, nettoyer, zoomer, repérer les battements *(cœur de la séance)* |
| `pathologies.html` | Module 5 — Détective des pathologies (diagnostic, patient mystère) |
| `metiers.html` | Module 6 — Les métiers de l'ingénieur (réseaux, génie logiciel) |

Et des modules **bonus**, à débloquer en explorant la carte :

| Page | Bonus |
|---|---|
| `spectro.html` | La photo du son (spectrogramme) |
| `reels.html` | Coulisses Matlab (3 vrais cœurs) |
| `montre.html` | La montre du sang (photopléthysmographie) |
| `piano.html` | Piano Anti-Parasite (filtrage audio) |
| `nfc.html` | Paiement sans contact (principe du NFC) |
| `foot.html` | Le ballon connecté — Coupe du Monde, capteurs embarqués et télécommunication |
| `radar.html` | Bonus secret — déblocage non conventionnel sur la carte d'accueil |

### Fichiers partagés

| Fichier | Rôle |
|---|---|
| `progression.js` | Gestion des badges, des sons et des animations (confettis, toasts) — `sessionStorage` |
| `signaux.js` | Bibliothèque commune : génération et traitement de signaux ECG |
| `style.css` | Design system commun à toutes les pages (thème « papier ECG ») |
| `fonts/`, `lib/` | Polices et bibliothèques embarquées localement (aucun CDN) |

---

## ⚙️ Choix techniques

- **Aucun framework** : HTML / CSS / JavaScript natifs.
- **`sessionStorage`**, pas `localStorage` : la progression d'un élève se réinitialise volontairement à la fermeture de l'onglet, pour repartir propre à chaque session.
- **Aucune dépendance réseau** : la seule bibliothèque externe (Plotly, pour les graphes du Labo) est embarquée dans `lib/`.
- **Badges & progression** géré par `progression.js` : un badge n'est attribué qu'à la fin réelle d'un module, jamais en navigant simplement vers l'accueil.

---

## 🩺 Les pathologies abordées

- **Tachycardie** = cœur trop rapide (plus de 100 BPM)
- **Bradycardie** = cœur trop lent (moins de 60 BPM)
- **Extrasystole** = un battement isolé en avance
- **Fibrillation** = rythme chaotique, où les ondes nettes disparaissent

---

## ⏱️ Déroulé indicatif de l'atelier

| Bloc | En une phrase |
|---|---|
| **0 — Accueil + Démo + Mission** | On se présente, on surprend avec une démo en direct, on lance la mission |
| **1 — C'est quoi un signal ?** | Notions de base, bruit, filtres |
| *Pause* | — |
| **2 — Le cœur et l'ECG** | Biologie express, ondes P-QRS-T |
| **3 — Le Labo ECG** | Les élèves nettoient eux-mêmes un signal cardiaque *(cœur de la séance)* |
| *Pause* | — |
| **4 — Détective des pathologies** | Diagnostic, patient mystère |
| **5 — Métiers de l'ingénieur** | Réseaux, génie logiciel, et après ? |
| **Clôture** | Remise à chaque élève d'un diplôme « ingénieur biomédical junior », questions, retours |

### Les 3 règles de rythme

1. **On alterne en permanence** — jamais plus de 10-15 min d'explication sans manipulation par les élèves.
2. **On montre avant d'expliquer** — d'abord une démo qui surprend, ensuite le « pourquoi ».
3. **On change de posture** — debout au tableau, assis à l'écran, et des pauses régulières.

---

## 👥 Auteurs

**Damien Belharet** & **Dima Husseini** — Département Télécommunications, ENSEIRB-MATMECA.

---

*Projet réalisé et testé en conditions réelles avec plusieurs groupes de lycéens.*
