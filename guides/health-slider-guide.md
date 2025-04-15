# Créer une Barre de Vie UI pour le Joueur

Dans ce guide, nous allons apprendre à créer une barre de vie flottante au-dessus du joueur dans un jeu de type Vampire Survivors. Cette technique utilise un Slider UI en mode World Space pour représenter visuellement la santé du personnage.

## Objectifs
- Créer un Canvas en mode World Space attaché au joueur
- Configurer un Slider UI comme barre de vie
- Personnaliser l'apparence de la barre de vie

## Étape 1 : Créer un Canvas pour la Barre de Vie

1. Sélectionnez votre GameObject Player dans la hiérarchie
2. Faites un clic droit → UI → Canvas pour ajouter un Canvas comme enfant
3. Renommez-le "Health Canvas"

## Étape 2 : Configurer le Canvas en Mode World Space

1. Sélectionnez le "Health Canvas" dans l'inspecteur
2. Dans le composant Canvas :
   - Changez **Render Mode** à **World Space**
   - *Note : Un avertissement concernant la caméra peut apparaître, c'est normal*

3. Dans le composant Rect Transform :
   - Réglez **Scale** à **0.01** pour X, Y et Z
   - Réglez **Position X** et **Position Y** à **0**

```
Ces valeurs de scale réduisent la taille du Canvas pour l'adapter
à l'échelle de votre monde de jeu.
```

## Étape 3 : Ajouter un Slider UI

1. Sélectionnez le "Health Canvas"
2. Faites un clic droit → UI → Slider
3. Positionnez le slider légèrement au-dessus de la tête du joueur

## Étape 4 : Simplifier et Personnaliser le Slider

1. Dans la hiérarchie, développez le Slider pour voir ses composants enfants
2. Supprimez le composant "Handle Slide Area" (nous n'en avons pas besoin pour une barre de vie)

## Étape 5 : Personnaliser l'Arrière-Plan du Slider

1. Sélectionnez "Background" sous le Slider dans la hiérarchie
2. Dans l'inspecteur, sous le composant Image :
   - Supprimez l'image de sprite (cliquez sur le petit cercle avec une croix)
   - Changez la couleur de fond (Background Color) à une nuance de noir ou autre couleur de votre choix

## Étape 6 : Personnaliser la Barre de Remplissage

1. Naviguez vers Fill Area → Fill dans la hiérarchie du Slider
2. Dans l'inspecteur, sous le composant Image :
   - Supprimez l'image de sprite (UISprite)
   - Changez la couleur à une nuance de rouge (ou toute couleur représentant la santé)

## Étape 7 : Corriger les Problèmes d'Affichage

Si votre barre ne se remplit pas correctement (ne va pas jusqu'au bout), suivez ces étapes :

1. Avec le Slider sélectionné, réglez Value à 1 dans l'inspecteur
2. Sélectionnez Fill Area → Fill :
   - Dans Rect Transform, réglez Left, Top, Right, Bottom à 0
   - Définissez Anchor Preset à "Stretch Full"

3. Sélectionnez Fill Area :
   - Réglez Left, Top, Right, Bottom à 0
   - Définissez Anchor Preset à "Stretch Full"

4. Sélectionnez Background :
   - Réglez Left, Top, Right, Bottom à 0
   - Définissez Anchor Preset à "Stretch Full"

## Étape 8 : Ajuster la Taille Finale

Maintenant que votre slider fonctionne correctement, vous pouvez :
- Ajuster la largeur et la hauteur du Slider dans le Rect Transform
- Positionner correctement la barre au-dessus de la tête du joueur

## Résultat Final

Vous devriez maintenant avoir une barre de vie élégante flottant au-dessus de votre personnage. Dans le prochain guide, nous verrons comment connecter cette interface visuelle avec le code pour refléter la santé réelle du joueur.

## Exercices Pratiques

1. **Personnalisation avancée** : Essayez d'ajouter un contour à votre barre de vie en utilisant un autre élément UI Image.
2. **Barre multi-couleurs** : Modifiez le code pour que la barre change de couleur en fonction du niveau de santé (vert quand plein, jaune à mi-vie, rouge quand presque vide).
3. **Animation** : Ajoutez une petite animation lorsque la santé diminue (comme un effet de secousse ou de flash).

---

Dans le prochain guide, nous verrons comment mettre à jour dynamiquement cette barre de vie en fonction des dégâts subis par le joueur, en utilisant le script `PlayerHealth`.
