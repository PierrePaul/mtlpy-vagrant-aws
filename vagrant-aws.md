# Vagrant, Amazon, vagrant-aws

![test](/cloud-999px.png)

*obligatory cloud image*

---

# Vagrant, c'est quoi?

- Virtualisation de machine, orienté vers les développeurs
- Fonctionne sous tous les OS
- Utilise VirtualBox, mais peut être changé pour du LXC ou des machines Amazon

---

# Installation

- [Vagrant](http://downloads.vagrantup.com/tags/v1.3.4)

---

# Tour du propriétaire

    !bash
    vagrant init
    vagrant box add precise64 http://files.vagrantup.com/precise64.box

Modifier le fichier Vagrantfile pour faire référence à la bonne "box"

    !bash
    config.vm.box = "precise64"
    config.vm.box_url = "http://files.vagrantup.com/precise64.box"

Pour démarrer la machine

    !bash
    vagrant up
    vagrant status
    vagrant ssh

Note : le dossier courrant va être automatiquement mis dans le dossier /vagrant de la machine virtuelle.

---

# Multi-machines

C'est possible de reproduire une topologie de serveur un peu plus complexe avec Vagrant.

Par exemple, on peut avoir un serveur db et web sur deux machines virtualisées qui se parlent entre eux.

    !ruby
    Vagrant.configure("2") do |config|
      config.vm.provision "shell", inline: "echo Hello"

      config.vm.define "web" do |web|
        web.vm.box = "apache"
      end

      config.vm.define "db" do |db|
        db.vm.box = "mysql"
      end
    end

[Documentation](http://docs.vagrantup.com/v2/multi-machine/index.html)

---

# Multi-machines partie 2

Dans le cas des multi-machines, les commandes vagrant vont être légèrement différente.

Par exemple :

    !bash
    vagrant up web
    vagrant up db
    vagrant status [web,db]

---

# Demo Vagrant


---

# Provision

Possible (et même recommandé) de configurer la machine en utilisant des système comme Ansible, Puppet, Chef ou même un script shell.
[Provision](http://docs.vagrantup.com/v2/cli/provision.html)

Si la machine est déjà en route et vous désirez mettre à jour la configuration :

    !bash
    vagrant provision

---

#Amazon

### [AWS](http://aws.amazon.com/)

- Machine virtualisée (ou dédié, selon vos besoins) avec possibilité de couvrir différentes partie du monde
- Pour tous les budgets, selon vos besoins
- Gratuit la première année pour les instances t1.micro (1 VCPU, 613mo de RAM) roulant plusieurs saveurs de linux
- Après la première année, une t1.micro coûte 3.66$ par mois plus un paiement de 62$ par mois. On parle d'environ 110$ pour une année.

[La calculatrice](http://calculator.s3.amazonaws.com/calc5.html) sera votre meilleur ami.

---

# Reserved whut?

Les instances réservées vous permettent de faire des économies importantes si vous savez que vous aurez besoin de vos machines 24 heures sur 24, comme dans le cas d'un serveur web.

Il s'agit d'un paiement annuel (si vous achetez d'Amazon) qui réduit votre prix par heure sur une machine, dans une région précise et dans une zone précise.

Il y aussi un "marketplace" pour ceux qui désire revendre leur "reserved instance". Ce qui est utile si nos besoins ont soudainement changé.

---

# AMI

Un AMI est simplement un "snapshot" d'une machine. Cannonical et beaucoup d'autres offrent des images de leur système d'opération pré-installée et prête à l'utilisation.

Vous pouvez faire votre propres AMI à partir de vos machines, mais vous devrez les mettre hors ligne pour le faire.

---

# IAM

Amazon est fan d'acronyme qui mélange tout le monde.

Permet de gérer

- Les clefs d'accès pour les API
- Ajouter des utilisateurs
- Ajouter des groupes d'utilisateurs


Cette section est nécessaire pour créer des clefs d'API qui vont permettre à Vagrant de créer les machines sur Amazon.

---

# L'interface

L'interface, en date d'aujourd'hui, a beaucoup de problèmes.

Rien n'est facile d'accès, mais il est possible presque tout faire via l'interface web. Amazon est toujours en constante évolution vis-à-vis les interfaces de chacun de ses produits.

---

# Demo Amazon



---

# vagrant-aws

Nous avons besoin du [plugin vagrant-aws](https://github.com/mitchellh/vagrant-aws) pour pousser notre machine sur Amazon :

    !bash
    vagrant plugin install vagrant-aws
    vagrant box add dummy https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box

Le plugin est fait par le créateur de Vagrant.

---

# Vagrantfile

Le projet demo pour la présentation : [github](https://github.com/PierrePaul/mtlpy-vagrant-aws) 

Pour utiliser vagrant-aws (le provider), nous devons ajouter quelques détails dans le [Vagrantfile](https://github.com/PierrePaul/mtlpy-vagrant-aws/blob/master/vagrant/Vagrantfile).

    !bash
    aws.access_key_id
    aws.secret_access_key
    aws.keypair_name
    aws.ami
    aws.instance_type
    aws.security_groups
    override.ssh.username
    override.ssh.private_key_path


---

# Demo vagrant-aws

---

# Notes

Impossible d'avoir une machine qui roule de manière concurrente même si les providers sont différends. C'est une limitation qui devrait être levée dans une future version de Vagrant.

Possible d'utiliser les variables d'environnements dans le Vagrantfile pour éviter de storer les clefs d'accès dans git ou hg.

Possible de définir des tags sur les machines. Via l'interface web, le nom de la machine est associé au tag "Name".
