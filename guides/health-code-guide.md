# Connecter la Barre de Vie au Joueur avec le Code

Ce guide fait suite à notre précédent tutoriel sur la création visuelle d'une barre de vie UI. Maintenant, nous allons voir comment connecter cette interface au code de notre jeu pour qu'elle affiche correctement la santé du joueur.

## Objectifs
- Référencer le Slider UI dans notre script de santé
- Initialiser la barre de vie avec les valeurs correctes
- Mettre à jour la barre de vie lorsque le joueur subit des dégâts
- Désactiver l'interaction utilisateur avec le slider

## Étape 1 : Préparation du Script PlayerHealthController

Notre script PlayerHealthController va gérer la santé du joueur et mettre à jour l'UI. Si vous n'avez pas déjà ce script, créez-le et attachez-le au GameObject de votre joueur.

1. Ouvrez votre IDE (comme Visual Studio)
2. Créez ou ouvrez le script PlayerHealthController.cs

## Étape 2 : Déclarer les Variables Nécessaires

Ajoutez ces variables à votre classe PlayerHealthController :

```csharp
public class PlayerHealthController : MonoBehaviour
{
    // Variables pour la santé
    public float currentHealth, maxHealth;
    
    // Référence au slider UI
    public Slider healthSlider;
    
    // Reste du code...
}
```

Assurez-vous d'importer le namespace UI :
```csharp
using UnityEngine;
using UnityEngine.UI;
```

## Étape 3 : Initialiser la Barre de Vie dans Start()

Dans la méthode Start(), nous initialisons la santé du joueur et configurons le slider :

```csharp
private void Start()
{
    // Initialiser la santé au maximum
    currentHealth = maxHealth;
    
    // Important de le faire dans cet ordre là
    healthSlider.maxValue = maxHealth;
    healthSlider.value = currentHealth;
}
```

**Note importante :** L'ordre des opérations est crucial ici. Nous définissons d'abord la valeur maximale du slider avant de définir sa valeur actuelle.

## Étape 4 : Créer une Méthode pour Gérer les Dégâts

Ajoutez une méthode qui sera appelée lorsque le joueur subit des dégâts :

```csharp
public void TakeDamage(float damageToTake)
{
    // Réduire la santé actuelle
    currentHealth -= damageToTake;
    
    // Vérifier si le joueur est mort
    if (currentHealth <= 0)
    {
        // Désactiver le joueur ou déclencher la fin de partie
        gameObject.SetActive(false);
    }
    
    // Mettre à jour le slider avec la nouvelle valeur de santé
    healthSlider.value = currentHealth;
}
```

## Étape 5 : Désactiver l'Interaction avec le Slider

C'est une étape importante souvent oubliée ! Par défaut, les sliders peuvent être manipulés par l'utilisateur, ce que nous ne voulons pas pour une barre de vie.

Pour désactiver l'interaction :

1. Sélectionnez votre Slider dans la hiérarchie
2. Dans l'inspecteur, décochez la case "Interactable" dans le composant Slider

## Étape 6 : Connecter le Slider au Script

Maintenant que notre code est prêt, nous devons connecter notre slider UI au script :

1. Sélectionnez votre GameObject Player dans la hiérarchie
2. Dans l'inspecteur, localisez le composant PlayerHealthController
3. Trouvez le champ "Health Slider" et faites glisser votre Slider depuis la hiérarchie vers ce champ
4. Définissez également les valeurs de Max Health selon les besoins de votre jeu (par exemple, 100)

## Étape 7 : Tester la Barre de Vie

Pour tester si tout fonctionne correctement, vous pouvez utiliser le système d'ennemis déjà implémenté (voir le guide sur l'EnemyController). Les ennemis vont automatiquement infliger des dégâts au joueur lors d'une collision, ce qui permettra de voir la barre de vie diminuer en temps réel.

Si vous n'avez pas encore implémenté les ennemis, suivez d'abord le guide "Système de Dégâts des Ennemis" pour configurer correctement les collisions et les dégâts.

## Résumé du Code Complet

Voici à quoi devrait ressembler votre script PlayerHealthController complet :

```csharp
using UnityEngine;
using UnityEngine.UI;

public class PlayerHealthController : MonoBehaviour
{
    // Variables pour la santé
    public float currentHealth, maxHealth;
    
    // Référence au slider UI
    public Slider healthSlider;
    
    private void Awake()
    {
        // Si besoin d'initialiser des références
        Instance = this;
    }
    
    private void Start()
    {
        // Initialiser la santé au maximum
        currentHealth = maxHealth;
        
        // Important de le faire dans cet ordre là
        healthSlider.maxValue = maxHealth;
        healthSlider.value = currentHealth;
    }
    
    private void Update()
    {
        // Code de test ou logique supplémentaire
        Debug.Log("Hello there");
    }
    
    public void TakeDamage(float damageToTake)
    {
        // Réduire la santé actuelle
        currentHealth -= damageToTake;
        
        // Vérifier si le joueur est mort
        if (currentHealth <= 0)
        {
            // Désactiver le joueur ou déclencher la fin de partie
            gameObject.SetActive(false);
        }
        
        // Mettre à jour le slider avec la nouvelle valeur de santé
        healthSlider.value = currentHealth;
    }
}
```

## Points Importants à Retenir

1. **Ordre d'initialisation** : Toujours définir maxValue avant value dans le slider
2. **Désactiver l'interaction** : Ne pas oublier de décocher "Interactable" sur le Slider
3. **Mise à jour dynamique** : Le slider se met à jour automatiquement lorsque vous modifiez sa valeur via code
4. **Réglage de l'échelle** : Si la barre semble trop grande ou trop petite, ajustez le RectTransform du slider

## Exercices Pratiques

1. **Ajouter une régénération de santé** : Modifiez le script pour que la santé se régénère lentement au fil du temps.
2. **Effet visuel lors des dégâts** : Ajoutez un effet de clignotement à la barre de vie lorsque le joueur subit des dégâts.
3. **Texte d'affichage** : Ajoutez un élément Text UI qui affiche la valeur numérique de la santé à côté de la barre.
4. **Barre multi-couleurs** : Modifiez la couleur de la barre en fonction du niveau de santé (vert > jaune > rouge).

---

Ce guide vous a montré comment implémenter le code nécessaire pour faire fonctionner votre barre de vie. Dans le prochain tutoriel, nous aborderons comment créer un système de dégâts qui utilisera cette méthode TakeDamage.
