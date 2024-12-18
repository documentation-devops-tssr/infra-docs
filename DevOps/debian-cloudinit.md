
# Création d'un Template Cloud-Init Debian avec une Image QCOW2 dans Proxmox

Cette documentation explique comment utiliser une image Debian Cloud-Init au format **QCOW2** pour créer un template dans **Proxmox**, prêt à être utilisé pour des déploiements automatisés.

## Pré-requis

- Accès à un serveur Proxmox.
- Une image **Debian 12 Cloud-Init** au format QCOW2, disponible ici : [Debian 12 Cloud-Init QCOW2](https://cloud.debian.org/images/cloud/bookworm/latest/debian-12-genericcloud-amd64.qcow2)
- Un client SSH pour accéder au serveur Proxmox.

## Étapes

### Étape 1 : Téléchargement de l’Image QCOW2

Téléchargez l'image QCOW2 de Debian 12 Cloud-Init depuis le site officiel :

```bash
wget https://cloud.debian.org/images/cloud/bookworm/latest/debian-12-genericcloud-amd64.qcow2
```

### Étape 2 : Transférer l'Image QCOW2 vers Proxmox

Utilisez **SCP** pour transférer l'image QCOW2 téléchargée vers le stockage de Proxmox, typiquement dans le dossier `/var/lib/vz/images/` :

```bash
scp debian-12-genericcloud-amd64.qcow2 root@your-proxmox-server:/var/lib/vz/images/
```

### Étape 3 : Créer une VM Vide dans Proxmox

Dans l'interface de Proxmox :

1. Cliquez sur **Créer VM**.
2. Donnez un nom à la VM.
3. Dans l'étape **OS**, choisissez un ISO lambda temporairement (n'importe quel ISO disponible). Vous pourrez supprimer ce lecteur ISO plus tard dans les paramètres de la VM.
4. Dans l’étape **Disque dur**, **ne configurez pas de disque** (ne sélectionnez pas de disque, car nous allons ajouter l'image QCOW2 manuellement).
5. Continuez les étapes restantes pour finaliser la création de la VM.
6. Notez l'**ID de la VM** (par exemple, `1000`).
7. Une fois la VM créée, allez dans l'onglet **Hardware** de la VM, sélectionnez le lecteur **CD/DVD Drive** avec l'ISO temporaire, et supprimez-le pour éviter qu'il interfère avec le démarrage.

### Étape 4 : Importer l'Image QCOW2 dans la VM avec `qm importdisk`

1. Connectez-vous à votre serveur Proxmox en SSH.
2. Utilisez la commande `qm importdisk` pour attacher l'image QCOW2 en tant que disque principal de la VM nouvellement créée.

   ```bash
   qm importdisk 1000 /var/lib/vz/images/debian-12-genericcloud-amd64.qcow2 local-lvm
   ```

   Remplacez :
   - `1000` par l'ID de votre VM.
   - `/var/lib/vz/images/debian-12-genericcloud-amd64.qcow2` par le chemin complet vers l'image QCOW2.
   - `local-lvm` par le nom de votre stockage sur Proxmox.

### Étape 5 : Associer le Disque Importé comme Disque de Démarrage

Dans l'interface Proxmox :

1. Allez dans **Datacenter > Votre VM > Hardware**.
2. Le disque importé devrait maintenant apparaître sous **Hard Disk**.
3. Si nécessaire, cliquez sur **Options** > **Boot Order** et assurez-vous que le disque importé est le premier périphérique de démarrage.

### Étape 6 : Configurer Cloud-Init

1. Toujours dans **Hardware**, cliquez sur **Add > Cloud-Init Drive**.
2. Configurez les paramètres de Cloud-Init selon vos besoins dans l’onglet **Cloud-Init** (nom d’utilisateur, mot de passe, clé SSH, configuration réseau).

### Étape 7 : Démarrer la VM et Vérifier le Boot

1. Démarrez la VM.
2. Allez dans l'onglet **Console** pour voir le processus de démarrage et confirmer que Debian démarre correctement.

Vous devriez voir le système démarrer et appliquer les configurations Cloud-Init.

### Étape 8 : Convertir la VM en Template

Si tout fonctionne comme prévu, vous pouvez convertir cette VM en template pour faciliter les futurs déploiements.

```bash
qm template 1000
```

---

## Utilisation du Template

Vous pouvez maintenant utiliser ce template pour créer de nouvelles instances Debian avec Cloud-Init en utilisant des outils comme Terraform, Packer, ou directement dans l'interface de Proxmox.

---

### Remarques

- **Vérifiez les paramètres réseau** pour garantir que les nouvelles instances configurées via Cloud-Init aient accès au réseau.
- **Test SSH** : Si vous avez configuré une clé SSH dans Cloud-Init, essayez de vous connecter en SSH pour valider l'accès.

Cette méthode vous permet d'avoir un template Debian prêt à l'emploi pour des déploiements rapides et automatisés avec Proxmox et Cloud-Init.

---

# Sources et Références

- [Documentation officielle de Proxmox VE](https://pve.proxmox.com/wiki/Main_Page) - Pour en savoir plus sur l'utilisation et la gestion de Proxmox.
- [Guide Cloud-Init](https://cloudinit.readthedocs.io/en/latest/) - Documentation complète de Cloud-Init pour la configuration des machines.
- [Debian Cloud Images](https://cloud.debian.org/images/cloud/) - Source pour les images Debian avec Cloud-Init.
- [Commande `qm importdisk` dans Proxmox](https://pve.proxmox.com/pve-docs/qm.1.html) - Documentation pour importer un disque dans une VM Proxmox avec `qm importdisk`.
