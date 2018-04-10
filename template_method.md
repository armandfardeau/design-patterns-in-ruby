
# Template Method Pattern

## Problème
Nous avons un peu de code complexe, mais quelque part au milieu, il y a un peu de code qui doit varier.

## Solution
L'idée générale de la méthode des modèles est de construire une classe de base abstraite avec une méthode squelettique, qui conduit le bit du traitement qui doit varier en faisant des appels à des méthodes abstraites, qui sont ensuite fournies par les sous-classes concrètes. Ainsi, la classe de base abstraite contrôle le traitement de niveau supérieur et les sous-classes remplissent simplement les détails.

## Exemple

Nous devons générer un rapport HTML, donc nous arrivons à quelque chose comme ceci :

```ruby
class Report
  def initialize
    @title = 'Monthly Report'
    @text = ['Things are going', 'really, really well.']
  end

  def output_report
    puts('<html>')
    puts(' <head>')
    puts("<title>#{@title}</title>")
    puts(' </head>')
    puts(' <body>')
    @text.each do |line|
      puts("<p>#{line}</p>")
    end
    puts(' </body>')
    puts('</html>')
  end
end
```

Plus tard, on se rend compte qu'il faut ajouter un nouveau format : le texte brut. Facile, nous pouvons passer le format comme paramètre et décider ce qu'il faut afficher en fonction de celui-ci :
```ruby
class Report
  def initialize
    @title = 'Monthly Report'
    @text = ['Things are going', 'really, really well.']
  end

  def output_report(format)
    if format == :plain
      puts("*** #{@title} ***")
    elsif format == :html
      puts('<html>')
      puts(' <head>')
      puts("<title>#{@title}</title>")
      puts(' </head>')
      puts(' <body>')
    else
      raise "Unknown format: #{format}"
    end
    @text.each do |line|
      if format == :plain
        puts(line)
      else
        puts("<p>#{line}</p>")
      end
    end
    if format == :html
      puts(' </body>')
      puts('</html>')
    end
  end
end
```

C'est un peu désordonné, le code qui gère les deux formats est emmêlé et, pire encore, il n'est pas extensible du tout (et si on veut ajouter un nouveau format ?). Refacturons le code en cherchant ce qui reste le même. Dans la plupart des rapports, le flux de base est le même, quel que soit le format : en-tête de sortie, titre de sortie, sortie de chaque ligne du rapport et sortie de tous les éléments de suivi requis par le format. Nous pourrions créer une classe de base abstraite qui exécute toutes ces étapes mais laisse les détails à une sous-classe :

```ruby
class Report
  def initialize
    @title = 'Monthly Report'
    @text = ['Things are going', 'really, really well.']
  end

  def output_report
    output_start
    output_head
    output_body_start
    output_body
    output_body_end
    output_end
  end

  def output_body
    @text.each do |line|
      output_line(line)
    end
  end

  def output_start
    raise 'Called abstract method: output_start'
  end

  def output_head
    raise 'Called abstract method: output_head'
  end

  def output_body_start
    raise 'Called abstract method: output_body_start'
  end

  def output_line(line)
    raise 'Called abstract method: output_line'
  end

  def output_body_end
    raise 'Called abstract method: output_body_end'
  end

  def output_end
    raise 'Called abstract method: output_end'
  end
end
```

Nous pouvons maintenant définir une sous-classe qui implémente les détails :

```ruby
class PlainTextReport < Report
  def output_start
  end

  def output_head
    puts("**** #{@title} ****")
  end

  def output_body_start
  end

  def output_line(line)
    puts(line)
  end

  def output_body_end
  end

  def output_end
  end
end
```

```ruby
report = PlainTextReport.new
report.output_report
```
