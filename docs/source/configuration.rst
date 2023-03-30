Configurer son GeoNature
========================

Votre GeoNature nouvellement installé fonctionne très bien, mais vous aurez certainement besoin d'en adpater les paramètres pour qu'ils correspondent à vos besoins.

Les paramètres généraux
***********************

Les paramètres généraux de l'application sont situés dans le fichier ``geonature_config.toml``.

::

  nano /home/geonatureadmin/geonature/config/geonature_config.toml

Vous pouvez alors y ajouter des paramètres pour votre application. La liste de tous les paramètres possibles est disponible dans le fichier ``default_config.toml.example``, localisé dans le même dossier, `également consultable sur ce lien <https://github.com/PnX-SI/GeoNature/blob/master/config/default_config.toml.example>`_.

Pour afficher vos modifications, sauvegardez simplement le fichier et rafraichissez la page de votre navigateur en vidant le cache : CTRL + SHIFT + R

.. IMPORTANT::

    Depuis la version 2.12 du 27 mars 2023, l'affichage des paramètres custom est dynamique, c'est à dire que vous n'avez pas d'action de recompilation du code à effectuer après l'ajout d'un fichier. 
    Sur une version antérieure, chaque fois que vous modifiez le fichier de configuration geonature_config.toml, vous devez relancer les commandes suivantes pour qu'elles soient prises en compte :

    ::

        cd /home/geonatureadmin/geonature/backend
        source venv/bin/activate
        geonature update_configuration
        deactivate

Parmis les paramètres à modifier, en voici quelques'uns qui peuvent être important :

Modifier le nom de l'application
--------------------------------

Dans la première partie, vous pouvez ajouter le nom de votre application, qui apparaitra en haut de votre GeoNature (à placer après le paramètre API_TAXHUB par exemple) :

::

  # Nom de l'application dans la page d'accueil.
  appName = "GeoNature de mon organisme gestionnaire"
  

Paramètrer la création de compte
--------------------------------

Vous pouvez également paramétrer GeoNature pour que les utilisateurs puissent faire une demande de création de compte.
Pour cela, vous devez indiquez des informations de connexion à UsersHub avec un utilisateur qui a les droits d'administrateur :

::

    [USERSHUB]
        URL_USERSHUB = 'https://usershub.mondomaine.fr' # sans slash f$
        #Administrateur de mon application
        ADMIN_APPLICATION_LOGIN = "nomutilisateuradmin"
        ADMIN_APPLICATION_PASSWORD = "motdepasseutilisateuradmin"

Vous devez également ajouter des paramètres pour la gestion des comptes (et autoriser la création de comptes, décider s'il nécessite une validation ou non) :

::

  # Gestion des demandes d'inscription
    [ACCOUNT_MANAGEMENT]
        # Activer l'affichage du lien vers le formulaire d'inscription
        ENABLE_SIGN_UP = true

        # Activer l'affichage de l'onglet de gestion des demandes de compte
        # utilisateur
        ENABLE_USER_MANAGEMENT = false

        # Valider automatiquement la demande de création de compte (=true),
        # sinon nécessite une validation (=false)
        AUTO_ACCOUNT_CREATION = false

        # Créer automatiquement (=true) un nouveau jeu de données pour chaque
        # compte utilisateur
        AUTO_DATASET_CREATION = true

      # Email du validateur si auto_account_creation = false.
      # Deux syntaxes alternatives sont autorisées pour ce paramètre :
      # - une chaine contenant un ou plusieurs emails séparés par des
      #   virgules (exemple ci-dessous).
      # - une liste de chaine contenant un email.
      # Un email encadré par des chevrons (<>) peut être précédé par un
      # libellé qui sera utilisé lors de l'envoi.
      # Les espaces présents en début et fin de chaine ainsi qu'avant et
      # après les virgules sont ignorés.
      VALIDATOR_EMAIL = """
          Théo LECHEMIA <theo.lechemia@ecrins-parcnational.fr>,
          Carl von LINNÉ <c.linnaeus@linnaeus.se>
          """

Paramètrer les envois de mails
------------------------------

Vous devez également donner des informations d'un serveur mail, pour que l'application puisse envoyer des mails (au nouvel utilisateur ou à l'administrateur).

Pour cela, rapprochez vous de l'administrateur de vos boîtes mails, et demandez lui les informations à remplir pour les champs suivants :

::

  [MAIL_CONFIG]
    MAIL_SERVER = "my_email_server"
    MAIL_PORT = 465
    MAIL_USE_TLS = false
    MAIL_USE_SSL = true
    MAIL_USERNAME = "my_user_name - email address of the sender"
    MAIL_PASSWORD = "my_pass"
    MAIL_DEFAULT_SENDER = "my_email@email.com"
    # Nombre max d'email que le serveur peut envoyer aucours d'une seule connection
    # Laisser en commentaire si le serveur n'a pas de limite.
    # MAIL_MAX_EMAILS = 100
    MAIL_ASCII_ATTACHMENTS = false
    # Email(s) où envoyer les erreurs générées par le backend de GeoNature.
    # Deux syntaxes alternatives sont autorisées pour ce paramètre :
    # - une chaine contenant un ou plusieurs emails séparés par des
    #   virgules (exemple ci-dessous).
    # - une liste de chaine contenant un email.
    # Un email encadré par des chevrons (<>) peut être précédé par un
    # libellé qui sera utilisé lors de l'envoi.
    # Les espaces présents en début et fin de chaine ainsi qu'avant et
    # après les virgules sont ignorés.
    ERROR_MAIL_TO = ["Prénom NOM <email@email.com>", "email2@email.com"]


Changer le logo de l'application (et autres images)
***************************************************

Lorsque vous lancer un nouveau GeoNature, vous avez un logo neutre qui apparait dans l'encart supérieur "MON LOGO". Vous pouvez bien sûr le personnaliser en ajoutant le logo de votre structure, de la manière suivante :

Un dossier ``/home/<user>/geonature/custom`` existe, crééez dedans un dossier ``images`` avec la commande suivante :

.. code-block:: 

    cd
    cd geonature/custom/
    mkdir images

Ajouter votre image nomée ``logo_structure.png`` dans ce dossier.

Rafraichissez la page de votre navigateur en vidant le cache : CTRL + SHIFT + R

Vous pouvez sursoucher l'ensemble des fichiers présents dans  ``geonature/backend/static/`` par des fichiers portant le même nom et même sous-dossier dans le dossier ``geonature/custom``.
Ainsi toutes les images de l'application peuvent être modifiées, comme le logo GeoNature du quart supérieur gauche (logo_sidebar.jpg) ou l'image de fond de l'accueil de l'application (login_background.jpg).

Personnaliser la navbar et le texte d'accueil
*********************************************

Le texte d’introduction, le titre et le pied de page de la page d’Accueil de GeoNature peuvent être modifiés à tout moment, sans réinstallation de l’application.

Pour cela, renseignez les paramètres dans le fichier de configuration de GeoNature (config/geonature_config.toml) :

.. code-block:: 

    [HOME]
        TITLE = "Bienvenue dans GeoNature"
        INTRODUCTION = ""
        FOOTER = ""

Vous pouvez renseigner du code HTML sur plusieurs lignes dans ces paramètres, en le plaçant entre triple quote (« «  »<b>Hello</b> » » »).


Les couleurs de textes, couleurs de fonds, forme des boutons etc peuvent être adaptées en renseignant un fichier ``custom/css/frontend.css`` contenant votre surcouche des classes CSS de l’application.

Par exemple, pour remplacer la couleur de fond du bandeau de navigation par une image, on peut apporter la modification suivante :


.. code-block:: css

    html body pnx-root pnx-nav-home mat-sidenav-container.sidenav-container.mat-drawer-container.mat-sidenav-container mat-sidenav-content.mat-drawer-content.mat-sidenav-content mat-toolbar#app-toolbar.row.mat-toolbar
    {
    background :
    url(../images/bandeau_test.jpg)
    }

Dans ce cas, l’image ``bandeau_test.jpg`` doit se trouver dans le répertoire ``custom/images``.

Personnaliser le nom et les pictos des modules
**********************************************

.. IMPORTANT::

    Ce point est repris de la documentation officielle : https://docs.geonature.fr/admin-manual.html#customiser-les-noms-et-pictos-des-modules

Vous pouvez modifier l'intitulé et le pictogramme des modules dans le menu des modules. Pour cela, adaptez le contenu des champs ``module_label`` et ``module_picto`` (avec des icones de la librairie Font Awesome - https://fontawesome.com) dans la table ``gn_commons.t_modules``.

Exemple :

.. code-block:: SQL

    -- Module Occtax
    UPDATE gn_commons.t_modules SET module_label = 'Occtax' WHERE module_code = 'OCCTAX';
    UPDATE gn_commons.t_modules SET module_picto = 'fa-map-marker' WHERE module_code = 'OCCTAX';
    -- Module Occhab
    UPDATE gn_commons.t_modules SET module_label = 'Occhab' WHERE module_code = 'OCCHAB';
    UPDATE gn_commons.t_modules SET module_picto = 'fa-leaf' WHERE module_code = 'OCCHAB';
    -- Module Import
    UPDATE gn_commons.t_modules SET module_label = 'Import' WHERE module_code = 'IMPORT';
    UPDATE gn_commons.t_modules SET module_picto = 'fa-upload' WHERE module_code = 'IMPORT';
    -- Module Export
    UPDATE gn_commons.t_modules SET module_label = 'Export' WHERE module_code = 'EXPORTS';
    UPDATE gn_commons.t_modules SET module_picto = 'fa-download' WHERE module_code = 'EXPORTS';
    -- Module Dashboard
    UPDATE gn_commons.t_modules SET module_label = 'Dashboard' WHERE module_code = 'DASHBOARD';
    UPDATE gn_commons.t_modules SET module_picto = 'fa-bar-chart' WHERE module_code = 'DASHBOARD';
    -- Module Validation
    UPDATE gn_commons.t_modules SET module_label = 'Validation' WHERE module_code = 'VALIDATION';
    UPDATE gn_commons.t_modules SET module_picto = 'fa-check' WHERE module_code = 'VALIDATION';
    -- Module Monitorings (Suivis)
    UPDATE gn_commons.t_modules SET module_label = 'Suivis' WHERE module_code = 'MONITORINGS';
    UPDATE gn_commons.t_modules SET module_picto = 'fa-eye' WHERE module_code = 'MONITORINGS';

Depuis la version 2.5.0, il est aussi possible de customiser l'ordre des modules dans le menu, par ordre alphabétique par défaut, en renseignant le champs ``gn_commons.t_modules.module_order``.


Paramètrer l'affichage cartographique
*************************************

Il est possible de paramètrer le niveau de zoom et le centre de votre carte. Pour cela, modifier les paramètres suivants à votre guise :

::

    # Configuration de l'affichage des cartes dans GeoNature
    [MAPCONFIG]

        # Coordonnées par défaut du centre de la carte à afficher
        # Attention : les coordonnées sont au format [Y, X]
        # Cf Leaflet configuration (https://leafletjs.com/reference-1.4.0.html#latlng-l-latlng)
        CENTER = [46.52863469527167, 2.43896484375]

        # Zoom par défaut
        ZOOM_LEVEL = 6

        # Zoom à partir duquel on peut pointer un relevé
        ZOOM_LEVEL_RELEVE = 15

        # Zoom appliqué sur la carte lors du clic sur une liste
        ZOOM_ON_CLICK = 16
        
.. NOTE::

    Les valeurs de zoom s'appuient sur l'outil Leaflet, consulter la documentation pour plus de détails : https://leafletjs.com/examples/zoom-levels/


Personnaliser les fonds de carte et l'affichage de zonages
**********************************************************

Personnaliser les fonds de carte
--------------------------------

Depuis la version XX, il est possible de personaliser ses fonds de cartes via des flux WMS/WMTS paramètrables dans le fichier ``geonature_config.toml``.
Voici quelques exemples de fonds de cartes:

::

    # Liste des fonds de carte proposés sur les cartes de GeoNature
    # chaque section [[MAPCONFIG.BASEMAP]] définit un fond de carte
    # l'option service est obligatoire uniquement pour les wms
    # l'ensemble des paramètre WMS peuvent être passé dans cette section
    [[MAPCONFIG.BASEMAP]]
        name = "OpenstreetMap"
        url = "//{s}.tile.openstreetmap.fr/hot/{z}/{x}/{y}.png"
        attribution = "OSM contributors"
    [[MAPCONFIG.BASEMAP]]
        name = "IGN Plan v2"
        url = "https://wxs.ign.fr/cartes/geoportail/wmts?&REQUEST=GetTile&SERVICE=WMTS&VERSION=1.0.0&STYLE=normal&TILEMATRIXSET=PM&FORMAT=image/png&LAYER=GEOGRAPHICALGRIDSYSTEMS.PLANIGNV2&TILEMATRIX={z}&TILEROW={y}&TILECOL={x}"
        attribution = "&copy IGN"
    [[MAPCONFIG.BASEMAP]]
        name = "IGN Ortho"
        url = "https://wxs.ign.fr/ortho/geoportail/wmts?&REQUEST=GetTile&SERVICE=WMTS&VERSION=1.0.0&STYLE=normal&TILEMATRIXSET=PM&FORMAT=image/jpeg&LAYER=ORTHOIMAGERY.ORTHOPHOTOS&TILEMATRIX={z}&TILEROW={y}&TILECOL={x}"
        attribution = "&copy IGN" 
    [[MAPCONFIG.BASEMAP]]
        name = "IGN Ortho IRC"
        url = "https://wxs.ign.fr/ortho/geoportail/wmts?service=WMTS&REQUEST=GetTile&version=1.0.0&tilematrixset=PM&tilematrix={z}&tilecol={x}&tilerow={y}&layer=ORTHOIMAGERY.ORTHOPHOTOS.IRC&format=image/jpeg&style=normal"
        attributions = "IGN-F/Géoportail"
    [[MAPCONFIG.BASEMAP]]
        name = "IGN Scan 25"
        url = "https://wxs.ign.fr/YOUR-IGN-KEY/geoportail/wmts?LAYER=GEOGRAPHICALGRIDSYSTEMS.MAPS&EXCEPTIONS=text/xml&FORMAT=image/jpeg&SERVICE=WMTS&VERSION=1.0.0&REQUEST=GetTile&STYLE=normal&TILEMATRIXSET=PM&TILEMATRIX={z}&TILEROW={y}&TILECOL={x}"
        attribution = "&copy IGN"
    [[MAPCONFIG.BASEMAP]]
        name = "IGN Cadastre"
        url = "https://wxs.ign.fr/parcellaire/geoportail/wmts?&REQUEST=GetTile&SERVICE=WMTS&VERSION=1.0.0&STYLE=normal&TILEMATRIXSET=PM&FORMAT=image/png&LAYER=CADASTRALPARCELS.PARCELS&TILEMATRIX={z}&TILEROW={y}&TILECOL={x}"
        attribution = "&copy IGN"
    [[MAPCONFIG.BASEMAP]]
        attribution = "GoogleSatellite"
        name = "google"
        url = "//{s}.google.com/vt/lyrs=s&x={x}&y={y}&z={z}"
        subdomains = ["mt0", "mt1", "mt2", "mt3"]
        

    [[MAPCONFIG.BASEMAP]]
        name = "WMS"
        url = "https://mon_flux_wms"
        service = "wms"
        layers = 'ma_layer'
        attribution = 'lala'


Personnaliser l'affichage de zonages (2.11)
-------------------------------------------

Depuis la version 2.11 de GeoNature, il est possible de surcoucher l'affichage de zonages sur la carte. L'activation des couches se fera dans le même encart que le choix des fonds de carte à droite de la carte. Vous pouvez décider de les faire afficher ou non à l'ouverture de l'application en mettant le paramètre ``activate`` à ``true``. 

A noter que les zonages peuvent provenir du schéma ref_geo (référentiel géographique interne), d'un flux WMS ou WFS, ou d’un fichier ou flux GeoJSON. Voici quelques exemple de zonages à faire afficher :

::

    # Via le schéma ref_geo
    [[MAPCONFIG.REF_LAYERS]]
        code = "COM"
        label = "Communes"
        type = "area"
        activate = false
        style = { color = "grey", fill = false, fillOpacity = "0.0", weight = 2 }
        params = {limit = 2000}
    [[MAPCONFIG.REF_LAYERS]]
        code = "RNN"
        label = "Réserves Naturelles nationales (RNN)"
        type = "area"
        activate = false
        style = { color = "green", fill = false, fillOpacity = "0.0", weight = 2 } 
        params = {limit = 2000}
    [[MAPCONFIG.REF_LAYERS]]
        code = "RNR"
        label = "Réserves Naturelles régionales (RNR)"
        type = "area"
        activate = false
        style = { color = "lightgreen", fill = false, fillOpacity = "0.0", weight = 2 } 
        params = {limit = 2000}
    [[MAPCONFIG.REF_LAYERS]]
        code = "PNR"
        label = "Parcs Naturels régionaux (PNR)"
        type = "area"
        activate = false
        style = { color = "darkblue", fill = false, fillOpacity = "0.0", weight = 2 } 
        params = {limit = 2000}
    [[MAPCONFIG.REF_LAYERS]]
        code = "PNM"
        label = "Parcs Naturels marins (PNM)"
        type = "area"
        activate = false
        style = { color = "#00ffff", fill = false, fillOpacity = "0.0", weight = 2 }  
        params = {limit = 2000}

    # Via un flux WMS
    [[MAPCONFIG.REF_LAYERS]]
        code = "limitesadministratives"
        label = "Limites administratives (IGN)"
        type = "wms"
        activate = false
        url = "https://wxs.ign.fr/essentiels/geoportail/r/wms"
        params = {VERSION = "1.3.0", crs = "CRS:84", dpiMode = 7, format = "image/png", layers = "LIMITES_ADMINISTRATIVES_EXPRESS.LATEST", styles = ""}
        

Paramètrer des filtres cartographiques personnalisés (Module SYNTHESE et VALIDATION)
************************************************************************************

Depuis la version XX, il est possible de rajouter des filtres cartographique sur la recherche d'occurrences dans les modules SYNTHESE et VALIDATION. Il est ainsi possible de choisir les zonages de votre référentiel géographique interne (schéma ``ref_geo``) sur lesquels vous souahitez filtrer vos données.

Dans la balise ``[SYNTHESE]`` de votre fichier ``geonature_config.toml`` (le créer si non-existant), rajouter les éléments suivants (à personaliser selon vos besoins) : 

::

    # Configuration du module Synthese
    [SYNTHESE]
        # Liste des entités géographiques sur lesquelles les filtres
        # géographiques de la synthese s'appuient
        # (id_area = id de l'entité géo, table ref_geo.bib_areas_types).
        # ATTENTION : Si de nouveaux types d'entités géographiques sont intégrés
        # dans la table ref_geo.l_areas, il faut bien recalculer les valeurs de
        # la table gn_synthese.cor_area_synthese en conséquence pour que
        # les données déjà présentes dans la synthèse soient associées à
        # ces nouvelles géométries.
        AREA_FILTERS = [
            { "label" = "Communes", "type_code" = "COM" },
            { "label" = "Régions", "type_code" = "REG" },
            { "label" = "Réserve naturelles nationales", "type_code" = "RNN" },
            { "label" = "Réserve naturelles régionales", "type_code" = "RNR" }
        ]

Même si l'on paramètre ses filtres dans la balise ``[SYNTHESE]``, ils se répercuteront dans le module VALIDATION.

Exemples de gestion des permissions
***********************************

.. WARNING::

    Pour bien comprendre la gestion des permissions, veuillez vous référer à la documentation officielle : https://docs.geonature.fr/admin-manual.html#gestion-des-droits

Cas d'une personne mobilisée sur deux réserves, avec des jeux de données par réserve.
-------------------------------------------------------------------------------------

Dans le cas où vous avez un technicien affecté sur 2 réserves, que vos jeux de données sont séparés par réserve, et que vous souhaitez ne pas donner accès en écriture à toutes les réserves :

1. Créer **un groupe et un organisme pour chaque réserve** dans USERSHUB
2. Ajouter la personne concernée dans les deux groupes
3. Ajouter la personne dans un des deux organismes "réserve", peu importe
4. Définir les permissions au "niveau de l'organisme" (Portée 2 au max) pour les deux groupes, dans le module ADMIN
5. Rajouter les 2 organismes "réserve" dans les métadonnées des jeux de données respectifs (en tant que "Producteur de la donnée" par exemple)
6. La personne concernée aura normalement les permissions CRUVED sur les jeux de données des deux réserves

Créer ses exports personnalisés (Module EXPORT)
***********************************************

La création des exports personalisés dans le module EXPORT s'appuie sur le concept de "vue" dans PostgreSQL. Les vues se manifestent comme le résultat d'une requête SQL sous la forme d'un tableau de données.

Ainsi pour créer son export personnalisé :

1. Définir sa requête SQL (qu'est-ce que l'on souhaite exporter, quelles colonnes, etc...)
2. Garder le **nom de la vue** et du **schéma** dans lequel vous allez la créer
3. Dans le module ADMIN, allez dans le "Backoffice GeoNature". Dans la partie "Export", cliquez sur "Exports" puis "Create" dans le bandeau supérieur.
4. Définissez les paramètres : "Nom de l'export" = Nom libre affiché dans le module EXPORT, Nom du schema PostgreSQL, Nom de la vue PostgreSQL, une description qui s'affichera dans le module EXPORT, le nom et le code SRID du champ géométrique (facultiatif si vous ne définissez pas de champ géométrique dans votre requête)
5. Cliquez ensuite sur "". Sur le même principe, cliquez sur "Create" et associer un rôle (qui peut être un groupe) à votre export nouvellement créé
6. Ca y est ! Vous pouvez visualiser votre export dans le module EXPORTS (A noter que vous devez lier votre export au Grp_admin pour le visualiser en tant qu'administrateur)
