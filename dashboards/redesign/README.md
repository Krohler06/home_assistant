# Dashboard Home Assistant — redesign

Cinq fragments de vue Lovelace en mode YAML : `accueil.yaml`, `pieces.yaml`,
`climat.yaml`, `maison.yaml` et `systeme.yaml`. Ils utilisent les cartes
`bubble-card`, `mushroom` et `layout-card` ; installer ces cartes via HACS et
les enregistrer comme ressources Lovelace avant l'utilisation.

## Installation

1. Copier le répertoire `dashboards/redesign/` dans `/config/dashboards/redesign/`.
2. Dans le YAML du dashboard Lovelace, conserver `views:` puis insérer une
   directive d'inclusion listant les vues du dossier. Exemple, si le dashboard
   autorise les inclusions YAML :

   ```yaml
   views: !include_dir_merge_list dashboards/redesign/
   ```

   Si votre éditeur Lovelace ne prend pas en charge les inclusions dans la
   définition du dashboard, copier le contenu des cinq fichiers (chaque fichier
   est une liste de vues) sous la clé `views:` du dashboard existant. Ces
   fragments ne remplacent pas la configuration Home Assistant et n'écrasent
   aucun dashboard préexistant.
3. Adapter les chemins de navigation (`/lovelace/pieces`, `/lovelace/climat`,
   `/lovelace/maison`) au slug réel du dashboard si nécessaire.
4. Redémarrer/recharger le dashboard et vérifier l'état de chaque entité dans
   Home Assistant.

## Remarques

- Les identifiants employés ont été repris de la configuration et de la liste
  d'entités de référence disponibles. Les capteurs Dressing incluent le cumulus
  `water_heater.dhwp_actuator` ainsi que ses deux états binaires et les relevés
  de température/humidité identifiés.
- Le Bureau utilise les trois capteurs référencés (température, humidité,
  batterie). Les lumières restent volontairement une section vide : TODO pour
  les identifiants, sans supposition.
- Aucune entité n'est filtrée ou supprimée. Une entité débranchée reste dans
  sa carte et Home Assistant peut y afficher `unavailable` / indisponible.
- Les fichiers ne contiennent ni secrets ni identifiants de connexion. Le
  modèle `secrets.yaml.example` concerne les secrets de configuration, pas les
  cartes Lovelace.

## Validation

Relire chaque YAML. Si `yamllint` est installé, lancer depuis la racine du dépôt :

```sh
yamllint -c .yamllint dashboards/redesign/*.yaml
```

Puis valider la configuration Lovelace dans Home Assistant avant déploiement.
