
# Domain-Specific Language Pattern

## Problème
Nous voulons construire une syntaxe pratique pour résoudre les problèmes d'un domaine spécifique.

## Solution
Ruby est une syntaxe vraiment flexible et a une syntaxe humaine, parfois la lecture d'un morceau de code Ruby donne même l'impression de lire un livre. **Le modèle DSL (Domain-Specific Language (DSL)** en profite et suggère de construire un nouveau langage sur Ruby. Il commence par la construction de structures de données qui contiennent les informations sur les tâches à exécuter. Ensuite, il définit plusieurs méthodes de haut niveau qui supportent le DSL et que l'utilisateur final utilisera pour construire un programme avec le langage nouvellement créé. Enfin, le programme est évalué et interprété comme des appels à la méthode Ruby.

## Exemple
Nous voulons fournir un moyen simple de créer des sauvegardes périodiques de certains dossiers. Nous pouvons facilement le faire avec Ruby, mais nos utilisateurs finaux ne connaissent peut-être rien à la programmation, alors nous construirons un DSL. Tout d'abord, nous devons mettre en place des structures de données :

```ruby
class Backup
  include Singleton

  attr_accessor :backup_directory, :interval
  attr_reader :data_sources
  
  def initialize
    @data_sources = []
    @backup_directory = '/backup'
    @interval = 60
  end

  def backup_files
    this_backup_dir = Time.new.ctime.tr(' :','_')
    this_backup_path = File.join(backup_directory, this_backup_dir)
    @data_sources.each {|source| source.backup(this_backup_path)}
  end

  def run
    while true
      backup_files
      sleep(@interval*60)
    end
  end
end

class DataSource
  attr_reader :directory, :finder_expression

  def initialize(directory, finder_expression)
    @directory = directory
    @finder_expression = finder_expression
  end

  def backup(backup_directory)
    files=@finder_expression.evaluate(@directory)
    files.each do |file|
      backup_file( file, backup_directory)
    end
  end

  def backup_file( path, backup_directory)
    copy_path = File.join(backup_directory, path)
    FileUtils.mkdir_p( File.dirname(copy_path) )
    FileUtils.cp( path, copy_path)
  end
end
```

La classe `Backup` contient les informations sur les répertoires à sauvegarder et une méthode pour l'exécuter, tandis que la classe `DataSource` est un conteneur pour un chemin d'accès à un répertoire et a des capacités de recherche de fichiers. Il ne reste que les méthodes de haut niveau qui utiliseront les structures de données et permettront à l'utilisateur final de définir son programme :

```ruby
def backup(dir, find_expression=All.new)
  Backup.instance.data_sources << DataSource.new(dir, find_expression)
end

def to(backup_directory)
  Backup.instance.backup_directory = backup_directory
end

def interval(minutes)
  Backup.instance.interval = minutes
end

eval(File.read('backup.pr'))
Backup.instance.run
```

C'est un langage assez simple : avec la méthode de `backup` nous réglons les répertoires à sauvegarder, avec `to` le dossier de destination de la copie et avec `interval` la fréquence d'exécution de la copie. Ensuite, le programme créé par l'utilisateur final est lu et interprété comme du code Ruby avec `eval`. À quoi ressemble un programme créé avec notre DSL ? Plutôt simple :

```ruby
backup '/home/russ/documents'

backup '/home/russ/music', file_name('*.mp3') & file_name('*.wav')

backup '/home/russ/images', except(file_name('*.tmp'))

to '/external_drive/backups'

interval 60
```
