## Creative :

TryHackMe Easy

Énoncé :

> Exploitez une application Web vulnérable et certaines erreurs de configuration pour obtenir des privilèges root.

J'ai commencer par un scan nmap avec la commande suivant `nmap -sV -sC 10.10.97.21` ou j'ai trouver le port 80 et 22 d'ouvert.

![alt text](image.png)

Je me suis rendu dans mon navigateur pour voir le site web mais impossible. J'ai donc ajouter l'ip de la machine dans mon fichier `/etc/hosts` en indiquant : 10.10.97.21 creative.thm
Une fois sur le site creative.thm j'ai tenter de l'injection de code HTML et XSS mais sans succes.

Je suis passer a la phase d'enumeration avec gobuster sur avec plusieurs wordlists mais aucun resultat mise a part un dossier "assets". J'ai donc poursuivi en utilisant ffuf avec la commande : `ffuf -u http://creative.thm -w /usr/share/seclists/Discovery/Web-Content/raft-medium-words.txt -H "Host: FUZZ.creative.thm" -fw 6`

![alt text](image-1.png)

J'ai immediatement ajouter "beta.creative.thm" dans mon fichier `/etc/hosts`et j'ai pu acceder a cette page.

![alt text](image-2.png)

J'ai tenter de rentrer l'adresse 127.0.0.1:80 ce qui ma renvoyer le resultat suivant

![alt text](image-3.png)

J'ai egalement tenter sur le port 443

![alt text](image-4.png)

Apres ca, j'ai lister tous les ports avec Intruder de Burpsuite

![alt text](image-5.png)
![alt text](image-6.png)
![alt text](image-7.png)

Apres avoir trouver un resultat sur le port 1337, j'ai tenter d'acceder au repertoir /home ou j'ai pu trouver un utilisateur.

![alt text](image-8.png)

J'ai tenter une connexion ssh grace a l'id_rsa
![alt text](image-9.png)
![alt text](image-10.png)

Pour extraire l'empreinte du mot de passe j'ai utiliser la commande `ssh2john id_rsa > hash.txt` puis `john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt` afin du brute forcce le hash du mot de passe

![alt text](image-11.png)
![alt text](image-12.png)

Le premier flag user
![alt text](image-13.png)

J'ai pu trouver un mot de passe dans le fichier `.bash_history

![alt text](image-14.png)

Pour l'escalation de privilege je me suis servi de cette source : https://www.hackingarticles.in/linux-privilege-escalation-using-ld_preload/

![alt text](image-15.png)
![alt text](image-16.png)
