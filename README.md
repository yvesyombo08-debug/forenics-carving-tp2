Rapport de TP : Analyse Forensic et Extraction d'Artefacts (Data Carving)

1. Objectifs du TP
L'objectif de ce travail pratique est de se mettre dans la peau d'un analyste SOC ou d'un enquêteur numérique afin d'analyser un fichier suspect (`suspect_file`). Il s'agit d'identifier son type réel à l'aide de ses signatures binaires (*Magic Numbers*), d'en extraire des chaînes de caractères dissimulées, et de tenter une reconstruction automatisée des fichiers cachés ou imbriqués via des techniques de Data Carving.


2. Étape 1 : Analyse Initiale et Détection de Type

Identification du type de fichier
La première étape consiste à ignorer l'absence d'extension et à analyser l'en-tête réel du binaire à l'aide de la commande `file` :

Analyse forensic : Contrairement à une image matricielle classique (comme un JPEG commençant par les octets FF D8 FF), le fichier analysé débute par du texte ASCII structuré en XML propre aux images vectorielles SVG.

Extraction des chaînes de caractères masquées

L'utilisation de l'outil strings permet d'isoler les éléments textuels lisibles codés en ASCII afin de rechercher des indicateurs de compromission (IoC), des adresses IP ou des identifiants :
Bash

Preuves découvertes : * Adresse IP identifiée : 192.168.1.50

    Secret / Mot de passe récupéré : MasterChief2026

    Anomalie forensic : Les chaînes d'artefacts critiques ont été concaténées de force directement à la fin de la balise de fermeture </svg>.

3. Étape 2 & 3 : Analyse de Structure et Data Carving

Analyse des structures imbriquées avec Binwalk

Nous avons soumis le fichier suspect à binwalk pour cartographier les signatures binaires décalées (offsets)

Tentative d'extraction automatisée avec Foremost

L'outil foremost a été configuré pour scanner le fichier secteur par secteur afin de reconstruire les structures connues :

Le dossier output_extracted/ ne contient aucun sous-dossier de fichiers récupérés (comme jpg/ ou zip/), mis à part le journal d'audit.

Explication technique : L'image source utilisée pour créer le scénario (image_cachee.jpg) n'était pas un vrai fichier JPEG encodé en binaire, mais un script texte SVG renommé. Les outils de découpage comme foremost se basent strictement sur la détection d'en-têtes hexadécimaux binaires standardisés (ex: 0xFFD8FFE0 pour un JPEG). Le fichier suspect ne contenant qu'une suite de texte XML ASCII, les algorithmes de carving n'ont détecté aucune signature binaire valide à reconstruire.
