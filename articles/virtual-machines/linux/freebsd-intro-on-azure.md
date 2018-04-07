---
title: Introduktion till FreeBSD i Azure | Microsoft Docs
description: Lär dig mer om hur du använder FreeBSD-virtuella datorer på Azure
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: huishao
ms.openlocfilehash: 9c7cf223eab3e989436e12c39b122f2aee7619a0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="introduction-to-freebsd-on-azure"></a>Introduktion till FreeBSD på Azure
Det här avsnittet innehåller en översikt över en FreeBSD virtuell dator som körs i Azure.

## <a name="overview"></a>Översikt
FreeBSD för Microsoft Azure är en avancerad operativsystem används för att power moderna servrar, stationära datorer, och inbäddade plattformar.

Microsoft Corporation gör avbildningar av FreeBSD tillgängligt på Azure med den [Azure VM-Gästagent](https://github.com/Azure/WALinuxAgent/) förkonfigurerade. För närvarande erbjuds följande FreeBSD-versioner som avbildningar av Microsoft:

- FreeBSD 10.3-RELEASE
- FreeBSD 11.0-versionen
- FreeBSD 11.1-RELEASE

Agenten är ansvarig för kommunikation mellan FreeBSD VM och Azure-strukturen för till exempel etablering av den virtuella datorn vid första användning (användarnamn, lösenord eller SSH-nyckel, värdnamn, etc.) och aktiverar funktioner för selektiv VM-tillägg.

För framtida versioner av FreeBSD är strategin att hålla dig informerad om de senaste versionerna tillgängliga strax efter att de har publicerats av FreeBSD versionen teknikteamet.

## <a name="deploying-a-freebsd-virtual-machine"></a>Distribuera en virtuell dator FreeBSD
Distribuera en virtuell dator FreeBSD är enkelt att använda en avbildning från Azure Marketplace från Azure portal:

- [FreeBSD 10.3 på Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
- [FreeBSD 11.0 på Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/)
- [FreeBSD 11,1 på Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD111)

### <a name="create-a-freebsd-vm-through-azure-cli-20-on-freebsd"></a>Skapa en FreeBSD-VM via Azure CLI 2.0 på FreeBSD
Du måste först installera [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) trots att följande kommando på en FreeBSD-dator.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Om bash inte är installerat på datorn FreeBSD, kör du följande kommando innan installationen. 

```bash
sudo pkg install bash
```

Om python inte är installerat på datorn FreeBSD, kör du följande kommandon innan installationen. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Under installationen uppmanas du `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`. Om du svarar `y` och ange `/etc/rc.conf` som `a path to an rc file to update`, du kan uppfylla problemet `ERROR: [Errno 13] Permission denied`. Lös problemet, bör du bevilja skrivningen till aktuell användare mot filen `etc/rc.conf`.

Du kan nu logga in i Azure och skapa din FreeBSD VM. Nedan visas ett exempel för att skapa en virtuell dator 11.0 FreeBSD. Du kan också lägga till parametern `--public-ip-address-dns-name` med ett globalt unikt DNS-namn för en nyligen skapad offentliga IP-adress. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Sedan kan du logga in till din FreeBSD-VM via ip-adressen som skrivs ut i ovanstående distribution utdata. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>VM-tillägg för FreeBSD
Följande är VM-tillägg som stöds i FreeBSD.

### <a name="vmaccess"></a>VMAccess
Den [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) tillägg kan:

* Återställa lösenordet för den ursprungliga sudo-användaren.
* Skapa en ny sudo-användare med det angivna lösenordet.
* Ange den offentliga värdnyckeln med nyckel anges.
* Återställa nyckeln offentliga värden som angavs under VM etablering om värdnyckeln inte har angetts.
* Öppna SSH-port (22) och återställa sshd_config om reset_ssh har angetts till true.
* Ta bort befintliga användare.
* Kontrollera diskar.
* Reparera en lagts till disk.

### <a name="customscript"></a>CustomScript
Den [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) tillägg kan:

* Om du hämta anpassade skript från Azure Storage eller externa offentlig lagring (till exempel GitHub).
* Kör skriptet post punkt.
* Stöd för infogade kommandon.
* Konvertera automatiskt Windows-typ ny rad i gränssnittet och Python-skript.
* Ta bort BOM i gränssnittet och Python skript automatiskt.
* Skydda känsliga data i CommandToExecute.

> [!NOTE]
> FreeBSD VM stöder endast CustomScript version 1.x nu.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Autentisering: användarnamn, lösenord och SSH-nycklar
När du skapar en FreeBSD virtuell dator med hjälp av Azure portal, måste du ange ett användarnamn, lösenord eller offentlig SSH-nyckel.
Användarnamn för att distribuera en FreeBSD virtuell dator på Azure måste inte matcha namnen på Systemkonton (UID < 100) finns redan i den virtuella datorn (”rot”, till exempel).
För närvarande stöds endast den RSA SSH-nyckeln. Flerradig SSH-nyckeln måste börja med `---- BEGIN SSH2 PUBLIC KEY ----` och sluta med `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>Hämta superanvändare
Det användarkonto som anges under distributionen av virtuella datorer instans i Azure är ett konto med privilegier. Paket med sudo har installerats i publicerade FreeBSD-avbildningen.
När du är inloggad med det här användarkontot, kan du köra kommandon som rot med hjälp av kommandosyntax.

```
$ sudo <COMMAND>
```

Du kan du hämta ett rot-gränssnitt med hjälp av `sudo -s`.

## <a name="known-issues"></a>Kända problem
Den [Azure VM-Gästagent](https://github.com/Azure/WALinuxAgent/) version 2.2.2 har [känt problem] (https://github.com/Azure/WALinuxAgent/pull/517) som orsakar felet etablera för FreeBSD VM på Azure. Korrigering inhämtats via [Azure VM-Gästagent](https://github.com/Azure/WALinuxAgent/) version 2.2.3 och senare versioner. 

## <a name="next-steps"></a>Nästa steg
* Gå till [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/) att skapa en FreeBSD VM.
