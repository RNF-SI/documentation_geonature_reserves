Configurer un nom de domaine
============================

Une adresse IP pour accéder à l'application, ce n'est pas forcément le plus simple. Vous disposez peut-être d'un nom de domaine, pour votre site internet par exemple (le nom de domaine de Réserves Naturelles de France est reserves-naturelles.org).

Nous allons voir comment configurer nos applications pour y accéder avec des urls sous la forme nomapplication.nomdomaine.extensiondomaine (par exemple pour le geonature de RNF : geonature.reserves-naturelles.org)

Demandez au gestionnaire de votre site internet, qui doit gérer votre domaine, de vous créer les zones DNS avec les paramètres suivant :

* Nome de la zone : geonature.nomdomaine.extensiondomaine ET/OU usershub.nomdomaine.extensiondomaine ET/OU taxhub.nomdomaine.extensiondomaine
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

Pour chaque application, il nous faut créer ou éditer un fichier localisé dans ``/etc/apache2/sites-available/`` qui portera le nom de l'application avec l'extension ``.conf``. Qu'il existe ou non, vous pouvez éditer un fichier avec ``Nano``.

Pour TaxHub, tapez la commande suivante :

::

  sudo nano /etc/apache2/sites-available/taxhub.conf

Ajoutez-y les informations suivantes en ramplacant mondomaine.fr par votre domaine à vous :

::

  # Configuration TaxHub sur sous-domaine
    <VirtualHost *:80>
            ServerName taxhub.mondomaine.fr

            <Location />
                    ProxyPass http://127.0.0.1:5000/
                    ProxyPassReverse http://127.0.0.1:5000/
            </Location>
    </VirtualHost>
  # Fin de configuration de TaxHub

.. note::

  Après avoir fait ``CTRL + X`` pour quitter ``Nano`` et validé les modifications en tapant ``O``, il vous sera demandé de valider le nom du fichier, tapez simplement sur ``Entrée``

Pour UsersHub, tapez la commande suivante :

::

  sudo nano /etc/apache2/sites-available/usershub.conf

Ajoutez-y les informations suivantes en ramplacant mondomaine.fr par votre domaine à vous :

::

  # Configuration UsersHub sur sous-domaine
      <VirtualHost *:80>
              ServerName usershub.mondomaine.fr
              <Location />
                      ProxyPass http://127.0.0.1:5001/
                      ProxyPassReverse http://127.0.0.1:5001/
          </Location>
      </VirtualHost>
  # Fin de configuration de UsersHub

Enfin pour GeoNature, tapez la commande suivante :

::

  sudo nano /etc/apache2/sites-available/geonature.conf

Vous vérez alors que le fichier existe déjà. Vous pouvez effacer son contenu actuel, ou le commenter en mettant des # devant chaque ligne.

Ajoutez-y les informations suivantes en ramplacant mondomaine.fr par votre domaine à vous :

::

  #Configuration GeoNature sur sous-domaine
      <VirtualHost *:80>
              ServerName geonature.mondomaine.fr
              DocumentRoot /home/geonatureadmin/geonature/frontend/dist

              <Directory /home/geonatureadmin/geonature/frontend/dist>
                      Require all granted
              </Directory>

              <Location /api>
                      ProxyPass http://127.0.0.1:8000/api
                      ProxyPassReverse  http://127.0.0.1:8000/api
              </Location>
      </VirtualHost>
  # Fin de configuration de GeoNature

Modifiez également le fichier suivant :

::

  sudo nano /etc/apache2/sites-available/geonature_maintenance.conf

Il vous sera utile pour passer votre application en mode maintenance (pour une mise à jour par exemple).

Modifiez les informations suivantes :

::

    <VirtualHost *:80>
        ServerName geonature.mondomaine.fr

        ErrorLog "/var/log/apache2/geonature_error.log"
        CustomLog "/var/log/apache2/geonature_access.log" combined
    </VirtualHost>

=====================> A MODIFIER A MON AVIS CE FICHIER <=====================


Lancer ensuite les commandes suivantes :

::

  sudo a2ensite taxhub.conf
  sudo a2ensite usershub.conf
  sudo a2ensite geonature.conf

.. warning::

  On ne fait pas ``sudo a2ensite geonature_maintenance.conf`` car il ne doit être activé qu'en cas de maintenance

On redémare ensuite le serveur apache (qui gère les applications web) en lançant la commande suivante :

::

  sudo apachectl restart

Il est nécessaire de mettre à jour le fichier de configuration de GeoNature ``geonature_config.toml`` :

::

  nano /home/geonatureadmin/geonature/config/geonature_config.toml

Modifiez les lignes suivantes (déjà présentes dans le fichier), en remplaçant le domaine par le votre :

::

  URL_APPLICATION = 'http://geonature.mondomaine.fr'
  API_ENDPOINT = 'http://geonature.mondomaine.fr/api'
  API_TAXHUB = 'http://taxhub.mondomaine.fr/api'

Pour que ces modifications soient prises en compte, lancer les commandes suivantes :

::

  cd /home/geonatureadmin/geonature/backend
  source venv/bin/activate
  geonature update_configuration
  deactivate

Faites la même chose avec le fichier de configuration de UsersHub :

::

  nano /home/geonatureadmin/usershub/config/settings.ini

Modifiez la ligne suivante (déjà présentes dans le fichier), en remplaçant le domaine par le votre :

::

  url_application=http://usershub.mondomaine.fr

Ainsi que :

::

  nano /home/geonatureadmin/usershub/config/config.py

Modifiez la ligne suivante (déjà présentes dans le fichier), en remplaçant le domaine par le votre :

::

  URL_APPLICATION ='http://usershub.mondomaine.fr'

Relancez les services de UsersHub et GeoNature :

::

  sudo service usershub restart
  sudo service geonature restart

Vos applications doivent maintenant être accessibles via des liens semblalbes à :

* http://geonature.mondomaine.fr
* http://taxhub.mondomaine.fr
* http://usershub.mondomaine.fr

Si vous rencontrez une Internal Server Error pour TaxHub, aller modifier le fichier suivant :

::

  nano taxhub/apptax/config.py


Modifiez la ligne suivante (déjà présentes dans le fichier) :

::

  APPLICATION_ROOT = '/'

Relancez le service de TaxHub :

::

  sudo service taxhub restart

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

Relancez la mise à jour de configuration de GeoNature :

::

  cd /home/geonatureadmin/geonature/backend
  source venv/bin/activate
  geonature update_configuration
  deactivate

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
