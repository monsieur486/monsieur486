# Refonte du site & CV — Laurent Touret (positionnement Java × IA agentique)

**Date :** 2026-06-10
**Auteur :** Laurent Touret (avec Claude Code)
**Statut :** Validé — prêt pour le plan d'implémentation

## 1. Objectif

Refondre entièrement le site personnel (`mr486.com`) et le CV PDF de Laurent Touret
pour refléter sa double identité professionnelle : **développeur Java back-end** ET
**praticien de l'IA agentique** (Claude Code au quotidien), à parts égales (50/50).

Le site actuel (thème Bootstrap 4 « Resume ») est daté, sa section réalisations est vide,
et le CV PDF affiche encore « Développeur Web Junior » sans aucune mention de l'IA.

### Critères de réussite

- Le positionnement Java × IA agentique est lisible dès le hero, sur le site et le CV.
- La section réalisations est remplie avec des projets réels et présentables.
- Le rôle de l'IA est explicite mais crédible (pas de survente).
- Le site se déploie tel quel dans l'infra existante (Docker + Apache, port 2024).
- Le CV PDF se régénère facilement et reste cohérent avec le site.

## 2. Décisions validées (brainstorming)

| Sujet | Décision |
|---|---|
| Positionnement | **50/50** : Java back-end et IA agentique sur un pied d'égalité |
| Contenu projets | Projets GitHub réels (`github.com/monsieur486`) — formation + perso |
| Tech du site | **Statique modernisé** : HTML/CSS/JS vanilla, **aucun build**, déployable tel quel |
| CV PDF | **Page HTML imprimable** (`cv.html`, format A4, print CSS) → Ctrl+P → PDF |
| Direction visuelle | **A · Terminal / Dev dark** — thème sombre, accents monospace & néon |

## 3. Direction visuelle (design system)

Thème « terminal de développeur », sombre, inspiré de l'esthétique GitHub dark / éditeur de code.

- **Couleurs**
  - Fond : `#010409` (page), `#0d1117` (cartes), `#161b22` (puces/tags)
  - Bordures : `#21262d` / `#30363d`
  - Texte : `#e6edf3` (principal), `#c9d1d9` (courant), `#8b949e` (atténué)
  - Accent **vert terminal** : `#7ee787` (prompts, CTA principal, Java/back-end)
  - Accent **bleu** : `#79c0ff` (titres de sous-sections, stacks)
  - Accent **orange** : `#ffa657` (dates, « × »)
  - Accent **violet IA** : `#d2a8ff` sur fond `#1c1429` / bordure `#5b3a8a` (tout ce qui touche l'IA agentique)
- **Typographie**
  - Titres / corps : `system-ui, sans-serif`
  - Accents techniques (prompts, labels de section, contacts) : police **monospace**
    (`'SF Mono', Menlo, Consolas, monospace`)
- **Motifs récurrents**
  - Labels de section en commentaire de code : `// compétences`, `// réalisations`
  - Hero en invite de commande : `$ whoami`, curseur clignotant `▊`
  - Trois pastilles macOS (rouge/jaune/vert) en tête des blocs « fenêtre »
  - Tags/puces arrondis pour les technologies
- **Responsive** : mobile-first ; grilles 2 colonnes qui passent en 1 colonne sur mobile.
- **Accessibilité** : contrastes AA respectés, navigation clavier, `aria-label` sur les icônes,
  `prefers-reduced-motion` respecté pour le curseur clignotant.

## 4. Architecture & structure des fichiers

Site statique mono-page (`index.html`) + une page CV imprimable (`cv.html`), servis par Apache.

```
www/
├── index.html              # page principale (sections ci-dessous)
├── cv.html                 # CV imprimable A4 (print CSS) — Ctrl+P → PDF
├── css/
│   ├── styles.css          # design system + styles écran (réécrit)
│   └── print.css           # styles d'impression du CV (A4, masquage navigation)
├── js/
│   └── scripts.js          # scroll fluide, nav active, curseur, année auto
├── data/
│   └── projets.js          # (optionnel) liste des projets en 1 endroit, rendue en JS
└── assets/
    ├── img/                # profile.jpg, favicon.ico (conservés)
    └── files/
        └── CV-Laurent-Touret.pdf   # PDF régénéré depuis cv.html
```

Décisions d'isolation :

- **`styles.css`** porte tout le design system (variables CSS `:root`) + le rendu écran.
- **`print.css`** est chargé uniquement par `cv.html` (`media="print"` + écran A4) ; il ne
  dépend que des variables/structure, pas du JS.
- **`scripts.js`** est non bloquant et purement progressif : le site reste lisible sans JS.
- **`data/projets.js`** (optionnel, à trancher au plan) : centralise les projets pour éviter
  la duplication entre `index.html` et `cv.html`. Si jugé trop lourd pour du statique, on
  duplique le contenu en dur — à décider lors du plan.

## 5. Contenu — sections du site (`index.html`)

Ordre validé en maquette :

1. **Navigation** (sticky, style barre de terminal) : `~/laurent-touret` + liens
   compétences · réalisations · expériences · formations · CV.
2. **Hero / Contact**
   - `$ whoami` → **Laurent Touret**
   - Sous-titre : `Développeur Java back-end × IA agentique`
   - Pitch (≈ 2 phrases) : diplômé Java OpenClassrooms, conçoit des back-ends Spring Boot
     robustes et orchestre l'IA agentique (Claude Code) au quotidien, du back au front.
   - CTA : « Voir mes réalisations → » (primaire vert) + « Télécharger le CV ».
   - Contacts (monospace) : Strasbourg · email · tél · GitHub · LinkedIn (icônes).
3. **Compétences** — 2 colonnes :
   - *Back-end & langages* : Java, Spring Boot, Microservices, SQL/MongoDB, Docker/K8s,
     PHP, Python, JS (tags). Méthodes : Agile/SCRUM, SOLID, TDD.
   - *⚡ Workflow IA agentique* (bloc violet) : Claude Code, prompt engineering,
     orchestration d'agents, développement full-stack assisté par IA, revue de code assistée.
4. **Réalisations** (remontée avant les expériences) :
   - **GestoMS** — vitrine ★ : générateur de plateforme microservices complète
     (Eureka, Gateway WebFlux, Keycloak/OAuth2, blacklist JWT Redis, RabbitMQ, batch,
     WebSocket, Spring Boot Admin). Lien GitHub. Ligne ⚡ « Conçu & itéré avec Claude Code ».
   - **Tarot Des Amis** — app déployée en prod sur **https://tda.mr486.com** : scores temps
     réel WebSocket, UI mobile-first (Spring Boot · Thymeleaf · WebSocket · Docker).
     Lien GitHub **+ lien « démo live »** vers tda.mr486.com.
     Ligne ⚡ « Front-end abordé grâce à l'IA ».
   - **gestozip** — démo : arborescence de fichiers + téléchargement ZIP (Spring Boot · REST).
   - **Projets de formation OpenClassrooms** : liste compacte avec liens (microservices,
     application web Java de A à Z, sécurisation back-end, tests Java, etc.).
   - Chaque carte : titre, description courte, tags techno, lien GitHub, et le cas échéant
     une ligne ⚡ sur l'apport de l'IA.
5. **Expériences professionnelles** (compactes) : OpenClassrooms (dev Java), Wild Code School,
   Ionos 1&1 (référent technique), Markanne (responsable info), Kredit Bank (technicien).
6. **Formations** : Développeur d'application Java — OpenClassrooms (diplôme niv. 6, 2022/2024) ;
   Développeur Web & Mobile — Wild Code School (niv. 5).
7. **Diplômes & certifications** : DECF (UV comptabilité/maths/éco), Baccalauréat G2 mention Bien.
8. **Pied de page** : lien CV, réseaux, année automatique.

### Note de véracité du contenu

Les libellés actuels du site contiennent des incohérences à corriger lors de l'implémentation
(ex. « Septembre 2020 - Actuel » pour OpenClassrooms alors que le CV indique 2022 ;
« applications mobiles » dans les aptitudes). Le contenu sera aligné sur les faits réels
fournis par Laurent et validé par lui avant mise en ligne. **Aucune compétence ou expérience
ne sera inventée.**

## 6. Contenu — CV imprimable (`cv.html`)

- Reprend les mêmes données que le site, mis en page pour **A4 une page** (recto, idéalement),
  débordement maîtrisé sur 2 pages si nécessaire.
- Structure type CV : en-tête (nom, titre Java × IA agentique, contacts), colonne
  compétences (dont bloc IA agentique), pitch, formations, expériences, diplômes.
- `print.css` : masque la navigation/CTA web, force fond blanc + texte sombre OU conserve
  une version sombre « print-friendly » (à trancher au plan ; par défaut **version claire
  imprimable** pour l'encre et la lisibilité ATS, tout en gardant l'accent visuel Java/IA).
- Régénération : ouvrir `cv.html` → Ctrl+P → « Enregistrer en PDF » → remplacer
  `assets/files/CV-Laurent-Touret.pdf`.
- **Lisibilité ATS** : le CV reste du vrai texte sélectionnable (pas une image), titres
  standards, pour passer les filtres automatiques de recrutement.

## 7. Déploiement

Inchangé : `docker-compose.yaml` build l'image Apache (`httpd`) et monte `./www`.
Aucun build front. `update.sh` (git pull + docker compose up) reste valable.
Vérifier que `cv.html` et les nouveaux assets sont bien copiés dans l'image.

## 8. Tests & vérification

Pas de framework de test pour un site statique ; vérification manuelle structurée :

- **Rendu visuel** : hero, compétences, réalisations, responsive mobile (≤ 480px),
  tablette, desktop. Validation via le navigateur (compagnon visuel ou `docker compose up`).
- **CV imprimable** : aperçu avant impression A4, vérifier pagination et lisibilité.
- **Liens** : tous les liens GitHub/démos/réseaux fonctionnent (pas de 404).
- **Accessibilité** : contraste, navigation clavier, `aria-label` icônes, lecteur d'écran
  sur le hero.
- **SEO/méta** : `<title>`, `og:title`/`og:description` corrigés (le `og:title` actuel
  vaut « Title Here »), `description`, JSON-LD Person à jour.
- **Performance** : envisager de remplacer le chargement Font Awesome JS (lourd, v5.13 via
  use.fontawesome) par des SVG inline ou une feuille CSS allégée — à trancher au plan.

## 9. Hors périmètre (YAGNI)

- Pas de CMS, pas de back-end, pas de base de données.
- Pas de framework JS (React/Astro) — choix statique assumé.
- Pas de blog ni de système multi-pages au-delà de `index.html` + `cv.html`.
- Pas de génération PDF automatisée (impression navigateur suffit).
- Pas d'internationalisation (FR uniquement).

## 10. Risques & points à trancher au plan

- **Visibilité réelle des dépôts** : confirmer lesquels sont publics/présentables avant
  de les lier (certains projets perso anciens ne doivent pas être mis en avant).
- **Démo Tarot Des Amis** : démo live confirmée → **https://tda.mr486.com** (à lier).
- **Duplication contenu** site/CV : `data/projets.js` partagé vs contenu dupliqué.
- **CV sombre vs clair à l'impression** : trancher (défaut : clair imprimable).
- **Font Awesome** : conserver vs remplacer par SVG (perf).
```
