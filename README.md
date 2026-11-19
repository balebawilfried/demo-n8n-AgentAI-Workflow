# AGENT IA & WORFLOW D'AUTOMATISATION

**Objectif :** Ce projet a pour but de mettre sur pied un serveur IA. Ce serveur nous permettra de créer nos workflows et nos agents IA et simuler des certains certaines choses. 


### I- PERIMETRE 
Ce projet ce structure en deux une partie de collecte avec prometheus et une autre avec Grafana pour une collecte de logs et une visuation via des dashbord des données collectées pour l'ensemble de l'ensemble collecté. Pour ce lab nous sommes limités à un seul un serveur qui jouera à la fois le rôle de serveur de monitoring et de serveur d'application. Bien que dans la pratique (production), nous pouvons avoir des serveurs d'applications des serveurs de monitoring (SOC ou NOC). Il ne s'agit pas de monitroing __d'équipements réseau__ (switch, routeur, parefeu, etc). 

<p align="center">
  <img src="./images/n8n.png" width="45%">
</p>


### II- PREREQUIS
Bien que ce readme a pour but de donner des indications sur comment le reproduire, ainsi que les commentaires dans les fichiers yaml (.yml) qui vise à faciliter la compréhension. Il est important que vous ayez des notions en **containeurisation, orchestration de container, langage yml, prompt engineering, etc**. Il faut aussi avoir installé et configuré sur une **VM/VPS/VDS** Linux le necessaire suivant :
- Docker et Docker compose ;
- tree ;



### III- MISE EN PLACE


Pour mettre en marche nos outils :
1) Modifier l'adresse IP définit dans nos fichiers de configuration et remplacer par l'IP de votre server.

2) Démarrage de n8n, Ollama, Postgresql 
```powershell
# Passer en mode superutilisateur
- sudo -s

# Installation et démarrage de n8n et du nécessaire 
- docker compose up -d

# Vérifier que les conteneurs (prometheus, node exporter, blackbox exporter, etc) ont bien démarré
- docker container ls 

# Pour recreer nos conteneurs si vous vous êtes trompés dans l'ordre d'exécution
docker compose up -d --force-recreate nom_containeur

# Modification des droits du fichier .env
chmod 600 ./.env

# Lister les modèles disponibles
docker exec -it ollama ollama list
```
3) Ajout de modèle de Ollama à n8n
Nous allons nous rendre dans la configuration de Ollama Chat Model et au niveau de url nous allons renseigner **https:ollama:11434**. Au lieu d'utiliser l'ip du containeur, nous allons plus tôt utiliser son nom vu qu'ils sont dans le même réseau. 

4) Ajout et suppression de modèles de LLM à notre Ollama
```powershell
# Télécharger les modèles
docker exec -it ollama ollama pull llama3.2
docker exec -it ollama ollama pull qwen2.5:3b
docker exec -it ollama ollama pull mistral

# Télécharger un autre modèle plus tard
docker exec -it ollama ollama pull deepseek-r1:1.5b

# Supprimer un modèle
docker exec -it ollama ollama rm mistral
```

5) Recréation du conteneur après les modifications 

```powershell
# Recréer le conteneur Ollama
docker compose up -d --force-recreate ollama
```

6) Accéder à l'interface graphique de vos outils :
    - Pour n8n : http://votre_ipv4:5678
    - Pour ollama : http://votre_ipv4:11434
  

**NB :** Il faudra exécuter les commandes ci-dessus dans le terminal en étant à la racine du projet. D'autres part, il important de modifier les informations de configuration du serveur SMTP présentes ici (**./alertmanager/*.yml**) avec les vôtres sinon vous n'allez pas reçevoir les notification de alertmanager.

