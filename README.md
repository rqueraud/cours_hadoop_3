# Cours Hadoop 3

Dans ce TP, nous allons configurer un environnement Spark (Spark se base sur Hadoop) afin de pouvoir utiliser un interpreteur PySpark.

## Partie 1 - Lancement d'une nouvelle instance et installation de JAVA

:warning:  
Dans tout le TP, les instances que vous lancerez devront avoir le *security-group* `launch-wizard-17` pour être accessibles. Pensez à le selectionner à la création de votre instance !  
:warning:

Lancez une nouvelle instance EC2 m5a.xlarge, connectez vous en SSH et faites les opérations habituelles : 

```bash
sudo apt-get update && sudo apt-get upgrade -y  # Pour mettre à jour la liste des paquets
```

Puis installez le paquet ubuntu `openjdk-8-jdk` avec la commande `apt-get`. Ce paquet installe une version de `java`, nécessaire au fonctionnement de l'environnement `Hadoop`.  
*Rappel*: Vous pouvez préceder toute commande de l'instruction `sudo` pour l'executer en mode administrateur.

Puis, ajoutez cette ligne à la fin du fichier `~/.bashrc`:

```bash
export JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64/"
```

Le fichier `~/.bashrc` est executé à chaque ouverture de session ubuntu. Pour l'executer, vous pouvez-donc vous deconnecter et reconnecter à votre session SSH, ou bien executer la commande `source ~/.bashrc`.

## Partie 2 - Installation et configuration de Spark

Téléchargez **Spark 2.4.4 for Hadoop 2.7** disponible sur cette page https://spark.apache.org/downloads.html (attention aux versions !).  
*Rappel*: La commande `wget` permet de télécharger des fichiers.

Vous pouvez ensuite décompresser l'archive grâce à la commande `tar` et les options `-xvzf`. Vous pouvez consulter son fonctionnement sur internet. Vous devez obtenir un dossier `spark-2.4.4-bin-hadoop2.7`, deplacez-vous dedans.

Nous devons spécifier la version de python qui sera utilisée par Spark. Comme pour JAVA, rajoutez la ligne `export PYSPARK_PYTHON=python3` à la fin du fichier `~/.bashrc`, puis pensez à vous deconnecter/reconnecter ou à sourcer le fichier.

```bash
wget http://mirrors.ircam.fr/pub/apache/spark/spark-2.4.4/spark-2.4.4-bin-hadoop2.7.tgz
tar -xvzf spark-2.4.4-bin-hadoop2.7.tgz
rm spark-2.4.4-bin-hadoop2.7.tgz
```

## Partie 3 - Lancement du master et du slave

L'instance que vous venez de parametrer sera l'instance **master**. Lancez une nouvelle instance avec la même configuration qui sera l'instance **slave**. Pour cela, vous pouvez soit refaire toutes les étapes précedentes, soit créer une image **AMI** du **master** qui sera utilisée pour la création du **slave**.  
Reportez-vous aux TPs précédents ou aidez-vous d'internet pour savoir comment créer une **AMI**.

Deconnectez-vous des deux instances, nous allons nous reconnecter en utilisant un tunnel SSH pour la redirection de ports. Les commandes suivantes sont à executer depuis le terminal de votre machine :

```bash
# Depuis un premier terminal, pour la connection au noeud master
ssh -i VOTRE_CLÉ.pem -o ServerAliveInterval=60 -L 8157:localhost:8080 -L 8160:localhost:4040 ubuntu@IP.PUBLIQUE.DU.MASTER

# Depuis un second terminal, pour la connection au noeud slave
ssh -i VOTRE_CLÉ.pem -o ServerAliveInterval=60 -L 8159:localhost:8081 ubuntu@IP.PUBLIQUE.DU.SLAVE
```

### Partie 3.1 - Service Spark sur le noeud master

Sur le noeud **master**, déplacez-vous dans le dossier de Spark et lancez la commande suivante pour démarrer le service Spark.

```bash
# Sur le noeud master
./sbin/start-master.sh
```

Vous pouvez consulter l'interface du **master** depuis votre navigateur à l'adresse http://localhost:8157. Cette adresse, configurée dans le tunnel SSH, redirige vers le port 8080 du noeud **master**.  
Récuperez l'adresse **spark** du master, qui est au format `spark://...:7077`, elle va nous servir pour connecter notre **slave** au cluster.

### Partie 3.2 - Service Spark sur le noeud slave

Sur le noeud **slave**, déplacez-vous dans le dossier de Spark et lancez la commande suivante pour démarrer le service Spark.

```bash
# Sur le noeud master
./sbin/start-slave.sh spark://...:7077  # Renseignez l'adresse spark de votre master, récupérée à la partie précedente
```

Vous pouvez consulter l'interface du **slave** depuis votre navigateur à l'adresse http://localhost:8159. Cette adresse, configurée dans le tunnel SSH, redirige vers le port 8081 du noeud **slave**.

# Partie 4 - Démarrage de Spark

Depuis le noeud master, vous pouvez lancer une *application Spark* depuis le dossier de Spark grâce à la commande `./bin/pyspark --master spark://...:7077` en renseignant l'adresse spark de votre **master**.

Vous pouvez consulter l'interface de *l'application* depuis votre navigateur à l'adresse http://localhost:8160. Cette adresse, configurée dans le tunnel SSH, redirige vers le port 4040 du noeud **master**.

Un invité de commande Spark vous propose d'executer du code Spark, vous pouvez par exemple copier-coller le contenu du fichier `estimate_pi.pyspark.py` et verifier depuis l'interface de l'application que votre code s'est bien executé.

# Partie 5 - Environnement de notebook Zeppelin

Sur le noeud **master**, téléchargez Zeppelin (http://apache.crihan.fr/dist/zeppelin/zeppelin-0.8.2/zeppelin-0.8.2-bin-all.tgz) et décompressez le.

Zeppelin expose normalement son interface sur le port 8080, mais ce port est déjà utilisé par Spark. Changez le port, mettez pas exemple le port `8082`, comme expliqué sur la documentation (https://zeppelin.apache.org/docs/latest/setup/operation/configuration.html#zeppelin-properties). Profitez-en également pour mettre le chemin vers votre dossier Spark dans la variable d'environnement SPARK_HOME en suivant la documentation (https://zeppelin.apache.org/docs/latest/interpreter/spark.html#1-export-spark_home, attention car le chemin du dossier dans la documentation n'est pas forcément le même que le votre !).

Pensez à vous reconnecter en SSH en rajoutant un nouveau tunnel, par exemple `8082:localhost:8082`, puis démarrez le service Zeppelin en suivant la documentation (http://zeppelin.apache.org/docs/latest/quickstart/install.html#starting-apache-zeppelin).

Connectez-vous à l'interface de Zeppelin depuis votre navigateur à l'adresse http://localhost:8082, puis configurez l'interpreteur Spark pour pointer vers l'adresse de votre **master** en suivant la documentation (https://zeppelin.apache.org/docs/latest/interpreter/spark.html#2-set-master-in-interpreter-menu).

# Partie 6 - Développer en PySpark

Sur l'instance **master**:
  - Créez un dossier `data` dans votre repertoire home `~/`.
  - Téléchargez et dezippez (commande `unzip`) les données dans ce repertoire. Les données sont disponibles à l'adresse https://rqueraud-inseec.s3-eu-west-1.amazonaws.com/nhl-game-data.zip.