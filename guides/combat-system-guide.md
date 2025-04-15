# Guide : Implémentation d'un système de combat dans un jeu type Vampire Survivors

## Introduction

Dans un jeu de type Vampire Survivors, le système de combat est au cœur du gameplay. Le joueur doit survivre face à des vagues d'ennemis tout en récoltant de l'expérience et en améliorant ses capacités. Dans ce guide, nous allons voir comment implémenter un système de combat basique en utilisant les scripts `PlayerHealthController` et `EnemyController` que tu as déjà créés.

## Prérequis

- Comprendre les bases de Unity et C#
- Avoir un système de déplacement pour le joueur fonctionnel
- Avoir un système de spawn d'ennemis
- Avoir les scripts `PlayerHealthController` et `EnemyController` (que tu as déjà créés)

## Analyse du code existant

### PlayerHealthController

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerHealthController : MonoBehaviour
{
    public float current_health, max_health;
    
    // Start is called before the first frame update
    void Start()
    {
        current_health = max_health;
    }

    // Update is called once per frame
    void Update()
    {
    }

    public void TakeDamage(float damageToTake)
    {
        current_health -= damageToTake;
        
        if (current_health <= 0)
        {
            gameObject.SetActive(false);
        }
    }
}
```

### EnemyController

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class EnemyController : MonoBehaviour
{
    Rigidbody2D rb;
    
    public float moveSpeed;
    private Transform target;
    
    // Start is called before the first frame update
    void Start()
    {
        target = FindFirstObjectByType<PlayerController>().transform;
        rb = GetComponent<Rigidbody2D>();
    }

    // Update is called once per frame
    void Update()
    {
        rb.velocity = (target.position - transform.position).normalized * moveSpeed;
    }
}
```

## Explications détaillées

### Système de santé du joueur

Le script `PlayerHealthController` est un bon début pour gérer la santé du joueur. Il contient:
- Des variables pour la santé actuelle (`current_health`) et maximale (`max_health`)
- Une méthode `Start()` qui initialise la santé actuelle à la valeur maximale
- Une méthode `TakeDamage()` qui réduit la santé et désactive le joueur s'il n'a plus de vie

### Déplacement des ennemis

Le script `EnemyController` gère le déplacement des ennemis vers le joueur. Il:
- Recherche le joueur au démarrage
- Obtient le composant `Rigidbody2D` pour gérer le mouvement
- Déplace l'ennemi en direction du joueur à chaque frame

## Implémentation du système de combat

Voici plusieurs façons d'implémenter le système de combat entre le joueur et les ennemis, du plus simple au plus élaboré:

### 1. Collision simple (méthode la plus facile)

Cette méthode utilise les collisions Unity pour détecter quand un ennemi touche le joueur.

1. Ajoute ce code à ton script `EnemyController`:

```csharp
public float damageAmount = 10f; // Quantité de dégâts infligés
public float damageInterval = 0.5f; // Intervalle entre chaque dégât (évite les dégâts trop rapides)
private float damageCounter; // Compteur pour suivre le temps écoulé
private PlayerHealthController playerHealth;

void Start()
{
    target = FindFirstObjectByType<PlayerController>().transform;
    rb = GetComponent<Rigidbody2D>();
    playerHealth = target.GetComponent<PlayerHealthController>();
}

void Update()
{
    rb.velocity = (target.position - transform.position).normalized * moveSpeed;
    
    // Décrémenter le compteur de dégâts
    if (damageCounter > 0)
        damageCounter -= Time.deltaTime;
}

private void OnCollisionStay2D(Collision2D collision)
{
    // Vérifier si nous sommes en collision avec le joueur
    if (collision.gameObject.CompareTag("Player") && damageCounter <= 0)
    {
        // Infliger des dégâts
        playerHealth.TakeDamage(damageAmount);
        
        // Réinitialiser le compteur d'intervalle
        damageCounter = damageInterval;
    }
}
```

2. Assure-toi que ton objet joueur a bien le tag "Player" dans l'inspecteur Unity.

3. Vérifie que tes objets ont des colliders appropriés:
   - Le joueur devrait avoir un Collider2D (Circle ou Box) et un Rigidbody2D
   - Les ennemis devraient avoir un Collider2D et un Rigidbody2D

### 2. Système basé sur les déclencheurs (Triggers)

Cette méthode utilise des triggers pour une détection plus précise et personnalisable.

1. Modifie les colliders de tes ennemis pour qu'ils soient en mode "Is Trigger".

2. Ajoute ce code à ton `EnemyController`:

```csharp
public float damageAmount = 10f;
public float damageInterval = 0.5f;
private float damageCounter;
private PlayerHealthController playerHealth;

void Start()
{
    target = FindFirstObjectByType<PlayerController>().transform;
    rb = GetComponent<Rigidbody2D>();
    playerHealth = target.GetComponent<PlayerHealthController>();
}

void Update()
{
    rb.velocity = (target.position - transform.position).normalized * moveSpeed;
    
    if (damageCounter > 0)
        damageCounter -= Time.deltaTime;
}

private void OnTriggerStay2D(Collider2D collision)
{
    if (collision.CompareTag("Player") && damageCounter <= 0)
    {
        playerHealth.TakeDamage(damageAmount);
        damageCounter = damageInterval;
    }
}
```

### 3. Système avancé avec Feedback visuel

Pour une meilleure expérience de jeu, ajoutons des effets visuels quand le joueur prend des dégâts.

1. D'abord, améliorons le script `PlayerHealthController`:

```csharp
using System.Collections;
using UnityEngine;
using UnityEngine.UI; // Pour l'UI

public class PlayerHealthController : MonoBehaviour
{
    public float current_health, max_health;
    public Image healthBar; // Référence à une barre de vie UI (facultatif)
    public float invincibilityTime = 0.5f; // Temps d'invincibilité après avoir pris des dégâts
    
    private bool isInvincible = false;
    private SpriteRenderer spriteRenderer; // Pour l'effet de clignotement
    
    void Start()
    {
        current_health = max_health;
        spriteRenderer = GetComponent<SpriteRenderer>();
        
        // Mettre à jour la barre de vie au démarrage
        UpdateHealthBar();
    }

    void Update()
    {
        // Tu peux ajouter des choses ici si nécessaire
    }

    public void TakeDamage(float damageToTake)
    {
        // Si le joueur est invincible, ne pas prendre de dégâts
        if (isInvincible)
            return;
            
        current_health -= damageToTake;
        
        // Mettre à jour l'UI
        UpdateHealthBar();
        
        // Effet visuel et invincibilité temporaire
        StartCoroutine(FlashRoutine());
        
        if (current_health <= 0)
        {
            Die();
        }
    }
    
    void UpdateHealthBar()
    {
        if (healthBar != null)
        {
            healthBar.fillAmount = current_health / max_health;
        }
    }
    
    void Die()
    {
        // Désactiver le joueur comme avant
        gameObject.SetActive(false);
        
        // Ici tu pourrais ajouter un effet de mort, un son, etc.
        Debug.Log("Player died!");
        
        // Optionnel: Déclencher un Game Over après un délai
        // Invoke("GameOver", 2f);
    }
    
    IEnumerator FlashRoutine()
    {
        isInvincible = true;
        
        // Faire clignoter le sprite 3 fois
        for (int i = 0; i < 3; i++)
        {
            spriteRenderer.color = new Color(1, 0.5f, 0.5f); // Rouge pâle
            yield return new WaitForSeconds(invincibilityTime / 6);
            spriteRenderer.color = Color.white;
            yield return new WaitForSeconds(invincibilityTime / 6);
        }
        
        isInvincible = false;
    }
}
```

2. Ajouter une barre de vie UI:
   - Crée un Canvas dans ta scène
   - Ajoute une Image comme enfant du Canvas (ce sera l'arrière-plan de la barre)
   - Ajoute une autre Image comme enfant de la première (ce sera la barre elle-même)
   - Configure l'image de la barre pour qu'elle utilise "Fill" comme type d'image
   - Fais glisser cette image vers la variable `healthBar` dans l'inspecteur de ton PlayerHealthController

### 4. Système d'attaque du joueur vers les ennemis

Maintenant, ajoutons un script pour que le joueur puisse attaquer les ennemis:

1. Créé un nouveau script `EnemyHealth.cs`:

```csharp
using UnityEngine;

public class EnemyHealth : MonoBehaviour
{
    public float health = 30f;
    public float pointsWorth = 10f; // Points gagnés quand l'ennemi est vaincu
    
    // Référence au gestionnaire de score (facultatif)
    // private ScoreManager scoreManager;
    
    void Start()
    {
        // Si tu as un gestionnaire de score
        // scoreManager = FindObjectOfType<ScoreManager>();
    }
    
    public void TakeDamage(float damage)
    {
        health -= damage;
        
        if (health <= 0)
        {
            Die();
        }
    }
    
    void Die()
    {
        // Ajouter des points au score
        // if (scoreManager != null)
        //     scoreManager.AddPoints(pointsWorth);
            
        // Jouer un effet de mort
        // Instantiate(deathEffect, transform.position, Quaternion.identity);
        
        // Détruire l'ennemi
        Destroy(gameObject);
    }
}
```

2. Créé un script pour les armes du joueur, par exemple `PlayerWeapon.cs`:

```csharp
using UnityEngine;

public class PlayerWeapon : MonoBehaviour
{
    public float damage = 10f;
    public float attackRadius = 2f;
    public float attackRate = 1f; // Attaques par seconde
    public LayerMask enemyLayer;
    
    private float attackCounter;
    
    void Update()
    {
        // Décrémenter le compteur d'attaque
        if (attackCounter > 0)
            attackCounter -= Time.deltaTime;
        else
        {
            // Attaquer automatiquement (style Vampire Survivors)
            Attack();
            attackCounter = 1f / attackRate; // Réinitialiser le compteur
        }
    }
    
    void Attack()
    {
        // Trouver tous les ennemis dans le rayon d'attaque
        Collider2D[] hitEnemies = Physics2D.OverlapCircleAll(transform.position, attackRadius, enemyLayer);
        
        // Appliquer des dégâts à chaque ennemi
        foreach (Collider2D enemy in hitEnemies)
        {
            EnemyHealth enemyHealth = enemy.GetComponent<EnemyHealth>();
            if (enemyHealth != null)
            {
                enemyHealth.TakeDamage(damage);
            }
        }
    }
    
    // Pour visualiser le rayon d'attaque dans l'éditeur
    void OnDrawGizmosSelected()
    {
        Gizmos.color = Color.red;
        Gizmos.DrawWireSphere(transform.position, attackRadius);
    }
}
```

3. Assure-toi de créer une Layer "Enemy" et de l'assigner à tes ennemis
4. Assigne cette Layer à la variable `enemyLayer` du script `PlayerWeapon`

## Améliorations possibles

Voici quelques améliorations que tu pourrais apporter à ton système:

1. **Système de niveau et d'expérience**:
   - Les ennemis donnent de l'XP quand ils sont vaincus
   - Le joueur monte en niveau et peut choisir de nouvelles armes ou améliorations

2. **Plusieurs types d'ennemis**:
   - Ennemis rapides mais faibles
   - Ennemis lents mais résistants
   - Ennemis qui attaquent à distance

3. **Effets visuels et sonores**:
   - Animations de dégâts
   - Sons lors des attaques et des dégâts
   - Particules pour les morts d'ennemis

4. **Interface utilisateur complète**:
   - Barre de vie
   - Affichage du score
   - Affichage du niveau et de l'XP

5. **Power-ups et objets à ramasser**:
   - Objets de soin
   - Bonus de vitesse ou de dégâts temporaires
   - Nouvelles armes

## Conclusion

Tu as maintenant toutes les bases pour implémenter un système de combat complet dans ton jeu de type Vampire Survivors. Commence par la méthode la plus simple, puis ajoute progressivement des fonctionnalités à mesure que tu te sens à l'aise avec le système.

N'oublie pas de tester régulièrement ton jeu pour t'assurer que tout fonctionne comme prévu et pour ajuster l'équilibrage (la difficulté, les dégâts, etc.).

## Exercices pratiques

1. **Exercice de base**: Implémente le système de collision simple et fais en sorte que les ennemis infligent des dégâts au joueur.

2. **Exercice intermédiaire**: Ajoute une barre de vie UI et l'effet de clignotement quand le joueur prend des dégâts.

3. **Exercice avancé**: Implémente le système d'attaque du joueur pour qu'il puisse éliminer les ennemis et gagner des points.

4. **Défi supplémentaire**: Ajoute un système d'armes avec différentes caractéristiques (portée, dégâts, vitesse d'attaque) que le joueur peut débloquer en montant de niveau.

Bon courage dans le développement de ton jeu! 🎮
