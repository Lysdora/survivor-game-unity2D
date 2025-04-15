# Guide: Contrôleur de caméra dans Unity

![Unity Logo](../images/unity-logo.png)

## Introduction

Bienvenue dans ce guide sur la création d'un contrôleur de caméra dans Unity! Cette fonctionnalité est essentielle pour tout jeu, car elle permet à la caméra de suivre automatiquement le joueur tout au long de son parcours. Dans ce tutoriel, nous allons analyser un script simple mais efficace qui fait exactement cela.

## Prérequis

- Unity (version récente)
- Un personnage joueur déjà configuré avec son script PlayerController
- Connaissances de base en C#

## Objectif du script CameraController

L'objectif principal de ce script est de créer une caméra qui:
- Trouve automatiquement le joueur dans la scène
- Suit en temps réel sa position horizontale et verticale (X et Y)
- Maintient une distance fixe par rapport au joueur sur l'axe Z

## Analyse du script CameraController

Plongeons dans le code et examinons-le section par section.

### 1. Imports et définition de la classe

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class CameraController : MonoBehaviour
{
    private Transform target; // Référence à la cible (le joueur)
```

Cette première partie:
- Importe les bibliothèques nécessaires
- Définit la classe `CameraController` qui hérite de `MonoBehaviour`
- Déclare une variable privée `target` qui contiendra la référence au Transform du joueur

> **Note**: L'héritage de `MonoBehaviour` est essentiel car il permet d'utiliser les méthodes du cycle de vie d'Unity comme `Start()` et `LateUpdate()`.

### 2. Initialisation et recherche du joueur

```csharp
// Start est appelé avant la première mise à jour
void Start()
{
    // Trouve le joueur dans la scène et récupère son Transform
    target = FindObjectOfType<PlayerController>().transform;
}
```

La méthode `Start()` est appelée une seule fois au lancement du jeu. Dans cette méthode:

1. On utilise `FindObjectOfType<PlayerController>()` pour localiser le premier GameObject dans la scène qui possède un composant `PlayerController`
2. On accède à sa propriété `transform` pour obtenir sa position
3. On stocke cette référence dans la variable `target` pour une utilisation ultérieure

> **Astuce**: Bien que pratique, `FindObjectOfType` peut être coûteux en performance dans de grandes scènes. Pour un jeu plus optimisé, vous pourriez préférer assigner la référence via l'inspecteur ou utiliser un système de Singleton.

### 3. Suivi de la position du joueur

```csharp
// LateUpdate est utilisé pour suivre la position du joueur après que tout soit mis à jour
void LateUpdate()
{
    // Met à jour la position de la caméra pour suivre le joueur
    // Utilise target.position.x et target.position.y pour définir la position de la caméra
    transform.position = new Vector3(target.position.x, target.position.y, transform.position.z); // La caméra est placée à une distance fixe sur l'axe Z
}
```

La méthode `LateUpdate()` est appelée à chaque frame, mais après que toutes les méthodes `Update()` des autres objets aient été exécutées. C'est la méthode idéale pour les suivis de caméra, car:

1. Elle assure que le joueur a déjà été mis à jour et déplacé à sa nouvelle position
2. Elle évite les problèmes de tremblement ou de décalage visuel

Dans cette méthode:
- On crée un nouveau Vector3 qui:
  - Prend les coordonnées X et Y du joueur (via `target.position.x` et `target.position.y`)
  - Conserve la coordonnée Z actuelle de la caméra (`transform.position.z`)
- On assigne ce nouveau vecteur à la position de la caméra

> **Important**: Conserver la valeur Z de la caméra est crucial car c'est ce qui détermine la distance entre la caméra et le joueur, et donc le champ de vision.

## Améliorations possibles

### 1. Ajouter un décalage (offset)

Pour plus de flexibilité, vous pourriez ajouter un décalage personnalisable:

```csharp
[SerializeField] private Vector3 offset = new Vector3(0f, 0f, -10f);

void LateUpdate()
{
    transform.position = new Vector3(target.position.x + offset.x, target.position.y + offset.y, offset.z);
}
```

### 2. Implémentation d'un suivi en douceur

Pour éviter les mouvements brusques, vous pourriez ajouter un suivi en douceur:

```csharp
[SerializeField] private float smoothSpeed = 0.125f;

void LateUpdate()
{
    Vector3 desiredPosition = new Vector3(target.position.x, target.position.y, transform.position.z);
    Vector3 smoothedPosition = Vector3.Lerp(transform.position, desiredPosition, smoothSpeed);
    transform.position = smoothedPosition;
}
```

### 3. Limites de la caméra

Pour éviter que la caméra ne sorte des limites du niveau:

```csharp
[SerializeField] private float minX, maxX, minY, maxY;

void LateUpdate()
{
    float clampedX = Mathf.Clamp(target.position.x, minX, maxX);
    float clampedY = Mathf.Clamp(target.position.y, minY, maxY);
    transform.position = new Vector3(clampedX, clampedY, transform.position.z);
}
```

## Utilisation pratique

Pour utiliser ce script dans votre jeu:

1. Créez une caméra dans votre scène (ou utilisez la caméra principale)
2. Attachez le script `CameraController` à cette caméra
3. Assurez-vous que votre joueur a bien un composant `PlayerController`
4. Lancez le jeu et admirez votre caméra qui suit parfaitement le joueur!

## Conclusion

Ce script de caméra simple mais efficace constitue une base idéale pour tout jeu 2D nécessitant que la caméra suive le joueur. Bien qu'il fonctionne parfaitement tel quel, n'hésitez pas à l'étendre avec les améliorations suggérées pour répondre aux besoins spécifiques de votre jeu.

## Exercice pratique

Essayez d'implémenter un "room-based camera system" où la caméra reste fixe dans une "pièce" jusqu'à ce que le joueur atteigne un certain point, puis se déplace rapidement vers la pièce suivante. Ce type de système était couramment utilisé dans les jeux Zelda classiques!

[Retour à la page principale](../README.md)
