# Home Assistant — configuration de la maison

[![YAML & Home Assistant config](https://github.com/Krohler06/home_assistant/actions/workflows/yamllint.yml/badge.svg)](https://github.com/Krohler06/home_assistant/actions/workflows/yamllint.yml)
![Home Assistant OS](https://img.shields.io/badge/Home%20Assistant-OS-41BDF5?logo=homeassistant&logoColor=white)
![Configuration](https://img.shields.io/badge/configuration-YAML-ccaa33)

Configuration versionnée pour le dépôt [`Krohler06/home_assistant`](https://github.com/Krohler06/home_assistant). Le tableau de bord Maison regroupe les vues Accueil, pièces, plan, bureau et batteries. Ce dépôt n'embarque pas l'état privé/runtime de l'instance Home Assistant.

## 1. Stack technique

- **Plateforme** : Home Assistant OS (HAOS), version indiquée dans le contexte comme 14 ; vérifier la version Core effectivement installée avant un changement.
- **Interface** : tableau de bord Lovelace YAML, vues Sections, cartes natives et thème `visionos` utilisé de manière cohérente.
- **Cartes personnalisées activement utilisées dans ce dashboard** (à installer/maintenir avec HACS) : `area-card-plus`, `status-card`, `pronote-homework-card`, `pronote-averages-card`, `pronote-grades-card`, `xiaomi-vacuum-map-card`, `advanced-camera-card`, `entity-progress-badge` et `firemote-card`. Le YAML source contenait aussi `entity-progress-card`, `layout-card` et `horizontal-layout`, non requis par cette refonte. La nouvelle configuration ne dépend pas de Mushroom.
- Les ressources frontend HACS sont volontairement exclues du dépôt : elles sont téléchargées et servies par HA/HACS.

## 2. Prérequis

1. Une instance HAOS opérationnelle, avec accès administrateur et sauvegarde récente.
2. HACS correctement installé, si l'on souhaite utiliser les cartes personnalisées conservées.
3. Les intégrations et entités nécessaires déjà disponibles dans l'instance. Les identifiants d'entités restent propres à chaque installation ; comparez-les à **Outils de développement → États**.
4. Git pour cloner/publier le dépôt, ou un accès Samba/SSH au répertoire `/config`.

Les cartes personnalisées doivent être installées dans HACS puis leurs ressources frontend chargées (généralement après redémarrage de Home Assistant et rechargement complet du navigateur). Le dépôt ne fige pas les versions des cartes.

## 3. Installation

1. Sauvegardez HAOS et vérifiez les identifiants d'entités notés plus bas.
2. Copiez `dashboards/dashboard_redesign.yaml` dans `/config/dashboards/dashboard_redesign.yaml` (créez le dossier au besoin).
3. Dans `/config/configuration.yaml`, ajoutez ou fusionnez cette déclaration sans dupliquer une clé `lovelace` existante :

   ```yaml
   lovelace:
     mode: storage
     dashboards:
       maison:
         mode: yaml
         title: Maison
         icon: mdi:home
         show_in_sidebar: true
         filename: dashboards/dashboard_redesign.yaml
   ```

   Si `lovelace:` ou `dashboards:` existe déjà, fusionnez les blocs au lieu de remplacer les autres tableaux de bord.
4. Contrôlez la configuration depuis **Outils de développement → YAML** (ou l'outil de vérification disponible dans votre version), puis redémarrez Home Assistant si nécessaire. Ouvrez ensuite le tableau de bord **Maison** et confirmez que les cartes HACS sont chargées.
5. Ajustez les chemins de navigation générés par les cartes de zones si le slug/URL de votre tableau de bord diffère de `/dashboard-maison/`.

## 4. Utilisation

- **Accueil** : trois blocs — présence et météo, accès aux pièces, alertes et équipements.
- **Vues de pièce** : disposition cohérente par fonction (confort, chauffage/climatisation, médias ou prises). Le Bureau reprend uniquement les entités bureau présentes dans le dashboard source.
- **Plan** : conserve l'image locale `/local/floor/maison1.png`. Placez votre plan à `/config/www/floor/maison1.png` ou modifiez le chemin pour correspondre à votre installation.
- **Batteries** : n'affiche que les niveaux inférieurs à 30 % dans la section d'alerte. L'inventaire complet n'est pas affiché afin de garder la vue épurée ; il reste consultable dans les vues **Bureau**, **Chambre Nina**, **Entrée** et **Local technique** selon l'appareil. Vérifiez que les capteurs renvoient bien un état numérique comparable ; les états indisponibles ne seront pas interprétables comme un pourcentage.
- Les automatisations/interphone, caméras, commandes médias, pronote et l'aspirateur apparaissent uniquement si leurs intégrations et entités correspondantes sont configurées.

## 5. Configuration & secrets

| Élément | Référence observée | Action / précaution |
|---|---|---|
| Aspirateur Valetudo | `vacuum.valetudo_huguette` ; sujet `valetudo/huguette` | Entité corrigée conformément à la demande. Vérifier la caméra de carte et le capteur de batterie portant le suffixe `huguette` dans cette instance. |
| Climatisation Noé | `climate.clim_noe`, `sensor.current_clim_noe`, `select.mode_clim_noe`, `select.fan_clim_noe` | Ne pas réutiliser le capteur `current_clim_noe` dans la vue parentale. |
| Chambre parentale | `climate.clim_nous`, `sensor.current_clim_nous`, sélecteurs `select.*_clim_nous` | Capteur de température corrigé en `sensor.current_clim_nous`. |
| Scolarité / Pronote | capteurs `sensor.pronote_farsetti_lattelais_nina_*`, calendrier `calendar.emploi_du_temps_de_nina` observé dans la source | Les cartes Pronote sont conservées ; le calendrier source n'est pas affiché dans cette version. |
| Appareils Xiaomi / médias | noms d'entités `media_player.*xiaomi*`, `switch.prise_*`, télécommande `remote.boitier_tv_xiaomi` | Entités détectées par leur identifiant ; le YAML ne permet pas d'affirmer quelle intégration HA les fournit toutes. |
| Capteurs / météo | `sensor.thermometre_*`, `sensor.wifi_temperature_humidity_*`, `weather.nice`, capteurs pollen Kleenex | Confirmer que les entités sont disponibles après import. |
| Caméras / sécurité | `camera.camera_bal_fluide`, `camera.camera_allee_fluide`, `camera.cuisine_camera`, capteurs de contact et porte | Vérifier les droits et le chargement du flux depuis le navigateur/appareil cible. |
| Plan de maison | `/local/floor/maison1.png` | Fichier servi depuis le dossier `/config/www/floor/` ; il n'est pas fourni ici. |
| Secrets | `secrets.yaml.example` | Exemple sans valeur réelle. Garder `secrets.yaml`, `.storage`, bases de données, journaux et sauvegardes hors Git. Ne jamais publier tokens, mots de passe, URL privées ou identifiants. |

Les intégrations/requêtes explicitement suggérées par le YAML source sont **Valetudo**, **Pronote**, la gestion de clim/chauffage, les appareils/capteurs Xiaomi, météo, médias et caméras. Les noms d'entités seuls ne garantissent pas l'intégration exacte ni sa configuration : cette dernière doit être vérifiée dans l'instance HA. Aucune valeur de secret du YAML source n'est reproduite dans ce dépôt.

### Entity ID utilisés dans le tableau de bord

Liste dédupliquée extraite du YAML livré (89 identifiants) :

```text
automation.open_door
automation.placard
binary_sensor.contact_placard
binary_sensor.porte_entree_porte
button.enregistreur_gambetta_answer_call
button.enregistreur_gambetta_cancel_call
button.enregistreur_gambetta_hangup_call
camera.camera_allee_fluide
camera.camera_bal_fluide
camera.cuisine_camera
climate.clim_noe
climate.clim_nous
climate.radiateur_couloir_2
climate.radiateur_cuisine
climate.seche_serviette
light.ampoule_hall
light.ampoule_toilette
media_player.android_tv_192_168_5_86
media_player.boitier_tv_xiaomi
media_player.boitier_tv_xiaomi_2
media_player.chambre_parentale
media_player.chambre_parentale_2
media_player.playstation_4
media_player.salon_boitier_xiaomi
media_player.samsung_6_series_50
media_player.tv_parents
media_player.tv_parents_2
media_player.tv_samsung_6_series_50
person.gambetta_bis
person.magali
person.nina
select.fan_clim_noe
select.fan_clim_nous
select.mode_clim_noe
select.mode_clim_nous
sensor.contact_placard_batterie
sensor.current_clim_noe
sensor.current_clim_nous
sensor.ipad_de_magali_2_battery_level
sensor.ipad_de_magali_battery_level
sensor.iphone_battery_level
sensor.iphone_de_jerem_battery_level
sensor.iphone_de_jerem_watch_battery_level
sensor.iphone_de_magali_batterie_2
sensor.iphone_de_magali_watch_battery_level
sensor.kleenex_pollen_radar_maison_arbres_niveau
sensor.kleenex_pollen_radar_maison_graminees_niveau
sensor.kleenex_pollen_radar_maison_herbacees_niveau
sensor.nice_humidity
sensor.pc_nina_battery_level_2
sensor.porte_entree_batterie
sensor.pronote_farsetti_lattelais_nina_averages
sensor.pronote_farsetti_lattelais_nina_grades
sensor.pronote_farsetti_lattelais_nina_homework
sensor.seche_serviette_io_2096051_4_relative_humidity
sensor.seche_serviette_io_2096051_7_temperature
sensor.sm_t510_battery_plus
sensor.system_monitor_temperature_du_processeur
sensor.thermometre_bureau
sensor.thermometre_bureau_batterie
sensor.thermometre_bureau_humidite
sensor.thermometre_couloir_humidite
sensor.thermometre_couloir_temperature
sensor.thermometre_cuisine_humidite
sensor.thermometre_cuisine_temperature
sensor.thermometre_dressing_humidite
sensor.thermometre_dressing_temperature
sensor.thermometre_nina_batterie
sensor.thermometre_nina_humidite
sensor.thermometre_nina_temperature
sensor.thermometre_salon_humidite
sensor.thermometre_salon_temperature_2
sensor.valetudo_huguette_battery_level
sensor.wifi_temperature_humidity_10_batterie
sensor.wifi_temperature_humidity_10_temperature
sensor.wifi_temperature_humidity_11_batterie
sensor.wifi_temperature_humidity_12_batterie
sensor.wifi_temperature_humidity_8_batterie
sensor.wifi_temperature_humidity_9_batterie
switch.multimedia_salon
switch.prise_congelateur
switch.prise_connectee
switch.prise_lave_linge_socket_1_2
switch.prise_machine_cafe
switch.prise_microonde
switch.prise_ordi
vacuum.valetudo_huguette
water_heater.dhwp_actuator
weather.nice
```

Cartes custom activement utilisées : `custom:advanced-camera-card`, `custom:area-card-plus`, `custom:firemote-card`, `custom:pronote-averages-card`, `custom:pronote-grades-card`, `custom:pronote-homework-card`, `custom:status-card`, `custom:xiaomi-vacuum-map-card`.

## 6. Qualité & CI

Le workflow `.github/workflows/yamllint.yml` lance `yamllint` sur les fichiers YAML versionnés et exécute `check_config` du conteneur Home Assistant. Le test `check_config` utilise volontairement une **configuration HA minimale générée dans le job** : il ne valide ni les entités de votre instance ni le schéma frontend des cartes Lovelace. `yamllint` contrôle la syntaxe/forme YAML, pas l'existence des cartes HACS ou entités. Validez le dashboard dans votre instance de test avant déploiement.

Exécution locale :

```sh
python -m pip install yamllint
find . -type f \( -name '*.yaml' -o -name '*.yml' \) -not -path './.git/*' -print0 | xargs -0 yamllint -c .yamllint
```

La vérification `check_config` complète requiert Docker et utilise la même fixture minimale que le workflow ; ne la confondez pas avec le contrôle du schéma Lovelace. Pour un changement important, testez d'abord dans un environnement HA distinct et restaurez la sauvegarde si nécessaire.

## 7. Déploiement sur HAOS

**Option A — add-on Git pull** : installez/configurez un add-on Git pull de confiance pour cloner le dépôt dans `/config` (ou synchroniser ses fichiers). Définissez le dépôt et la branche voulus dans l'add-on, excluez les fichiers secrets/runtime et vérifiez les permissions. Après mise à jour, placez/maintenez le YAML à `/config/dashboards/dashboard_redesign.yaml`, validez les ressources et redémarrez/rechargez selon le besoin.

**Option B — Samba ou SSH** : clonez le dépôt sur un poste de travail puis copiez le tableau de bord vers `/config/dashboards/` par le partage Samba HAOS ou SSH/SCP. Utilisez un transfert contrôlé, faites une sauvegarde avant remplacement et ne copiez jamais `.storage`, `secrets.yaml`, les bases, logs ni sauvegardes dans le dépôt.

Dans les deux cas : révisez le diff, vérifiez le succès de la CI, contrôlez le fichier final et testez l'interface après déploiement. La CI ne publie ni ne pousse de configuration directement sur l'instance.

## 8. Structure du dépôt & limites connues

```text
.
├── .github/workflows/yamllint.yml
├── .gitignore
├── .yamllint
├── README.md
├── dashboards/dashboard_redesign.yaml
├── docs/validation.md
└── secrets.yaml.example
```

Le plan image `/local/floor/maison1.png` reste un prérequis externe et n'est pas inventé/embarqué. Les identifiants `camera.valetudo_v1_huguette_camera` et `sensor.valetudo_huguette_battery_level` ont été déduits par remplacement du slug du robot dans les identifiants de la carte/capteur source ; confirmez leur existence dans HA, car seul `vacuum.valetudo_huguette` a été expressément validé. Le seuil d'alerte batteries est fixé à 30 % comme choix de présentation, faute de seuil précisé. Le seuil et les états doivent être adaptés aux capteurs en unités différentes ou non numériques.
