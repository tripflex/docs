---
title: Objekt-Container synchronisieren
excerpt: Hier erfahren Sie, wie Sie zwei Container untereinander synchronisieren.
slug: objekt-container_synchronisieren
section: Object Storage
legacy_guide_number: g1919
---

> [!primary]
> Diese Übersetzung wurde durch unseren Partner SYSTRAN automatisch erstellt. In manchen Fällen können ungenaue Formulierungen verwendet worden sein, z.B. bei der Beschriftung von Schaltflächen oder technischen Details. Bitte ziehen Sie beim geringsten Zweifel die englische oder französische Fassung der Anleitung zu Rate. Möchten Sie mithelfen, diese Übersetzung zu verbessern? Dann nutzen Sie dazu bitte den Button “Mitmachen“ auf dieser Seite.
>

**Stand 23.09.2021**

## Ziel

Wenn Sie Ihre Objekte von einem Rechenzentrum in ein anderes oder sogar von einem Projekt in ein anderes verschieben möchten, ist die Synchronisation von Objekten zwischen Containern die beste Lösung, um eine Unterbrechung des Dienstes während der Migration zu vermeiden. In dieser Anleitung erfahren Sie, wie Sie diese Lösung umsetzen.

## Voraussetzungen

- [Umgebung für die Verwendung der OpenStack-API vorbereiten](https://docs.ovh.com/de/public-cloud/vorbereitung_der_umgebung_fur_die_verwendung_der_openstack_api/) mit dem Swift-Client vorbereiten.
- [Laden der OpenStack-Umgebungsvariablen](https://docs.ovh.com/de/public-cloud/die-variablen-der-umgebung-openstack-laden/)
- 2 Objektcontainer in 2 verschiedenen Rechenzentren.

## In der praktischen Anwendung

> [!warning]
>
> Wenn Ihre Container Large Objects enthalten (Objekt über 5 Gb), müssen Ihre Container denselben Namen haben.
>

### Synchronisationskonfiguration

#### Erstellung des Synchronisationsschlüssels

Damit sich die Container identifizieren können, muss ein Schlüssel erstellt und auf jedem Objekt-Container konfiguriert werden:

- Schlüssel erstellen:

```bash
root@serveur-1:~$ sharedKey=$(openssl rand -base64 32)
```

#### Konfiguration des Empfänger-Containers

Zuerst muss der Schlüssel auf dem Container konfiguriert werden, der die Daten empfängt. In unserem Fall befindet sich dieser in BHS.

- Die geladene Region in den Umgebungsvariablen überprüfen:

```bash
root@serveur-1:~$ env | grep OS_REGION
OS_REGION_NAME=BHS1
```

- Den Key im Empfänger-Container konfigurieren:

```bash
root@serveur-1:~$ swift post --sync-key "$sharedKey" containerBHS
```

Sie können die Konfiguration mit folgendem Befehl überprüfen:

```bash
  root@serveur-1:~$ swift stat containerBHS
                         Account: AUTH_b3e269xxxxxxxxxxxxxxxxxxxx2b0ba29
                       Container: containerBHS
                         Objects: 0
                           Bytes: 0
                        Read ACL:
                       Write ACL:
                         Sync To:
                        Sync Key: 4cA5j5LyaaG2ws32d1fsdQSxnvIJv+y2qFnbnm6Kw=
Meta Access-Control-Allow-Origin: https://www.ovh.com
                   Accept-Ranges: bytes
                X-Storage-Policy: Policy-0
                      Connection: close
                     X-Timestamp: 1444812373.28095
                      X-Trans-Id: B2210C05:895E_9E45A961:01BB_561E52E1_16A3:5298
                    Content-Type: text/plain; charset=utf-8
```

- Rufen Sie die Adresse des Empfänger-Containers ab und konfigurieren Sie diese anschließend im Quell-Container:

```bash
root@serveur-1:~$ destContainer=$(swift --debug stat containerBHS 2>&1 | grep 'curl -i.*storage' | awk '{ print $4 }')
```

#### Konfiguration des Quellcontainers

- Region in den Umgebungsvariablen ändern:

```bash
root@serveur-1:~$ export OS_REGION_NAME=GRA1
```

- Den Key im Quellcontainer konfigurieren:

```bash
root@serveur-1:~$ swift post --sync-key "$sharedKey" containerGRA
```

- Den Empfänger im Quellcontainer konfigurieren:

```bash
root@serveur-1:~$ swift post --sync-to "//OVH_PUBLIC_CLOUD/{zone}/AUTH_account/containerDest" containerGRA
```

Wie zuvor kann die Konfigurationssicherheit mit folgendem Befehl überprüft werden:

```bash
root@serveur-1:~$ swift stat containerGRA
         Account: AUTH_b3e269f057d14af594542d6312b0ba29
       Container: containerGRA
         Objects: 3
           Bytes: 15
        Read ACL:
       Write ACL:
         Sync To: //OVH_PUBLIC_CLOUD/BHS1/AUTH_b3e269f057d14af594542d6312b0ba29/containerBHS
        Sync Key: 4cA5j5LyaaG2wU4lDYnDmEwQSxnvIJv+y2qFnbnm6Kw=
   Accept-Ranges: bytes
      Connection: close
     X-Timestamp: 1444813114.55493
      X-Trans-Id: B2210C05:879E_052711B1:01BB_561E559B_24AE:6B1B
X-Storage-Policy: Policy-0
    Content-Type: text/plain; charset=utf-8
```

#### Synchronisierungsprüfung

Nach einigen Augenblicken (abhängig von Anzahl und Größe der zu versendenden Dateien) kann überprüft werden, ob die Synchronisation korrekt abläuft, indem die Dateien in jedem Container aufgelistet werden.

- Die im Quellcontainer vorhandenen Dateien auflisten:

```bash
root@serveur-1:~$ swift list containerGRA
test1.txt
test2.txt
test3.txt
```

- Die im Empfänger-Container vorhandenen Dateien auflisten:

```bash
root@serveur-1:~$ swift list containerBHS
test1.txt
test2.txt
test3.txt
```

> [!success]
>
> Diese Anleitung kann auch für die Migration von RunAbove-Objekten in die Public Cloud verwendet werden.
>

## Weiterführende Informationen

Für den Austausch mit unserer User Community gehen Sie auf <https://community.ovh.com/en/>.
