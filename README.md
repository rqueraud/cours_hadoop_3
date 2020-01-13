# Cours Hadoop 4

Dans ce TP, nous allons lancer une instance depuis une AMI avec une installation existante de Spark, puis faire des exercices de PySpark pour aborder la notion de RDD.

## Partie 1 - Lancement de l'instance depuis l'AMI existante

Selectionnez l'AMI *rqueraud_master_pour_tp* et lancez une nouvelle instance *m5a.large*.

Connectez-vous en SSH sur l'instance (donc soit avec Bitvise, soit en ligne de commande), puis faites les étapes suivantes:
  - Créez un dossier `cours` (son chemin complet est donc `~/cours/` ou `/home/ubuntu/cours`).
  - Créez un fichier `evaluation.txt` dans ce dossier.

Puis écrivez dans le fichier (avec la commande `nano`) et indiquez:
  - Ce que vous avez apprécié dans ce cours.
  - Ce que vous n'avez pas apprécié.
  - Comment cela pourrait être amélioré l'an prochain.

Pensez à enregistrer le fichier, puis pensez à me transmettre la clé-privée de votre instance pour que je puisse aller lire le fichier :):
  - Soit par clé USB.
  - Soit par mail: `r.queraud@catie.fr`.

## Partie 2 - Démarrage du service Zeppelin

Déplacez-vous dans le dossier de *zeppelin* et lancez le service avec la commande:

```bash
bin/zeppelin-daemon.sh start
```

Zeppelin est accessible sur le port *8082*, mais il faut pour cela que vous ayez indiqué le tunnel avec l'option SSH `-L 8082:localhost:8082`. Pensez-donc à vous deconnecter du SSH (raccourci *Ctrl+D* sur Windows) et à vous reconnecter en SSH avec cette option.  
Note: On se deconnecte uniquement du SSH, on n'éteint pas l'instance !

## Partie 3 - Exercices en PySpark

Si vous avez bien fait l'étape précédente, Zeppelin est disponible sur http://localhost:8082. Avec l'entraînement python de ce matin, vous êtes capables de faire les exercices qui sont globalement les mêmes que la semaine dernière.  

Bon courage, et n'hésitez pas à poser des questions si vous ne comprenez pas !
