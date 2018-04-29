
# Factory Pattern

## Problème
Nous devons créer des objets sans avoir à spécifier la classe exacte de l'objet qui sera créé.

## Solution
Le modèle **Factory** est une spécialisation du modèle[Template](template.md). Nous commençons par créer une classe de base générique où nous ne prenons pas la décision "quelle classe". Au lieu de cela, chaque fois qu'il a besoin de créer un nouvel objet, il appelle une méthode qui est définie dans une sous-classe. Ainsi, en fonction de la sous-classe que nous utilisons (**usine**), nous créons des objets d'une classe ou d'une autre (**produits**).

## Exemple
Imaginez que l'on vous demande de construire une simulation de la vie dans un étang, où il y a beaucoup de canards :

```ruby
class Pond
  def initialize(number_ducks)
    @ducks = number_ducks.times.inject([]) do |ducks, i|
      ducks << Duck.new("Duck#{i}")
      ducks
    end
  end

  def simulate_one_day
    @ducks.each {|duck| duck.speak}
    @ducks.each {|duck| duck.eat}
    @ducks.each {|duck| duck.sleep}
  end
end

pond = Pond.new(3)
pond.simulate_one_day
```

Mais comment modéliserions-nous notre étang si nous voulions avoir des grenouilles au lieu de canards ? Dans l'implémentation ci-dessus, nous spécifions dans l'initialisateur de l'étang qu'il doit être rempli de canards. Donc, nous allons le refactoriser pour que la décision de créer un type d'animal ou un autre soit prise dans une sous-classe :

```ruby
class Pond
  def initialize(number_animals)
    @animals = number_animals.times.inject([]) do |animals, i|
      animals << new_animal("Animal#{i}")
      animals
    end
  end

  def simulate_one_day
    @animals.each {|animal| animal.speak}
    @animals.each {|animal| animal.eat}
    @animals.each {|animal| animal.sleep}
  end
end

class FrogPond < Pond
  def new_animal(name)
    Frog.new(name)
  end
end

pond = FrogPond.new(3)
pond.simulate_one_day
```
