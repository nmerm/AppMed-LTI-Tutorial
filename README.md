# Interconnexion d’une application web et d’une plate-forme d’apprentissage en ligne
Ce tutoriel présente les différentes étapes à suivre pour obtenir une installation de test du standard LTI ([Learning Tools Interoperability](https://www.imsglobal.org/activity/learning-tools-interoperability)). Il décrit l'installation d'[une plate-forme d'apprentissage en ligne, Moodle](https://moodle.org/), qui jouera le rôle de _Tool Consumer_ et d'[une application de démonstration, _Rating_](http://www.spvsoftwareproducts.com/php/rating/), qui jouera le rôle de _Tool Provider_.


## Prérequis
- Un serveur web (par ex. Apache)
- Une base de données (par ex. MySQL, compatible avec les deux applications)
- PHP correctement configuré
- git

## 1. Installation de la plate-forme d'apprentissage en ligne Moodle (_Tool Consumer_)
Ce point reprend directement le [guide rapide d'installation](https://docs.moodle.org/3x/fr/Guide_rapide_d%27installation) disponible sur le site de Moodle. Pour plus de détails, consultez également la page [installation de Moodle](https://docs.moodle.org/3x/fr/Installation_de_Moodle).

### 1.1 Télécharger Moodle
Téléchargez le code depuis le dépôt Git :

```$ git clone -b MOODLE_34_STABLE git://git.moodle.org/moodle.git```

Un sous-dossier `moodle` est créé et le contenu du dépôt Git y est téléchargé.


On peut aussi télécharger une archive (.zip ou .tgz) depuis  https://download.moodle.org/releases/latest/. Pour une installation de développement en local, on pourrait même utiliser des paquets tout prêts [pour Windows](https://download.moodle.org/windows/) ou [pour Mac OS X](https://download.moodle.org/macosx/) qui contiennent tout ce qu'il faut (Apache, MySQL, PHP).

### 1.2 Créer une base de données
- Depuis le serveur de bases de données de votre choix, créez une nouvelle base de données vide. L'encodage par défaut doit être en UTF8.
Par exemple, avec MySQL :

```CREATE DATABASE moodle DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;```

- Créez un couple de nom d'utilisateur et mot de passe et donnez-lui les autorisations nécessaires sur la base de données.

Par exemple, avec MySQL :

```mysql> GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,CREATE TEMPORARY TABLES,DROP,INDEX,ALTER ON moodle.* TO moodleuser@localhost IDENTIFIED BY 'motdepasse';```

### 1.3 Créer le dossier de données (moodledata)
Créez un dossier vide `moodledata` qui contiendra les fichiers de Moodle :
- en cas d'installation sur un serveur de développement en local : il suffit de créer le dossier `moodledata` au même niveau que le dossier `moodle` ;
- en cas d'installation sur un serveur public : le dossier *NE DOIT PAS* se trouver dans la zone utilisée par le serveur web *MAIS* doit disposer des privilèges nécessaires pour que le logiciel serveur puisse y écrire.

NB: en cas d'installation sur un hébérgement partagé, [utilisez un fichier `.htaccess` pour sécuriser l'accès au dossier](https://docs.moodle.org/3x/fr/Installation_de_Moodle#S.C3.A9curiser_le_r.C3.A9pertoire_moodledata_dans_un_dossier_web).

### 1.4 Installer Moodle
- Décompressez/déplacez/copiez le dossier `moodle` (récupéré ci-dessus) afin que votre serveur puisse l'utiliser, et vérifiez les privilèges.
- Rendez-vous à l'adresse de votre site Moodle depuis un navigateur internet et suivez les instructions de l'assistant d'installation.
#### Remarques
- Vous aurez besoin des informations vous permettant de vous connectez à votre base de données.
- En cas d'installation sur un serveur de développement local, vous pourriez avoir à activer cURL. Par exemple, pour régler le problème avec EasyPHP, suivez [ces instructions](https://stackoverflow.com/a/38530865).

## 2. Installation de l'application "Rating" (_Tool Provider_)
L'application _Rating_ (http://www.spvsoftwareproducts.com/php/rating/) vise à offrir une démonstration de la méthode à suivre pour la construction d'un _IMS LTI tool provider_.
### 2.1 Télécharger l'application
- Téléchargez l'archive .zip [ici](http://projects-archive.oscelot.org/gf/download/frsrelease/925/5883/php-rating-1.2.00.zip).

### 2.2 Installer l'application
Une partie des instructions suivantes sont directement traduites du fichier install.txt qui se trouve dans l'archive :

Pour installer l'application en utilisant une base de données MySQL ou SQLite, suivez les étapes suivantes:

- Dézippez le fichier de distribution php-rating, copiez le fichier src.zip sur votre serveur web et dézippez-le (ou l'inverse).
- Editez le fichier config.php pour y entrer les détails de connexion à votre base de données.
- Ajoutez des des restrictions pour évitez un accès non-autorisé au répértoire 'admin'. Par exemple utilisez un fichier .htaccess.

## 3. Interconnecter Moodle (_Tool Consumer_) et Rating (_Tool Provider_)
(NB: pour les points suivants, remplacez "http://domain/path/to/rating/" par l'adresse du serveur et du dossier où vous avez installé l'application...)

### 3.1 Ajouter un _Tool Consumer_ à l'application Rating
- Dans votre navigateur, ouvrez la page https://domain/path/to/rating/admin/index.php et configurez un _Tool Consumer_ (_key_, _secret_ et cocher la case _Enabled_).

### 3.2 Créer un cours sur Moodle
Connectez-vous avec le compte administrateur créé durant la phase d'installation de Moodle. Ensuite, [créez un nouveau cours](https://docs.moodle.org/3x/fr/Ajouter_un_cours).

### 3.3 Ajouter une activité _External Tool_
- Dans le cours que vous venez de créer, [activez le mode édition](https://docs.moodle.org/3x/fr/%C3%89diter_un_cours) puis cliquez sur "Ajouter une activité ou une ressource". Sélectionnez ensuite l'activité de type [outil externe](https://docs.moodle.org/3x/fr/Outil_externe).
- Donnez un nom à l'activité
- Sous "URL de l'outil", entrez l'adresse : https://domain/path/to/rating/connect.php.
- Sous "Clef client", entrez la clé (_key_) paramétrée au point 3.1.
- Sous "Secret partagé", entrez le _secret_ paramétré au point 3.1.
- Sous "Conteneur de lancement", sélectionnez "Nouvelle fenêtre" (facultatif)

Les autres paramètres peuvent être laissés tels quels.

Cliquez sur "Enregistrer et afficher" puis sur "Ouvrir dans une nouvelle fenêtre" (si vous avez choisi l'option correspondante ci-dessus). Vous êtes connecté à l'application Rating et identifié comme ayant les droits d'édition. Vous avez la possibilité d'ajouter des entrées.


En accédant au cours avec un compte "Etudiant", l'utilisateur pourra seulement consulter les entrées et leur attribuer une note.

---
Tutoriel réalisé dans le cadre du cours _Approfondissement média_ @[HEIG-VD](https://heig-vd.ch/), 2018.
