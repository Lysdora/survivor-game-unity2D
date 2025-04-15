# Guide: Animation d'ennemi légère dans Unity



## Introduction

Bienvenue dans ce guide sur la création d'animations légères pour les ennemis dans Unity. Lorsque vous développez un jeu de type "bullet hell" ou "horde survival" comme Vampire Survivors, vous devez gérer potentiellement des centaines d'ennemis à l'écran simultanément. Dans ces conditions, utiliser des Animators sur chaque ennemi peut rapidement devenir gourmand en ressources et causer des problèmes de performance. Ce guide explique comment implémenter des animations visuelles efficaces et légères en utilisant uniquement du code.

## Prérequis

- Unity (version récente)
- Connaissances de base en C#
- Un prefab d'ennemi configuré avec un SpriteRenderer

## Objectif du script EnemyAnimation

L'objectif principal de ce script est de créer un effet de pulsation visuelle pour les ennemis qui:
- Ne nécessite pas d'Animator
- Consomme peu de ressources
- Crée un mouvement visuel dynamique
- Peut être appliqué à des centaines d'instances sans ralentir le jeu

## Analyse du script EnemyAnimation

Examinons le code en détail, section par section.

### 1. Imports et définition de la classe

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class EnemyAnimation : MonoBehaviour
{
    private Vector3 originalScale;
    private float scaleFactor = 1.1f;
    private float animationSpeed = 2.0f;
```

Cette partie initiale:
- Importe les bibliothèques nécessaires
- Définit la classe `EnemyAnimation` qui hérite de `MonoBehaviour`
- Déclare les variables essentielles:
  - `originalScale`: stocke l'échelle initiale de l'ennemi
  - `scaleFactor`: détermine l'amplitude de la pulsation (à quel point l'ennemi grossit)
  - `animationSpeed`: contrôle la vitesse de l'animation

> **Note**: La valeur de `scaleFactor` de 1.1f signifie que l'ennemi grossira jusqu'à 110% de sa taille originale. Une valeur de 1.5f le ferait grossir jusqu'à 150%.

### 2. Sauvegarde de l'échelle initiale

```csharp
void Start()
{
    originalScale = transform.localScale;
}
```

La méthode `Start()` est appelée une seule fois au lancement du jeu. Dans cette méthode:

- On sauvegarde l'échelle locale initiale de l'ennemi dans `originalScale`
- Cette référence est importante car elle sera utilisée comme base pour les calculs d'animation

> **Astuce**: Sauvegarder l'échelle initiale permet d'appliquer l'animation même si vos différents ennemis ont des tailles de base différentes.

### 3. Mise à jour de l'échelle pour créer la pulsation

```csharp
void Update()
{
    float scale = Mathf.SmoothStep(1f, scaleFactor, Mathf.PingPong(Time.time * animationSpeed, 1f));
    transform.localScale = originalScale * scale;
}
```

La méthode `Update()` est exécutée à chaque frame. C'est ici que la magie de l'animation se produit:

1. `Time.time * animationSpeed`: multiplie le temps écoulé depuis le début du jeu par la vitesse d'animation
2. `Mathf.PingPong(..., 1f)`: crée une valeur qui oscille continuellement entre 0 et 1
3. `Mathf.SmoothStep(1f, scaleFactor, ...)`: interpole entre 1 et scaleFactor avec une courbe douce (accélération et décélération)
4. La valeur résultante est multipliée par l'échelle originale et appliquée à l'objet

> **Important**: L'utilisation de `Mathf.SmoothStep` est ce qui rend l'animation fluide et naturelle. Sans elle, l'animation aurait une vitesse constante qui paraîtrait robotique.

## Comment fonctionne cette animation

Cette animation fonctionne sur un principe simple mais efficace:

1. **Ping-Pong**: Cette fonction fait rebondir une valeur entre 0 et 1 comme une balle de ping-pong. Avec le temps multiplié par la vitesse d'animation, la valeur oscillera plus ou moins vite.

2. **SmoothStep**: Cette fonction prend une valeur entre 0 et 1 et l'interpole entre deux autres valeurs (ici 1 et scaleFactor) avec une accélération au début et une décélération à la fin, créant un mouvement plus naturel.

3. **Mise à l'échelle**: En multipliant l'échelle originale par cette valeur interpolée, l'ennemi grossit et rétrécit de manière cyclique.

## Comprendre les paramètres

### animationSpeed (Vitesse d'animation)

Ce paramètre contrôle la rapidité de la pulsation:

- `animationSpeed = 1.0f`: Une pulsation complète prend environ 2 secondes
- `animationSpeed = 2.0f`: Une pulsation complète prend environ 1 seconde
- `animationSpeed = 4.0f`: Une pulsation complète prend environ 0.5 seconde
- `animationSpeed = 8.0f`: Une pulsation complète prend environ 0.25 seconde

### scaleFactor (Facteur d'échelle)

Ce paramètre contrôle l'amplitude de la pulsation:

- `scaleFactor = 1.05f`: Subtil, l'ennemi grossit à 105% de sa taille
- `scaleFactor = 1.1f`: Modéré, l'ennemi grossit à 110% de sa taille
- `scaleFactor = 1.2f`: Plus prononcé, l'ennemi grossit à 120% de sa taille
- `scaleFactor = 1.5f`: Très visible, l'ennemi grossit à 150% de sa taille

## Améliorations possibles

### 1. Vitesse d'animation aléatoire pour chaque ennemi

Pour que chaque ennemi ait sa propre vitesse d'animation et ajouter plus de variété visuelle :

```csharp
private Vector3 originalScale;
private float scaleFactor = 1.1f;
private float animationSpeed;  // On ne l'initialise plus ici

void Start()
{
    originalScale = transform.localScale;
    
    // Assigne une vitesse aléatoire à chaque ennemi
    animationSpeed = Random.Range(1.5f, 3.5f);
}

void Update()
{
    float scale = Mathf.SmoothStep(1f, scaleFactor, Mathf.PingPong(Time.time * animationSpeed, 1f));
    transform.localScale = originalScale * scale;
}
```

Ce code génère aléatoirement une vitesse d'animation différente pour chaque ennemi au moment de sa création. Vous pouvez également lier cette vitesse à d'autres caractéristiques de l'ennemi :

```csharp
public float moveSpeed = 2.0f;  // Vitesse de déplacement de l'ennemi

void Start()
{
    originalScale = transform.localScale;
    
    // Associe la vitesse d'animation à la vitesse de déplacement
    animationSpeed = moveSpeed + Random.Range(-0.5f, 0.5f);
}
```

Cette approche est particulièrement intéressante car elle crée une cohérence visuelle : les ennemis qui se déplacent rapidement pulsent également plus vite, ce qui les rend encore plus menaçants !

### 2. Ajouter un décalage aléatoire pour désynchroniser les animations

Pour éviter que tous vos ennemis ne pulsent en même temps, vous pouvez ajouter un décalage:

```csharp
private float timeOffset;

void Start()
{
    originalScale = transform.localScale;
    timeOffset = Random.Range(0f, 1f); // Décalage aléatoire entre 0 et 1 seconde
}

void Update()
{
    float scale = Mathf.SmoothStep(1f, scaleFactor, Mathf.PingPong((Time.time + timeOffset) * animationSpeed, 1f));
    transform.localScale = originalScale * scale;
}
```

### 2. Combiner avec une légère rotation

Vous pouvez ajouter une rotation subtile pour encore plus de dynamisme:

```csharp
private float rotationSpeed = 15f;

void Update()
{
    // Animation d'échelle
    float scale = Mathf.SmoothStep(1f, scaleFactor, Mathf.PingPong(Time.time * animationSpeed, 1f));
    transform.localScale = originalScale * scale;
    
    // Animation de rotation
    transform.Rotate(0, 0, rotationSpeed * Time.deltaTime);
}
```

### 3. Optimisation pour de très grandes hordes

Si vous avez des centaines d'ennemis, vous pouvez optimiser davantage:

```csharp
private float updateInterval = 0.05f; // Mettre à jour l'animation toutes les 0.05 secondes au lieu de chaque frame
private float nextUpdateTime;

void Start()
{
    originalScale = transform.localScale;
    nextUpdateTime = Time.time + Random.Range(0f, updateInterval);
}

void Update()
{
    if (Time.time >= nextUpdateTime)
    {
        float scale = Mathf.SmoothStep(1f, scaleFactor, Mathf.PingPong(Time.time * animationSpeed, 1f));
        transform.localScale = originalScale * scale;
        nextUpdateTime = Time.time + updateInterval;
    }
}
```

### 4. Animation basée sur la distance au joueur

Vous pouvez faire varier l'intensité de l'animation en fonction de la distance au joueur:

```csharp
private Transform playerTransform;
private float maxDistance = 10f;

void Start()
{
    originalScale = transform.localScale;
    playerTransform = GameObject.FindGameObjectWithTag("Player").transform;
}

void Update()
{
    // Calcul de l'intensité basée sur la distance
    float distanceToPlayer = Vector3.Distance(transform.position, playerTransform.position);
    float intensityFactor = Mathf.Clamp01(1f - (distanceToPlayer / maxDistance));
    
    // Plus l'ennemi est proche du joueur, plus sa pulsation est intense
    float currentScaleFactor = 1f + (scaleFactor - 1f) * intensityFactor;
    
    float scale = Mathf.SmoothStep(1f, currentScaleFactor, Mathf.PingPong(Time.time * animationSpeed, 1f));
    transform.localScale = originalScale * scale;
}
```

## Utilisation pratique

Pour utiliser ce script dans votre jeu:

1. Créez un script C# nommé "EnemyAnimation"
2. Copiez-y le code présenté
3. Attachez ce script à vos prefabs d'ennemis
4. Ajustez les valeurs de `scaleFactor` et `animationSpeed` selon vos préférences

## Conclusion

Cette technique d'animation est extrêmement efficace pour les jeux comportant de nombreux ennemis. Contrairement à l'utilisation d'Animators, cette approche basée sur le code:

- Consomme très peu de ressources CPU
- Peut s'appliquer à des centaines d'objets simultanément
- Crée un effet visuel intéressant qui ajoute du dynamisme au jeu
- Est facilement personnalisable via quelques paramètres simples

Ce type d'animation légère est idéal pour les jeux de type "bullet hell" ou "horde survival" comme Vampire Survivors, où la performance est cruciale.

## Exercice pratique

Essayez de combiner cette animation d'échelle avec une légère variation de couleur. Par exemple, faites osciller la teinte du sprite entre deux couleurs pour créer un effet de "pulsation" visuelle encore plus marqué.

[Retour à la page principale](../README.md)
