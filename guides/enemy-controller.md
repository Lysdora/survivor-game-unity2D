# Guide: Contrôleur d'ennemi dans Unity

![Unity Logo](../images/unity-logo.png)

## Introduction

Bienvenue dans ce guide sur la création d'un contrôleur d'ennemi simple mais efficace dans Unity! Dans ce tutoriel, nous allons explorer comment implémenter un système permettant à un ennemi de suivre et de poursuivre le joueur. Ce type de script est essentiel pour tout jeu d'action, RPG ou d'aventure, et particulièrement important pour un jeu de type survival comme le nôtre.

## Prérequis

- Unity (version récente)
- Un personnage joueur déjà configuré avec son script PlayerController
- Un objet ennemi avec un composant Rigidbody2D
- Connaissances de base en C#

## Objectif du script EnemyController

L'objectif principal de ce script est de créer un ennemi qui:
- Localise automatiquement le joueur dans la scène
- Se déplace vers le joueur en utilisant la physique (Rigidbody2D)
- Ajuste sa vitesse en fonction d'un paramètre configurable

## Analyse du script EnemyController

Examinons le code en détail, section par section.

### 1. Imports et définition de la classe

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class EnemyController : MonoBehaviour
{
    Rigidbody2D rb;
    public float moveSpeed;
    private Transform target;
```

Cette partie initiale:
- Importe les bibliothèques nécessaires
- Définit la classe `EnemyController` qui hérite de `MonoBehaviour`
- Déclare les variables essentielles:
  - `rb`: référence au composant Rigidbody2D de l'ennemi
  - `moveSpeed`: vitesse de déplacement (publique pour être modifiable dans l'inspecteur)
  - `target`: référence au Transform du joueur que l'ennemi va poursuivre

> **Note**: La variable `moveSpeed` est déclarée comme publique, ce qui permet de l'ajuster facilement depuis l'inspecteur d'Unity sans avoir à modifier le code.

### 2. Initialisation et recherche du joueur

```csharp
// Start is called before the first frame update
void Start()
{
    target = FindFirstObjectByType<PlayerController>().transform;
    rb = GetComponent<Rigidbody2D>();
}
```

La méthode `Start()` est appelée une seule fois au lancement du jeu. Dans cette méthode:

1. On utilise `FindFirstObjectByType<PlayerController>()` pour localiser le premier GameObject dans la scène qui possède un composant `PlayerController`
2. On accède à sa propriété `transform` pour obtenir sa position et on la stocke dans `target`
3. On récupère la référence au composant Rigidbody2D attaché à l'ennemi avec `GetComponent<Rigidbody2D>()`

> **Astuce**: `FindFirstObjectByType` est une méthode plus récente et plus optimisée que `FindObjectOfType`. Elle récupère le premier objet du type spécifié qu'elle trouve, ce qui est généralement suffisant pour un joueur unique.

### 3. Poursuite du joueur

```csharp
// Update is called once per frame
void Update()
{
    rb.velocity = (target.position - transform.position).normalized * moveSpeed;
}
```

La méthode `Update()` est exécutée à chaque frame. C'est ici que se produit la magie de la poursuite:

1. `(target.position - transform.position)` calcule un vecteur directionnel de l'ennemi vers le joueur
2. `.normalized` transforme ce vecteur en un vecteur unitaire (de longueur 1), conservant uniquement la direction
3. La multiplication par `moveSpeed` détermine la vitesse à laquelle l'ennemi se déplace
4. Ce vecteur final est assigné à `rb.velocity`, ce qui fait déplacer le Rigidbody2D de l'ennemi vers le joueur

> **Important**: L'utilisation de `.normalized` est cruciale pour garantir que l'ennemi se déplace à une vitesse constante, quelle que soit la distance entre lui et le joueur.

## Améliorations possibles

### 1. Ajouter une distance de détection

Pour que l'ennemi ne poursuive le joueur que lorsqu'il est à une certaine distance:

```csharp
[SerializeField] private float detectionRange = 5f;

void Update()
{
    float distanceToPlayer = Vector2.Distance(transform.position, target.position);
    
    if(distanceToPlayer <= detectionRange)
    {
        rb.velocity = (target.position - transform.position).normalized * moveSpeed;
    }
    else
    {
        rb.velocity = Vector2.zero; // L'ennemi s'arrête quand le joueur est hors de portée
    }
}
```

### 2. Implémentation d'une patrouille

Pour que l'ennemi patrouille lorsqu'il ne poursuit pas le joueur:

```csharp
[SerializeField] private Transform[] patrolPoints;
[SerializeField] private float patrolSpeed = 2f;
private int currentPatrolIndex = 0;

void Update()
{
    float distanceToPlayer = Vector2.Distance(transform.position, target.position);
    
    if(distanceToPlayer <= detectionRange)
    {
        // Mode poursuite
        rb.velocity = (target.position - transform.position).normalized * moveSpeed;
    }
    else
    {
        // Mode patrouille
        Patrol();
    }
}

void Patrol()
{
    if(patrolPoints.Length == 0) return;
    
    // Se déplacer vers le point de patrouille actuel
    Vector2 direction = (patrolPoints[currentPatrolIndex].position - transform.position).normalized;
    rb.velocity = direction * patrolSpeed;
    
    // Vérifier si l'ennemi a atteint le point de patrouille
    float distanceToPatrolPoint = Vector2.Distance(transform.position, patrolPoints[currentPatrolIndex].position);
    if(distanceToPatrolPoint < 0.2f)
    {
        // Passer au point suivant
        currentPatrolIndex = (currentPatrolIndex + 1) % patrolPoints.Length;
    }
}
```

### 3. Ajout d'animations en fonction de la direction

Pour animer l'ennemi en fonction de sa direction de déplacement:

```csharp
private Animator animator;

void Start()
{
    target = FindFirstObjectByType<PlayerController>().transform;
    rb = GetComponent<Rigidbody2D>();
    animator = GetComponent<Animator>();
}

void Update()
{
    Vector2 direction = (target.position - transform.position).normalized;
    rb.velocity = direction * moveSpeed;
    
    // Mettre à jour les paramètres de l'animateur
    animator.SetFloat("Horizontal", direction.x);
    animator.SetFloat("Vertical", direction.y);
    animator.SetFloat("Speed", rb.velocity.magnitude);
}
```

## Gestion des collisions

Pour gérer les dégâts lorsque l'ennemi touche le joueur, vous pouvez ajouter:

```csharp
void OnCollisionEnter2D(Collision2D collision)
{
    if(collision.gameObject.CompareTag("Player"))
    {
        // Infliger des dégâts au joueur
        PlayerHealth playerHealth = collision.gameObject.GetComponent<PlayerHealth>();
        if(playerHealth != null)
        {
            playerHealth.TakeDamage(10);
        }
    }
}
```

## Utilisation pratique

Pour implémenter ce script dans votre jeu:

1. Créez un GameObject pour votre ennemi
2. Ajoutez un composant Rigidbody2D (Configuration recommandée: "Dynamic" avec "Gravity Scale" à 0)
3. Ajoutez un Collider2D (BoxCollider2D ou CircleCollider2D selon la forme de votre ennemi)
4. Attachez le script EnemyController
5. Définissez la valeur de moveSpeed dans l'inspecteur (valeur recommandée: 2-4)
6. Assurez-vous que votre joueur a bien un composant PlayerController

## Conclusion

Ce script de contrôleur d'ennemi simple mais efficace constitue une base solide pour tout jeu nécessitant des ennemis qui poursuivent le joueur. Son utilisation de la physique via Rigidbody2D garantit des mouvements fluides et des collisions réalistes.

Dans notre jeu de type survival, ces ennemis peuvent être instanciés en grand nombre et avec différentes statistiques pour créer des vagues toujours plus difficiles à mesure que le joueur progresse.

## Exercice pratique

Essayez d'ajouter un système de "knock-back" lorsque le joueur frappe l'ennemi, le faisant reculer temporairement avant de reprendre sa poursuite. Cela ajoutera une dimension stratégique au combat!

[Retour à la page principale](../README.md)
