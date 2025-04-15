# Système de Spawn d'Ennemis Basique

Dans ce guide, nous allons créer un système de spawn d'ennemis simple mais efficace pour un jeu de type Vampire Survivors développé avec Unity. Ce système permettra de faire apparaître des ennemis à intervalles réguliers.

## Objectif du système
- Faire apparaître des ennemis dans le jeu à des intervalles définis
- Comprendre les bases d'un système de spawn

## Prérequis
- Un prefab d'ennemi déjà configuré
- Unity 2D configuré pour votre projet

## Étape 1: Création du script EnemySpawner

Commençons par créer un script appelé `EnemySpawner.cs` qui sera responsable de faire apparaître les ennemis :

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class EnemySpawner : MonoBehaviour
{
    // Référence à l'objet ennemi que nous voulons faire apparaître
    public GameObject enemyToSpawn;
    
    // Interval de temps entre chaque spawn d'ennemi
    public float timeToSpawn;
    
    // Compteur pour suivre le temps écoulé
    private float spawnCounter;
    
    void Start()
    {
        // Initialisation du compteur avec la valeur de timeToSpawn
        spawnCounter = timeToSpawn; // (2 ds l'éditeur)on veut donner une petite pause au player au lancement du jeu avant le 1er enemy
    }

    // Update is called once per frame
    void Update()
    {
        // On doit se dire de copier bêtement le compteur de enemyController
        //mais là on veut seulement faire spawn des enemy
        spawnCounter -= Time.deltaTime; // on le diminue et ça va être à zero puis
        
        // Si le compteur atteint zéro ou moins
        if (spawnCounter <= 0) // si on est au même niveau que la valeur de timeToSpawn (2) ds l'éditeur
        {
            // On réinitialise le compteur
            spawnCounter = timeToSpawn; // reboucle à 2 et ça sans arrêt
            
            // Faire apparaître l'ennemi à la position actuelle de ce GameObject
            Instantiate(enemyToSpawn, transform.position, transform.rotation);
            // Teste le jeu tu verras ! mais on peut aussi faire...
            // Ne t'inquiète pas pour le moment si tu ne comprends pas tout
            // là c'est le système de base pour apprendre à instantier un objet :)
        }
    }
}
```

## Étape 2: Configurer le EnemySpawner dans Unity

1. Crée un nouvel objet vide dans ta scène et nomme-le "EnemySpawner"
2. Ajoute le script `EnemySpawner.cs` que nous venons de créer
3. Assigne le prefab d'ennemi que tu souhaites faire apparaître à la variable `enemyToSpawn`
4. Définis le temps entre chaque spawn (par exemple, 2 secondes) dans la variable `timeToSpawn`

![Configuration du EnemySpawner](https://lysdora.github.io/survivor-game-unity2D/images/enemy-spawner-config.png)

## Comment ça fonctionne?

Notre système de spawn est simple mais efficace:

1. Le script initialise un compteur avec la valeur de `timeToSpawn` (configurée dans l'éditeur)
2. À chaque frame, le compteur diminue en fonction du temps écoulé (`Time.deltaTime`)
3. Lorsque le compteur atteint zéro ou moins, le script:
   - Réinitialise le compteur
   - Crée une instance de l'ennemi à la position du spawner

## Astuce

- Tu peux placer plusieurs spawners à différents endroits de ta scène pour créer des zones d'apparition d'ennemis
- Essaie de modifier la valeur de `timeToSpawn` pour voir comment cela affecte la fréquence d'apparition des ennemis

## Prochaines améliorations

Ce système est très basique et sert de fondation pour comprendre le concept de spawn. Dans notre prochain guide, nous allons l'améliorer pour:
- Faire apparaître des ennemis autour du joueur
- Gérer différents types d'ennemis
- Augmenter progressivement la difficulté avec le temps
- Ajouter des limites au nombre d'ennemis simultanés

N'hésite pas à tester ce système de base pour bien comprendre comment il fonctionne avant de passer aux versions plus avancées!
