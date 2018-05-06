
# Observer Pattern

## Problème
Nous voulons construire un système hautement intégré, c'est-à-dire un système où chaque partie est consciente de l'état de l'ensemble. Nous voulons aussi qu'il soit maintenable, donc nous devrions éviter le couplage entre les classes.

## Solution
Si nous voulons qu'un composant (observateur) connaisse les activités d'un autre (sujet), nous pourrions simplement câbler les deux classes et informer le premier sur certaines actions effectuées sur le second. Cela signifie que nous devrions passer une référence à l'observateur lorsque nous créons le sujet, et appeler certaines de ses méthodes lorsque ce dernier change. Cependant, dans cette approche, nous faisons quelque chose que nous voulons éviter : augmenter le couplage. De plus, si nous voulions informer un autre observateur, nous devrions modifier la mise en œuvre du sujet pour qu'il le notifie, même si rien n'a changé. Une bien meilleure approche consiste à tenir une liste d'objets intéressés par les changements de sujet et à définir une interface propre entre la source de l'information (le sujet) et les consommateurs (les observateurs). De cette façon, chaque fois qu'il y a un changement sur le sujet, il suffit d'itération sur la liste des observateurs et de les avertir en utilisant l'interface que nous avons définie.

## Exemple
Considérons un objet `Employee` qui a un bien `salary`. Nous aimerions pouvoir modifier son salaire et tenir le système de paie au courant de ces modifications. La façon la plus simple d'y parvenir est de passer une référence à la paie et de l'informer chaque fois que nous modifions le `salary` de l'employé :

```ruby
class Employee
  attr_reader :name, :title
  attr_reader :salary

  def initialize( name, title, salary, payroll)
    @name = name
    @title = title
    @salary = salary
    @payroll = payroll
  end

  def salary=(new_salary)
    @salary = new_salary
    @payroll.update(self)
  end
end
```

Le problème est que si nous voulons notifier quelqu'un d'autre (`TaxMan`, par exemple), nous devons modifier l'employé. Cela signifie que d'autres classes sont à l'origine des changements apportés à `Employee`, même si rien n'y a changé. Fournissons un moyen de garder une liste des objets intéressés sur les changements de salaire.

```ruby
class Employee
  attr_reader :name, :title
  attr_reader :salary

  def initialize(name, title, salary)
    @name = name
    @title = title
    @salary = salary
    @observers = []
  end

  def salary=(new_salary)
    @salary = new_salary
    notify_observers
  end

  def add_observer(observer)
    @observers << observer
  end

  def delete_observer(observer)
    @observers.delete(observer)
  end

  def notify_observers
    @observers.each do |observer|
      observer.update(self)
    end
  end
end
```

Maintenant nous pouvons utiliser la méthode `add_observer` pour ajouter autant d'objets que nous voulons à la liste des observateurs, et tous seront notifiés chaque fois que le salaire change.

```ruby
fred = Employee.new('Fred', 'Crane Operator', 30000.0)

payroll = Payroll.new
fred.add_observer(payroll)

tax_man = TaxMan.new
fred.add_observer(tax_man)

fred.salary=35000.0
```

Même si nous pouvons implémenter le pattern par nous-mêmes, la bibliothèque standard Ruby possède un module pré-construit qui nous permet de rendre n'importe lequel de nos objets observables, nous libérant ainsi de définir les mêmes méthodes partout. Repenser la classe `Employee` pour utiliser le module `Observable`:

```ruby
require 'observer'

class Employee
  include Observable

  attr_reader :name, :title
  attr_reader :salary

  def initialize(name, title, salary)
    @name = name
    @title = title
    @salary = salary
  end

  def salary=(new_salary)
    @salary = new_salary
    changed
    notify_observers(self)
  end
end
```
