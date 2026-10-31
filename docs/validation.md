# Contrôles avant déploiement

- Charger `dashboards/dashboard_redesign.yaml` avec un parseur YAML.
- Vérifier que les références au slug de l'ancien aspirateur test ne subsistent pas.
- Confirmer dans **Outils de développement → États** les entités du robot, notamment caméra de carte et batterie, ainsi que les entités de température et les capteurs batterie.
- Confirmer que chaque ressource HACS est installée et chargée côté frontend.
- Tester la navigation, le flux caméra, Pronote, les commandes de prises/chauffage et le filtre < 30 % dans une instance de test.
- Adapter les chemins `/dashboard-maison/` et `/local/floor/maison1.png` à l'instance réelle.
