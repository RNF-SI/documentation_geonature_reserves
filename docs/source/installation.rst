Installer GeoNature
===================

GeoNature repose sur les composants suivants :

- PostgreSQL / PostGIS
- Python 3 et dépendances Python nécessaires à l'application
- Flask (framework web Python)
- Apache
- Angular 7, Angular CLI, NodeJS
- Librairies javascript (Leaflet, ChartJS)
- Librairies CSS (Bootstrap, Material Design)


Tous seront installés via la procédure qui suit.

Deux méthodes d’installation existent :

- L'installation complète de GeoNature, TaxHub et UsersHub.
- L'installation de GeoNature uniquement, TaxHub et UsersHub ne sont pas installé (mais leur schémas sont tous de même créés dans la base de données).

TaxHub et UsersHub sont indispensables au fonctionnement de GeoNature, mais peuvent être déjà installés sur un autre serveur (dans le cas où l'on dispose de plusieurs instances GeoNature par exemple).

Nous verrons ici comment réaliser l'installation complète. Pour une installation de GeoNature uniquement, référez vous à la documentation officielle (http://docs.geonature.fr/installation.html#installation-standalone).

Commencer la procédure en se connectant au serveur en SSH (voir partie :ref:`connexion-serveur`) avec l’utilisateur dédié précédemment créé (voir partie :ref:`creer-utilisateur`), c'est à dire ``geonatureadmin``).

Récupérer les fichiers d'installation
*************************************

GitHub est un service web d'hébergement et de gestion de code fait pour les développeurs.

L'ensemble du code de GeoNature, logiciel open-source, est disponible sur `le GitHub du Parc National des Ecrins <https://github.com/PnEcrins/GeoNature>`_. Nous allons donc télécharger les fichiers d'installation directement depuis leur dépot GitHub.

.. warning::

  Soyez sûr d'être à la racine du dossier de l'utilisateur geonatureadmin ``/home/geonatureadmin`` (voir partie :ref:`explorer-repertoires`), sinon l'installation ne fonctionnera pas.

Lancez ensuite les commandes suivantes (en remplaçant ``2.8.1`` par le numéro de la `dernière version stable de GeoNature <https://github.com/PnEcrins/GeoNature/releases>`_):

::

  wget https://raw.githubusercontent.com/PnX-SI/GeoNature/2.8.1/install/install_all/install_all.ini
  wget https://raw.githubusercontent.com/PnX-SI/GeoNature/2.8.1/install/install_all/install_all.sh

Les fichiers sont maintenant téléchargés. Pour le vérifier, vous pouvez lancer la commande ``ls``, et vous devriez avoir les fichiers ``install_all.ini`` et ``install_all.sh`` de listés.

Modifier le fichier d'installation
**********************************

Certains paramètres doivent être précisés avant de procéder à l'installation. Ils sont stockés dans le fichier ``install_all.ini``.

Pour modifier un fichier sur un serveur, il existe plusieurs solutions, dont en voici deux :

1. La première consiste à modifier le fichier directement dans la console. Pour cela, nous utiliserons le programme appelé ``Nano`` :
  Lancez la commande suivante :

  ::

    nano install_all.ini

  Votre fichier texte apparait dans la console. Votre curseur est en haut, vous pouvez vous déplacer dans le fichier en utilisant la roulette de la souris ou les flèches du clavier.
  Effectuez les modifications désirées de manière classique, comme sur un logiciel de traitement de texte.

  Pour quitter ``Nano``, appuyez simultanément sur les touches ``CTRL + X``. En bas de la console, il vous est proposé de sauvegarder ou non les modifications. Tapez simplement sur la touche ``O`` pour les sauvegarder (ou ``N`` pour ne pas les sauvegarder).

2. La seconde solution, si vous n'êtes pas à l'aise avec la console, consiste à charger le fichier texte sur votre PC, le modifier, et le renvoyer sur le serveur.

  Pour cela, vous allez devoir créer une connexion SFTP au serveur. Si vous utilisez MobaXterm, répétez la même manipulation que pour une connexion SSH (décrite dans la partie :ref:`connexion-serveur`), mais cliquez sur ``SFTP`` au lieu de cliquer sur ``SSH``.

  Une fois connecté avec l'utilisateur geonatureadmin, vous arrivez directement dans le répertoire de cet utilisateur : ``/home/geonatureadmin/`` :

  .. image :: _static/connexion_sftp.PNG


  Vous pouvez facilement transférer vos fichiers, depuis votre ordinateur (panel de gauche) vers le serveur (panel de droite).

  Sur votre serveur vous voyez normalement vos fichiers ``install_all.ini`` et ``install_all.sh``.

  Vous pouvez cliquer droit sur ``install_all.ini`` et cliquer sur ``Open with ...``. Choisissez le programme que vous préférez, le bloc-notes fera très bien l'affaire.

  Vous pouvez alors modifier le fichier. Une fois terminé, sauvegardez-le (``Fichier/Enregistrer`` ou ``CTRL + S``). Une fenêtre de MobaXterm s'affiche alors :

  .. image :: _static/save_remote_file.PNG


  Cliquez simplement sur ``Yes``. Vos mises à jour seront sauvegardées sur le serveur.

  .. warning::

    En double cliquant sur le fichier sur MobaXterm, votre fichier va s'ouvrir avec l'éditeur de MobaXterm. Néanmoins, vous constaterez que les caractères accentués ne s'affichent pas correctement (propriÃ©taire au lieu de propriétaire par exemple). C'est lié à un défaut de compatibilité d'encodage de ce logiciel.

    Si toutes vos modifications concernent des parties sans caractères spéciaux, alors vous pouvez sauvegarder avec, mais dans le doute, il est conseillé de ne pas l'utiliser.

Les paramètres à saisir
***********************

Ci-après le fichier ``install_all.ini``, avec en commentaire sur la même ligne les informations à modifier (# => commentaire).

Renseignez à minima l’URL (ou IP) de votre serveur (avec un ``/`` à la fin) ainsi que l’utilisateur PostgreSQL que vous souhaitez et son mot de passe. Le script se chargera d’installer PostgreSQL et de créer l’utilisateur de base de données que vous avez renseigné.

Pour la définition des numéros de version des dépendances, voir `le tableau de compatibilité des versions de GeoNature avec ses dépendances <https://github.com/PnX-SI/GeoNature/blob/master/docs/versions-compatibility.rst>`_. Il est déconseillé de modifier ces versions, chaque nouvelle version de GeoNature étant fournie avec les versions adaptées de ses dépendances.

::

  # Mode d’installation
  # Indiquer dev pour une installation de developement
  mode=prod # => NE PAS MODIFIER

  # Langue du serveur
  # valeur possible : fr_FR.UTF-8, en_US.utf8
  # locale -a pour voir la liste des locales disponible
  my_local=fr_FR.UTF-8 # => NE PAS MODIFIER

  # My host URL or IP, starting with http and with / at the end
  my_url=http://mon.domaine.com/ # => ENTRER L'ADRESSE IP DE VOTRE SERVEUR (LA MEME QUE POUR LA CONNEXION SSH) PRECEDEE DE http:// ET SUIVIE D'UN /, PAR EXEMPLE : my_url=http://188.165.104.13/

  ### CONFIGURATION PostgreSQL ###

  # Localisation du server PostgreSQL.
  # Les scripts d'installation automatique ne fonctionnent que si votre BDD est sur le même serveur (localhost)
  pg_host=localhost # => NE PAS MODIFIER
  # Port sur lequel PostgreSQL ecoute
  pg_port=5432 # => NE PAS MODIFIER
  # Nom de l'utilisateur propriétaire des bases UsersHub, GeoNature, TaxHub
  user_pg=geonatadmin # => VOUS POUVEZ METTRE CE QUE VOUS VOULEZ, OU LAISSER COMME CA
  # Mot de passe de l'utilisateur propriétaire des bases UsersHub, GeoNature, TaxHub
  user_pg_pass=monpassachanger # => ENTREZ VOTRE MOT DE PASSE ICI, CE QUE VOUS VOULEZ

  ### CONFIGURATION USERSHUB ###

  # Installation de l'application UsersHub
  install_usershub_app=true # => NE PAS MODIFIER
  # Version de UsersHub
  usershub_release=2.2.1 # => NE PAS MODIFIER

  ### CONFIGURATION GEONATURE ###

  # Version de GeoNature
  geonature_release=2.8.1 # => NE PAS MODIFIER
  # Effacer la base de données GeoNature existante lors de la réinstallation
  drop_geonaturedb=false # => NE PAS MODIFIER, A MOINS DE PROCEDER A UNE REINSTALLATION DE GEONATURE
  # Nom de la base de données GeoNature
  geonaturedb_name=geonature2db # => NE PAS MODIFIER
  # SRID de la projection locale (2154 conseillé pour la métropole)
  srid_local=2154 # => A MODIFIER UNIQUEMENT SI VOUS N'ETES PAS EN METROPOLE
  # Intègre des couches SIG (communes de métropole)
  # Seules des couches de métropole sont fournies (false conseillé hors métropole)
  install_sig_layers=true # => A MODIFIER UNIQUEMENT SI VOUS N'ETES PAS EN METROPOLE
  # Installe les grilles INPN (1, 5 et 10km)
  install_grid_layer=true # => NE PAS MODIFIER
  # Install default French DEM (Mainland France only - BD alti 250m))
  install_default_dem=true # => A MODIFIER UNIQUEMENT SI VOUS N'ETES PAS EN METROPOLE
  # Vectorise DEM raster for more performance. This will increase installation duration and use more disk space
  vectorise_dem=false # => VOUS POUVEZ METTRE true SI VOUS LE SOUHAITEZ, PLUS LONG A L'INSTALLATION MAIS AUGMENTE LES PERFORMANCES DE L'APPLICATION ENSUITE
  # Installer les données exemples (valid pour la métropole uniquement ; false conseillé hors métropole)
  add_sample_data=true # => METTRE false SI VOUS ETES FAMILIE DE GEONATURE

  ## MODULES GEONATURE ##

  # Installer le module validation ?
  install_module_validation=true # => NE PAS MODIFIER
  # Installer le module occurrence d'habitat ?
  install_module_occhab=true # => NE PAS MODIFIER

  # Proxy - si le serveur sur lequel se trouve GeoNature se trouve derrière un proxy
  # Laisser vide si vous n'avez pas de proxy
  proxy_http= # => NE PAS MODIFIER
  proxy_https= # => NE PAS MODIFIER

  ### CONFIGURATION TAXHUB ###

  # Version de TaxHub
  taxhub_release=1.9.1 # => NE PAS MODIFIER
  # Pour des questions de performances de GeoNature, il n'y a pas de base de données spécifique pour TaxHub
  # Le schéma "taxonomie" de TaxHub est intégré dans la BDD de GeoNature pour que les requêtes ne soient pas trop pénalisées
  # par un accès à une BDD distante.

Lancer l'installation
*********************

C'est bon, vous êtes maintenant prêt à installer GeoNature !

Lancez simplement les commandes suivantes (toujours depuis /home/geonatureadmin/) :

::

  touch install_all.log
  chmod +x install_all.sh
  ./install_all.sh 2>&1 | tee install_all.log

Il vous sera demandé de taper le mot de passe de l'utilisateur (le mot de passe que vous avez donc configuré pour ``geonatureadmin``), potentiellement à plusieurs reprises.

.. note::

  Lorsque vous tapez un mot de passe sur la console, rien n'apparait (ni le caractère tapé, ni une étoile comme on en à l'habitude). Tapez bien votre mot de passe en entier et tapez sur "Entrée", ça marchera sans problèmùe ;)

De (très) nombreuses lignes vont ensuite se succéder, pour télécharger les différents packages ou réferentiels nécessaires et les installer.

Certaines étapes peuvent paraitre longues, en particulier si vous avez mis ``true`` à l'option de vectorisation du MNT. Mais ne touchez à rien (sauf éventuellement taper le mot de passe quand c'est demandé) tant que le message ``L'installation est terminée!`` n'apparait pas.

A titre indicatif, et dépendant des performances de votre serveur, l'installation peut durer 1h30.

Une fois le message ``L'installation est terminée!`` affiché, il vous reste encore une petite commande à lancer :

::

  exec bash

Et voilà, GeoNature est installé sur votre serveur, félicitations !

Vous pouvez y accéder aux adresses suivantes :

* http://monadresseip/geonature/
* http://monadresseip/taxhub/
* http://monadresseip/usershub/

Vous pouvez vous connecter avec l’utilisateur intégré par défaut (admin/admin).

.. note::

  Pour en savoir plus sur TaxHub et UsersHub, leur configuration et leur utilisation, reportez vous à leur documentation officielle :

  * `Documentation de TaxHub <https://taxhub.readthedocs.io>`_
  * `Documentation de UsersHub <https://usershub.readthedocs.io>`_

En cas d'erreur
***************

Vous pouvez à tout moment contacter le chargé de mission géomaticien de RNF : `zacharie.moulin-rnf@espaces-naturels.fr <mailto:zacharie.moulin-rnf@espaces-naturels.fr>`_

Lors du processus d'installation, des fichiers dits de logs sont créés. Il s'agit de rapports sur l'état de l'installation, qui détaillent les éventuelles erreurs.

Vous pouvez les consulter (utiliser Nano comme décrit précédemment) ou les charger sur votre ordinateur (avec une connexion SFTP) :

* Logs de l’installation de la base de données : /home/geonatureadmin/geonature/var/log/install_db.log
* Log général de l’installation de l’application : /home/geonatureadmin/install_all.log
