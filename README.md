Mega TP : Administration de systèmes répartis

Déploiement Automatisé d'une Infrastructure Haute Disponibilité & Sécurisée
Présentation du Projet
Ce projet consiste à répondre aux besoins de refonte de l'infrastructure d'une PME en passant d'un déploiement manuel à une approche Infrastructure as Code (IaC). L'objectif est de livrer un environnement capable de tolérer des pannes, sécurisé par défaut et surveillé en temps réel

Architecture Technique (Le Lab)

L'infrastructure est composée de 4 machines virtuelles déployées automatiquement via un Vagrantfile unique. Le pilotage de la configuration est centralisé sur un nœud de contrôle Admin sous Ubuntu.
Schéma d'architecture réseau : Le diagramme suivant détaille la connectivité entre le nœud Admin, le cluster RedHat, le contrôleur de domaine Windows et les flux de supervision Zabbix.

<img width="806" height="902" alt="schema" src="https://github.com/user-attachments/assets/54ab928b-6512-4678-91d1-f7214a9e4c0b" />


MISSION 1 : Haute Disponibilité (HA)

L'objectif est de configurer un cluster Pacemaker et Corosync sur les nœuds Node01 et Node02. Nous avons mis en place un service Web (Nginx) et un partage de fichiers (Samba) en mode Active/Passive. Une IP Flottante (VIP) a été définie pour assurer la continuité de service en cas de basculement.
Preuve du statut du Cluster : Cette capture montre que le groupe de ressources (HA-Group) est démarré et que l'IP virtuelle, Nginx et Samba sont tous actifs sur le même nœud.

<img width="948" height="446" alt="sec" src="https://github.com/user-attachments/assets/3d01c65a-b40f-467e-9483-b3ada887f451" />
<img width="650" height="494" alt="screen1" src="https://github.com/user-attachments/assets/bd8d65c6-e564-4126-8ace-6bc747f6ba49" />

MISSION 2 : Sécurisation Linux (Hardening)

Pour protéger les nœuds RedHat, nous avons appliqué un durcissement du système via Ansible. Cela inclut la fermeture des ports inutiles dans le pare-feu, la désactivation de l'accès SSH en root et l'automatisation des mises à jour de sécurité.
Vérification du Pare-feu (Firewalld) : Ici, nous vérifions que seuls les services autorisés (HTTP, Samba, High-Availability, Zabbix Agent) sont actifs, réduisant ainsi la surface d'attaque.

<img width="948" height="446" alt="sec" src="https://github.com/user-attachments/assets/abeccf60-59ce-466a-9c86-70ae79be08d4" />
<img width="553" height="228" alt="regles appliquer" src="https://github.com/user-attachments/assets/c44a438b-132a-45a0-916a-7f70ee4499c9" />

MISSION 3 : Windows Server & Active Directory

La machine WinSrv a été promue en Contrôleur de Domaine (lab.local) de manière totalement automatisée. En parallèle, une couche de sécurité "Hardening" a été appliquée : désactivation du compte invité, fermeture de SMBv1 et application d'une politique de mot de passe stricte (12 caractères min. et complexité).
Succès du Playbook de Sécurité Windows : Ce screenshot confirme l'exécution sans erreur de toutes les tâches de sécurité, notamment la désactivation de SMBv1 et LLMNR.
Vérification du Domaine Active Directory : Cette capture d'écran valide que le serveur Windows est bien le maître du domaine lab.local et que la forêt est opérationnelle.

<img width="857" height="514" alt="execution playbook" src="https://github.com/user-attachments/assets/5c7ef027-ccad-4b25-ba46-c5ffd946cb80" />

MISSION 4 : Supervision avec Zabbix

Pour la surveillance, nous avons installé Zabbix Server sur la machine Admin. Des agents Zabbix sont déployés sur les nœuds RedHat pour monitorer l'utilisation des ressources (CPU/RAM) et la disponibilité du service Nginx.
Dashboard de Supervision Zabbix : Le tableau de bord suivant affiche l'état de santé de l'infrastructure et déclenchera une alerte visuelle en cas d'indisponibilité d'un nœud ou d'un service.

<img width="957" height="556" alt="zabbix" src="https://github.com/user-attachments/assets/48c7e2c2-8a20-4c6c-a1f7-a3970ca1f3ad" />

Documentation d'installation

Lancement : Exécuter vagrant up pour créer les 4 VMs.
Configuration : Lancer le déploiement global avec ansible-playbook -i hosts site.yml.
Vérification : Tester l'accès au service Web via la VIP sur 192.168.56.100.

