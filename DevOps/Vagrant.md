# Documentation sur Vagrant

## Introduction

Vagrant est un outil de gestion de machines virtuelles qui permet de définir, configurer et gérer des environnements de développement reproductibles. Il repose sur des fichiers de configuration appelés `Vagrantfile`.

---

## Commandes de base

### Commandes essentielles

- **Initialisation :**

  ```bash
  vagrant init <nom_box>
  ```

  Crée un fichier `Vagrantfile` pour une box spécifique.
- **Lancement de la VM :**

  ```bash
  vagrant up
  ```

  Démarre et provisionne la machine virtuelle.
- **Connexion SSH :**

  ```bash
  vagrant ssh
  ```

  Permet d'accéder à la VM via SSH.
- **Arrêt de la VM :**

  ```bash
  vagrant halt
  ```

  Arrête proprement la machine virtuelle.
- **Suppression de la VM :**

  ```bash
  vagrant destroy
  ```

  Supprime complètement la machine virtuelle.

---

## Configuration du Vagrantfile

### Exemple de configuration basique

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  config.vm.hostname = "lab1-vm"
  config.vm.network "private_network", type: "dhcp"
end
```

- **`config.vm.box` :** Définit l'image de base utilisée pour la VM.
- **`config.vm.hostname` :** Donne un nom à la VM.
- **`config.vm.network` :** Configure un réseau privé avec adresse IP dynamique.

### Provisioning avec un script shell

```ruby
config.vm.provision "shell", inline: <<-SHELL
  apt-get update
  apt-get install -y nginx
SHELL
```

- Installe automatiquement Nginx lors du démarrage.

### Partage de fichiers

```ruby
config.vm.synced_folder "./data", "/vagrant_data"
```

- Synchronise un dossier local avec un répertoire dans la VM.

---

## Gestion des Vagrant Boxes

### Commandes principales

- **Lister les boxes locales :**

  ```bash
  vagrant box list
  ```

  Affiche les boxes disponibles localement.
- **Ajouter une nouvelle box :**

  ```bash
  vagrant box add <box_name>
  ```

  Télécharge une box depuis Vagrant Cloud.
- **Supprimer une box :**

  ```bash
  vagrant box remove <box_name>
  ```

  Supprime une box inutilisée.

---

## Déploiement sur Vagrant Cloud

### Étapes principales

1. **Créer une box personnalisée :**
   - Configurez et démarrez une VM comme d'habitude.
   - Packager la box :

     ```bash
     vagrant package --output custom_box.box
     ```

2. **Uploader sur Vagrant Cloud :**
   - Créez un compte sur [Vagrant Cloud](https://app.vagrantup.com/).
   - Publiez la box via l'interface ou la CLI.

---

## Bonnes pratiques

1. **Organisez votre projet :**
   - Suivez une arborescence claire :

     ```text
     lab-1/
     ├── README.md
     ├── Vagrantfile
     └── .gitignore
     ```

2. **Versionnez vos configurations :**
   Utilisez Git pour tracer les modifications du `Vagrantfile`.
3. **Automatisez :**
   Utilisez des outils comme Ansible ou des scripts pour gérer le provisioning.

---

## Ressources utiles

- [Documentation officielle Vagrant](https://developer.hashicorp.com/vagrant)
- [Laboratoires Vagrant Labs](https://github.com/vagrant-labs-eazytrainings)
