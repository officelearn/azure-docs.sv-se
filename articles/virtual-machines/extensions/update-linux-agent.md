---
title: Uppdatera Azure Linux-agenten från GitHub
description: Lär dig hur du uppdaterar Azure Linux Agent för din Virtuella Linux-dator i Azure
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: e4489f7c810799ca8e89565fe698f398f942b089
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251708"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Så här uppdaterar du Azure Linux-agenten på en virtuell dator

Om du vill uppdatera din [Azure Linux-agent](https://github.com/Azure/WALinuxAgent) på en virtuell Linux-dator i Azure måste du redan ha:

- En virtuell Linux-dator som körs i Azure.
- En anslutning till den virtuella Linux-datorn med SSH.

Du bör alltid söka efter ett paket i Linux-distributionsarkivet först. Det är möjligt att paketet som är tillgängligt kanske inte är den senaste versionen, men att aktivera autoupdate kommer att se till att Linux-agenten alltid kommer att få den senaste uppdateringen. Om du har problem med att installera från pakethanterare bör du söka stöd från distributionsleverantören.

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Minsta stöd för agenter för virtuella datorer i Azure
Verifiera [stöd för minsta version för agenter för virtuella datorer i Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) innan du fortsätter.

## <a name="updating-the-azure-linux-agent"></a>Uppdatera Azure Linux-agenten

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Kontrollera din aktuella paketversion

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Uppdatera paketcache

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Installera den senaste paketversionen

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Se till att automatisk uppdatering är aktiverad

Kontrollera först om det är aktiverat:

```bash
cat /etc/waagent.conf
```

Sök efter 'AutoUpdate.Enabled'. Om du ser den här utdata är den aktiverad:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Så här aktiverar du körning:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Starta om waagent-tjänsten

#### <a name="restart-agent-for-1404"></a>Starta om agent för 14,04

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Starta om agent för 16.04 / 17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Röd hatt / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Kontrollera din aktuella paketversion

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

#### <a name="ensure-auto-update-is-enabled"></a>Se till att automatisk uppdatering är aktiverad 

Kontrollera först om det är aktiverat:

```bash
cat /etc/waagent.conf
```

Sök efter 'AutoUpdate.Enabled'. Om du ser den här utdata är den aktiverad:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Så här aktiverar du körning:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Starta om waagent-tjänsten

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>Kontrollera din aktuella paketversion

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

#### <a name="ensure-auto-update-is-enabled"></a>Se till att automatisk uppdatering är aktiverad 

Kontrollera först om det är aktiverat:

```bash
cat /etc/waagent.conf
```

Sök efter 'AutoUpdate.Enabled'. Om du ser den här utdata är den aktiverad:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Så här aktiverar du körning:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Starta om waagent-tjänsten

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>Kontrollera din aktuella paketversion

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Kontrollera tillgängliga uppdateringar

Ovanstående utdata visar dig om paketet är uppdaterat.

#### <a name="install-the-latest-package-version"></a>Installera den senaste paketversionen

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Se till att automatisk uppdatering är aktiverad 

Kontrollera först om det är aktiverat:

```bash
cat /etc/waagent.conf
```

Sök efter 'AutoUpdate.Enabled'. Om du ser den här utdata är den aktiverad:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Så här aktiverar du körning:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Starta om waagent-tjänsten

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>Kontrollera din aktuella paketversion

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Kontrollera tillgängliga uppdateringar

I utdata från ovanstående visar detta dig om paketet är uppdaterat.

#### <a name="install-the-latest-package-version"></a>Installera den senaste paketversionen

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Se till att automatisk uppdatering är aktiverad 

Kontrollera först om det är aktiverat:

```bash
cat /etc/waagent.conf
```

Sök efter 'AutoUpdate.Enabled'. Om du ser den här utdata är den aktiverad:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Så här aktiverar du körning:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Starta om waagent-tjänsten

```bash
sudo systemctl restart waagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-jesse-debian-7-stretch"></a>Debian 7 "Jesse"/ Debian 7 "Stretch"

#### <a name="check-your-current-package-version"></a>Kontrollera din aktuella paketversion

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Uppdatera paketcache

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Installera den senaste paketversionen

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Aktivera automatisk uppdatering av agenten
Den här versionen av Debian har ingen version >= 2.0.16, därför är AutoUpdate inte tillgängligt för den. Utdata från kommandot ovan visar dig om paketet är uppdaterat.



### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie" / Debian 9 "Stretch"

#### <a name="check-your-current-package-version"></a>Kontrollera din aktuella paketversion

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>Uppdatera paketcache

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Installera den senaste paketversionen

```bash
sudo apt-get install waagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Se till att automatisk uppdatering är aktiverad
Kontrollera först om det är aktiverat:

```bash
cat /etc/waagent.conf
```

Sök efter 'AutoUpdate.Enabled'. Om du ser den här utdata är den aktiverad:

```bash
AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Så här aktiverar du körning:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
Restart the waagent service
sudo systemctl restart walinuxagent.service
```

## <a name="oracle-linux-6-and-oracle-linux-7"></a>Oracle Linux 6 och Oracle Linux 7

För Oracle Linux kontrollerar `Addons` du att databasen är aktiverad. Välj att redigera `/etc/yum.repos.d/public-yum-ol6.repo`filen (Oracle Linux `/etc/yum.repos.d/public-yum-ol7.repo`6) eller (Oracle Linux) `enabled=0` och ändra raden `enabled=1` till under **[ol6_addons]** eller **[ol7_addons]** i den här filen.

Om du sedan vill installera den senaste versionen av Azure Linux Agent skriver du:

```bash
sudo yum install WALinuxAgent
```

Om du inte hittar tilläggsdatabasen kan du helt enkelt lägga till dessa rader i slutet av din .repo-fil enligt din Oracle Linux-version:

För virtuella Oracle Linux 6-datorer:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

För virtuella Oracle Linux 7-datorer:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```

Skriv sedan:

```bash
sudo yum update WALinuxAgent
```

Vanligtvis är detta allt du behöver, men om du https://github.com av någon anledning behöver installera det från direkt, använd följande steg.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Uppdatera Linux-agenten när det inte finns något agentpaket för distribution

Installera wget (det finns vissa distributioner som inte installerar det som standard, till exempel Red Hat, CentOS och Oracle Linux `sudo yum install wget` versioner 6.4 och 6.5) genom att skriva på kommandoraden.

### <a name="1-download-the-latest-version"></a>1. Ladda ner den senaste versionen
Öppna [versionen av Azure Linux Agent i GitHub](https://github.com/Azure/WALinuxAgent/releases) på en webbsida och ta reda på det senaste versionsnumret. (Du kan hitta den aktuella versionen genom att `waagent --version`skriva .)

#### <a name="for-version-22x-or-later-type"></a>Om du vill ha version 2.2.x eller senare skriver du:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

Följande rad använder version 2.2.0 som exempel:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Installera Azure Linux-agenten

#### <a name="for-version-22x-use"></a>För version 2.2.x använder du:
Du kan behöva installera `setuptools` paketet först - se [här](https://pypi.python.org/pypi/setuptools). Kör sedan:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Se till att automatisk uppdatering är aktiverad

Kontrollera först om det är aktiverat:

```bash
cat /etc/waagent.conf
```

Sök efter 'AutoUpdate.Enabled'. Om du ser den här utdata är den aktiverad:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Så här aktiverar du körning:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Starta om waagent-tjänsten
För de flesta av Linux distributioner:

```bash
sudo service waagent restart
```

För Ubuntu, använd:

```bash
sudo service walinuxagent restart
```

För CoreOS använder du:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Bekräfta Azure Linux Agent-versionen
    
```bash
waagent -version
```

För CoreOS kanske kommandot ovan inte fungerar.

Du kommer att se att Azure Linux Agent-versionen har uppdaterats till den nya versionen.

Mer information om Azure Linux-agenten finns i [Azure Linux Agent README](https://github.com/Azure/WALinuxAgent).
