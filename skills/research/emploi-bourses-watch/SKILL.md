---
name: emploi-bourses-watch
description: "Daily research of actionable job offers and international scholarships/grants. Prioritizes Burkina Faso, Ghana, Côte d’Ivoire, West Africa and worldwide remote roles, while retaining global coverage."
version: 1.1.0
author: Toutfaire Agent
license: MIT
dependencies: []
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [jobs, burkina-faso, ghana, cote-divoire, west-africa, remote, scholarships, grants, research, daily-digest, career, education-funding]
    related_skills: [blogwatcher, anysearch, gread, research-innovation-explorer]

---

# Veille Emploi & Bourses Internationales

Recherche quotidienne d’offres d’emploi, missions freelance/remote, bourses et subventions, tous secteurs et tous niveaux. La priorité régionale est : **Burkina Faso**, puis **Ghana**, **Côte d’Ivoire** et Afrique de l’Ouest/UMOA ; elle complète, sans la remplacer, la veille mondiale.

## Quand l’utiliser

Pour le cron quotidien `emploi-bourses-digest` ou pour une demande directe d’opportunités actuelles.

## Règle de qualité : une offre doit être actionnable

Ne jamais publier un simple titre ou une rediffusion non vérifiée. Si une information essentielle est absente, chercher la page de l’employeur/organisme ; sinon, écarter l’annonce.

**Pour chaque emploi :**

- Intitulé, organisation et pays/ville ; ajouter le tag pays (`🇧🇫 Burkina Faso`, `🇬🇭 Ghana`, `🇨🇮 Côte d’Ivoire`, etc.).
- Modalité : présentiel, hybride ou 100 % remote. Pour le remote, indiquer explicitement `Mondial`, `Afrique`, ou les pays autorisés.
- Date de publication si disponible et date limite ; si candidature continue, écrire `au fil de l’eau`.
- Contrat, niveau d’expérience et compétences/langues indispensables.
- Rémunération si publiée ; sinon écrire `non communiquée`.
- Lien de candidature direct. Un agrégateur peut servir à découvrir l’offre, mais le lien final doit pointer vers l’employeur, l’institution ou le formulaire officiel quand il existe.

**Pour chaque bourse/subvention :**

- Programme, financeur, niveau et domaines éligibles.
- Pays d’accueil et nationalités/pays autorisés.
- Couverture exacte, documents requis, deadline future vérifiée et lien de candidature direct.

## Passage obligatoire à chaque exécution

La veille ne doit pas se limiter aux résultats mondiaux. À chaque passage, effectuer les quatre recherches ci-dessous, même si un bloc finit vide.

### 1. Burkina Faso — priorité absolue

Chercher largement tous secteurs, niveaux, CDD/CDI, stages, appels à candidatures, ONG, institutions, entreprises privées et missions de consultance.

Sources à vérifier à chaque passage :

- **Alerte Job** — `https://alertejob.org/job-list/` ; filtrer/étiqueter Burkina Faso et distinguer les appels d’offres des emplois.
- **Emplois Burkina** — `https://emploisburkina.bf/` ; exploiter les offres datées, leurs catégories et les liens `POSTULER`.
- Pages carrière officielles des employeurs burkinabè, institutions publiques, agences des Nations Unies, ONG et bailleurs présents au Burkina.
- ReliefWeb, Impactpool, Devex et UN Jobs avec recherche Burkina Faso / Ouagadougou / Bobo-Dioulasso.
- Recherches Web ciblées en français : `offre emploi Burkina Faso`, `recrutement Ouagadougou`, `appel à candidature Burkina Faso`, plus le secteur et la date courante.

Ne pas confondre appel d’offres de fourniture/prestation et emploi salarié : inclure les deux uniquement s’ils sont clairement étiquetés.

### 2. Ghana — passage dédié en anglais

- **Jobberman Ghana** — `https://www.jobberman.com.gh/jobs` ; parcourir les catégories, le niveau et les postes récents.
- Pages carrière officielles d’entreprises, ONG, universités, institutions financières et organisations internationales présentes au Ghana.
- ReliefWeb, Impactpool, Devex et UN Jobs filtrés Ghana / Accra / Kumasi.
- Requêtes en anglais : `jobs Ghana`, `vacancy Accra`, `graduate jobs Ghana`, avec secteur et date courante.

### 3. Côte d’Ivoire — passage dédié

- **Afrique Emplois Côte d’Ivoire** — `https://afriqueemplois.com/ci` ; vérifier les dates et la destination réelle du bouton de candidature.
- Emploi.ci et Educarriere.ci uniquement lorsqu’ils sont accessibles publiquement, sans contourner de CAPTCHA ou de contrôle de sécurité.
- Pages carrière officielles des employeurs ivoiriens, BAD/AfDB, AGL, banques, télécoms, universités, ONG et agences des Nations Unies.
- ReliefWeb, Impactpool, Devex et UN Jobs filtrés Côte d’Ivoire / Abidjan / Bouaké ; recherches françaises ciblées sur les offres récentes.

### 4. Afrique de l’Ouest, UMOA et remote international

**Afrique de l’Ouest / UMOA** : Bénin, Burkina Faso, Côte d’Ivoire, Guinée-Bissau, Mali, Niger, Sénégal, Togo, ainsi que Ghana. Vérifier en rotation : UEMOA, BCEAO, CEDEAO/ECOWAS, BAD/AfDB, Union africaine, agences ONU, ONG/humanitaire et pages carrière des employeurs directs.

**Remote** : vérifier Remote OK, We Work Remotely et Himalayas, puis les pages carrière directes. Ne retenir une offre remote que si elle accepte réellement des candidats internationaux, africains ou du pays indiqué ; exclure les offres géographiquement restreintes (par exemple États-Unis uniquement) sauf si cette restriction est signalée clairement et qu’elle reste pertinente. Inclure tous domaines : tech, design/création, marketing, support, vente, opérations, finance, éducation, santé, recherche, logistique et administration.

## Sources mondiales emploi

- LinkedIn Jobs et Indeed pour la découverte large ; confirmer ensuite l’annonce chez l’employeur.
- ReliefWeb, Devex, UN Jobs, Impactpool pour organisations internationales, ONG et humanitaire.
- Remote OK, We Work Remotely, Himalayas et pages carrière officielles pour le remote.
- Recherches ciblées par pays, ville, secteur, langue et date de publication. Varier les requêtes plutôt que répéter la même recherche chaque jour.

## Bourses et subventions

Sources généralistes : Opportunity Desk, ScholarshipDb, ScholarshipsAds, Mastersportal et Scholarshipportal. Vérifier en rotation les programmes DAAD, Chevening, Fulbright, Erasmus Mundus, Commonwealth, Australia Awards, MEXT, Eiffel et Vanier, toujours auprès de la source officielle.

Pour l’Afrique : Mastercard Foundation Scholars Program, Mo Ibrahim Foundation, AWARD, AIMS, BAD, Union africaine, YALI/Mandela Washington Fellowship, Campus France, CAMES et les programmes chinois, marocains ou tunisiens ouverts aux pays concernés. Vérifier les critères de nationalité avant inclusion.

## Anti-doublons et fraîcheur

- Contrôler que la deadline n’est pas expirée au moment de l’envoi.
- Dédupliquer par organisation + intitulé + lieu, même si plusieurs sites reprennent l’annonce.
- Une bourse déjà signalée ne revient que si la date limite est à moins de sept jours ou qu’un détail important a changé.
- Pour l’emploi, privilégier les publications nouvelles ; ne répéter une offre que si sa date limite approche ou que les modalités ont changé.
- Ne jamais inventer une date, un salaire, une éligibilité ou l’ouverture internationale d’un poste remote.

## Format du digest quotidien

Digest court et scannable sur Telegram, en quatre blocs : **Emploi Monde**, **Emploi Afrique de l’Ouest / UMOA**, **Bourses Monde**, **Bourses Afrique de l’Ouest / UMOA**. Trois à cinq éléments maximum par bloc ; intégrer les offres Burkina, Ghana et Côte d’Ivoire dans le bloc Afrique de l’Ouest avec leurs tags pays. Chaque élément tient en trois ou quatre lignes condensées, puis le lien direct. Si rien de solide n’a été trouvé dans un passage obligatoire, écrire explicitement : `Rien de nouveau aujourd’hui qui remplisse les critères`.

## Limites

- Une couverture large ne signifie pas publier tout ce qui existe : la fiabilité, la fraîcheur et la possibilité de candidater priment.
- Respecter les accès publics des sites ; ne pas contourner CAPTCHA, connexion, paywall ou contrôle de sécurité.
- Les dates, montants et critères changent : revérifier toute information avant chaque digest.
