# FrancoPrép — Plateforme SaaS pédagogique (Langue et littérature françaises, Maroc)

Plateforme complète destinée à l’enseignant du secondaire qualifiant : planification annuelle,
modules et séquences, emploi du temps, cahier journal automatique, cahier de textes, générateur de
cours, évaluations corrigées, quiz autocorrigés, banque de ressources, progression, rapports et
exports PDF / Word. Conforme aux **Orientations pédagogiques marocaines de 2007**.

## 1. Architecture générale

- **Frontend / Backend** : Next.js 16 (App Router), React 19, TypeScript, Tailwind CSS v4.
- **Données** : PostgreSQL + Drizzle ORM (`src/db/schema.ts`).
- **API** : Server Actions (`src/app/actions.ts`) + routes REST (`/api/ai`, `/api/export`, `/api/quiz/attempt`, `/api/health`).
- **Authentification** : e-mail / mot de passe, hachage `scrypt` + sel, sessions en base, cookie `httpOnly` `SameSite=Lax`.
- **Multi-tenant** : toutes les tables métier portent un `user_id` ; chaque requête est filtrée par l’enseignant connecté.
- **PWA** : `public/manifest.webmanifest` + `public/sw.js` (consultation hors connexion des pages déjà visitées).

## 2. Schéma de base de données

`users`, `sessions`, `classes`, `modules`, `sequences`, `lessons` (fiches), `timetable_entries`,
`journal_entries`, `textbook_entries`, `assessments`, `assessment_results`, `quizzes`,
`quiz_questions`, `quiz_attempts`, `resources`, `reminders`.

## 3. Pages

`/login` · `/tableau-de-bord` · `/classes` · `/classes/[id]` · `/emploi-du-temps` ·
`/cahier-journal` · `/cahier-textes` · `/modules` · `/modules/[id]` · `/sequences` · `/fiches` ·
`/fiches/nouveau` (générateur) · `/fiches/[id]` · `/evaluations` · `/evaluations/[id]` · `/quiz` ·
`/quiz/[id]` · `/quiz-eleve/[code]` (accès élève) · `/resultats` · `/ressources` · `/progression` ·
`/rapports` · `/parametres`.

## 4. Installation

```bash
npm install
cp .env.example .env            # DATABASE_URL=postgresql://...
npx drizzle-kit push            # création du schéma
npm run build && npm start
```

Au premier démarrage, l’application crée automatiquement les données initiales
(enseignant, 4 classes, 7 modules, 63 séquences, emploi du temps, 70 évaluations corrigées,
7 quiz, ressources, cahier journal des deux dernières semaines, cahier de textes).

**Compte de démonstration** : `m.elkhoumssi@menara.ma` / `Pedagogie2026`.

## 5. Guide d’utilisation rapide

1. **Emploi du temps** : saisissez vos créneaux (détection automatique des conflits).
2. **Cahier journal** : bouton « Générer le cahier journal de la semaine / du mois ». Les séances
   sont remplies à partir des séquences du module correspondant au niveau de la classe.
3. **Cahier de textes** : bouton « Synchroniser » — alimenté par les séances réalisées, modifiable.
4. **Générateur de cours** : choisissez niveau, classe, module, séquence, activité, support, durée →
   fiche en 16 rubriques, modifiable, imprimable, exportable.
5. **Évaluations** : 10 par module (sujet, barème, grille, corrigé). Génération, duplication, saisie
   des notes et statistiques.
6. **Quiz** : création, questions (QCM, vrai/faux, réponse courte, conjugaison, transformation…),
   lien et QR code pour les élèves, correction automatique et explications.
7. **Exports** : chaque document propose PDF (impression navigateur) et Word (`.doc`), avec en-tête
   institutionnel, pied de page et informations de l’enseignant.

## 6. Assistance intelligente

`/api/ai` utilise `OPENAI_API_KEY` si la variable est définie, sinon un moteur pédagogique interne
déterministe (fiches, objectifs, questions, exercices, corrigés, remédiation, résumés, quiz…).
L’enseignant peut toujours modifier, régénérer, accepter ou refuser une proposition.

## 7. Plan d’évolution

1. Gestion des élèves et bulletins par classe.
2. Interface arabe (RTL) et bascule de langue.
3. Téléversement de fichiers (PDF, audio, vidéo) dans la banque de ressources.
4. Espace établissement multi-enseignants + facturation en ligne.
5. Export `.docx` natif et génération PDF côté serveur.
