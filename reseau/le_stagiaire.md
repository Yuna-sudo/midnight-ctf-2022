Dans ce challenge on nous fournit un fichier de bytecode python

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5963f543-a3d4-4ffd-a454-8516bf4abe18/Untitled.png)

Je décompile le binaire afin d’obtenir un fichier python lisible

```python
# Source Generated with Decompyle++
# File: le_stagiaire.pyc (Python 3.10)

def useless_encrypt(string):
    bad_string = ''
    looser()
    exit(0)

def encrypt_string(string):
    tmp_string = ''
    looser()

def encrypt_string1(string):
    tmp_string = ''
    it = 1
    tmp_string += chr(ord(letter) - 1)
    it = 1
    continue
    return tmp_string

def tryme(string):
    print(string)
    if string == 'NBUE|sStd^ShhnMN~':
        print('GG ! Use this password as flag')
        return None
    None()

def encrypt_string2(string):
    tmp_string = ''
    if tmp_string == 'MCTF{fake_flag}':
        looser()
        return None
    None()

def looser():
    print('Bad password, looser !')

def func1(string):
    print(useless_encrypt(string))

def func2(string):
    print(encrypt_string(string))

def func3(string):
    tryme(encrypt_string1(string))

def func4(string):
    print(encrypt_string2(string))

def main():
    user_input = input('Please enter the password : ')
    funcmap = {
        1: func1,
        2: func2,
        3: func3,
        4: func4 }
    if len(user_input) == 12:
        len(user_input)
        funcmap[1](user_input)
        return None
    if len(user_input) == 15:
        len(user_input)
        funcmap[2](user_input)
        return None
    if len(user_input) == 17:
        funcmap[3](user_input)
        return None
    if None == None:
        funcmap[4](user_input)
        return None
    looser()

main()
```

On peut y voir une string encodé via un algorithme lorsqu’on utilise la fonction 3 on peut voir qu’il passe notre string à la fonction et qu’il appelle tryme afin de tester la validité de notre flag

Code de l’algorithme:

```python
def encrypt_string1(string):
    tmp_string = ''
    it = 1
    tmp_string += chr(ord(letter) - 1)
    it = 1
    continue
    return tmp_string
```

bon ce n’est pas bien dur si on réfléchis trois seconde on sait qu’il y a une boucle qui itère sur chacun des caractères de la chaine et soustrait -1 à sa valeur en revanche si l’on test cela on remarque que cette action ne fonctionne que 1/ 2 je me suis donc dis que j’allais une fois sur 2 ajouter 2 à ma valeur. A la fin mon exploit ressemble à ceci

```ruby
def decode(string)
	string = string.split("")
	flag = []
	string.map.with_index do |value, i|
		if i % 2 == 0
			flag << (value.ord - 1).chr
		else
			flag << (value.ord + 1).chr
		end
	end
	return flag.join
end

print(decode('NBUE|sStd^ShhnMN~'))
```

`MCTF{tRuc_RigoLO}`
