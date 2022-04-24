Nous avions un binaire ELF rien de plus simple je le desassemble

```c
undefined8 main(int param_1)

{
  size_t sVar1;
  long in_FS_OFFSET;
  int local_100;
  int local_fc;
  int local_f8 [12];
  undefined4 local_c8 [14];
  byte abStack144 [12];
  undefined8 local_84;
  undefined4 local_7c;
  char local_78 [104];
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  if (param_1 == 1) {
    local_f8[0] = 0x10;
    local_f8[1] = 0x2b;
    local_f8[2] = 0xffffffff;
    local_f8[3] = 0xffffffec;
    local_f8[4] = 0x17;
    local_f8[5] = 0x13;
    local_f8[6] = 0x3f;
    local_f8[7] = 0xfffffffd;
    local_f8[8] = 0x20;
    local_f8[9] = 0xfffffffb;
    local_f8[10] = 0x30;
    local_f8[11] = 0xffffffda;
    local_c8[0] = 0x1b;
    local_c8[1] = 0x30;
    local_c8[2] = 6;
    local_c8[3] = 0x3a;
    local_c8[4] = 0x10;
    local_c8[5] = 9;
    local_c8[6] = 0x13;
    local_c8[7] = 0x14;
    local_c8[8] = 0x3a;
    local_c8[9] = 0x57;
    local_c8[10] = 0x48;
    local_c8[11] = 0x17;
    local_84 = 0x67616c665f746f6e;
    local_7c = 0x282d3a5f;
    puts("Give me your password : ");
    __isoc99_scanf(&DAT_00102045,local_78);
    sVar1 = strlen(local_78);
    if (sVar1 == 0xc) {
      for (local_100 = 0; local_100 < 0xc; local_100 = local_100 + 1) {
        abStack144[local_100] =
             *(byte *)((long)&local_84 + (long)local_100) ^ (byte)local_c8[local_100];
      }
      for (local_fc = 0; local_fc < 0xc; local_fc = local_fc + 1) {
        if ((int)(char)abStack144[local_fc] - (int)local_78[local_fc] != local_f8[local_fc]) {
          puts("Wrong password.");
          goto LAB_00101431;
        }
      }
      printf("Good job ! Use this password as flag : %s\n",local_78);
    }
    else {
      puts("Wrong password.");
    }
  }
  else {
    puts("Please launch the app without args.");
  }
LAB_00101431:
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```

Bon comme on peut le voir, le script demande un mot de passe via scanf et par la suite fait une suite d’opération arithmétique. Je vais décomposer cela.

Tout d’abord il va controler via strlen la taille de notre flag, on sait que la taille du flag sera de 11 caractères.

Bon rien de mieux que de vous expliquer l’algo de base via une formule mathématique sur un element et la formule d’inversement d’un element.

```c
for (local_100 = 0; local_100 < 0xc; local_100 = local_100 + 1) {
        abStack144[local_100] =
             *(byte *)((long)&local_84 + (long)local_100) ^ (byte)local_c8[local_100];
}
```

### $e_i = x_i \oplus y_i$

```ruby
for (local_fc = 0; local_fc < 0xc; local_fc = local_fc + 1) {
        if ((int)(char)abStack144[local_fc] - (int)local_78[local_fc] != local_f8[local_fc]) {
          puts("Wrong password.");
          goto LAB_00101431;
        }
      }
```

### $e_i - f_i = d_i$

En effet comme on peut le voir l’élement chaque caractère de notre valeur de local_f8 doit être le resultat de la difference entre chaque caractère xoré avec le caractère du flag.

C’est cet algorithme qu’il va falloir reverse.

Pour ce faire nous allons faire cette opération sur le premier caractère.

Le première caractère est donc 0x6e de notre chaine local_84

D’abord nous allons le xorer avec sa valeur correspondant au meme index dans local_c8.

### $e_1 = 110 \oplus 27$

### $e_1 = 117$

Nous avons donc la bonne valeur xoré du premier caractère.

Il faut donc maintenant inverser l’opération de la soustraction afin d’obtenir notre caractère.

Voici notre equation:

$117 - f_1 = 16$

16 car la première valeur de notre local_f8 est 0x10 soit 16.

Et voici donc la résolution de cette simple equation:

### $e_i - d_i = f_i$

Si l’on applique ça à notre première opération nous obtenons

$117 - 16 = 101$

Si l’on met 101 en caractère ascii nous obtenons la lettre “e”. Il n’y a plus qu’a faire ça sur les autres char.

Attention il y a certaines subtilités qu’il faut penser dans certains cas.

Voici un exemple:

Si l’on prend l’exemple du 3ème char qui est 0x74, nous allons effectuer notre opération initiale.

### $e_2 = 116 \oplus 6$

### $e_2 = 114$

Ensuite la suite de l’opération dit que nous allons soustraire la valeur de local_c8 la valeur de local_f8

Cependant l’on peut voir que notre valeur est 0xffffffff ce qui rend la chose plus chiante que prévu. Car en réalite il faut introduire ici la valeur decimal du complément a2 de ce nombre soit -1

Voici mon exploit final (je precise qu’il n’est pas dutout propre par flemme):

```python
import numpy

local_f8 = [0x10, 0x2b, 0xffffffff, 0xffffffec, 0x17, 0x13, 0x3f, 0xfffffffd, 0x20, 0xfffffffb, 0x30, 0xffffffda]
local_c8 = [0x1b, 0x30, 0x6, 0x3a, 0x10, 0x9, 0x13, 0x14, 0x3a, 0x57, 0x48, 0x17]

local_84 = [0x67, 0x61, 0x6c, 0x66, 0x5f, 0x74, 0x6f, 0x6e][::-1]
local_7c = [0x28, 0x2d, 0x3a, 0x5f][::-1]

tmp_flag = []

for i in range(0x8):
    tmp_flag.append(local_84[i]^local_c8[i])

for i in range(0x8):
    tmp_flag[i] = chr(tmp_flag[i]-numpy.int32(local_f8[i]))

tmp_flag.append('E')
tmp_flag.append('r')
tmp_flag.append('5')
tmp_flag.append('e')

print(''.join(tmp_flag))
```

ps: j’ai use numpy pour la conversion en decimal complément a2.

`e4sy_R3vEr5e`
