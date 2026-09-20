# Toutfaire

Toutfaire est la version personnelle de Hermes Agent utilisee par P4T6R. Le
fork conserve le moteur et la compatibilite Hermes, avec une identite visuelle
et un ensemble de competences centres sur les usages quotidiens du proprietaire.

## Usages principaux

- **Emploi et bourses** : veille quotidienne mondiale, Afrique et zone
  UMOA/UEMOA, avec dates limites, eligibilite, financement et liens directs.
- **Photographie et Lightroom** : prise de vue, composition, exposition,
  retouche et suivi des fonctions IA de Lightroom.
- **IA pour le code** : suivi des assistants, modeles et outils de programmation
  assistes par IA.
- **QGIS et GeoAI** : extraction et nettoyage de batiments, recalage,
  jointures fiscales, detection de changements, piscines et panneaux solaires.

## Identite

Le theme integre `toutfaire` affiche le nom Toutfaire dans le terminal et dans
les panneaux de reponse. Il est le theme par defaut de ce fork. Pour revenir au
theme Hermes original :

```yaml
display:
  skin: default
```

## Competences personnalisees

- `skills/research/emploi-bourses-watch`
- `skills/creative/photography-lightroom`
- `skills/autonomous-ai-agents/ai-coding-news`
- `skills/gis/`

Les identifiants Telegram, cles API, historiques, resultats de cron et donnees
personnelles d'execution ne font pas partie du depot. Ils doivent rester dans
la configuration privee de l'instance deployee.

## Origine

Toutfaire est base sur [Hermes Agent](https://github.com/NousResearch/hermes-agent)
de Nous Research et conserve sa licence MIT. Les mises a jour du moteur doivent
etre recuperees depuis le remote `upstream`.
