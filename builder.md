
# Builder Pattern

## Problème
Nous devons créer un objet complexe et difficile à configurer.

## Solution
Le motif **Builder** encapsule la logique de construction d'objets complexes dans sa propre classe. Il définit une interface pour configurer l'objet étape par étape, en masquant les détails de l'implémentation.

## Exemple
Imaginons que nous devons construire un système qui garde la trace des composants d'un ordinateur.

```ruby
class Computer
  attr_accessor :display
  attr_accessor :motherboard
  attr_reader :drives
  
  def initialize(display=:crt, motherboard=Motherboard.new, drives=[])
    @motherboard = motherboard
    @drives = drives
    @display = display
  end
end
```

La création d'un objet `Ordinateur` peut devenir vraiment complexe, car la `carte mère`, par exemple, est un objet entier :

```ruby
class CPU
  # Common CPU stuff...
end

class BasicCPU < CPU
  # Lots of not very fast CPU-related stuff...
end

class TurboCPU < CPU
  # Lots of very fast CPU stuff...
end

class Motherboard
  attr_accessor :cpu
  attr_accessor :memory_size

  def initialize(cpu=BasicCPU.new, memory_size=1000)
    @cpu = cpu
    @memory_size = memory_size
  end
end
```

Ainsi, le processus de construction d'un `Ordinateur` peut être vraiment fastidieux :

```ruby
# Build a fast computer with lots of memory...
motherboard = Motherboard.new(TurboCPU.new, 4000)

# ...and a hard drive, a CD writer, and a DVD
drives = []
drives << Drive.new(:hard_drive, 200000, true)
drives << Drive.new(:cd, 760, true)
drives << Drive.new(:dvd, 4700, false)
computer = Computer.new(:lcd, motherboard, drives)
```

Il peut être rendu beaucoup plus simple en encapsulant la logique de construction dans une classe :

```ruby
class ComputerBuilder
  attr_reader :computer

  def initialize
    @computer = Computer.new
  end

  def turbo(has_turbo_cpu=true)
    @computer.motherboard.cpu = TurboCPU.new
  end

  def display=(display)
    @computer.display=display
  end

  def memory_size=(size_in_mb)
    @computer.motherboard.memory_size = size_in_mb
  end

  def add_cd(writer=false)
    @computer.drives << Drive.new(:cd, 760, writer)
  end

  def add_dvd(writer=false)
    @computer.drives << Drive.new(:dvd, 4000, writer)
  end

  def add_hard_disk(size_in_mb)
    @computer.drives << Drive.new(:hard_disk, size_in_mb, true)
  end
end

builder = ComputerBuilder.new
builder.turbo
builder.add_cd(true)
builder.add_dvd
builder.add_hard_disk(100000)
```
