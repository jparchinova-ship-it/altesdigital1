# PLATEFORME ALTESDIGITAL — DOCUMENT MAÎTRE DE CONCEPTION

**Version** : 1.0  
**Date** : Mars 2026  
**Statut** : Conception stratégique — Niveau cabinet international

---

## 1. ARBORESCENCE COMPLÈTE DU SITE

```
altesdigital.com/
├── /                          # Page d'accueil
├── /a-propos                  # À propos (Mission, Vision 2030, Valeurs, Expertise)
├── /services                  # Liste des services
│   ├── /developpement-mobile  # Applications mobiles
│   ├── /creation-saas         # Création SaaS
│   ├── /digitalisation-gouvernementale
│   ├── /paiements-africains   # Intégration paiements
│   ├── /intelligence-artificielle
│   └── /edition-premium
├── /contact                   # Contact & demande de devis
├── /blog                      # Blog stratégique SEO
├── /pays                      # Pages pays ciblés (SEO Afrique)
│   ├── /bf                    # Burkina Faso
│   ├── /sn                    # Sénégal
│   └── ...
│
├── # ZONE AUTHENTIFIÉE
├── /connexion
├── /inscription
├── /mot-de-passe-oublie
│
├── # SAAS MULTI-ENTREPRISES
├── /app/                      # Dashboard principal (redirect selon rôle)
│   ├── /dashboard             # Tableau de bord
│   ├── /entreprise            # Profil entreprise
│   ├── /utilisateurs          # Gestion utilisateurs internes
│   ├── /projets               # Gestion projets
│   ├── /historique-ia         # Historique conversations IA
│   ├── /fichiers              # Téléchargement / dépôt fichiers
│   ├── /facturation           # Factures & abonnements
│   └── /parametres
│
├── # MODULE IA MULTI-AGENTS
├── /app/ia                    # Interface chat IA
│   ├── /chat                  # Chat avec sélection d'agent
│   └── /credits               # Gestion crédits IA
│
├── # MARKETPLACE
├── /marketplace               # Catalogue services
│   ├── /prestataires          # Liste prestataires
│   ├── /prestataire/[id]      # Profil + notation
│   ├── /commande              # Tunnel de commande
│   └── /mes-commandes         # Historique (connecté)
│
├── # PAIEMENT & LEGAL
├── /tarifs                    # Grille tarifaire (Basic / Pro / Institutionnel)
├── /confidentialite
├── /conditions-generales
└── /mentions-legales
```

---

## 2. WIREFRAMES LOGIQUES (DESCRIPTION)

### 2.1 Page d'accueil
- **Hero** : Visuel impactant, slogan type « La tech africaine au service de votre croissance », sous-titre, 2 CTA (Créer un projet | Demander un devis).
- **Présentation** : 2–3 blocs (Qui sommes-nous, Ce que nous faisons, Pourquoi nous).
- **Statistiques** : 4 KPIs (projets livrés, pays, clients, années d’expérience).
- **Services** : 6 cartes cliquables vers sous-pages services.
- **Témoignages** : Carousel 3 témoignages avec photo, nom, entreprise.
- **CTA final** : Bandeau « Prêt à transformer votre activité ? » + bouton.

### 2.2 À propos
- Sections : Mission, Vision 2030, Valeurs (cartes), Expertise (domaines), Engagement sécurité & souveraineté (badges, certifications).

### 2.3 Page service type
- Problème résolu (texte + visuel).
- Solution proposée (étapes ou schéma).
- Stack technologique (logos / liste).
- CTA : Demander un devis / Créer un projet.

### 2.4 Dashboard SaaS (/app)
- Sidebar : Dashboard, Entreprise, Utilisateurs, Projets, IA, Fichiers, Facturation, Paramètres.
- Zone principale : widgets (résumé abo, derniers projets, activité IA récente).
- Header : recherche, notifications, profil.

### 2.5 Module IA (/app/ia/chat)
- Liste agents à gauche (Web Builder, Business Strategist, Fintech, Government Secure, Édition Premium, Marketplace).
- Zone chat centrale (messages, input, pièces jointes).
- Panneau droit : crédits restants, export PDF/DOCX, historique de session.

### 2.6 Marketplace
- Filtres (catégorie, pays, note, prix).
- Grille prestataires (avatar, nom, note, prix min, bouton « Commander »).
- Page prestataire : profil, services, avis, notation, bouton commander.

---

## 3. ARCHITECTURE MICROSERVICES

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           CDN + WAF (Anti-DDoS)                              │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
┌─────────────────────────────────────────────────────────────────────────────┐
│                    API GATEWAY (Kong / AWS API GW)                           │
│                    Auth JWT + rate limiting + routing                        │
└─────────────────────────────────────────────────────────────────────────────┘
        │              │              │              │              │
        ▼              ▼              ▼              ▼              ▼
┌───────────┐  ┌───────────┐  ┌───────────┐  ┌───────────┐  ┌───────────┐
│  Auth     │  │  Core     │  │  IA       │  │ Marketplace│  │ Payment   │
│  Service  │  │  (Tenant) │  │  Agents   │  │  Service   │  │  Service  │
│  (FastAPI)│  │  (FastAPI)│  │  (FastAPI)│  │  (FastAPI) │  │  (FastAPI)│
│  JWT+MFA  │  │  CRUD     │  │  LLM      │  │  Escrow    │  │  OM/MoMo  │
│  Users    │  │  Projets  │  │  Credits  │  │  Reviews   │  │  Stripe   │
└─────┬─────┘  └─────┬─────┘  └─────┬─────┘  └─────┬─────┘  └─────┬─────┘
      │              │              │              │              │
      └──────────────┴──────────────┴──────────────┴──────────────┘
                                      │
                    ┌─────────────────┼─────────────────┐
                    ▼                 ▼                 ▼
              ┌──────────┐     ┌──────────┐     ┌──────────┐
              │PostgreSQL│     │  Redis   │     │  S3/     │
              │ (primary)│     │  (cache) │     │  Storage │
              └──────────┘     └──────────┘     └──────────┘
```

**Services détaillés :**
- **Auth** : Inscription, connexion, MFA, reset password, gestion rôles (super_admin, admin_tenant, user_tenant, prestataire, client_marketplace).
- **Core** : Tenants (entreprises), utilisateurs par tenant, projets, fichiers, facturation (émission factures PDF).
- **IA** : Routage vers agents (Web Builder, Business Strategist, Fintech, Government Secure, Édition Premium, Marketplace), consommation crédits, génération PDF/DOCX, historique.
- **Marketplace** : Catalogue prestataires, notation, commandes, escrow, litiges.
- **Payment** : Orange Money BF, Mobile Money multi-pays, Stripe (carte), abonnements, webhooks, facturation PDF.

---

## 4. SCHÉMA BASE DE DONNÉES SIMPLIFIÉ

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   tenants   │────<│   users     │     │   roles     │
│ id, name,   │     │ id, tenant_ │     │ id, name    │
│ plan, ...   │     │ id, email,  │>────│             │
└──────┬──────┘     │ role_id     │     └─────────────┘
       │            └──────┬──────┘
       │                   │
       ▼                   ▼
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  projects   │     │ ai_sessions │     │ ai_credits  │
│ id, tenant_ │     │ id, user_id │     │ id, tenant_ │
│ id, name    │     │ agent_type  │     │ id, balance │
└─────────────┘     └─────────────┘     └─────────────┘

┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│ prestataires│     │  orders     │     │  reviews    │
│ id, user_id │────<│ id, prestat │     │ id, order_  │
│ profile     │     │ id, amount  │>────│ id, rating  │
└─────────────┘     └─────────────┘     └─────────────┘

┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  invoices   │     │  payments   │     │ audit_logs  │
│ id, tenant_ │     │ id, invoice │     │ id, user_   │
│ id, pdf_url │     │ _id, method │     │ id, action  │
└─────────────┘     └─────────────┘     └─────────────┘
```

**Principes :**
- **Multi-tenant** : `tenant_id` sur toutes les tables métier (projets, users, ai_credits, invoices).
- **Isolation** : Chaque requête filtre par `tenant_id` (row-level security possible).
- **Audit** : Table `audit_logs` pour actions sensibles (connexion, paiement, changement rôle).

---

## 5. PLAN CYBERSÉCURITÉ

| Mesure | Implémentation |
|--------|----------------|
| **Transport** | HTTPS obligatoire, TLS 1.3, HSTS |
| **Authentification** | JWT (short-lived) + refresh token, MFA (TOTP) pour admin / institutionnel |
| **Autorisation** | RBAC (rôles), contrôle d’accès par tenant (pas de cross-tenant) |
| **Données sensibles** | Chiffrement AES-256 au repos (DB, backups), secrets dans vault (e.g. Vault / AWS Secrets) |
| **Paiements** | Pas de stockage carte complète; PCI-DSS via Stripe; tokenisation Orange Money / MoMo |
| **Réseau** | WAF, anti-DDoS (CDN + rate limiting API Gateway) |
| **Applications** | Validation entrées, requêtes paramétrées (anti-SQLi), CORS strict, CSP |
| **Audit** | Logs d’accès et d’actions sensibles (auth, paiements, admin), rétention 12–24 mois |
| **Institutions** | Option PCA/PRA (sauvegardes, RTO/RPO), hébergement souverain possible (option pays) |

---

## 6. ROADMAP DÉVELOPPEMENT 6–12 MOIS

| Phase | Période | Livrables |
|-------|---------|-----------|
| **M0–M2** | Mois 1–2 | Spec détaillée, maquettes, repo, CI/CD, Auth + Core (tenants, users, projets), front Next.js (accueil, à propos, services, connexion/inscription) |
| **M3** | Mois 3 | Dashboard SaaS (dashboard, entreprise, utilisateurs, projets), facturation PDF basique, premier paiement (Stripe) |
| **M4** | Mois 4 | Module IA : 2–3 agents (ex. Web Builder, Business Strategist), chat, crédits, export PDF/DOCX |
| **M5** | Mois 5 | Paiements africains (Orange Money BF, 1 autre MoMo), abonnements Basic/Pro, marketplace (catalogue + profil prestataire + notation) |
| **M6** | Mois 6 | Marketplace (commande, escrow, litiges), tous les agents IA, plan Institutionnel, SEO (blog, pages pays) |
| **M7–M9** | Mois 7–9 | Optimisation, PWA, monitoring, PCA/PRA, certifications (ISO 27001 si cible institutionnel) |
| **M10–M12** | Mois 10–12 | Scale (K8s si besoin), nouveaux pays paiement, modules optionnels (documents pro avancés, intégrations) |

---

## 7. ESTIMATION BUDGÉTAIRE (ORDRE DE GRANDEUR)

| Poste | Fourchette (EUR) |
|-------|-------------------|
| Conception (UX/UI, spec, sécurité) | 15 000 – 25 000 |
| Développement front (Next.js, PWA) | 30 000 – 50 000 |
| Développement back (FastAPI, microservices) | 40 000 – 70 000 |
| Intégrations (paiements, IA, storage) | 15 000 – 30 000 |
| Infra (cloud, CDN, 12 mois) | 10 000 – 20 000 |
| Tests, déploiement, formation | 10 000 – 15 000 |
| **Total projet (12 mois)** | **120 000 – 210 000** |

*À affiner selon équipe interne / offshore et périmètre exact.*

---

## 8. PLAN DE MONÉTISATION DÉTAILLÉ

| Source | Modèle | Détail |
|--------|--------|--------|
| **Abonnements SaaS** | Basic / Pro / Institutionnel | Mensuel ou annuel (remise 15–20 %) ; limites projets, utilisateurs, stock, support. |
| **Crédits IA** | Pack de crédits | Achat de crédits (ex. 100 / 500 / 2000) ; consommation par requête/agent ; renouvellement mensuel optionnel. |
| **Marketplace** | Commission | 10–20 % sur chaque transaction (ajustable par catégorie) ; option abonnement prestataire (listing premium). |
| **Services sur mesure** | Devis / forfait | Développement mobile, SaaS, digitalisation gov, édition premium ; facturation projet ou récurrente. |
| **Paiements africains** | Marge ou fee | Marge sur transactions Orange Money / MoMo (selon accord opérateur). |
| **Projection Afrique 3 ans** | Croissance | Cible : 500+ tenants (année 1), 2000+ (année 2), 5000+ (année 3) ; montée en gamme Pro/Institutionnel et marketplace. |

---

## 9. POSITIONNEMENT STRATÉGIQUE

La plateforme permet de positionner Altesdigital comme :

1. **Leader SaaS au Burkina Faso** : Offre structurée Basic/Pro/Institutionnel + outils IA intégrés.
2. **Fournisseur technologique institutionnel** : Digitalisation gouvernementale, sécurité, PCA/PRA, souveraineté.
3. **Plateforme IA africaine innovante** : Multi-agents (stratégie, fintech, gov, édition, marketplace) + crédits et intégration métier.
4. **Acteur de la transformation digitale** : Marketplace de services tech + paiements locaux + génération de documents professionnels.

---

*Document produit dans le cadre de la conception plateforme officielle Altesdigital — Ouagadougou, 2026.*
