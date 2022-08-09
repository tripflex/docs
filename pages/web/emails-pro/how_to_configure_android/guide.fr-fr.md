---
title: "Configurer son compte E-mail Pro sur Android via l'application Gmail"
slug: configuration-android
excerpt: "Apprenez à configurer un compte E-mail Pro sur Android, via l'application Gmail"
section: 'Configuration sur smartphone'
order: 2
---

**Dernière mise à jour le 19/03/2020**

## Objectif

Les comptes E-mail Pro peuvent être configurés sur différents logiciels de messagerie compatibles. Cela vous permet d’utiliser votre adresse e-mail depuis l’appareil de votre choix.

**Apprenez à configurer un compte E-mail Pro sur Android, via l'application Gmail.**

> [!warning]
>
> OVHcloud met à votre disposition des services dont la configuration, la gestion et la responsabilité vous incombent. Il vous revient de ce fait d'en assurer le bon fonctionnement.
> 
> Nous mettons à votre disposition ce guide afin de vous accompagner au mieux sur des tâches courantes. Néanmoins, nous vous recommandons de faire appel à un [prestataire spécialisé](https://partner.ovhcloud.com/fr/) et/ou de contacter l'éditeur du service si vous éprouvez des difficultés. En effet, nous ne serons pas en mesure de vous fournir une assistance. Plus d'informations dans la section « Aller plus loin » de ce guide.
> 

## Prérequis

- Disposer d'une offre [E-mail Pro](https://www.ovhcloud.com/fr/emails/email-pro/){.external}.
- Disposer de l'application Gmail sur votre appareil. Vous pouvez installer cette dernière depuis le Google Play Store.
- Disposer des identifiants relatifs à l'adresse e-mail que vous souhaitez paramétrer.

> [!primary]
>
> Cette documentation a été réalisée depuis un appareil utilisant  Android, version 12. Pour une question d'uniformisation, nous utilisons l'application Gmail pouvant être installée depuis le Play Store. Si vous souhaitez utiliser une autre application, la marche à suivre pourrait alors être différente. 
>

## En pratique

### Comprendre les protocoles de réception POP et IMAP

Avant de débuter la configuration de votre compte e-mail sur votre terminal, il est important de bien comprendre le fonctionnement des protocoles de réception **POP** et **IMAP** pour choisir celui qui conviendra à votre utilisation.

#### POP

POP signifie **P**ost **O**ffice **P**rotocol, c'est le plus ancien des deux protocoles. Son principe est de venir relever les e-mails du serveur via un logiciel de messagerie depuis un terminal (ordinateur, smarphone, tablette, etc.), une fois téléchargé sur le terminal, les e-mails sont supprimés du serveur et le logiciel de messagerie ferme la connexion avec le serveur. 

Exemple:
- L'e-mail est envoyé et stocké sur le serveur de réception ( **Mail Server** sur le schéma ci-dessous). 
- Les terminaux (ordinateur, smarphone, tablette, etc.) sont notifiés de la réception de l'e-mail sur le serveur.
- Lorsque le logiciel de messagerie est lancé sur un terminal, il relève le courrier et vient stocker l'e-mail sur la mémoire interne du terminal. 
- Le courrier relevé n'est alors plus présent sur le serveur. Les autres terminaux verront la boite de réception vide.

![POP](images/POP01.gif){.thumbnail}

> [!primary]
>
> Le protocole POP peut toutefois être paramétré pour qu'une copie soit conservée sur le serveur lorsque le courrier est relevé pour permettre de consulter le même e-mail sur plusieurs terminaux, cela reste un comportement qui n'est pas défini par défaut. Il est donc nécessaire de le paramétrer de cette façon sur tous les terminaux utilisant le protocole POP.

#### IMAP

IMAP signifie **I**nteractive **M**essage **A**ccess **P**rotocol, il a été créé pour répondre à une utilisation plus contemporaine par rapport au POP. Lorsqu'un e-mail est transmis au serveur de réception, le logiciel de messagerie va notifier l'utilisateur. Lorsque l'utilisateur va consulter l'e-mail sur un des terminaux, les autres verront cet e-mail comme lu, de la même manière lorsqu'un message est supprimé. Le protocole IMAP applique un principe de synchronisation.
  
Exemple:
- L'e-mail est envoyé et stocké sur le serveur de réception ( **Mail Server** sur le schéma ci-dessous). 
- Les terminaux (ordinateur, smarphone, tablette, etc.) sont notifiés de la réception de l'e-mail sur le serveur.
- Lorsque le logiciel de messagerie est lancé sur un des terminaux, il vient synchroniser le contenu du serveur et afficher l'e-mail reçu. 
- L'e-mail reste stocké sur le serveur si l'utilisateur ne demande pas sa suppression, les autres terminaux peuvent ainsi le lire.

![IMAP](images/IMAP01.gif){.thumbnail}

### Étape 1 : ajouter le compte

> [!primary]
>
> Dans notre exemple, nous utilisons la mention serveur : pro**?**.mail.ovh.net. Vous devrez remplacer le « ? » par le chiffre désignant le serveur de votre service E-mail Pro.
> 
> Retrouvez ce chiffre dans votre [espace client OVHcloud](https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.com/fr/&ovhSubsidiary=fr){.external}, dans la rubrique `Web Cloud`{.action} puis `E-mail Pro`{.action}. Le nom du serveur est visible dans le cadre **Connexion** de l'onglet `Informations Générales`{.action}.
> 

Depuis votre appareil Android, rendez-vous dans l'application `Gmail`{.action}. L’ajout d’un compte peut s’initier de deux manières différentes :

- **si aucun compte n'est paramétré** : passez l'étape de bienvenue puis appuyez sur `Ajouter une adresse e-mail`{.action}. 

- **si un compte a déjà été paramétré** : appuyez sur le menu « burger » en haut à gauche, puis sur `Paramètres`{.action}. Appuyez sur  `Ajouter un compte`{.action} et choisissez `Autre`{.action}. 

| | |
|---|---|
|![android](images/android-emailpro01.png){.thumbnail}|Sélectionnez `Autre`{.action} dans la fenêtre **Configurer votre messagerie**|
|Saisissez votre adresse e-mail, dans la fenêtre  **Ajouter votre adresse e-mail**, puis appuyer sur `Suivant`{.action} |![android](images/android-emailpro02.png){.thumbnail}|
|![android](images/android-emailpro03.png){.thumbnail}|À la question **De quel type de compte s'agit-il?**, vous avez deux choix: <br>-  **Personnel (POP)** : les e-mails seront transmis en local sur votre terminal et supprimé du serveur<br>- **Personnel (IMAP)** (recommandé) : Vos e-mails seront synchronisés avec vos différents terminaux|
|Saisissez votre **mot de passe**, puis appuyer sur `Suivant`{.action} |![android](images/android-emailpro04.png){.thumbnail}|
|![android](images/android-emailpro05.png){.thumbnail}|Depuis la fenêtre **Paramètres du serveur entrant**, Saisissez les informations suivantes: <br>- **Nom d'utilisateur** :  votre adresse e-mail complète<br>- **Mot de passe** :  le mot de passe associé à votre adresse e-mail<br>- **Serveur** :  pro**?**.mail.ovh.net<br>- **Supprimer les e-mails du serveur**: choisissez le comportement qui vous convient<br> Appuyez sur `Suivant`{.action}|
|Validez le certificat depuis la fenêtre qui apparait, puis appuyez sur `Suivant`{.action}|![android](images/android-emailpro06.png){.thumbnail}|
|![android](images/android-emailpro07.png){.thumbnail}|La fenêtre **Paramètres du serveur entrant** apparait de nouveau, complété les informations suivante:<br>- **Nom d'utilisateur** :  votre adresse e-mail complète<br>- **Mot de passe** :  le mot de passe associé à votre adresse e-mail<br>- **Serveur** :  pro**?**.mail.ovh.net<br>- **Port**: 993(IMAP) ou 995(POP)<br>- **Type de sécurité** : SSL/TLS (accepter tous les certificats)<br>- **Supprimer les e-mails du serveur**: choisissez le comportement qui vous convient<br> Appuyez sur `Suivant`{.action}|
|![android](images/android-emailpro08.png){.thumbnail}|Depuis la fenêtre **Paramètres du serveur sortant**, Saisissez les informations suivantes: <br>- **Exiger une connexion** :  cochez la case<br>- **Serveur SMTP** :  pro**?**.mail.ovh.net<br> Appuyez sur `Suivant`{.action}|
|Validez le certificat depuis la fenêtre qui apparait, puis appuyez sur `Suivant`{.action}|![android](images/android-emailpro04.png){.thumbnail}|
|![android](images/android-emailpro08.png){.thumbnail}|La fenêtre **Paramètres du serveur sortant**apparait de nouveau, saisissez les informations suivantes: <br>- **Exiger une connexion** :  cochez la case<br>- **Serveur SMTP** :  pro**?**.mail.ovh.net<br>- **Port**: 465<br>- **Type de sécurité** : SSL/TLS (accepter tous les certificats)<br> Appuyez sur `Suivant`{.action}|
|Depuisla fenêtre **Options du compte**, appliquez les paramètres qui vous conviennent|![android](images/android-emailpro04.png){.thumbnail}|

### Rappel des ports à utiliser en POP et IMAP

- **pour une configuration en IMAP**

|Type de serveur|Nom du serveur|Type de sécurité|Port|
|---|---|---|---|
|Entrant|pro**?**.mail.ovh.net|SSL/TLS|993|
|Sortant|pro**?**.mail.ovh.net|STARTTLS|587|

- **pour une configuration en POP**

|Type de serveur|Nom du serveur|Type de sécurité|Port|
|---|---|---|---|
|Entrant|pro**?**.mail.ovh.net|SSL/TLS|995|
|Sortant|pro**?**.mail.ovh.net|STARTTLS|587|

### Étape 2 : utiliser l'adresse e-mail

Une fois l'adresse e-mail configurée, il ne reste plus qu’à l'utiliser ! Vous pouvez dès à présent envoyer et recevoir des messages.

OVHcloud propose également une application web disposant de [fonctions collaboratives](https://www.ovhcloud.com/fr/emails/){.external} accessible à l’adresse [https://www.ovh.com/fr/mail/](https://www.ovh.com/fr/mail/){.external}. Vous pouvez vous y connecter grâce aux identifiants de votre adresse e-mail.

## Aller plus loin

[Configurer son adresse e-mail comprise dans l’offre MX Plan ou dans une offre d’hébergement web sur Android via l'application Gmail](https://docs.ovh.com/fr/emails/configuration-android-6/){.external}.

[Configurer son compte Exchange sur Android via l'application Gmail](https://docs.ovh.com/fr/microsoft-collaborative-solutions/exchange-configuration-sous-android/){.external}.

Échangez avec notre communauté d'utilisateurs sur [https://community.ovh.com](https://community.ovh.com){.external}.