
# Singleton Pattern

## Problème
Nous avons besoin d'avoir une seule instance d'une certaine classe dans toute l'application.

## Solution
Dans le modèle **Singleton**, l'accès au constructeur est limité, de sorte qu'il ne peut pas être instancié. Ainsi, la création de l'instance unique se fait à l'intérieur de la classe et est tenue dans une variable de classe, et on peut y accéder par l'intermédiaire d'un "getter" à travers l'application.

## Example
Considérons l'implémentation d'une classe "logger" :

```ruby
class SimpleLogger
  attr_accessor :level

  ERROR = 1
  WARNING = 2
  INFO = 3
  
  def initialize
    @log = File.open("log.txt", "w")
    @level = WARNING
  end

  def error(msg)
    @log.puts(msg)
    @log.flush
  end

  def warning(msg)
    @log.puts(msg) if @level >= WARNING
    @log.flush
  end

  def info(msg)
    @log.puts(msg) if @level >= INFO
    @log.flush
  end
end
```

La journalisation (logging) est une fonctionnalité utilisée dans l'ensemble de l'application, il est donc logique qu'il n'existe qu'une seule instance de l'enregistreur. Nous pouvons nous assurer que personne n'instancie deux fois la classe `SimpleLogger' en rendant son constructeur privé :

```ruby
class SimpleLogger

  # Lots of code deleted...
  @@instance = SimpleLogger.new

  def self.instance
    return @@instance
  end

  private_class_method :new
end

SimpleLogger.instance.info('Computer wins chess game.')
```

Nous pouvons obtenir le même comportement en incluant le module `Singleton`, de sorte que nous pouvons éviter de dupliquer le code si nous créons plusieurs singletons :

```ruby
require 'singleton'

class SimpleLogger
  include Singleton
  # Lots of code deleted...
end
```
