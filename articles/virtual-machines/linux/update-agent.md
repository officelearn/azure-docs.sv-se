---
title: Uppdatera Azure Linux-agenten från GitHub | Microsoft Docs
description: Lär dig hur du uppdaterar Azure Linux-agenten för dina Linux VM i Azure
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: danis
ms.openlocfilehash: ed5bea98bc9b4849436ff5fafc90f85c99d582aa
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Så här uppdaterar du Azure Linux-agenten på en virtuell dator

Uppdatera din [Azure Linux-agenten](https://github.com/Azure/WALinuxAgent) på en Linux-VM i Azure måste du ha:

- En körande Linux VM i Azure.
- En anslutning för att Linux-VM via SSH.

Du bör alltid kontrollera för ett paket i Linux distro databasen först. Det är möjligt tillgängliga paketet inte kanske den senaste versionen, men att aktivera automatisk uppdatering säkerställer Linux-Agent kommer alltid att hämta den senaste uppdateringen. Om du har problem med att installera från paketet chefer, bör du söka stöd från distro leverantören.

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Stöd för agenten av minsta virtuella datorer i Azure
Kontrollera den [minst Versionsstöd för virtuell dator agenter i Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) innan du fortsätter.

## <a name="updating-the-azure-linux-agent"></a>Uppdatera Azure Linux-agenten

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Kontrollera din nuvarande Paketversion

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Uppdatera paketcachen

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Installera den senaste paketversionen

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Kontrollera att automatiska uppdateringar är aktiverat

Kontrollera först om det är aktiverat:

```bash
cat /etc/waagent.conf
```

Hitta 'AutoUpdate.Enabled'. Om du ser den här utdatan aktiveras:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aktivera Kör:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Starta om tjänsten waagent

#### <a name="restart-agent-for-1404"></a>Starta om agenten för 14.04

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Starta om agenten för 16.04 / 17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-wheezy"></a>Debian 7 ”Wheezy”

#### <a name="check-your-current-package-version"></a>Kontrollera din nuvarande Paketversion

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Uppdatera paketcachen

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Installera den senaste paketversionen

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Aktivera agenten för automatisk uppdatering
Den här versionen av Debian inte har en version > = 2.0.16, automatisk uppdatering är därför inte tillgänglig för den. Utdata från kommandot ovan visar om paketet är uppdaterad.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 ”Jessie” / Debian 9 ”Stretch”

#### <a name="check-your-current-package-version"></a>Kontrollera din nuvarande Paketversion

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>Uppdatera paketcachen

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Installera den senaste paketversionen

```bash
sudo apt-get install waagent
```
#### <a name="ensure-auto-update-is-enabled"></a>Kontrollera att automatiska uppdateringar är aktiverat 

Kontrollera först om det är aktiverat:

```bash
cat /etc/waagent.conf
```

Hitta 'AutoUpdate.Enabled'. Om du ser den här utdatan aktiveras:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aktivera Kör:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Starta om tjänsten waagent

```
sudo systemctl restart walinuxagent.service
```

## <a name="redhat--centos"></a>Redhat / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Kontrollera din nuvarande Paketversion

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Kontrollera tillgängliga uppdateringar

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Installera den senaste paketversionen

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Kontrollera att automatiska uppdateringar är aktiverat 

Kontrollera först om det är aktiverat:

```bash
cat /etc/waagent.conf
```

Hitta 'AutoUpdate.Enabled'. Om du ser den här utdatan aktiveras:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aktivera Kör:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Starta om tjänsten waagent

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>Kontrollera din nuvarande Paketversion

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Kontrollera tillgängliga uppdateringar

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Installera den senaste paketversionen

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Kontrollera att automatiska uppdateringar är aktiverat 

Kontrollera först om det är aktiverat:

```bash
cat /etc/waagent.conf
```

Hitta 'AutoUpdate.Enabled'. Om du ser den här utdatan aktiveras:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aktivera Kör:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Starta om tjänsten waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>Kontrollera din nuvarande Paketversion

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Kontrollera tillgängliga uppdateringar

Ovanstående utdata visar om paketet är uppdaterad.

#### <a name="install-the-latest-package-version"></a>Installera den senaste paketversionen

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Kontrollera att automatiska uppdateringar är aktiverat 

Kontrollera först om det är aktiverat:

```bash
cat /etc/waagent.conf
```

Hitta 'AutoUpdate.Enabled'. Om du ser den här utdatan aktiveras:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aktivera Kör:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Starta om tjänsten waagent

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>Kontrollera din nuvarande Paketversion

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Kontrollera tillgängliga uppdateringar

Utdata från den ovan och visar detta om paketet är upp till datum.

#### <a name="install-the-latest-package-version"></a>Installera den senaste paketversionen

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Kontrollera att automatiska uppdateringar är aktiverat 

Kontrollera först om det är aktiverat:

```bash
cat /etc/waagent.conf
```

Hitta 'AutoUpdate.Enabled'. Om du ser den här utdatan aktiveras:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aktivera Kör:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Starta om tjänsten waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-6-and-7"></a>Oracle 6 och 7

För Oracle Linux, kontrollera att den `Addons` databasen är aktiverad. Välj att redigera filen `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) eller `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux), och ändrar du raden `enabled=0` till `enabled=1` under **[ol6_addons]** eller **[ol7_addons]** på denna filen.

Om du vill installera den senaste versionen av Azure Linux-agenten, skriver du sedan:

```bash
sudo yum install WALinuxAgent
```

Om du inte hittar tillägget databasen du helt enkelt lägga till dessa rader i slutet av filen .repo enligt Oracle Linux-version:

För Oracle Linux 6 virtuella datorer:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

För Oracle Linux 7 virtuella datorer:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0
```

Sedan skriver du:

```bash
sudo yum update WALinuxAgent
```

Detta är vanligtvis allt du behöver, men om du av någon anledning måste du installera den från https://github.com direkt, Använd följande steg.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Uppdatera Linux-agenten när det finns ingen agentpaket för distribution

Installera wget (det finns vissa distributioner som inte installeras som standard, till exempel Redhat, CentOS och Oracle Linux-version 6.4 och 6.5) genom att skriva `sudo yum install wget` på kommandoraden.

### <a name="1-download-the-latest-version"></a>1. Hämta den senaste versionen
Öppna [versionen av Azure Linux-agenten i GitHub](https://github.com/Azure/WALinuxAgent/releases) i en webbsida och ta reda på det senaste versionsnumret. (Du hittar din nuvarande version genom att skriva `waagent --version`.)

#### <a name="for-version-22x-or-later-type"></a>För version 2.2.x eller senare, skriver du:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip.zip
cd WALinuxAgent-2.2.x
```

Följande rad använder version 2.2.0 som exempel:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Installera Azure Linux-agenten

#### <a name="for-version-22x-use"></a>Version för 2.2.x, Använd:
Du kan behöva installera paketet `setuptools` först-- [här](https://pypi.python.org/pypi/setuptools). Kör sedan:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Kontrollera att automatiska uppdateringar är aktiverat

Kontrollera först om det är aktiverat:

```bash
cat /etc/waagent.conf
```

Hitta 'AutoUpdate.Enabled'. Om du ser den här utdatan aktiveras:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aktivera Kör:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Starta om tjänsten waagent
För de flesta distributioner för Linux:

```bash
sudo service waagent restart
```

Ubuntu, Använd:

```bash
sudo service walinuxagent restart
```

För virtuell CoreOS, använder du:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Bekräfta Azure Linux-agentens version
    
```bash
waagent -version
```

För virtuell CoreOS fungerar inte kommandot ovan.

Du ser att versionen av Azure Linux-agenten har uppdaterats till den nya versionen.

Mer information om Azure Linux-agenten finns [Azure Linux-agenten viktigt](https://github.com/Azure/WALinuxAgent).
