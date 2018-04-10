
# Command Pattern

## Problème
Nous voulons effectuer une tâche spécifique sans savoir comment se déroule l'ensemble du processus ou avoir des informations sur le destinataire de la demande.

## Solution
Le modèle de commande découple l'objet qui a besoin d'exécuter une tâche spécifique de celui qui sait comment le faire. Il encapsule toutes les informations nécessaires à l'exécution du travail dans son propre objet, y compris l'identité du ou des destinataires, les méthodes à invoquer et les paramètres. De cette façon, tout objet qui veut exécuter la tâche n'a besoin que de connaître l'interface de l'objet de commande.

## Exemple
Considérons l'implémentation d'un bouton d'un framework GUI, qui a une méthode appelée clic de bouton.
```ruby
class SlickButton

  # Lots of button drawing and management
  # code omitted...

  def on_button_push
    # Do something when the button is pushed
  end
end
```

Ainsi, nous pourrions étendre la classe de bouton surchargeant la méthode `on_button_push` pour effectuer certaines actions chaque fois qu'un utilisateur clique dessus. Par exemple, si le but du bouton est d'enregistrer un document, nous pourrions faire quelque chose comme ceci :

```ruby
class SaveButton < SlickButton
  def on_button_push
    # Save the current document...
  end
end
```

Cependant, une GUI complexe pourrait avoir des centaines de boutons, ce qui signifie que nous finirions par avoir plusieurs centaines de sous-classes de notre bouton. Il y a un moyen plus facile. Nous pouvons factoriser le code qui exécute l'action dans son propre objet, qui implémente une interface simple. Ensuite, nous pouvons refactoriser l'implémentation de notre bouton pour recevoir l'objet de commande comme paramètre et l'appeler quand on clique dessus.

```ruby
class SaveCommand
  def execute
    # Save the current document...
  end
end

class SlickButton
  attr_accessor :command

  def initialize(command)
    @command = command
  end

  def on_button_push
    @command.execute if @command
  end
end

save_button = SlickButton.new(SaveCommand.new)
```

Le modèle de commande est très utile si nous avons besoin d'implémenter la fonction **undo****. Tout ce que nous avons à faire est d'implémenter la méthode `unexecute` dans notre objet de commande. Par exemple, c'est ainsi que nous pourrions mettre en œuvre la tâche de création d'un fichier :

```ruby
class CreateFile < Command
  def initialize(path, contents)
    super "Create file: #{path}"
    @path = path
    @contents = contents
  end

  def execute
    f = File.open(@path, "w")
    f.write(@contents)
    f.close
  end

  def unexecute
    File.delete(@path)
  end
end
```

Une autre situation où le modèle de commande est vraiment pratique est dans les programmes d'installation. En le combinant avec le motif **composite**, nous pouvons stocker une liste des tâches à effectuer :

```ruby
class CompositeCommand < Command
  def initialize
    @commands = []
  end

  def add_command(cmd)
    @commands << cmd
  end

  def execute
    @commands.each {|cmd| cmd.execute}
  end
end

cmds = CompositeCommand.new
cmds.add_command(CreateFile.new('file1.txt', "hello world\n"))
cmds.add_command(CopyFile.new('file1.txt', 'file2.txt'))
cmds.add_command(DeleteFile.new('file1.txt'))
```
