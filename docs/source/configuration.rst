Configurer son GeoNature
========================

Votre GeoNature nouvellement installé fonctionne très bien, mais vous aurez certainement besoin d'en adpater les paramètres pour qu'ils correspondent à vos besoins.

Les paramètres généraux
***********************

Les paramètres généraux de l'application sont situés dans le fichier ``geonature_config.toml``.

::

  nano /home/geonatureadmin/geonature/config/geonature_config.toml

Vous pouvez alors y ajouter des paramètres pour votre application. La liste de tous les paramètres possibles est disponible dans le fichier ``default_config.toml.example``, localisé dans le même dossier, `également consultable sur ce lien <https://github.com/PnX-SI/GeoNature/blob/master/config/default_config.toml.example>_`.

Parmis les paramètres à modifier, en voici quelques'uns qui peuvent être important :

Dans la première partie, vous pouvez ajouter le nom de votre application, qui apparaitra en haut de votre GeoNature (à placer après le paramètre API_TAXHUB par exemple) :

::

  # Nom de l'application dans la page d'accueil.
  appName = "GeoNature de mon organisme gestionnaire"

Vous pouvez également paramétrer GeoNature pour que les utilisateurs puissent faire une demande de création de compte.
Pour cela, vous devez indiquez des informations de connexion à UsersHub avec un utilisateur qui a les droits d'administrateur :

::

  [USERSHUB]
      URL_USERSHUB = 'https://usershub.mondomaine.fr' # sans slash f$
      #Administrateur de mon application
      ADMIN_APPLICATION_LOGIN = "nomutilisateuradmin"
      ADMIN_APPLICATION_PASSWORD = "motdepasseutilisateuradmin"

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

Enfin, vous devez également ajouter des paramètres pour la gestion des comptes (et autoriser la création de comptes, décider s'il nécessite une validation ou non) :

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

A chaque fois que vous modifiez le fichier de configuration geonature_config.toml, vous devez relancer les commandes suivantes pour qu'elles soient prises en compte :

::

  cd /home/geonatureadmin/geonature/backend
  source venv/bin/activate
  geonature update_configuration
  deactivate

Changer le logo de l'application
********************************

Lorsque vous lancer un nouveau GeoNature, vous avez un logo neutre qui apparait dans l'encart supérieur "MON LOGO". Vous pouvez bien sûr le personnaliser en ajoutant le logo de votre structure, de la manière suivante :
