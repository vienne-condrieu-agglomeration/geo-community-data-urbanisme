# geo-community-data-urbanisme

Données d'urbanisme publiées sur GitHub Pages, pour les modules GEO : **un fichier JSON par section cadastrale**, qui dit pour
chaque parcelle du territoire de Vienne Condrieu Agglomération quelles zones, prescriptions, informations, servitudes, droit de
préemption et SCOT s'y appliquent.

> Projet communautaire indépendant, **non affilié à Business Geografic**. GEO est un produit de Business Geografic.

## Adresse des données

`https://vienne-condrieu-agglomeration.github.io/geo-community-data-urbanisme/`

| Fichier | Contenu |
|---|---|
| `index.json` | version du format (`format`), date de l'export (`generated_at`), documents d'urbanisme sources (`sources`) et sections publiées par commune (`sections`) |
| `<code INSEE>/<section>.json` | les parcelles d'une section cadastrale (ex. `38087/AB.json`) |

Un module doit lire `format` dans `index.json` avant tout et refuser une version qu'il ne connaît pas.

## Format d'un fichier de section

Un objet dont les clés sont les **identifiants de parcelle (IDU) de 14 caractères** (ex. `38087000AB0215`). Chaque parcelle porte :

| Champ | Contenu |
|---|---|
| `parcel` | `idu`, `insee`, `section`, `number` (numéro cadastral sans zéros de tête), `area` (contenance en m², `null` si inconnue) |
| `locality` | lieu-dit le plus recouvrant la parcelle, ou `null` |
| `documents` | documents d'urbanisme de la commune (`idurba`, `type`, `approved_at`, `state`), le plus récent d'abord |
| `scot` | périmètres de SCOT (`name`, `link`), sans doublon |
| `zones` | zones recoupées par la parcelle, la plus étendue d'abord ; liste vide hors du périmètre couvert |

Chaque zone porte `name`, `type_code`, `type_label`, `approved_at`, `dossier` (règlement), `surface` (m²), `percent` (part de la
parcelle, sur la somme des zones) et `items`, la liste de ce qui s'y applique : `category` (`prescription`, `information`,
`sup_generateur`, `sup_assiette`, `dpu`, `zppaup`), `geometry` (`surface`, `linear`, `point`), `code`, `title`, `label`, `text`,
`references`, `measure` (m² ou m), `count` et `date` (délibération du DPU).

Règles appliquées à la production :

- les lignes d'une même zone qui décrivent le même objet sont regroupées, leurs mesures **sommées** (`count` en garde le nombre) ;
- la surface d'une zone est plafonnée à la contenance de la parcelle (sauf contenance nulle ou inconnue) et celle d'un objet
  surfacique à celle de sa zone ;
- les dates sont au format `AAAA-MM-JJ` ; une valeur absente est `null`, jamais une chaîne vide ;
- une intersection n'est retenue qu'au-delà de 1 m² **ou** de 0,1 % de la parcelle ;
- **aucune donnée nominative** : pas de propriétaire, uniquement des données publiques.

## Fonctionnement

Les données sont calculées par la chaîne d'intégration `vca-gpu-sync` (croisement des parcelles avec les couches du Géoportail de
l'Urbanisme, puis export JSON), qui les publie sur la branche `gh-pages` de ce dépôt, **réécrite à chaque publication** : un seul
commit, l'historique ne grossit pas. La publication est lancée à la main, après un export complet.

La branche `main` ne porte que cette documentation et la licence ; le site est servi depuis `gh-pages`. Les fichiers sont
diffusés en HTTPS avec `Access-Control-Allow-Origin: *` (lecture depuis le navigateur) et un cache d'environ 10 minutes.

## Licences

- **Données** : à préciser. Elles proviennent du Géoportail de l'Urbanisme et du cadastre, croisés par l'agglomération.
- **Documentation de ce dépôt** : [GPL-3.0-or-later](LICENSE) — © 2026 Vienne Condrieu Agglomération.
