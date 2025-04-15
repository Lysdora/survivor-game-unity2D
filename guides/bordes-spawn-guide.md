# Guide: Système de Spawn d'Ennemis sur les Bords

Dans ce guide, nous allons améliorer notre système de spawn d'ennemis pour un jeu de type Vampire Survivors. Nous passerons d'un simple spawner fixe à un système plus dynamique qui fait apparaître les ennemis sur les bords d'une zone rectangulaire définie, créant ainsi une expérience plus immersive et stratégique pour le joueur.

## Objectifs du système
- Créer une zone de spawn rectangulaire délimitée par deux points
- Faire apparaître les ennemis aléatoirement sur les quatre bords de cette zone
- Rendre l'apparition des ennemis plus imprévisible et intéressante

## Prérequis
- Avoir suivi le [guide du système de spawn basique](basic-enemy-spawner-guide.md)
- Un prefab d'ennemi déjà configuré

## Étape 1: Configuration de la zone de spawn

Notre système utilisera deux points de référence pour définir une zone rectangulaire:
1. Un point minimum (en bas à gauche)
2. Un point maximum (en haut à droite)

Commençons par configurer ces points dans la scène:

1. Sélectionne ton GameObject "EnemySpawner" dans la hiérarchie
2. Crée deux objets enfants:
   - Clique droit sur "EnemySpawner" → Create Empty et nomme-le "MinSpawn"
   - Clique droit sur "EnemySpawner" → Create Empty et nomme-le "MaxSpawn"
3. Positionne ces objets:
   - Place "MinSpawn" dans le coin inférieur gauche de ta zone de jeu
   - Place "MaxSpawn" dans le coin supérieur droit de ta zone de jeu



## Étape 2: Mise à jour du script EnemySpawner

Modifie ton script `EnemySpawner.cs` pour intégrer la nouvelle fonctionnalité de spawn sur les bords:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class EnemySpawner : MonoBehaviour
{
    public GameObject enemyToSpawn;
    public float timeToSpawn;
    private float spawnCounter;
    
    // Points définissant les limites de notre zone de spawn
    public Transform minSpawn, maxSpawn;

    void Start()
    {
        // Initialiser le compteur pour une petite pause au début du jeu
        spawnCounter = timeToSpawn;
    }

    void Update()
    {
        // Décrémenter le compteur
        spawnCounter -= Time.deltaTime;
        
        // Vérifier si c'est le moment de faire apparaître un ennemi
        if (spawnCounter <= 0)
        {
            // Réinitialiser le compteur
            spawnCounter = timeToSpawn;
            
            // Faire apparaître un ennemi à un point aléatoire sur les bords
            Instantiate(enemyToSpawn, SelectSpawnPoint(), transform.rotation);
        }
    }

    public Vector3 SelectSpawnPoint()
    {
        // Initialiser le point de spawn
        Vector3 spawnPoint = Vector3.zero;

        // 50% de chance de choisir un bord vertical (gauche/droite) vs horizontal (haut/bas)
        bool spawnVerticalEdge = Random.Range(0f, 1f) > .5f;

        if (spawnVerticalEdge) // Si on choisit un bord vertical (gauche ou droite)
        {
            // Choisir une position Y aléatoire entre les valeurs min et max
            spawnPoint.y = Random.Range(minSpawn.position.y, maxSpawn.position.y);

            // 50% de chance de choisir le bord droit ou gauche
            if (Random.Range(0f, 1f) > .5f)
            {
                // Utiliser la coordonnée X maximum (bord droit)
                spawnPoint.x = maxSpawn.position.x;
            }
            else
            {
                // Utiliser la coordonnée X minimum (bord gauche)
                spawnPoint.x = minSpawn.position.x;
            }
        }
        else // Si on choisit un bord horizontal (haut ou bas)
        {
            // Choisir une position X aléatoire entre les valeurs min et max
            spawnPoint.x = Random.Range(minSpawn.position.x, maxSpawn.position.x);

            // 50% de chance de choisir le bord supérieur ou inférieur
            if (Random.Range(0f, 1f) > .5f)
            {
                // Utiliser la coordonnée Y maximum (bord supérieur)
                spawnPoint.y = maxSpawn.position.y;
            }
            else
            {
                // Utiliser la coordonnée Y minimum (bord inférieur)
                spawnPoint.y = minSpawn.position.y;
            }
        }

        // Retourner le point de spawn calculé
        return spawnPoint;
    }
}
```

## Étape 3: Configuration dans l'éditeur Unity

1. Sélectionne ton objet "EnemySpawner" dans la hiérarchie
2. Dans l'Inspecteur, assigne les éléments suivants:
   - `Enemy To Spawn`: ton prefab d'ennemi
   - `Time To Spawn`: le délai entre chaque spawn (par exemple 2)
   - `Min Spawn`: glisse l'objet "MinSpawn" depuis la hiérarchie
   - `Max Spawn`: glisse l'objet "MaxSpawn" depuis la hiérarchie



## Comment ça fonctionne?

Ce système fonctionne selon les principes suivants:

1. **Définition de la zone**: Les deux points `minSpawn` et `maxSpawn` définissent un rectangle qui entoure généralement la zone de jeu.

2. **Sélection du bord**: À chaque spawn, le système détermine aléatoirement si l'ennemi apparaîtra sur un bord vertical (gauche/droite) ou horizontal (haut/bas).

3. **Position aléatoire sur le bord**:
   - Sur un bord vertical: la position Y est aléatoire, et X est soit au minimum (gauche) soit au maximum (droite)
   - Sur un bord horizontal: la position X est aléatoire, et Y est soit au minimum (bas) soit au maximum (haut)

4. **Instantiation**: L'ennemi est créé à cette position calculée, avec la rotation par défaut du spawner

Ce système crée une expérience beaucoup plus dynamique car:
- Les ennemis apparaissent tout autour du joueur
- L'apparition est imprévisible, ce qui augmente la tension
- Le joueur doit constamment surveiller tous les côtés

## Astuces de débogage

Pour visualiser ta zone de spawn dans l'éditeur, ajoute cette méthode à ton script:

```csharp
void OnDrawGizmos()
{
    if (minSpawn != null && maxSpawn != null)
    {
        // Dessiner le rectangle de la zone de spawn
        Gizmos.color = Color.red;
        
        // Calculer la taille du rectangle
        Vector3 size = new Vector3(
            maxSpawn.position.x - minSpawn.position.x,
            maxSpawn.position.y - minSpawn.position.y,
            0f
        );
        
        // Calculer le centre du rectangle
        Vector3 center = minSpawn.position + size/2;
        
        // Dessiner un cube en fil de fer
        Gizmos.DrawWireCube(center, size);
    }
}
```

## Améliorations futures

Ce système peut encore être amélioré de plusieurs façons:

1. **Distance au joueur**: S'assurer que les ennemis n'apparaissent pas trop près du joueur
2. **Types d'ennemis variés**: Utiliser un tableau de prefabs pour faire apparaître différents types d'ennemis
3. **Difficulté progressive**: Augmenter la fréquence de spawn avec le temps
4. **Vagues d'ennemis**: Créer des vagues avec différentes compositions d'ennemis
5. **Limiter le nombre d'ennemis**: Mettre une limite au nombre d'ennemis simultanés pour gérer les performances

Ces améliorations seront abordées dans nos prochains guides!

## Conclusion

Félicitations! Tu as maintenant un système de spawn qui fait apparaître des ennemis sur les bords d'une zone rectangulaire. Ce système simple mais efficace est une base solide pour créer une expérience de jeu dynamique de type Vampire Survivors.

N'hésite pas à expérimenter avec les positions des points `minSpawn` et `maxSpawn` pour ajuster la zone de spawn à tes besoins.
