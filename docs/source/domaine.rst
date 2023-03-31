Configurer un nom de domaine
============================

Une adresse IP pour accéder à l'application, ce n'est pas forcément le plus simple. Vous disposez peut-être d'un nom de domaine, pour votre site internet par exemple (le nom de domaine de Réserves Naturelles de France est reserves-naturelles.org).

Nous allons voir comment configurer nos applications pour y accéder avec des urls sous la forme nomapplication.nomdomaine.extensiondomaine (par exemple pour le geonature de RNF : geonature.reserves-naturelles.org)

Demandez au gestionnaire de votre site internet, qui doit gérer votre domaine, de vous créer les zones DNS avec les paramètres suivant :

* Nome de la zone : geonature.nomdomaine.extensiondomaine
* Type de zone : A
* Valeur : adresseipduserveur

Cette manipulation fait qu'en tapant ces adresses sur votre navigateur, internet sait qu'il doit vous rediriger vers votre serveur.

Si vous n'avez pas de nom de domaine accessible, en tant que gestionnaire de réserve naturelle, vous pouvez tout à fait demandé à avoir votre sous-domaine sous reserves-naturelles.org (du type geonature-nomdelorganisme.reserves-naturelles.org). Contactez le chargé de mission géomaticien pour faire cette demande.

Vous devez ensuite modifier des fichiers de votre serveur pour qu'il prenne en compte ces paramètres.

Sous-domaines (sans HTTPS)
**************************

Il existe 2 protocoles de connexion à une page web, que vous utilisez tout les jours. Le plus ancien et classique HTTP, qui est nécessaire pour se connecter (le http:// devant une adresse web).
Le second, plus récent, est le HTTPS. C'est un protocole sécurisé, qui est de plus en plus courant aujourd'hui. Et lorsqu'il n'est pas présent, notre navigateur nous avertit souvent par un cadenas barré.

Dans un premier temps, nous allons configurer nos sous-domaines en HTTP, nous verrons ensuite comment les passer en HTTPS (l'un découle de l'autre).

Deux possibilité existent pour créer les accès aux 3 applications (GeoNature, TaxHub et UsersHub) : 

* les 3 applications ont le même sous-domaine, avec le nom de l'application qui suit (sous-domaine.domaine.ext/geonature, sous-domaine.domaine.ext/taxhub ...). C'est la solution par défaut et la plus simple à mettre en oeuvre.
* les 3 applications ont chacun leur sous-domaine (geonature.domaine.ext, taxhub.domaine.ext, usershub.domaine.ext). Cela demande un peu plus de configuration. 

Sous-domaine unique
-------------------

Pour un sous-domaine unique, vous devez éditer fichier ``geonature_config.toml`` :

:: 

  nano geonature/config/geonature_config.toml

Remplacez l'adresse IP de votre serveur par votre domaine : 

:: 

  URL_APPLICATION = 'http://mon.domaine/geonature'
  API_ENDPOINT = 'http://mon.domaine/geonature/api'
  API_TAXHUB = 'http://mon.domaine/taxhub/api'

Vous devez ensuite lancer un script de configuration du serveur apache fourni par geonature :

::

  cd geonature
  ./install/06_configure_apache.sh

Remplacez à nouveau l'IP par votre domaine dans le fichier de conf apache suivant, puis relancer le serveur apache :

:: 

  sudo nano /etc/apache2/sites-available/geonature.conf
  sudo a2ensite geonature.conf
  sudo systemctl reload apache2

Remplacez également l'IP par votre domaine dans le fichier suivant :

::

  nano frontend/src/assets/config.json

Faites un update de GeoNature, et relancez le service :

::

  cd geonature
  source venv/bin/activate
  geonature update-configuration
  deactivate
  sudo systemctl restart geonature

Vous devez maintenant accéder à votre application sur votre domaine mon.domaine/geonature !

Sous-domaine distincts
----------------------

Pour plusieurs sous-domaine, vous devez éditer fichier ``geonature_config.toml`` :

:: 

  nano geonature/config/geonature_config.toml

Remplacez l'adresse IP de votre serveur par votre domaine : 

:: 

  URL_APPLICATION = 'http://geonature.mondomaine'
  API_ENDPOINT = 'http://geonature.mondomaine/api'
  API_TAXHUB = 'http://taxhub.mondomaine/api'

Vous devez ensuite lancer un script de configuration du serveur apache fourni par geonature :

::

  cd geonature
  ./install/06_configure_apache.sh

De base, un seul fichier dit ``vhost`` existe qui redirige vers les configuration des 3 applications (localisé à ``/etc/apache2/sites-available/geonature.conf``).

Vous devez modifier ce fichier comme suit, pour supprimer les liens vers les conf des autres applications :

::


  <VirtualHost *:80>
      ServerName geonature-test.reservenaturelle.fr

      IncludeOptional /etc/apache2/conf-available/geonature.conf

      ErrorLog "/var/log/apache2/geonature_error.log"
      CustomLog "/var/log/apache2/geonature_access.log" combined
  </VirtualHost>

Vous devez créer un fichier taxhub.conf dans le même dossier (avec la commande ``sudo nano /etc/apache2/sites-available/taxhub.conf``) qui contient ce qui suit :

:: 

  <VirtualHost *:80>
      ServerName taxhub-test.reservenaturelle.fr

      <Location />
          ProxyPass http://127.0.0.1:5000/
          ProxyPassReverse http://127.0.0.1:5000/
      </Location>

      Alias "/static" "/home/geonatureadmin/taxhub/static"
      <Directory "/home/geonatureadmin/taxhub/static">
          AllowOverride None
          Require all granted
      </Directory>



      ErrorLog "/var/log/apache2/geonature_error.log"
      CustomLog "/var/log/apache2/geonature_access.log" combined
  </VirtualHost>

Vous devez créer un fichier usershub.conf dans le même dossier (avec la commande ``sudo nano /etc/apache2/sites-available/usershub.conf``) qui contient ce qui suit :

::

  <VirtualHost *:80>
      ServerName usershub-test.reservenaturelle.fr

      <Location />
          ProxyPass http://127.0.0.1:5001/
          ProxyPassReverse http://127.0.0.1:5001/
      </Location>

      ErrorLog "/var/log/apache2/geonature_error.log"
      CustomLog "/var/log/apache2/geonature_access.log" combined
  </VirtualHost>



Modifiez le fichier de configuration de UsersHub :

::

  nano /home/geonatureadmin/usershub/config/config.py

Modifiez la ligne suivante (déjà présentes dans le fichier), en remplaçant le domaine par le votre :

::

  URL_APPLICATION ='http://usershub.mondomaine.fr'

Modifiez le fichier de configuration de TaxHub :

::

  nano taxhub/apptax/config.py


Modifiez la ligne suivante (déjà présentes dans le fichier) :

::

  APPLICATION_ROOT = '/'

Relancez les services de UsersHub et TaxHub :

::

  sudo service usershub restart
  sudo service taxhub restart


Remplacez également l'IP par votre domaine dans le fichier suivant :

::

  nano frontend/src/assets/config.json

Faites un update de GeoNature, et relancez le service :

::

  cd geonature
  source venv/bin/activate
  geonature update-configuration
  deactivate
  sudo systemctl restart geonature

Vos applications doivent maintenant être accessibles via des liens semblalbes à :

* http://geonature.mondomaine.fr
* http://taxhub.mondomaine.fr
* http://usershub.mondomaine.fr


Sécuriser ses sous-domaines (HTTPS)
***********************************

Sécuriser ses sous-domaines peut être intéressant, notamment pour que votre navigateur ne bloque pas l'accès à l'application. L'outil ``CertBot`` permet de le faire assez simplement, grâce au service `Let’s Encrypt <https://letsencrypt.org/>`_.

Installer CertBot :

::

  sudo apt-get install python3-certbot-apache

Un message de confirmation va apparaitre :

::

  Après cette opération, 9 759 ko d'espace disque supplémentaires seront utilisés.
  Souhaitez-vous continuer ? [O/n]

Le ``O`` pour oui est en majuscule, le ``n`` pour non est minuscule. Dans ces cas là, soit vous tapez sur la touche ``o`` de votre clavier, soit simplement sur ``Entrée`` (``O`` par défaut car en majuscule).

L'installation des différents composants nécessaire s'enchaine ensuite.

Tapez ensuite la commande suivante :

::

  sudo certbot

Il vous sera demandé votre adresse mail, entrez la et tapez sur ``Entrée``.
Pour accepter les conditions d'utilisation, tapez ``Y`` (pour Yes) et ``Entrée``.
Validez ou non le partage de votre mail à la fondation "CertBot" en tapant ``Y`` ou ``N`` puis ``Entrée``.

Tous ces éléments sont à saisir qu'à la première utilisation de CertBot.

Ensuite il vous pose une question : "Which names would you like to activate HTTPS for?" et la liste de vos URL existantes s'affiche (correspondant aux fichier .conf d'apache activés).

Saisissez le numéro de l'URL à certifier et tapez sur ``Entrée``.

Si vous avez le message "Congratulations!", c'est tout bon, votre certificat est activé. Vous pourrez le retrouver dans le dossier ``/etc/letsencrypt/live/``.

Répétez l'opération pour toutes les URLs.

Il faut maintenant à nouveau changer les paramètres des fichiers d'applications :

Pour GeoNature et TaxHub :

::

  nano /home/geonatureadmin/geonature/config/geonature_config.toml

Rajouter simplement un s après les http :

::

  URL_APPLICATION = 'https://geonature.mondomaine.fr'
  API_ENDPOINT = 'https://geonature.mondomaine.fr/api'
  API_TAXHUB = 'https://taxhub.mondomaine.fr/api'

Pour GeoNature ajouter un s à votre nom de domaine dans le fichier de conf de l'API :

::

  nano /home/geonatureadmin/geonature/frontend/src/assets/config.json

Faites un update de GeoNature, et relancez le service :

::

  cd geonature
  source venv/bin/activate
  geonature update-configuration
  deactivate
  sudo systemctl restart geonature

Pour UsersHub :

::

  nano /home/geonatureadmin/usershub/config/config.py

Rajouter simplement un s après le http :

::

  URL_APPLICATION ='https://usershub.mondomaine.fr'

Relancez le service de UsersHub :

::

  sudo nano service usershub restart

Et voilà, vous devriez maintenant avoir des URLs sécurisées, accessibles depuis des liens semblables à ceux-ci :

* https://geonature.mondomaine.fr
* https://taxhub.mondomaine.fr
* https://usershub.mondomaine.fr
