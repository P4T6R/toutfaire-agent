---
name: emploi-bourses-watch
description: "Daily research of job offers and international scholarships/grants across all fields, levels, and regions, with full actionable details (deadline, eligibility, funding, application link). Use when building or running a daily jobs+scholarships digest, or when asked directly for current job/scholarship opportunities."
version: 1.0.0
author: Hermes Agent
license: MIT
dependencies: []
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [jobs, scholarships, grants, research, daily-digest, career, education-funding]
    related_skills: [blogwatcher, anysearch, gread, research-innovation-explorer]

---

# Veille Emploi & Bourses Internationales

Daily research pass across job offers and international scholarships/grants, all fields, all levels, worldwide (no geographic filter) — producing a digest with every detail needed to actually act on a listing, not just a headline.

## When to use

Running the daily `emploi-bourses-digest` cron job, or whenever asked directly for current job openings or scholarship opportunities.

## Non-negotiable: every listing needs these fields

A listing without these is not useful — don't include headline-only mentions. Search until you have this, or drop the listing:

**Emploi :**
- Intitulé du poste + organisation
- Lieu (ville/pays) et modalité (présentiel / hybride / 100% remote)
- Date limite de candidature (ou "au fil de l'eau" si applicable — dites-le explicitement, ne laissez jamais ce champ implicite)
- Type de contrat (CDI, CDD, stage, freelance) et niveau d'expérience requis
- Fourchette de rémunération si publiée (ne jamais l'inventer si absente — écrire "non communiqué")
- Lien de candidature direct (pas juste le nom de l'entreprise à chercher soi-même)

**Bourses/subventions :**
- Nom du programme + organisme financeur
- Niveau d'études couvert (licence/master/doctorat/postdoc) et domaines éligibles
- Pays d'accueil et pays éligibles pour candidater (certaines bourses excluent des nationalités)
- Montant/couverture exacte (frais de scolarité, logement, billets d'avion, allocation mensuelle — préciser ce qui est couvert, pas juste "financement complet")
- Date limite de candidature — **le champ le plus important**, vérifier qu'elle n'est pas déjà passée avant de l'inclure
- Documents requis (lettre de motivation, relevés de notes, lettres de recommandation, test de langue) et lien de candidature direct

## Où chercher

**Emploi** (généraliste, tous secteurs) :
- LinkedIn Jobs, Indeed — recherche large sans filtre sectoriel
- ReliefWeb, Devex, UN Jobs, ImpactPool — pour les postes en organisations internationales/ONG (souvent avec dates limites strictes et détails complets, bonne source de qualité)
- RemoteOK, WeWorkRemotely, Himalayas — pour le remote toutes disciplines
- Recherche directe "site:linkedin.com/jobs" ou équivalent pour des requêtes ciblées du jour

**Bourses internationales** (tous niveaux, sans filtre géographique) :
- Agrégateurs généralistes : Opportunity Desk, ScholarshipDb, ScholarshipsAds, Mastersportal/Scholarshipportal
- Programmes gouvernementaux majeurs à vérifier en rotation (pas tous les jours, mais couvrir sur la semaine) : DAAD (Allemagne), Chevening (UK), Fulbright (US), Erasmus Mundus (UE), Commonwealth Scholarships, Australia Awards, MEXT (Japon), Eiffel (France), Vanier (Canada)
- Recherche directe pour les appels à candidature qui viennent d'ouvrir (deadline dans le futur, pas déjà clôturée)

## Couverture Afrique / zone UMOA (obligatoire, en plus de la veille mondiale)

Le volet mondial ci-dessus a tendance à sur-représenter les sources occidentales (LinkedIn, agrégateurs US/UE) — une recherche dédiée Afrique et zone UMOA est nécessaire à chaque passage, pas laissée au hasard du volet généraliste.

**Zone UMOA/UEMOA** : Bénin, Burkina Faso, Côte d'Ivoire, Guinée-Bissau, Mali, Niger, Sénégal, Togo (zone franc CFA, mêmes institutions régionales).

**Emploi — sources Afrique/UMOA à checker à chaque passage :**
- Sites emploi nationaux/régionaux : Emploi.bf (Burkina), Educarriere.ci / Emploi.ci (Côte d'Ivoire), Emploisenegal.com, Novojob, Jobzyn, Go Africa Online, Afrijobs, Jobartis
- Institutions régionales (postes souvent bien détaillés, deadlines strictes) : Commission de l'UEMOA (siège Ouagadougou), BCEAO, CEDEAO/ECOWAS, Banque Africaine de Développement (BAD/AfDB), Union Africaine
- ReliefWeb et Devex filtrés par pays africains — forte présence ONG/humanitaire sur le continent, déjà une bonne source pour l'Afrique spécifiquement
- Recherche directe par pays si rien de solide ne remonte des agrégateurs ("offre emploi [secteur] Ouagadougou/Abidjan/Dakar/Bamako [date du jour]")

**Bourses — programmes spécifiques Afrique à checker en rotation sur la semaine :**
- Mastercard Foundation Scholars Program, Mo Ibrahim Foundation, AWARD (African Women in Agricultural Research and Development), AIMS (African Institute for Mathematical Sciences)
- Bourses de la Banque Africaine de Développement, bourses de l'Union Africaine
- Mandela Washington Fellowship / YALI (jeunes leaders africains)
- Bourses françaises orientées Afrique francophone (Campus France, Eiffel — vérifier l'éligibilité pays UMOA), bourses chinoises pour étudiants africains, bourses marocaines/tunisiennes pour l'Afrique subsaharienne
- CAMES (Conseil Africain et Malgache pour l'Enseignement Supérieur) pour les opportunités académiques régionales UMOA

## Éviter la répétition inutile

Les bourses ont des deadlines à plusieurs semaines/mois — republier la même annonce chaque jour pendant des semaines est du bruit, pas un service. Règle : ne remonter une bourse déjà signalée que si sa deadline approche (< 7 jours) ou si un détail important a changé (nouvelle info, deadline prolongée). Pour l'emploi, la rotation est plus rapide (nouvelles offres quotidiennes), donc moins de souci de répétition, mais éviter quand même de rementionner une offre déjà vue sans nouvelle info.

## Format du digest quotidien

Court et scannable sur Telegram — pas un pavé. Structure en 4 blocs : **Emploi Monde**, **Emploi Afrique/UMOA**, **Bourses Monde**, **Bourses Afrique/UMOA** — 3 à 5 items max par bloc (au lieu de 5-8 par catégorie sans distinction géographique, pour garder une longueur totale raisonnable). Chaque item : champs non-négociables ci-dessus en 3-4 lignes condensées, lien direct en dernière ligne. Si un bloc n'a rien de solide un jour donné, le dire explicitement ("Rien de nouveau aujourd'hui qui remplisse les critères") plutôt que de forcer des listings incomplets pour remplir le quota — y compris pour les blocs Afrique/UMOA, ne pas inventer une offre pour ne pas laisser un bloc vide.

## Limites

- Les deadlines et montants changent — toujours vérifier la date au moment de la recherche, ne jamais réutiliser une info d'un jour précédent sans revérifier si elle est reprise dans le digest.
- Sans domaine/niveau/zone géographique filtrés (config actuelle), le volume de candidats-listings est énorme — la sélection de qualité (fiabilité de la source, deadline non expirée, détails complets) prime sur l'exhaustivité.
