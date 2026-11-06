# TpQgisTsi : extension QGIS d'adresse BAN et de zone tampon

Extension QGIS (Python / PyQt) qui permet de cliquer sur la carte pour :

1. obtenir les coordonnées du point et **l'adresse postale BAN la plus proche** ;
2. **compter les objets** d'une couche situés à X mètres du point cliqué ;
3. **visualiser la zone tampon** de X mètres autour du point ;
4. **fermer l'extension** en remettant QGIS dans son état initial.

Projet réalisé dans le cadre du TP QGIS du Master 2 TSI GéoData Paris.

---

## Fonctionnalités

### 1. Coordonnées et adresse BAN

- Bouton « Choisir sur la carte » : la fenêtre se masque, l'utilisateur clique sur la carte, puis la fenêtre se réaffiche.
- Les champs **latitude** et **longitude** (WGS84, EPSG:4326) sont remplis, quel que soit le système de coordonnées du projet.
- L'adresse postale la plus proche est interrogée via le service de géocodage inverse de la BAN (Géoplateforme) :
  - seules les **adresses postales** sont recherchées (pas de rues ni de lieux-dits) ;
  - sont affichés : le **numéro**, le **type de voie**, le **nom de la voie**, le **code INSEE** de la commune (et non le code postal), le **nom de la commune** et l'adresse complète.

> La BAN ne fournit pas le type de voie séparément. Il est déduit du premier mot du libellé de la voie (rue, avenue, boulevard, etc.). Pour une voie sans type reconnu (par exemple « Grande Rue »), le champ type reste vide.

### 2. Comptage d'objets autour du point

- L'utilisateur saisit une **distance en mètres** (nombre entier) et choisit une **couche de points** dans la liste déroulante.
- Après un clic sur la carte, l'extension affiche le nombre d'objets de cette couche situés à moins de X mètres du point cliqué.
- Les distances sont calculées en **vrais mètres sur l'ellipsoïde WGS84** (`QgsDistanceArea`), même si la couche est en degrés.


## Utilisation

1. Lancer l'extension depuis le menu ou la barre d'outils.
2. **Adresse** : cliquer sur « Choisir sur la carte », puis sur la carte. Les coordonnées et l'adresse s'affichent.
3. **Objets et tampon** : choisir la couche, saisir la distance en mètres, cliquer sur le bouton de comptage, puis sur la carte. Le nombre d'objets s'affiche et la zone tampon est dessinée.
4. Fermer avec la croix ou Échap pour garder la zone tampon, ou avec « Annuler » pour la supprimer.

---

## Structure du code

| Méthode | Rôle |
|---|---|
| `run()` | Crée la fenêtre et les outils de carte (une seule fois), puis affiche la fenêtre |
| `start_picking(tool)` | Masque la fenêtre et active l'outil de clic |
| `point_transform_to_4326(point)` | Reprojette le point du CRS du canvas vers WGS84 |
| `button_map(point)` | Enchaîne coordonnées et adresse |
| `button_map_objs(point)` | Enchaîne comptage et zone tampon, puis réaffiche la fenêtre |
| `get_coordinates(point)` | Remplit latitude et longitude |
| `get_adress(point)` | Interroge la BAN et remplit les champs d'adresse |
| `split_street(street)` | Sépare le type de voie et le nom de la voie |
| `get_objects(point)` | Compte les objets à X mètres du point |
| `get_buffer_layer()` | Retourne (ou crée) la couche temporaire « Zone tampon » |
| `draw_buffer(point)` | Calcule et dessine le cercle de X mètres |
| `restore_previous_tool()` | Réactive l'outil actif avant le lancement de l'extension |
| `remove_buffer_layer()` | Supprime la couche tampon du projet |