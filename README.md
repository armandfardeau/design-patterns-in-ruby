# Design Patterns en Ruby

Résumé des patrons de conception expliqués dans le livre [Design Patterns in Ruby](http://designpatternsinruby.com/), où [Russ Olsen](http://russolsen.com/) explique et adapte à Ruby 14 des 23 modèles de conception originaux du GoF (Gang des quatres).

Ce travail est une traduction du contenu original qui peut ètre trouvé ici : [davidgf/design-patterns-in-ruby](davidgf/design-patterns-in-ruby)

## Patrons de conception

### GoF Patterns

* [Adapter](adapter.md): aide deux interfaces incompatibles à travailler ensemble
* [Builder](builder.md): créer des objets complexes et difficiles à configurer
* [Command](command.md): exécute une tâche spécifique sans avoir d'informations sur le destinataire de la demande.
* [Composite](composite.md): construit une hiérarchie d'objets arborescents et interagit de la même manière avec tous ces objets.
* [Decorator](decorator.md): varier les responsabilités d'un objet en y ajoutant certaines caractéristiques.
* [Factory](factory.md): créer des objets sans avoir à spécifier la classe exacte de l'objet qui sera créé.
* [Interpreter](interpreter.md): fournit un langage spécialisé pour résoudre un problème bien défini de domaine de connaissance.
* [Iterator](iterator.md): permet d'accéder à une collection de sous-objets sans exposer la représentation sous-jacente.
* [Observer](observer.md): aide à la construction d'un système hautement intégré, maintenable et évite le couplage entre les classes.
* [Proxy](proxy.md): nous permet d'avoir plus de contrôle sur la façon et le moment où nous accédons à un certain objet.
* [Singleton](singleton.md): avoir une seule instance d'une certaine classe dans l'application.
* [Strategy](strategy.md): fait varier la partie d'un algorithme au moment de l'exécution.
* [Template Method](template_method.md): redéfinit certaines étapes d'un algorithme sans changer la structure de l'algorithme.

### Non-GoF Patterns: Patterns For Ruby

* [Convention Over Configuration](convention_over_configuration.md): construire un système extensible et ne pas porter le fardeau de la configuration.
* [Domain-Specific Language](dsl.md): construire une syntaxe pratique pour résoudre les problèmes d'un domaine spécifique.
* [Meta-Programming](meta_programming.md): gagner en flexibilité lors de la définition de nouvelles classes et créer des objets personnalisés à la volée.


## Contribution

Les contributions sont les bienvenues ! Que pourriez-vous faire ?:
* Trouver des fautes de frappe et de grammaire.
* Proposer une meilleure façon d'expliquer un modèle.
* Ajouter des exemples plus clairs d'utilisation d'un motif.
* Ajouter d'autres motifs GoF qui ne sont pas couverts dans le livre.

**Exemples de refactoring** Les PR ne seront **pas pris en compte**. Les exemples fournis par Russ Olsen dans son livre se veulent simples et auto-explicatifs, pas les plus performants ou les plus élégants, leur but est simplement éducatif.
