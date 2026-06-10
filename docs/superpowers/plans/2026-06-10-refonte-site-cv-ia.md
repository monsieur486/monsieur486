# Refonte site & CV (Java × IA agentique) — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Refondre entièrement le site statique et le CV PDF de Laurent Touret pour afficher un positionnement 50/50 « Développeur Java back-end × IA agentique », avec une section réalisations remplie de projets réels.

**Architecture:** Site statique mono-page (`index.html`) + page CV imprimable (`cv.html`), HTML/CSS/JS vanilla **sans build**, design system « terminal / dev dark » centralisé dans des variables CSS. Servi tel quel par l'image Docker Apache existante.

**Tech Stack:** HTML5, CSS3 (variables custom, grid/flex), JavaScript vanilla, Font Awesome (icônes), Docker + Apache (httpd) existant.

---

## Conventions de vérification (lire avant de commencer)

Pas de test runner pour un site statique. Chaque tâche se vérifie **dans le navigateur** :

```bash
# Serveur de prévisualisation local (depuis la racine du repo)
python3 -m http.server 8000 --directory www
# Puis ouvrir http://localhost:8000  (index.html) ou http://localhost:8000/cv.html
```

Laisser ce serveur tourner pendant toute l'implémentation (un seul terminal dédié).
« Expected » = ce que tu dois constater à l'écran après rechargement (Ctrl+Maj+R).

**Palette du design system** (rappel, définie en Task 1) :
fond `#010409`/`#0d1117`/`#161b22` · bordures `#21262d`/`#30363d` · texte `#e6edf3`/`#c9d1d9`/`#8b949e` ·
accent vert `#7ee787` · bleu `#79c0ff` · orange `#ffa657` · violet IA `#d2a8ff` (fond `#1c1429`, bordure `#5b3a8a`).

**Contenu de référence** : voir `docs/superpowers/specs/2026-06-10-refonte-site-cv-ia-design.md`.

---

## File Structure

| Fichier | Responsabilité | Action |
|---|---|---|
| `www/css/styles.css` | Design system (variables) + styles écran de toutes les sections | Réécriture complète |
| `www/css/print.css` | Styles d'impression du CV (A4, version claire) | Création |
| `www/index.html` | Page principale mono-page (8 sections) | Réécriture complète |
| `www/cv.html` | CV imprimable A4 (Ctrl+P → PDF) | Création |
| `www/js/scripts.js` | Scroll fluide, nav active, curseur clignotant, année auto | Réécriture complète |
| `www/assets/img/*` | profile.jpg, favicon.ico | Conservés tels quels |
| `Dockerfile` / `docker-compose.yaml` | Déploiement (copie tout `www/`) | Vérification seule |

> Décision tranchée : **pas** de `data/projets.js`. Pour 2 pages statiques, on duplique le
> contenu projets entre `index.html` et `cv.html` (plus simple, zéro JS de rendu, YAGNI).

---

## Task 1: Design system CSS (fondations)

**Files:**
- Modify: `www/css/styles.css` (réécriture complète)

- [ ] **Step 1: Remplacer entièrement le contenu de `www/css/styles.css`**

```css
/* ============================================================
   Laurent Touret — Design system « Terminal / Dev dark »
   ============================================================ */
:root {
  --bg:        #010409;
  --surface:   #0d1117;
  --surface-2: #161b22;
  --border:    #21262d;
  --border-2:  #30363d;
  --text:      #e6edf3;
  --text-soft: #c9d1d9;
  --text-mute: #8b949e;
  --green:     #7ee787;
  --blue:      #79c0ff;
  --orange:    #ffa657;
  --ia:        #d2a8ff;
  --ia-bg:     #1c1429;
  --ia-border: #5b3a8a;
  --mono: 'SF Mono', 'JetBrains Mono', Menlo, Consolas, monospace;
  --sans: system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
  --maxw: 980px;
  --radius: 8px;
}

* { box-sizing: border-box; margin: 0; padding: 0; }

html { scroll-behavior: smooth; }

body {
  background: var(--bg);
  color: var(--text-soft);
  font-family: var(--sans);
  line-height: 1.65;
  -webkit-font-smoothing: antialiased;
}

a { color: var(--blue); text-decoration: none; }
a:hover { text-decoration: underline; }

h1, h2, h3 { color: var(--text); line-height: 1.15; font-weight: 800; }

/* Conteneur de section */
.section { max-width: var(--maxw); margin: 0 auto; padding: 56px 24px; }
.section + .section { border-top: 1px solid var(--border); }

/* Label de section style commentaire de code */
.section-label {
  font-family: var(--mono);
  color: var(--green);
  font-size: .85rem;
  margin-bottom: 22px;
  display: block;
}

/* Tags / puces technologiques */
.tags { display: flex; flex-wrap: wrap; gap: 8px; }
.tag {
  background: var(--surface-2);
  border: 1px solid var(--border-2);
  color: var(--text-soft);
  font-size: .8rem;
  padding: 5px 12px;
  border-radius: 6px;
}
.tag--ia {
  background: var(--ia-bg);
  border-color: var(--ia-border);
  color: var(--ia);
}

/* Ligne « apport de l'IA » sur les cartes projet */
.ia-note {
  color: var(--ia);
  font-size: .82rem;
  margin-top: 10px;
  font-family: var(--mono);
}
.ia-note::before { content: "⚡ "; }

/* Boutons */
.btn {
  display: inline-block;
  font-size: .9rem;
  font-weight: 700;
  padding: 10px 18px;
  border-radius: var(--radius);
  border: 1px solid transparent;
  cursor: pointer;
}
.btn--primary { background: var(--green); color: #04140a; }
.btn--primary:hover { filter: brightness(1.08); text-decoration: none; }
.btn--ghost { border-color: var(--border-2); color: var(--text-soft); }
.btn--ghost:hover { background: var(--surface-2); text-decoration: none; }

/* Pastilles macOS pour blocs « fenêtre » */
.dots { display: flex; gap: 7px; margin-bottom: 16px; }
.dots span { width: 11px; height: 11px; border-radius: 50%; display: inline-block; }
.dots span:nth-child(1) { background: #ff5f56; }
.dots span:nth-child(2) { background: #ffbd2e; }
.dots span:nth-child(3) { background: #27c93f; }

@media (max-width: 640px) {
  .section { padding: 40px 18px; }
}
```

- [ ] **Step 2: Vérifier le chargement (test temporaire)**

Créer un fichier jetable `www/_test.html` :

```html
<!DOCTYPE html><html lang="fr"><head><meta charset="utf-8">
<link href="css/styles.css" rel="stylesheet"></head>
<body><div class="section"><span class="section-label">// test</span>
<div class="dots"><span></span><span></span><span></span></div>
<div class="tags"><span class="tag">Java</span><span class="tag tag--ia">Claude Code</span></div>
<p class="ia-note">Conçu avec l'IA</p>
<a class="btn btn--primary" href="#">Bouton primaire</a></div></body></html>
```

Run: `python3 -m http.server 8000 --directory www` puis ouvrir `http://localhost:8000/_test.html`
Expected: fond quasi noir, label vert `// test`, 3 pastilles colorées, tag « Java » gris + tag « Claude Code » violet, ligne « ⚡ Conçu avec l'IA » violette, bouton vert.

- [ ] **Step 3: Supprimer le fichier de test**

Run: `rm www/_test.html`

- [ ] **Step 4: Commit**

```bash
git add www/css/styles.css
git commit -m "feat(site): design system terminal/dev dark (variables + composants)"
```

---

## Task 2: Squelette `index.html` + `<head>` + navigation

**Files:**
- Modify: `www/index.html` (réécriture complète — on construit par étapes, sections vides pour l'instant)

- [ ] **Step 1: Réécrire `www/index.html` avec head corrigé, nav et sections vides**

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="utf-8"/>
    <meta content="width=device-width, initial-scale=1" name="viewport"/>
    <meta content="Laurent Touret — Développeur Java back-end &amp; praticien de l'IA agentique (Claude Code). Spring Boot, microservices, Docker." name="description"/>
    <meta content="Laurent Touret" name="author"/>
    <meta content="Laurent Touret — Développeur Java back-end × IA agentique" property="og:title"/>
    <meta content="website" property="og:type"/>
    <meta content="https://mr486.com/" property="og:url"/>
    <meta content="https://mr486.com/assets/img/profile.jpg" property="og:image"/>
    <meta content="Développeur Java back-end qui conçoit des back-ends Spring Boot robustes et orchestre l'IA agentique au quotidien." property="og:description"/>
    <script type="application/ld+json">
        {
            "@context": "https://schema.org/",
            "@type": "Person",
            "name": "Laurent Touret",
            "jobTitle": "Développeur d'application Java",
            "url": "https://mr486.com",
            "image": "https://mr486.com/assets/img/profile.jpg",
            "sameAs": [
                "https://www.linkedin.com/in/laurent-touret/",
                "https://github.com/monsieur486"
            ]
        }
    </script>
    <title>Laurent Touret — Développeur Java × IA agentique</title>
    <link href="assets/img/favicon.ico" rel="icon" type="image/x-icon"/>
    <link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;700&display=swap" rel="stylesheet"/>
    <script crossorigin="anonymous" src="https://use.fontawesome.com/releases/v5.13.0/js/all.js"></script>
    <link href="css/styles.css" rel="stylesheet"/>
</head>
<body>

<!-- Navigation -->
<nav class="topnav" id="topnav">
    <a class="topnav__brand" href="#hero">~/laurent-touret</a>
    <button class="topnav__toggle" id="navToggle" aria-label="Ouvrir le menu" aria-expanded="false">
        <i class="fas fa-bars"></i>
    </button>
    <ul class="topnav__links" id="navLinks">
        <li><a href="#competences">compétences</a></li>
        <li><a href="#realisations">réalisations</a></li>
        <li><a href="#experiences">expériences</a></li>
        <li><a href="#formations">formations</a></li>
        <li><a href="cv.html" target="_blank">CV ↓</a></li>
    </ul>
</nav>

<main>
    <section class="hero" id="hero"><!-- Task 3 --></section>
    <section class="section" id="competences"><!-- Task 4 --></section>
    <section class="section" id="realisations"><!-- Task 5 --></section>
    <section class="section" id="experiences"><!-- Task 6 --></section>
    <section class="section" id="formations"><!-- Task 7 --></section>
    <section class="section" id="diplomes"><!-- Task 7 --></section>
</main>

<footer class="footer" id="footer"><!-- Task 8 --></footer>

<script src="js/scripts.js"></script>
</body>
</html>
```

- [ ] **Step 2: Ajouter les styles de navigation à la fin de `www/css/styles.css`**

```css
/* ---- Navigation ---- */
.topnav {
  position: sticky; top: 0; z-index: 50;
  display: flex; align-items: center; gap: 20px;
  padding: 12px 24px;
  background: rgba(13, 17, 23, .85);
  backdrop-filter: blur(8px);
  border-bottom: 1px solid var(--border);
  font-family: var(--mono); font-size: .85rem;
}
.topnav__brand { color: var(--green); font-weight: 700; }
.topnav__brand:hover { text-decoration: none; }
.topnav__links { list-style: none; display: flex; gap: 18px; margin-left: auto; }
.topnav__links a { color: var(--text-mute); }
.topnav__links a:hover, .topnav__links a.is-active { color: var(--text); text-decoration: none; }
.topnav__toggle { display: none; background: none; border: 0; color: var(--text-soft); font-size: 1.2rem; cursor: pointer; }

@media (max-width: 720px) {
  .topnav__toggle { display: block; margin-left: auto; }
  .topnav__links {
    position: absolute; top: 100%; left: 0; right: 0;
    flex-direction: column; gap: 0; margin: 0;
    background: var(--surface); border-bottom: 1px solid var(--border);
    max-height: 0; overflow: hidden; transition: max-height .25s ease;
  }
  .topnav__links.is-open { max-height: 320px; }
  .topnav__links li { padding: 0; }
  .topnav__links a { display: block; padding: 14px 24px; border-top: 1px solid var(--border); }
}
```

- [ ] **Step 3: Vérifier dans le navigateur**

Ouvrir `http://localhost:8000/`
Expected: barre sticky en haut avec `~/laurent-touret` en vert à gauche et les liens à droite (gris). Réduire la fenêtre < 720px : les liens disparaissent, une icône ☰ apparaît (le menu déroulant sera câblé en Task 8). Le reste de la page est vide (normal).

- [ ] **Step 4: Commit**

```bash
git add www/index.html www/css/styles.css
git commit -m "feat(site): squelette index.html, head/SEO corrigés et navigation"
```

---

## Task 3: Section Hero / Contact

**Files:**
- Modify: `www/index.html` (remplir `<section class="hero" id="hero">`)
- Modify: `www/css/styles.css` (ajouter styles hero)

- [ ] **Step 1: Remplir la section hero dans `www/index.html`**

Remplacer `<section class="hero" id="hero"><!-- Task 3 --></section>` par :

```html
<section class="hero" id="hero">
    <div class="hero__inner">
        <div class="dots"><span></span><span></span><span></span></div>
        <p class="hero__cmd"><span class="hero__prompt">$</span> <span class="hero__fn">whoami</span></p>
        <h1 class="hero__name">Laurent <span class="hero__accent">Touret</span></h1>
        <p class="hero__role">Développeur Java back-end <span class="hero__x">×</span> IA agentique</p>
        <p class="hero__pitch">
            Diplômé Développeur d'application Java (OpenClassrooms). Je conçois des back-ends
            Spring Boot robustes et j'orchestre l'IA agentique (Claude Code) au quotidien
            pour livrer plus vite — du back-end jusqu'au front.
        </p>
        <div class="hero__cta">
            <a class="btn btn--primary" href="#realisations">Voir mes réalisations →</a>
            <a class="btn btn--ghost" href="cv.html" target="_blank">Télécharger le CV</a>
        </div>
        <p class="hero__contact">
            <span><i class="fas fa-map-marker-alt"></i> Strasbourg</span>
            <a href="mailto:laurent.touret@gmail.com"><i class="far fa-envelope"></i> laurent.touret@gmail.com</a>
            <a href="tel:+33781089873"><i class="fas fa-phone-volume"></i> +33 7 81 08 98 73</a>
        </p>
        <div class="hero__social">
            <a href="https://github.com/monsieur486" target="_blank" aria-label="GitHub"><i class="fab fa-github"></i></a>
            <a href="https://www.linkedin.com/in/laurent-touret" target="_blank" aria-label="LinkedIn"><i class="fab fa-linkedin-in"></i></a>
        </div>
    </div>
</section>
```

- [ ] **Step 2: Ajouter les styles hero à la fin de `www/css/styles.css`**

```css
/* ---- Hero ---- */
.hero { padding: 64px 0 8px; }
.hero__inner { max-width: var(--maxw); margin: 0 auto; padding: 0 24px; }
.hero__cmd { font-family: var(--mono); font-size: 1rem; }
.hero__prompt { color: var(--green); }
.hero__fn { color: var(--blue); }
.hero__name { font-size: clamp(2.4rem, 7vw, 4rem); letter-spacing: -1.5px; margin: 6px 0 4px; }
.hero__accent { color: var(--green); }
.hero__role { font-family: var(--mono); color: var(--text-mute); font-size: clamp(1rem, 3vw, 1.25rem); }
.hero__x { color: var(--orange); }
.hero__cursor { color: var(--green); animation: blink 1.1s steps(1) infinite; }
@keyframes blink { 50% { opacity: 0; } }
@media (prefers-reduced-motion: reduce) { .hero__cursor { animation: none; } }
.hero__pitch { max-width: 620px; margin: 22px 0; color: var(--text-soft); }
.hero__cta { display: flex; flex-wrap: wrap; gap: 12px; margin-bottom: 22px; }
.hero__contact { display: flex; flex-wrap: wrap; gap: 20px; font-family: var(--mono); font-size: .85rem; color: var(--text-mute); }
.hero__contact a { color: var(--text-mute); }
.hero__contact a:hover { color: var(--text); text-decoration: none; }
.hero__social { display: flex; gap: 16px; margin-top: 20px; font-size: 1.4rem; }
.hero__social a { color: var(--text-mute); }
.hero__social a:hover { color: var(--green); }
```

- [ ] **Step 3: Vérifier dans le navigateur**

Ouvrir `http://localhost:8000/`
Expected: `$ whoami` (vert + bleu), grand titre « Laurent Touret » (Touret en vert), sous-titre monospace « Développeur Java back-end × IA agentique » (× en orange), pitch, 2 boutons (vert plein + contour), ligne de contacts monospace avec icônes, icônes GitHub/LinkedIn qui deviennent vertes au survol. Redimensionner : le titre rétrécit proprement (clamp), les boutons passent à la ligne.

- [ ] **Step 4: Commit**

```bash
git add www/index.html www/css/styles.css
git commit -m "feat(site): section hero (positionnement Java x IA agentique)"
```

---

## Task 4: Section Compétences (2 colonnes + bloc IA)

**Files:**
- Modify: `www/index.html` (remplir `#competences`)
- Modify: `www/css/styles.css` (styles compétences)

- [ ] **Step 1: Remplir la section compétences dans `www/index.html`**

Remplacer `<section class="section" id="competences"><!-- Task 4 --></section>` par :

```html
<section class="section" id="competences">
    <span class="section-label">// compétences</span>
    <h2>Compétences</h2>
    <div class="skills">
        <div class="skills__col">
            <h3 class="skills__title skills__title--back">Back-end &amp; langages</h3>
            <div class="tags">
                <span class="tag">Java</span>
                <span class="tag">Spring Boot</span>
                <span class="tag">Microservices</span>
                <span class="tag">REST</span>
                <span class="tag">SQL</span>
                <span class="tag">MongoDB</span>
                <span class="tag">Docker</span>
                <span class="tag">Kubernetes</span>
                <span class="tag">PHP</span>
                <span class="tag">Python</span>
                <span class="tag">JavaScript</span>
                <span class="tag">Linux</span>
            </div>
            <h3 class="skills__title skills__title--back" style="margin-top:22px">Méthodes</h3>
            <div class="tags">
                <span class="tag">Agile / SCRUM</span>
                <span class="tag">SOLID</span>
                <span class="tag">TDD</span>
                <span class="tag">CI/CD</span>
            </div>
        </div>
        <div class="skills__col skills__col--ia">
            <h3 class="skills__title skills__title--ia">⚡ Workflow IA agentique</h3>
            <div class="tags">
                <span class="tag tag--ia">Claude Code</span>
                <span class="tag tag--ia">Prompt engineering</span>
                <span class="tag tag--ia">Orchestration d'agents</span>
                <span class="tag tag--ia">Dev full-stack assisté</span>
                <span class="tag tag--ia">Revue de code assistée</span>
                <span class="tag tag--ia">Génération de tests</span>
            </div>
            <p class="skills__ia-text">
                J'intègre l'IA agentique à mon flux de développement quotidien : conception,
                implémentation, tests et documentation. C'est ce qui m'a permis d'élargir mon
                périmètre du back-end vers le front-end.
            </p>
        </div>
    </div>
</section>
```

- [ ] **Step 2: Ajouter les styles compétences à la fin de `www/css/styles.css`**

```css
/* ---- Compétences ---- */
.skills { display: grid; grid-template-columns: 1fr 1fr; gap: 28px; margin-top: 8px; }
.skills__col--ia {
  background: linear-gradient(180deg, rgba(28,20,41,.6), transparent);
  border: 1px solid var(--ia-border);
  border-radius: var(--radius);
  padding: 20px;
}
.skills__title { font-size: 1rem; margin-bottom: 12px; }
.skills__title--back { color: var(--blue); }
.skills__title--ia { color: var(--ia); }
.skills__ia-text { margin-top: 16px; font-size: .9rem; color: var(--text-soft); }
@media (max-width: 720px) { .skills { grid-template-columns: 1fr; } }
```

- [ ] **Step 3: Vérifier dans le navigateur**

Ouvrir `http://localhost:8000/#competences`
Expected: titre « Compétences » sous le label vert `// compétences`. Deux colonnes : à gauche tags gris (Back-end & langages, Méthodes) ; à droite un encadré violet « ⚡ Workflow IA agentique » avec tags violets + paragraphe. En mobile (<720px) les colonnes s'empilent.

- [ ] **Step 4: Commit**

```bash
git add www/index.html www/css/styles.css
git commit -m "feat(site): section compétences avec bloc IA agentique"
```

---

## Task 5: Section Réalisations (projets réels)

**Files:**
- Modify: `www/index.html` (remplir `#realisations`)
- Modify: `www/css/styles.css` (styles cartes projet)

> **À valider par Laurent à l'exécution** : descriptions des projets de formation
> (basées sur les intitulés OpenClassrooms) et confirmation que chaque dépôt lié est public.

- [ ] **Step 1: Remplir la section réalisations dans `www/index.html`**

Remplacer `<section class="section" id="realisations"><!-- Task 5 --></section>` par :

```html
<section class="section" id="realisations">
    <span class="section-label">// réalisations</span>
    <h2>Réalisations</h2>

    <!-- Vitrine -->
    <article class="project project--feature">
        <div class="project__head">
            <h3>GestoMS — Générateur de plateforme microservices</h3>
            <span class="project__star">★ vitrine</span>
        </div>
        <p class="project__desc">
            Générateur produisant une plateforme microservices complète, clé en main :
            Eureka, Gateway WebFlux (avec révocation JWT réactive), Keycloak/OAuth2,
            blacklist JTI Redis, messagerie RabbitMQ, traitement batch, notifications
            WebSocket et Spring Boot Admin.
        </p>
        <div class="tags">
            <span class="tag">Spring Boot 3</span>
            <span class="tag">Keycloak / OAuth2</span>
            <span class="tag">Redis</span>
            <span class="tag">RabbitMQ</span>
            <span class="tag">WebFlux</span>
            <span class="tag">Docker Compose</span>
        </div>
        <p class="ia-note">Architecture conçue et itérée avec Claude Code</p>
        <div class="project__links">
            <a href="https://github.com/monsieur486/GestoMS" target="_blank"><i class="fab fa-github"></i> Code source</a>
        </div>
    </article>

    <!-- Grille des autres projets -->
    <div class="projects-grid">
        <article class="project">
            <h3>Tarot Des Amis 🃏</h3>
            <p class="project__desc">
                Application déployée en production : suivi de scores en temps réel via
                WebSocket, interface mobile-first. Démo live publique.
            </p>
            <div class="tags">
                <span class="tag">Spring Boot</span>
                <span class="tag">Thymeleaf</span>
                <span class="tag">WebSocket</span>
                <span class="tag">Docker</span>
            </div>
            <p class="ia-note">Front-end abordé grâce à l'IA</p>
            <div class="project__links">
                <a href="https://tda.mr486.com" target="_blank"><i class="fas fa-external-link-alt"></i> Démo live</a>
                <a href="https://github.com/monsieur486/newTarotDesAmis" target="_blank"><i class="fab fa-github"></i> Code</a>
            </div>
        </article>

        <article class="project">
            <h3>gestozip</h3>
            <p class="project__desc">
                Service Spring Boot qui construit une arborescence de répertoires et fichiers
                puis l'expose en téléchargement ZIP via un endpoint REST.
            </p>
            <div class="tags">
                <span class="tag">Spring Boot</span>
                <span class="tag">REST</span>
                <span class="tag">Java 17</span>
            </div>
            <p class="ia-note">Développé avec l'IA</p>
            <div class="project__links">
                <a href="https://github.com/monsieur486/gestozip" target="_blank"><i class="fab fa-github"></i> Code</a>
            </div>
        </article>
    </div>

    <!-- Projets de formation -->
    <h3 class="projects-subtitle">Projets de formation — OpenClassrooms (Développeur d'application Java)</h3>
    <ul class="formation-projects">
        <li>
            <a href="https://github.com/monsieur486/concevez_une_application_web_java_de_a_a_z" target="_blank">Concevez une application web Java de A à Z</a>
            — application web Spring Boot complète (architecture, persistance, vues).
        </li>
        <li>
            <a href="https://github.com/monsieur486/developpez_une_solution_en_microservices_pour_votre_client" target="_blank">Développez une solution en microservices</a>
            — découpage en microservices pour un client.
        </li>
        <li>
            <a href="https://github.com/monsieur486/ameliorez_votre_application_avec_des_systemes_distribues" target="_blank">Améliorez votre application avec des systèmes distribués</a>
            — passage à une architecture distribuée.
        </li>
        <li>
            <a href="https://github.com/monsieur486/completez_votre_backend_pour_rendre_votre_application_plus_securisee" target="_blank">Sécurisez votre back-end</a>
            — authentification, autorisations et bonnes pratiques de sécurité.
        </li>
        <li>
            <a href="https://github.com/monsieur486/touret-laurent-tester-java" target="_blank">Testez votre application Java</a>
            — tests unitaires et d'intégration (JUnit).
        </li>
    </ul>
</section>
```

- [ ] **Step 2: Ajouter les styles projets à la fin de `www/css/styles.css`**

```css
/* ---- Réalisations ---- */
.project {
  background: var(--surface);
  border: 1px solid var(--border-2);
  border-radius: var(--radius);
  padding: 20px;
}
.project--feature { margin-bottom: 16px; }
.project--feature .project__head { display: flex; align-items: center; gap: 12px; }
.project__star { margin-left: auto; color: var(--green); font-size: .8rem; font-family: var(--mono); white-space: nowrap; }
.project h3 { font-size: 1.1rem; margin-bottom: 8px; }
.project__desc { font-size: .9rem; color: var(--text-mute); margin-bottom: 12px; }
.project__links { display: flex; flex-wrap: wrap; gap: 16px; margin-top: 12px; font-size: .85rem; font-family: var(--mono); }
.projects-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; }
.projects-subtitle { font-size: 1rem; color: var(--blue); margin: 32px 0 14px; }
.formation-projects { list-style: none; display: flex; flex-direction: column; gap: 12px; }
.formation-projects li { font-size: .9rem; color: var(--text-mute); padding-left: 16px; border-left: 2px solid var(--border-2); }
@media (max-width: 720px) { .projects-grid { grid-template-columns: 1fr; } }
```

- [ ] **Step 3: Vérifier dans le navigateur**

Ouvrir `http://localhost:8000/#realisations`
Expected: carte vitrine GestoMS (badge « ★ vitrine » à droite, ligne ⚡ violette, lien code). En dessous une grille 2 colonnes (Tarot Des Amis avec liens « Démo live » + « Code », gestozip). Puis sous-titre bleu + liste des 5 projets de formation avec barre verticale à gauche. Cliquer « Démo live » ouvre tda.mr486.com. Mobile : grille en 1 colonne.

- [ ] **Step 4: Vérifier que tous les liens GitHub renvoient 200**

```bash
for u in GestoMS newTarotDesAmis gestozip concevez_une_application_web_java_de_a_a_z \
  developpez_une_solution_en_microservices_pour_votre_client \
  ameliorez_votre_application_avec_des_systemes_distribues \
  completez_votre_backend_pour_rendre_votre_application_plus_securisee \
  touret-laurent-tester-java; do
  code=$(curl -s -o /dev/null -w "%{http_code}" "https://github.com/monsieur486/$u"); echo "$code  $u"
done
```
Expected: `200` pour chaque dépôt. Si un `404` apparaît, retirer ou corriger le lien correspondant avec Laurent.

- [ ] **Step 5: Commit**

```bash
git add www/index.html www/css/styles.css
git commit -m "feat(site): section réalisations (projets réels + apport IA par projet)"
```

---

## Task 6: Section Expériences professionnelles

**Files:**
- Modify: `www/index.html` (remplir `#experiences`)
- Modify: `www/css/styles.css` (styles timeline)

- [ ] **Step 1: Remplir la section expériences dans `www/index.html`**

Remplacer `<section class="section" id="experiences"><!-- Task 6 --></section>` par :

```html
<section class="section" id="experiences">
    <span class="section-label">// expériences</span>
    <h2>Expériences professionnelles</h2>
    <div class="timeline">
        <article class="xp">
            <span class="xp__date">2013 – 2019</span>
            <div class="xp__body">
                <h3>Référent technique — Centre d'appels</h3>
                <p class="xp__org">TAKTIM / Terre d'appels — Strasbourg · Client : Ionos 1&amp;1</p>
                <p class="xp__desc">Soutien technique et procédures métier pour un hébergeur web. Formateur (initiation informatique, applications métier). Gestion de ~30 000 appels sur 5 ans.</p>
            </div>
        </article>
        <article class="xp">
            <span class="xp__date">2000 – 2013</span>
            <div class="xp__body">
                <h3>Responsable informatique</h3>
                <p class="xp__org">Markanne S.A. — Luxembourg</p>
                <p class="xp__desc">Maintenance du parc informatique (12 postes). Développement en Visual Basic d'une application de gestion d'agence de voyage (réservation, planning, documents de synthèse).</p>
            </div>
        </article>
        <article class="xp">
            <span class="xp__date">1998 – 2000</span>
            <div class="xp__body">
                <h3>Technicien informatique</h3>
                <p class="xp__org">Kredietbank — Luxembourg</p>
                <p class="xp__desc">Configuration, installation et maintenance du parc informatique client (2 000 postes).</p>
            </div>
        </article>
    </div>
</section>
```

- [ ] **Step 2: Ajouter les styles timeline à la fin de `www/css/styles.css`**

```css
/* ---- Timeline (expériences & formations) ---- */
.timeline { display: flex; flex-direction: column; gap: 26px; }
.xp { display: grid; grid-template-columns: 130px 1fr; gap: 18px; }
.xp__date { font-family: var(--mono); color: var(--orange); font-size: .85rem; padding-top: 3px; }
.xp__body h3 { font-size: 1.05rem; }
.xp__org { color: var(--blue); font-size: .9rem; margin: 2px 0 8px; }
.xp__desc { font-size: .9rem; color: var(--text-mute); }
@media (max-width: 640px) {
  .xp { grid-template-columns: 1fr; gap: 4px; }
  .xp__date { padding-top: 0; }
}
```

- [ ] **Step 3: Vérifier dans le navigateur**

Ouvrir `http://localhost:8000/#experiences`
Expected: 3 expériences en timeline 2 colonnes (date orange monospace à gauche, contenu à droite : titre, organisation bleue, description grise). Mobile : date au-dessus du contenu.

- [ ] **Step 4: Commit**

```bash
git add www/index.html www/css/styles.css
git commit -m "feat(site): section expériences professionnelles"
```

---

## Task 7: Sections Formations + Diplômes

**Files:**
- Modify: `www/index.html` (remplir `#formations` et `#diplomes`)

> Réutilise les styles `.timeline`/`.xp` (formations) et `.tags` déjà définis — pas de nouveau CSS.

- [ ] **Step 1: Remplir la section formations dans `www/index.html`**

Remplacer `<section class="section" id="formations"><!-- Task 7 --></section>` par :

```html
<section class="section" id="formations">
    <span class="section-label">// formations</span>
    <h2>Formations</h2>
    <div class="timeline">
        <article class="xp">
            <span class="xp__date">2022</span>
            <div class="xp__body">
                <h3>Développeur d'application Java — diplôme de niveau 6 (bac +3/4)</h3>
                <p class="xp__org">OpenClassrooms</p>
                <p class="xp__desc">Application web Java, bases de données, Spring Boot, tests unitaires et fonctionnels, JavaDoc, intégration continue, architecture microservices.</p>
            </div>
        </article>
        <article class="xp">
            <span class="xp__date">2019 – 2020</span>
            <div class="xp__body">
                <h3>Développeur Web et Mobile — diplôme de niveau 5</h3>
                <p class="xp__org">Wild Code School — Strasbourg</p>
                <p class="xp__desc">Back-end : Symfony, PHP, MVC, MySQL. Front-end : HTML5, CSS, SCSS, Ajax/JavaScript. Projets en équipe, méthode Agile.</p>
            </div>
        </article>
    </div>
</section>
```

- [ ] **Step 2: Remplir la section diplômes dans `www/index.html`**

Remplacer `<section class="section" id="diplomes"><!-- Task 7 --></section>` par :

```html
<section class="section" id="diplomes">
    <span class="section-label">// diplômes &amp; certifications</span>
    <h2>Diplômes &amp; certifications</h2>
    <ul class="formation-projects">
        <li><strong>1992 – 1995</strong> — DECF, unités de valeur obtenues : comptabilité, mathématiques, économie.</li>
        <li><strong>1990 – 1992</strong> — Baccalauréat G2, mention Bien.</li>
    </ul>
</section>
```

- [ ] **Step 3: Vérifier dans le navigateur**

Ouvrir `http://localhost:8000/#formations`
Expected: deux formations en timeline (dates orange 2022 / 2019-2020) ; section diplômes en liste avec barre verticale gauche, années en gras.

- [ ] **Step 4: Commit**

```bash
git add www/index.html
git commit -m "feat(site): sections formations et diplômes"
```

---

## Task 8: Footer + JavaScript (interactions)

**Files:**
- Modify: `www/index.html` (remplir `<footer>`)
- Modify: `www/js/scripts.js` (réécriture complète)
- Modify: `www/css/styles.css` (styles footer)

- [ ] **Step 1: Remplir le footer dans `www/index.html`**

Remplacer `<footer class="footer" id="footer"><!-- Task 8 --></footer>` par :

```html
<footer class="footer" id="footer">
    <div class="footer__inner">
        <p class="footer__cmd"><span class="hero__prompt">$</span> echo "Disponible pour de nouveaux défis"</p>
        <div class="footer__links">
            <a href="https://github.com/monsieur486" target="_blank" aria-label="GitHub"><i class="fab fa-github"></i></a>
            <a href="https://www.linkedin.com/in/laurent-touret" target="_blank" aria-label="LinkedIn"><i class="fab fa-linkedin-in"></i></a>
            <a href="mailto:laurent.touret@gmail.com" aria-label="Email"><i class="far fa-envelope"></i></a>
            <a href="cv.html" target="_blank">CV ↓</a>
        </div>
        <p class="footer__copy">© <span id="year">2026</span> Laurent Touret · Site conçu avec l'IA agentique (Claude Code)</p>
    </div>
</footer>
```

- [ ] **Step 2: Réécrire entièrement `www/js/scripts.js`**

```javascript
(function () {
  'use strict';

  // Année automatique dans le footer
  var yearEl = document.getElementById('year');
  if (yearEl) { yearEl.textContent = new Date().getFullYear(); }

  // Menu mobile
  var toggle = document.getElementById('navToggle');
  var links = document.getElementById('navLinks');
  if (toggle && links) {
    toggle.addEventListener('click', function () {
      var open = links.classList.toggle('is-open');
      toggle.setAttribute('aria-expanded', open ? 'true' : 'false');
    });
    // Fermer le menu après clic sur un lien (mobile)
    links.addEventListener('click', function (e) {
      if (e.target.tagName === 'A') { links.classList.remove('is-open'); toggle.setAttribute('aria-expanded', 'false'); }
    });
  }

  // Curseur clignotant ajouté après le rôle (désactivé si reduced-motion)
  var prefersReduced = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
  var role = document.querySelector('.hero__role');
  if (role && !prefersReduced) {
    var cursor = document.createElement('span');
    cursor.className = 'hero__cursor';
    cursor.textContent = ' ▊';
    cursor.setAttribute('aria-hidden', 'true');
    role.appendChild(cursor);
  }

  // Lien de nav actif selon la section visible
  var navLinks = Array.prototype.slice.call(document.querySelectorAll('.topnav__links a[href^="#"]'));
  var sections = navLinks
    .map(function (a) { return document.querySelector(a.getAttribute('href')); })
    .filter(Boolean);

  if ('IntersectionObserver' in window && sections.length) {
    var observer = new IntersectionObserver(function (entries) {
      entries.forEach(function (entry) {
        if (entry.isIntersecting) {
          var id = '#' + entry.target.id;
          navLinks.forEach(function (a) {
            a.classList.toggle('is-active', a.getAttribute('href') === id);
          });
        }
      });
    }, { rootMargin: '-40% 0px -55% 0px' });
    sections.forEach(function (s) { observer.observe(s); });
  }
})();
```

- [ ] **Step 3: Ajouter les styles footer à la fin de `www/css/styles.css`**

```css
/* ---- Footer ---- */
.footer { border-top: 1px solid var(--border); background: var(--surface); }
.footer__inner { max-width: var(--maxw); margin: 0 auto; padding: 40px 24px; text-align: center; }
.footer__cmd { font-family: var(--mono); color: var(--text-soft); margin-bottom: 18px; }
.footer__links { display: flex; justify-content: center; gap: 20px; font-size: 1.4rem; margin-bottom: 16px; }
.footer__links a { color: var(--text-mute); font-size: 1.4rem; }
.footer__links a:hover { color: var(--green); }
.footer__copy { font-size: .8rem; color: var(--text-mute); font-family: var(--mono); }
```

- [ ] **Step 4: Vérifier dans le navigateur**

Ouvrir `http://localhost:8000/`
Expected:
- Un curseur `▊` vert clignote après le sous-titre du hero.
- En scrollant, le lien de nav correspondant à la section visible passe en blanc (actif).
- Footer centré avec la ligne `$ echo ...`, icônes, et « © 2026 Laurent Touret · Site conçu avec l'IA agentique (Claude Code) » (l'année est injectée par JS).
- En mobile (<720px) : clic sur ☰ ouvre le menu, clic sur un lien le referme.

- [ ] **Step 5: Commit**

```bash
git add www/index.html www/js/scripts.js www/css/styles.css
git commit -m "feat(site): footer + interactions JS (nav active, menu mobile, curseur, année)"
```

---

## Task 9: Page CV imprimable (`cv.html` + `print.css`)

**Files:**
- Create: `www/cv.html`
- Create: `www/css/print.css`

> Décision : CV en **version claire imprimable** (fond blanc, texte sombre) pour la lisibilité
> encre + compatibilité ATS, tout en conservant les accents Java (vert) / IA (violet).
> `cv.html` charge UNIQUEMENT `print.css` (pas `styles.css`), pour un rendu autonome.

- [ ] **Step 1: Créer `www/css/print.css`**

```css
/* ============================================================
   CV imprimable A4 — version claire (lisibilité encre + ATS)
   ============================================================ */
:root {
  --ink:    #1f2937;
  --soft:   #4b5563;
  --line:   #d1d5db;
  --green:  #15803d;
  --blue:   #1d4ed8;
  --ia:     #7c3aed;
  --mono: 'JetBrains Mono', Menlo, Consolas, monospace;
  --sans: system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
}
* { box-sizing: border-box; margin: 0; padding: 0; }
body { font-family: var(--sans); color: var(--ink); line-height: 1.5; background: #e5e7eb; }

.cv {
  width: 210mm; min-height: 297mm; margin: 0 auto; background: #fff;
  padding: 16mm 16mm; display: grid; grid-template-columns: 62mm 1fr; gap: 10mm;
}

/* Colonne gauche */
.cv__side { font-size: 9.5pt; }
.cv__name { font-size: 22pt; font-weight: 800; line-height: 1.05; color: var(--ink); }
.cv__name span { color: var(--green); }
.cv__title { font-family: var(--mono); color: var(--soft); font-size: 9.5pt; margin: 4px 0 14px; }
.cv__photo { width: 34mm; height: 34mm; border-radius: 50%; object-fit: cover; margin-bottom: 12px; }
.cv__side h2 { font-size: 9pt; letter-spacing: 1px; text-transform: uppercase; color: var(--blue); margin: 14px 0 6px; }
.cv__side ul { list-style: none; }
.cv__side li { margin-bottom: 3px; }
.cv__contact a { color: var(--ink); text-decoration: none; }
.cv__ia { color: var(--ia); }

/* Colonne droite */
.cv__main { font-size: 9.5pt; }
.cv__main h2 { font-size: 10pt; letter-spacing: 1px; text-transform: uppercase; color: var(--blue); border-bottom: 1px solid var(--line); padding-bottom: 3px; margin: 0 0 8px; }
.cv__section { margin-bottom: 12px; }
.cv__pitch { color: var(--soft); margin-bottom: 12px; }
.cv__item { margin-bottom: 8px; }
.cv__item h3 { font-size: 9.5pt; }
.cv__item .meta { color: var(--soft); font-size: 8.5pt; }
.cv__item p { color: var(--soft); }
.cv__ianote { color: var(--ia); font-size: 8.5pt; }

@media print {
  body { background: #fff; }
  .cv { margin: 0; box-shadow: none; }
  @page { size: A4; margin: 0; }
}
```

- [ ] **Step 2: Créer `www/cv.html`**

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="utf-8"/>
    <meta content="width=device-width, initial-scale=1" name="viewport"/>
    <title>CV — Laurent Touret</title>
    <link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;700&display=swap" rel="stylesheet"/>
    <link href="css/print.css" rel="stylesheet"/>
</head>
<body>
<div class="cv">
    <aside class="cv__side">
        <img class="cv__photo" src="assets/img/profile.jpg" alt="Laurent Touret"/>
        <div class="cv__name">Laurent <span>Touret</span></div>
        <div class="cv__title">Dév. Java back-end × IA agentique</div>

        <h2>Contact</h2>
        <ul class="cv__contact">
            <li>Strasbourg</li>
            <li>+33 7 81 08 98 73</li>
            <li><a href="mailto:laurent.touret@gmail.com">laurent.touret@gmail.com</a></li>
            <li><a href="https://github.com/monsieur486">github.com/monsieur486</a></li>
            <li><a href="https://mr486.com">mr486.com</a></li>
        </ul>

        <h2>Back-end &amp; langages</h2>
        <ul>
            <li>Java · Spring Boot · Microservices</li>
            <li>REST · SQL · MongoDB</li>
            <li>Docker · Kubernetes · Linux</li>
            <li>PHP · Python · JavaScript</li>
        </ul>

        <h2 class="cv__ia">⚡ IA agentique</h2>
        <ul>
            <li class="cv__ia">Claude Code (quotidien)</li>
            <li class="cv__ia">Prompt engineering</li>
            <li class="cv__ia">Orchestration d'agents</li>
            <li class="cv__ia">Dev full-stack assisté</li>
        </ul>

        <h2>Méthodes</h2>
        <ul><li>Agile/SCRUM · SOLID · TDD · CI/CD</li></ul>

        <h2>Diplômes</h2>
        <ul>
            <li>DECF (compta, maths, éco) — 1992-1995</li>
            <li>Bac G2, mention Bien — 1990-1992</li>
        </ul>
    </aside>

    <main class="cv__main">
        <p class="cv__pitch">
            Développeur Java back-end diplômé (OpenClassrooms, niveau 6). Je conçois des
            back-ends Spring Boot robustes et j'intègre l'IA agentique (Claude Code) à mon
            flux de travail quotidien — ce qui m'a permis d'étendre mon périmètre au front-end.
            Disponible et motivé pour rejoindre une équipe qui aime relever des défis.
        </p>

        <section class="cv__section">
            <h2>Réalisations</h2>
            <div class="cv__item">
                <h3>GestoMS — Générateur de plateforme microservices</h3>
                <p>Plateforme clé en main : Eureka, Gateway WebFlux, Keycloak/OAuth2, blacklist JWT Redis, RabbitMQ, batch, WebSocket, Spring Boot Admin.</p>
                <p class="cv__ianote">⚡ Architecture conçue et itérée avec Claude Code</p>
            </div>
            <div class="cv__item">
                <h3>Tarot Des Amis — app déployée (tda.mr486.com)</h3>
                <p>Scores temps réel via WebSocket, UI mobile-first. Spring Boot · Thymeleaf · Docker.</p>
                <p class="cv__ianote">⚡ Front-end abordé grâce à l'IA</p>
            </div>
        </section>

        <section class="cv__section">
            <h2>Formations</h2>
            <div class="cv__item">
                <h3>Développeur d'application Java — niveau 6</h3>
                <p class="meta">2022 · OpenClassrooms</p>
                <p>Spring Boot, bases de données, tests, intégration continue, microservices.</p>
            </div>
            <div class="cv__item">
                <h3>Développeur Web et Mobile — niveau 5</h3>
                <p class="meta">2019-2020 · Wild Code School</p>
                <p>Symfony/PHP/MySQL (back) · HTML/CSS/JS (front).</p>
            </div>
        </section>

        <section class="cv__section">
            <h2>Expériences</h2>
            <div class="cv__item">
                <h3>Référent technique — centre d'appels</h3>
                <p class="meta">2013-2019 · TAKTIM / Ionos 1&amp;1 — Strasbourg</p>
                <p>Soutien technique hébergeur web, formateur, ~30 000 appels sur 5 ans.</p>
            </div>
            <div class="cv__item">
                <h3>Responsable informatique</h3>
                <p class="meta">2000-2013 · Markanne S.A. — Luxembourg</p>
                <p>Parc informatique + application Visual Basic de gestion d'agence de voyage.</p>
            </div>
            <div class="cv__item">
                <h3>Technicien informatique</h3>
                <p class="meta">1998-2000 · Kredietbank — Luxembourg</p>
                <p>Installation et maintenance du parc client (2 000 postes).</p>
            </div>
        </section>
    </main>
</div>
</body>
</html>
```

- [ ] **Step 3: Vérifier à l'écran**

Ouvrir `http://localhost:8000/cv.html`
Expected: une « feuille » A4 blanche centrée sur fond gris. Colonne gauche (photo ronde, nom avec « Touret » vert, titre, contacts, blocs compétences dont « ⚡ IA agentique » en violet, diplômes) ; colonne droite (pitch, réalisations avec lignes ⚡ violettes, formations, expériences). Tout le texte est sélectionnable.

- [ ] **Step 4: Vérifier l'aperçu d'impression**

Dans le navigateur : Ctrl+P. Expected: aperçu A4 propre, fond blanc, sans fond gris ni marges parasites, contenu tenant idéalement sur 1 page (2 max). Si débordement important, réduire les marges `.cv` (ex. `12mm`) ou la taille de police de base.

- [ ] **Step 5: Commit**

```bash
git add www/cv.html www/css/print.css
git commit -m "feat(cv): page CV imprimable A4 (Java x IA agentique)"
```

---

## Task 10: Passe finale — responsive, accessibilité, génération du PDF

**Files:**
- Modify: `www/index.html` / `www/css/styles.css` (corrections éventuelles)
- Modify: `www/assets/files/CV-Laurent-Touret.pdf` (régénération)

- [ ] **Step 1: Audit responsive multi-largeurs**

Dans le navigateur (DevTools, mode responsive), tester 375px, 768px, 1280px sur `index.html`.
Expected: aucune barre de scroll horizontale, hero lisible, grilles compétences/projets qui
s'empilent en mobile, nav burger fonctionnelle. Corriger tout débordement constaté.

- [ ] **Step 2: Audit accessibilité rapide**

- Navigation au clavier (Tab) : tous les liens/boutons atteignables, focus visible.
- Vérifier que les icônes seules ont un `aria-label` (hero social, footer) — déjà en place.
- Contraste : texte `--text-soft` sur `--bg` lisible (OK car palette GitHub dark).
Corriger si un focus n'est pas visible (ajouter au besoin dans `styles.css`) :

```css
a:focus-visible, .btn:focus-visible, .topnav__toggle:focus-visible {
  outline: 2px solid var(--green); outline-offset: 2px; border-radius: 4px;
}
```

- [ ] **Step 3: Régénérer le PDF du CV**

Ouvrir `http://localhost:8000/cv.html` → Ctrl+P → Destination « Enregistrer au format PDF » →
marges « Aucune » → cocher « Graphiques d'arrière-plan » → enregistrer.
Remplacer le fichier : déposer le PDF généré à `www/assets/files/CV-Laurent-Touret.pdf`.

Run (vérifier que le fichier a bien été mis à jour) :
```bash
ls -la www/assets/files/CV-Laurent-Touret.pdf
```
Expected: date de modification = aujourd'hui.

- [ ] **Step 4: Commit**

```bash
git add www/index.html www/css/styles.css www/assets/files/CV-Laurent-Touret.pdf
git commit -m "chore(site): passe responsive/a11y + régénération du CV PDF"
```

---

## Task 11: Vérification du déploiement Docker

**Files:**
- Read/verify: `Dockerfile`, `docker-compose.yaml` (aucune modif attendue)

- [ ] **Step 1: Confirmer que le Dockerfile copie tout `www/`**

Run: `cat Dockerfile`
Expected: `COPY ./www/ /usr/local/apache2/htdocs/` — copie l'intégralité de `www/`, donc
`cv.html`, `css/print.css` et les assets sont inclus automatiquement. Aucune modif nécessaire.

- [ ] **Step 2: Build + run local de l'image de prod**

```bash
docker compose up -d --build
curl -s -o /dev/null -w "index → %{http_code}\n" http://localhost:2024/
curl -s -o /dev/null -w "cv    → %{http_code}\n" http://localhost:2024/cv.html
curl -s -o /dev/null -w "css   → %{http_code}\n" http://localhost:2024/css/print.css
```
Expected: `200` pour les trois. Ouvrir `http://localhost:2024/` et `http://localhost:2024/cv.html`
dans le navigateur pour confirmer le rendu identique à la prévisualisation.

- [ ] **Step 3: Arrêter le conteneur de test**

```bash
docker compose down
```

- [ ] **Step 4: Commit final (si des ajustements ont été faits)**

```bash
git add -A
git commit -m "chore: vérification déploiement Docker (cv.html + assets servis)" || echo "rien à committer"
```

> **Déploiement réel (par Laurent, hors plan)** : `./update.sh` (git pull + docker compose up -d).
> Penser à vérifier au passage le problème SSL constaté sur `mr486.com` (certificat / conteneur).

---

## Self-Review (effectuée)

- **Couverture du spec** : design system (T1) ✓ · structure/SEO (T2) ✓ · hero positionnement (T3) ✓ ·
  compétences + bloc IA (T4) ✓ · réalisations projets réels + apport IA (T5) ✓ · expériences (T6) ✓ ·
  formations/diplômes (T7) ✓ · interactions/footer (T8) ✓ · CV imprimable + print.css (T9) ✓ ·
  responsive/a11y + PDF (T10) ✓ · déploiement Docker (T11) ✓.
- **Placeholders** : aucun ; les seuls points « à valider par Laurent » concernent l'exactitude
  factuelle du contenu (descriptions formation, visibilité des dépôts), pas du code manquant.
- **Cohérence des noms** : classes CSS (`.tag--ia`, `.ia-note`, `.skills__col--ia`, `.xp`, `.project`,
  `.cv__ia`) définies puis réutilisées de façon cohérente ; `print.css` autonome (n'utilise pas les
  variables de `styles.css`). IDs de nav (`competences`, `realisations`, `experiences`, `formations`)
  correspondent aux ancres et aux sections.
```
