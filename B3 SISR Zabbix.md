- - -
<div style="
    background-color: #4fc3f7;
    border-radius: 8px;
    padding: 20px;
    border: 2px solid #0288d1;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    margin: 20px 0;
">
<h1 style="
    color: white;
    text-align: center;
    margin: 0;
    padding: 10px;
    text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.3);
">Supervision Zabbix et PRTG</h1>
</div>
<br>

> **tip – HEGO Maxence**
>![Sio-bleu-ethernet-fond-removebg-preview(1)](images/Sio-bleu-ethernet-fond-removebg-preview(1).png|%20center%20|175)
>
>![Stluc-removebg-preview(1)](images/Stluc-removebg-preview(1).png|center|200)

---

> **note – Objectif :**
>Afin de renforcer la sécurité de notre **SI**, on souhaite mettre en place un **outil de supervision.** Lors de ce TP on va ainsi installer et tester deux outils de supervision : **Zabbix et PRTG** dans le but de les comparer et voir lequel est le plus adapter à nos besoins.

<br>
> **tip – Schéma :**
>Voici un schéma de notre architecture réseau comprenant les services que l'on veut monitorer :

<br>

---
<div style="
    background-color: #4fc3f7;
    border-radius: 4px;
    padding: 10px;
    border: 2px solid #0288d1;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    margin: 30px 0;
">
<h1 style="
    color: white;
    text-align: center;
    margin: 0;
    padding: 20px;
    text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.3);
">Partie 1 – Installation et configuration Zabbix</h1>
</div>
---

> **tip – Note :**
>On commence par tester Zabbix, voyons comment installer et configurer ce service dans l'objectif de monitorer notre SI.

<br>

---

<div style="
    background-color: #80deea;
    border-radius: 4px;
    padding: 10px;
    border: 2px solid #0288d1;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    margin: 30px 0;
">
<h1 style="
    color: white;
    text-align: center;
    margin: 0;
    padding: 20px;
    text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.3);
">Partie A – Installation Zabbix</h1>
</div>
---
<br>
#### a. Installation console bash :

> **tip – Note :**
>On installe le serveur Zabbix sur une debian 12, on suit ce tutoriel disponible sur le site de zabbix : https://www.zabbix.com/download?os_distribution=debian

> **note – Installation BDD :**
>Mise à jour du système :
>```bash
>apt update && apt upgrade -y
>```
>Installation de la base de données, on choisi mariadb :
>```bash
>apt install mariadb-server mariadb-client -y
>```
>Terminer installation de Mariadb :
>```bash
>mysql_secure_installation
>```

> **note – Installation des dépendances :**
>```bash
>apt install apache2 php php-mysql php-mysqli php-ldap php-bcmath php-mbstring php-gd php-xml php-curl -y
>```

> **note – Ajout du repository Zabbix :**
>```bash
>wget https://repo.zabbix.com/zabbix/6.4/debian/pool/main/z/zabbix-release/zabbix-release_6.4-1+debian12_all.deb
>```
>```bash
>dpkg -i zabbix-release_6.4-1+debian12_all.deb
>```
>```bash
>apt update
>```

> **note – Installation de Zabbix server, Frontend et Agent :**
>```bash
>apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent -y
>```

> **note – Configuration de la BDD :**
>```bash
>mysql -u root -p
>```
>Dans mariadb :
>```sql
>CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
>CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'sio%2025';
>GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
>FLUSH PRIVILEGES;
>EXIT;
>```

> **note – Importation du schéma de la BDD :**
>```bash
>zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql -uzabbix -p zabbix
>```

> **note – Configuration de Zabbix Server :**
>```bash
>nano /etc/zabbix/zabbix_server.conf
>```
>Dans le fichier de configuration on change ces lignes :
>```ini
>DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=votre_mot_de_passe_securise
>```

> **note – Activation des services :**
>```bash
>systemctl restart apache2
>systemctl enable zabbix-server zabbix-agent
>systemctl start zabbix-server zabbix-agent
>```

<br>
#### b. Installation interface web :

> **note – Interface Web :**
>On vérifie que l'installation fonctionne en se connectant à l'interface web : http://192.168.20.109/zabbix (IP de notre serveur Zabbix)
>
>![on se connecte](images/on%20se%20connecte.png)
>
>On arrive sur cette page d’accueil :
>
>![page d'acceuil](images/page%20d'acceuil.png)
>
>On vérifie que tout les prérequis sont bien installés :
>
>![prérequis installé 1](images/prérequis%20installé%201.png)
>
>On configure la connexion à la BDD:
>
>![bdd avec mdp](images/bdd%20avec%20mdp.png)
>
>On nomme maintenant notre serveur Zabbix :
>
>![nom serveur](images/nom%20serveur.png)
>
>On termine l'installation :
>
>![installer](images/installer.png)

> **note – Connexion Interface Web :**
>On peut maintenant se connecter à la pge de monitoring.
>
>On entre le login et le mot de passe : **Admin** et **zabbix**
>
>![admin zabbix](images/admin%20zabbix.png)
>
>On arrive sur cette page d'acceuil :
>
>![page d'acceuil2](images/page%20d'acceuil2.png)

<br>

---

<div style="
    background-color: #80deea;
    border-radius: 4px;
    padding: 10px;
    border: 2px solid #0288d1;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    margin: 30px 0;
">
<h1 style="
    color: white;
    text-align: center;
    margin: 0;
    padding: 20px;
    text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.3);
">Partie B – Installation des agents</h1>
</div>
---
<br>
> **note – Objectif :**
>On veut maintenant installer les agents sur les différents machines que l'on va monitorer afin de recevoir la data.
>On veut installer un agent sur une VM Linux, une VM windows 11 et un serveur Linux. Pour le serveur Linux on va monitorer un serveur LAMP.
<br>
#### a. Installation agent sur VM Linux :

> **tip – Note :**
>On commence par installer l'agent sur la VM Linux. La VM est une debian 12 sans interface graphique.

> **note – Installation Agent :**
>On installe l'agent Zabbix :
>```bash
>apt update
>apt install zabbix-agent
>```
>![install agent](images/install%20agent.png)

> **note – Fichier de configuration :**
>On modifie ces lignes dans le fichier de configuration :
>```bash
>nano /etc/zabbix/zabbix_agentd.conf
>```
>```ini
>Server=192.168.1.109
ServerActive=192.168.1.109
Hostname=Linux-client
>```
>
>![nano](images/nano.png)
>
>![adresse du server](images/adresse%20du%20server.png)
>
>![linux client ezabbix](images/linux%20client%20ezabbix.png)
>
>On redemare et on active l'agent :
>```bash
>systemctl restart zabbix-agent
>systemctl enable zabbix-agent
>```
>
>![enable](images/enable.png)

> **note – Ajout agent depuis interface web :**
>Maintenant que l'agent est installé, on veut créer l'hôte sur l'interface web.
>
>Depuis l'interface Web, on va dans Surveillance > Hôtes :
>
>![hote](images/hote.png)
>
>On arrive sur cette page, on sélectionne créer un hôte en haut à droite :
>
>![créer un hote](images/créer%20un%20hote.png)
>![zoom](images/zoom.png)
>
>On défini le nom du nouvel hôte :
>
>![nom 2](images/nom%202.png)
>
>Puis on défini le modèle de template :
>
>![modele](images/modele.png)
>
>On choisi ensuite dans quel groupe on met la machine :
>
>![le groupe](images/le%20groupe.png)
>
>On va maintenant ajouter l'interface :
>
>![ajout interface](images/ajout%20interface.png)
>
>![ip2](images/ip2.png)
>
>On termine l'installation en cliquant sur ajouter :
>
>![ip1 2](images/ip1%202.png)
>
>On a bien un nouvel agent qui apparaît :
>
>![hote ajouter et disponible](images/hote%20ajouter%20et%20disponible.png)
<br>
#### b. Installation agent serveur LAMP :

> **tip – Note :**
>On va maintenant installer un agent sur un serveur LAMP. La procédure d'installation est identique que lors de l'installation de l'agent sur la VM debian 12. La seule chose qui change est lors de la création de l'hôte sur l'interface web de zabbix.

> **note – Ajout agent depuis interface Web :**
>On suit la même procédure en nommant l'hôte puis, lors de la sélection des modèles de template, on sélectionne Appache by zabbix agent et mysql ny zabbix agent en plus de Linux by zabbix agent :
>
>![on rajoute appache en plus pour lamp](images/on%20rajoute%20appache%20en%20plus%20pour%20lamp.png)
>
>![et mysql](images/et%20mysql.png)
>
>Par la suite on ajoute l'interface comme précédemment. On peut maintenant ajouter ce nouvel hôte :
>
>![servuer lamp](images/servuer%20lamp.png)
>
>Le serveur LAMP apparaît bien dans les hôtes surveillés :
>
>![servuer lamp 1](images/servuer%20lamp%201.png)
<br>
#### c. Installation agent Windows 11 :

> **tip – Note :**
>On passe maintenant à l'installation de l'agent sur la VM Windows 11.

> **note – Installation agent :**
>On commence par aller sur le site de Zabbix depuis un naviguateur web pour télecharger l'agent. Le site : https://www.zabbix.com/download_agents?version=6.4&distribution=windows&architecture=64bit&package=msi
>
>![on telecharge l'agent sur windows](images/on%20telecharge%20l'agent%20sur%20windows.png)
>
>On lance ensuite le programme d'installation :
>
>![on lance le msi](images/on%20lance%20le%20msi.png)
>
>Lors de l'installation, on renseigne le nom d'hôte de la VM Windows11 puis on entre l'adresse IP du serveur Zabbix :
>
>![on resnisgne l'ip du serveur](images/on%20resnisgne%20l'ip%20du%20serveur.png)
>
>On peut vérifier que le service fonctionne en allant dans service :
>
>![services zabbix](images/services%20zabbix.png)

> **note – Ajout agent depuis interface web :**
>Même procéde que pour les agents Linux, on change juste le template utilisé :
>
>![windows zabbix](images/windows%20zabbix.png)
>
>![vm windows](images/vm%20windows.png)
>
>![mieux 1](images/mieux%201.png)

<br>
> **tip – Note :**
>On a maintenant pu remonter les données en utilisant les agents bla bla bla, on va maintenant faire en utilisant snmp.
<br>

---

<div style="
    background-color: #80deea;
    border-radius: 4px;
    padding: 10px;
    border: 2px solid #0288d1;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    margin: 30px 0;
">
<h1 style="
    color: white;
    text-align: center;
    margin: 0;
    padding: 20px;
    text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.3);
">Partie C – SNMP</h1>
</div>
---
<br>
> **note – Objectif :**
>On va maintenant utiliser snmp pour surveiller une machine Windows server 2019 et un switch.
<br>
#### a. Installation de SNMP sur Windows

> **note – Installation SNMP :**
>Dans le server manager, on clique sur gérer puis ajouter des rôles et fonctionnalités :
>
>![ws2019](images/ws2019.png)
>
>Dans fonctionnalité, on ajoute le service snmp :
>
>![snmp2](images/snmp2.png)

> **note – Configuration SNMP :**
>On va maintenant dans Outils > Service :
>
>![outils service](images/outils%20service.png)
>
>On double clique sur service SNMP :
>
>![service snmp](images/service%20snmp.png)
>
>Dans l'onglet sécurité, on accepte les paquets SNMP provenant de n'importe quelle hôte :
>
>![accepter les paquets](images/accepter%20les%20paquets.png)
>
>Puis on ajoute une communauté, je la nomme Zabbix :
>
>![ajout communauté](images/ajout%20communauté.png)
>
>On va ensuite dans interruption et pour la communauté Zabbix on ajoute l'adresse IP du serveur Zabbix :
>
>![in va interruption et ip](images/in%20va%20interruption%20et%20ip.png)

> **note – Configuration depuis interface Web :**
>On peut maintenant depuis l'interface Web ajouter un nouvel hôte pour le Windows Server.
>
>Comme pour la VM Windows, on défini le nom, on attribue un groupe et un modèle de template, on prends comme modèle Windows by SNMP.
>
>On ajoute ensuite une interface SNMP :
>
>![snmpinterfce1](images/snmpinterfce1.png)
>
>On entre alors l'IP du Windows Server :
>
>![configure interface avec ip ws](images/configure%20interface%20avec%20ip%20ws.png)
>
>On obtient bien les informations pour le Windows Server :
>
>![zoomzoom](images/zoomzoom.png)
<br>
#### b. Installation SNMP sur Switch :

> **note – Configuration Switch :**
>On veut maintenant pouvoir surveiller un switch. On commence par la configuration du switch.
>
>On commence par se connecter au switch en mode console en utilisant putty :
>
>![connexion console](images/connexion%20console.png)
>
>On met en place un accès telnet en définissant une ip pour le Vlan 1 :
>
>![vlan1](images/vlan1.png)
>
>![line vty](images/line%20vty.png)
>
>![enable pass](images/enable%20pass.png)
>
>On peut maintenant se connecter en telnet :
>
>![telent qui marche](images/telent%20qui%20marche.png)
>
>On peut maintenant configurer le snmp avec le nom de la communauté :
>
>![autre commu 1](images/autre%20commu%201.png)
>
>On défini ensuite l'adresse de l'hôte :
>
>![autre commu2](images/autre%20commu2.png)

> **note – Configuration depuis interface Web :**
>On passe maintenant à la configuration depuis l'interface web :
>
>![on ajpute hote](images/on%20ajpute%20hote.png)
>
>![on ajoute interface avec ip van 1](images/on%20ajoute%20interface%20avec%20ip%20van%201.png)
>
>![on prend le template cisco ios](images/on%20prend%20le%20template%20cisco%20ios.png)
>
>Dans macros on entre le nom de la commmunauté :
>
>![macromax](images/macromax.png)
>
>Le switch apparaît bien :
>
>![le switch apparaot bine](images/le%20switch%20apparaot%20bine.png)

> **note – Tout les équipements :**
>On observe que tout les équipements apparaissent bien, la supervision fonctionne.
>![supervisoin de tout les equipements](images/supervisoin%20de%20tout%20les%20equipements.png)
<br>

---

<div style="
    background-color: #80deea;
    border-radius: 4px;
    padding: 10px;
    border: 2px solid #0288d1;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    margin: 30px 0;
">
<h1 style="
    color: white;
    text-align: center;
    margin: 0;
    padding: 20px;
    text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.3);
">Partie D – Alertes et interfaces</h1>
</div>
---
<br>
> **tip – Note :**
>On veut maintenant faire en sorte de pouvoir recevoir des alertes lorsque un problème se présente. Voyons comment faire.

> **note – Configuration boite mail :**
>Avant toute chose, on doit configurer la boite mail de l'admin qui reçoit les mails. On utilise un compte gmail.
>
>On va dans paramètres > sécurité :
>
>![compte gmail sécurité](images/compte%20gmail%20sécurité.png)
>
>On active la validation à deux étapes :
>
>![valideation en deux étapes](images/valideation%20en%20deux%20étapes.png)
>
>![activer](images/activer.png)
>
>On se rend ensuite sur ce lien : https://myaccount.google.com/apppasswords et on génere un nouveau mot de passe pour une application que l'on garde de coté.
>
>![la plus le mdp](images/la%20plus%20le%20mdp.png)

> **note – Configuration SMTP :**
>Pour pouvoir recevoir des mails on a besoin d'installer un serveur SMTP. On l'installe sur la VM qui héberge le Zabbix.
>
>![apt install ssmptp](images/apt%20install%20ssmptp.png)
>
>On modifie ensuite le fichier de configuration :
>
>![fichier de config](images/fichier%20de%20config.png)
>
>On entre les informations suivantes : notre mail, le hubmail et le mot de passe que l'on a génère précédemment.
>
>![on mdofie le fichier de config comme ca](images/on%20mdofie%20le%20fichier%20de%20config%20comme%20ca.png)
>
>On réalise un test pour vérifier que le service fonctionne bien :
>
>![mail de test](images/mail%20de%20test.png)
>
>![test fonctionnem](images/test%20fonctionnem.png)
>
>C'est bon le service ssmtp fonctionne.

> **note – Alerte Mail :**
>On veut activer les alertes par mail.
>
>On va dans Alerte > Type de média:
>
>![Capture d’écran du 2025-10-05 11-21-15](images/Capture%20d’écran%20du%202025-10-05%2011-21-15.png)
>
>On décide d'activer les alertes par mail :
>
>![on active amil](images/on%20active%20amil.png)
>
>On configure maintenant le média mail en entrant le serveur SMTP et en entrant notre courriel :
>
>![on configure comme ca](images/on%20configure%20comme%20ca.png)
>
>On fait maintenant un test pour vérifier que le nouveau média est bien pris en compte :
>
>![on fiatun test qui fonctionne](images/on%20fiatun%20test%20qui%20fonctionne.png)
>
>![tester le sujet](images/tester%20le%20sujet.png)

> **note – Utilisateur alerté :**
>On décide maintenant quelle est l'utilisateur qui reçoit les mail, pour nous ce sera l'administrateur :
>
>On va dans Utilisateurs > utilisateurs :
>
>![user 1](images/user%201.png)
>
>On sélectionne ensuite l'admin :
>
>![admin](images/admin.png)
>
>On va dans média et on fait ajouter :
>
>![media](images/media.png)
>
>![ajouter](images/ajouter.png)
>
>On entre alors notre boite mail
>
>![le media admin](images/le%20media%20admin.png)
>
>On voit bien le nouveau média pris en compte pour l'administrateur :
>
>![nouveu mail](images/nouveu%20mail.png)

> **note – Création de l'alerte :**
>On va maintenant définir pour quelle problème notre admin reçoit une alerte mail.
>
>On va dans alertes > actions et on sélectionne action de déclencheur :
>
>![on va dans alerte actions](images/on%20va%20dans%20alerte%20actions.png)
>
>On crée une action
>
>![xréer une action](images/xréer%20une%20action.png)
>
>On nomme cette nouvelle action :
>
>![on nomme action et on jaoute](images/on%20nomme%20action%20et%20on%20jaoute.png)
>
>On sélectionne ensuite l'élement qui envoi le mail :
>
>![on selectionne la machine](images/on%20selectionne%20la%20machine.png)
>
>On choisi ensuite le problème pour lequel on veut être avertit :
>
>![on choisi le bon truc](images/on%20choisi%20le%20bon%20truc.png)
>
>On va ensuite dans opétations et on en ajoute une :
>
>![on va ensuite dans opération](images/on%20va%20ensuite%20dans%20opération.png)
>
>Enfin on sélectionne l'utilisateur ou les utilisateurs qui reçoivent l'alerte, ici l'admin reçoit une alerte mail.
>
>![on entre l'user et le mail](images/on%20entre%20l'user%20et%20le%20mail.png)

> **note – Test :**
>On vérifie que l'alerte fonctionne, on éteint la VM et on vérifie que l'on réçoit bien l'alerte par mail.
>
>![gmail2](images/gmail2.png)
>
>On reçoit bien le mail après que la machine se soit éteint.
<br>

> **tip – Interface personnalisé :**
>Zabbix nous permet également de personnalisé notre interface pour plus de visibilité sur les équipements ou pour que l'on puisse se concentrer sur les éléments qui nous plaisent le plus.
>
>Un exemple d'interfaces personnalisé :
>
>![on dispose de nombreuses options de personnalisaitons](images/on%20dispose%20de%20nombreuses%20options%20de%20personnalisaitons.png)
<br>

---
<div style="
    background-color: #4fc3f7;
    border-radius: 4px;
    padding: 10px;
    border: 2px solid #0288d1;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    margin: 30px 0;
">
<h1 style="
    color: white;
    text-align: center;
    margin: 0;
    padding: 20px;
    text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.3);
">Partie 2 – Comparaison avec PRTG</h1>
</div>
---
<br>
> **tip – Note :**
>PRTG est un autre logiciel de supervison de réseau. A la différence de Zabbix, il n'est pas open-source et il est payant. Voyons rapidement les avantages et les inconvénients des deux solutions Zabbix et PRTG pour déterminer laquelle est la plus adapté à nos besoins.
>
>Ci-dessous, un exemple d'une Interface PRTG :
>
>![Pasted image 20251011143637](images/Pasted%20image%2020251011143637.png)
<br>

> **note – Comparaison des deux solutions :**
>**Couts :**
>
>Zabbix est gratuit et open-source ce qui est un avantage sur son concurrent qui devient payant dès 100 capteurs, sachant que l'on peut déployer jusqu'à cinqs capteurs pour la surveillance d'une machine on arrive vite à la limite.
>
>On peut en revanche avoir recours à un support qui est lui payant pour Zabbix, bon à savoir en cas de problème.
>
>**Installation :**
>
>PPRTG est nettement plus simple à deployer que Zabbix qui nécessite des compétences en OS Linux, PRTG peu donc d'avérer une alternative pour un petit SI n'utillisant que Windows.
>
>**Cas d'usage recommandé :**
>
>**- PRTG :**
>
>Besoin d'une solution rapide à déployer et pour une équipe en info avec peu de connaissances en OS Linux, avec une infra windows dominante et si 100 capteurs suffisent ou que l'entreprise est prête à mettre le budget.
>
>**- Zabbix :**
>
>Zabbix est gratuit et ne nécessite que quelques compétences en Linux, adapté pour ceux qui préferent l'open source. De plus Zabbix est hautement customisable et peut donc être déployé pour des SI nécessitant du sur mesure. En plus comme c'est gratuit on peut l'utiliser pour de la supervision de grande échelle.
<br>

> **tip – Verdict :**
>La solution Zabbix est la plus adpaté à nos besoins, nottament pour L'AP en raison des grandes possibiltées de customisation, de plus j'ai une préférence à titre individuel pour les solutions open-source quand c'est possible.
