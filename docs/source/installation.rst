INSTALLATION
============

GeoNature repose sur les composants suivants :

- PostgreSQL / PostGIS
- Python 3 et dépendances Python nécessaires à l'application
- Flask (framework web Python)
- Apache
- Angular 7, Angular CLI, NodeJS
- Librairies javascript (Leaflet, ChartJS)
- Librairies CSS (Bootstrap, Material Design)


Deux méthodes d’installation existent :

- :ref:`installation-all` : Installation de GeoNature, TaxHub et UsersHub.
- :ref:`installation-standalone` : TaxHub et UsersHub ne sont pas installé (mais leur schémas sont tous de même créés dans la base de données).


Prérequis
*********

- Ressources minimum serveur :

  - Un serveur Debian 10 ou Debian 11 architecture 64-bits
  - 4 Go RAM
  - 20 Go d’espace disque


- GeoNature nécessite d’accéder à des ressources externes durant son installation et son fonctionnement. Si vous utilisez un serveur mandataire, celui-ci doit permettre l’accès aux domaines suivants :

  - https://pypi.python.org
  - https://geonature.fr/
  - https://codeload.github.com/
  - https://nodejs.org/dist
  - https://registry.npmjs.org
  - https://www.npmjs.com
  - https://raw.githubusercontent.com/
  - https://inpn.mnhn.fr/mtd
  - https://preprod-inpn.mnhn.fr/mtd
  - https://wxs.ign.fr/


.. _preparation-server:
