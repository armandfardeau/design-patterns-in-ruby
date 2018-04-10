
# Adapter Pattern

## Problème
Nous voulons qu'un objet parle à un autre mais leurs interfaces ne correspondent pas.

## Solution
Nous enveloppons simplement **l'adaptateur** avec notre nouvelle classe **d'adaptateur**. Cette classe implémente une interface que l'appelant comprend, bien que tout le travail soit effectué par l'objet adapté.

## Exemple
Pensons à une classe qui chiffre un fichier, qui prend deux fichiers ouverts, l'un pour la lecture et l'autre pour l'écriture :

```ruby
class Encrypter
  def initialize(key)
    @key = key
  end

  def encrypt(reader, writer)
    key_index = 0
    while not reader.eof?
      clear_char = reader.getc
      encrypted_char = clear_char ^ @key[key_index]
      writer.putc(encrypted_char)
      key_index = (key_index + 1) % @key.size
    end
  end
end
```

Que se passe-t-il si les données que nous voulons sécuriser se trouvent dans une chaîne de caractères plutôt que dans un fichier ? Nous avons besoin d'un objet qui ressemble à un fichier ouvert, c'est-à-dire qui supporte la même interface que l'objet Ruby `IO`. Nous pouvons créer un `StringIOAdapter` pour y parvenir :
```ruby
class StringIOAdapter
  def initialize(string)
    @string = string
    @position = 0
  end

  def getc
    if @position >= @string.length
      raise EOFError
    end
    ch = @string[@position]
    @position += 1
    return ch
  end

  def eof?
    return @position >= @string.length
  end
end
```

Maintenant nous pouvons utiliser une `String` comme s'il s'agissait d'un fichier ouvert (il n'implémente qu'une petite partie de l'interface `IO` essentiellement ce dont nous avons besoin).
```ruby
encrypter = Encrypter.new('XYZZY')
reader= StringIOAdapter.new('We attack at dawn')
writer=File.open('out.txt', 'w')
encrypter.encrypt(reader, writer)
```
