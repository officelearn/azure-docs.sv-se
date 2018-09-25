---
title: Introduktion till FreeBSD på Azure | Microsoft Docs
description: Läs om hur du använder FreeBSD-virtuella datorer på Azure
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
ms.openlocfilehash: 0eab96d85aa9cc8bea8d30c5a36c8489b8ea652e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974178"
---
# <a name="introduction-to-freebsd-on-azure"></a>Introduktion till FreeBSD på Azure
Den här artikeln innehåller en översikt över kör en FreeBSD-dator i Azure.

## <a name="overview"></a>Översikt
FreeBSD för Microsoft Azure är en avancerad operativsystem används för att power moderna servrar, stationära datorer och inbäddade plattformar.

Microsoft Corporation är att göra avbildningar av FreeBSD tillgängliga på Azure med den [Azure VM-Gästagent](https://github.com/Azure/WALinuxAgent/) redan har konfigurerats. För närvarande erbjuds följande FreeBSD-versioner som bilder av Microsoft:

- FreeBSD 10.3-version
- FreeBSD 10.4-version
- FreeBSD 11.1-version

Agenten är ansvarig för kommunikation mellan FreeBSD VM och Azure-infrastrukturen för åtgärder som etablerar den virtuella datorn vid första användning (användarnamn, lösenord eller SSH-nyckel, värdnamn, etc.) och funktioner för selektiv VM-tillägg.

För framtida versioner av FreeBSD är strategin att hålla dig uppdaterad och tillgängliggöra de senaste versionerna strax efter att de har publicerats av det tekniska teamet för FreeBSD-versionen.

## <a name="deploying-a-freebsd-virtual-machine"></a>Distribuera en FreeBSD virtuell dator
Distribuera en FreeBSD virtuell dator är enkelt att använda en avbildning från Azure Marketplace från Azure portal:

- [FreeBSD 10.3 på Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103)
- [FreeBSD 10.4 på Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.FreeBSD104)
- [FreeBSD 11.1 på Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD111)

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>Skapa en FreeBSD virtuell dator via Azure CLI på FreeBSD
Du måste först installera [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) trots att följande kommando på en FreeBSD-dator.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Om bash inte är installerat på datorn FreeBSD, kör du följande kommando före installationen. 

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

Under installationen uppmanas du `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`. Om du svarar `y` och ange `/etc/rc.conf` som `a path to an rc file to update`, du kan uppfylla problemet `ERROR: [Errno 13] Permission denied`. För att lösa problemet, bör du bevilja skrivningen direkt till aktuell användare mot filen `etc/rc.conf`.

Du kan nu logga in på Azure och skapa FreeBSD-VM. Nedan visas ett exempel för att skapa en virtuell dator 11.0 FreeBSD. Du kan också lägga till parametern `--public-ip-address-dns-name` med ett globalt unikt DNS-namn för en nyligen skapade offentliga IP-adress. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Sedan kan du logga in till din FreeBSD-VM via ip-adress som ut i utdata från ovan distribution. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>VM-tillägg för FreeBSD
Följande är VM-tillägg som stöds i FreeBSD.

### <a name="vmaccess"></a>VMAccess
Den [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) tillägg kan:

* Återställa lösenordet för den ursprungliga sudo-användaren.
* Skapa en ny sudo-användare med det angivna lösenordet.
* Ange offentlig värdnyckeln med nyckel anges.
* Återställa offentlig värdnyckeln som anges vid etablering av virtuella datorer om serverns värdnyckel inte har angetts.
* Öppna SSH-porten (22) och återställa sshd_config om reset_ssh har angetts till true.
* Ta bort den befintliga användaren.
* Kontrollera diskar.
* Reparera en disk som har lagts till.

### <a name="customscript"></a>CustomScript
Den [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) tillägg kan:

* Om du ladda ned anpassade skript från Azure Storage eller extern offentlig lagring (till exempel GitHub).
* Kör startpunktsskriptet.
* Stöd för infogad-kommandon.
* Konvertera automatiskt Windows-style ny rad i shell och Python-skript.
* Ta bort BOM i shell och Python-skript automatiskt.
* Skydda känsliga data i CommandToExecute.

> [!NOTE]
> FreeBSD VM stöder endast CustomScript version 1.x nu.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Autentisering: användarnamn, lösenord och SSH-nycklar
När du skapar en FreeBSD virtuell dator med hjälp av Azure-portalen, måste du ange ett användarnamn, lösenord eller offentlig SSH-nyckel.
Användarnamn för att distribuera en FreeBSD virtuell dator på Azure får inte matcha namnen på Systemkonton (UID < 100) finns redan i den virtuella datorn (”rot”, till exempel).
För närvarande stöds endast i RSA SSH-nyckeln. En flerradig SSH-nyckel måste börja med `---- BEGIN SSH2 PUBLIC KEY ----` och sluta med `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>Hämta superanvändare
Det användarkonto som anges under distributionen av virtuella datorer-instans på Azure är ett konto med privilegier. Paket med sudo har installerats på den publicerade FreeBSD-bilden.
När du har loggat in via det här användarkontot, kan du köra kommandon som rot med hjälp av kommandosyntax.

```
$ sudo <COMMAND>
```

Du kan också hämta ett rot-gränssnitt med hjälp av `sudo -s`.

## <a name="known-issues"></a>Kända problem
Den [Azure VM-Gästagent](https://github.com/Azure/WALinuxAgent/) version 2.2.2 handlar om ett [problem] (https://github.com/Azure/WALinuxAgent/pull/517) som orsakar felet etablera för FreeBSD VM på Azure. Korrigeringen samlades in av [Azure VM-Gästagent](https://github.com/Azure/WALinuxAgent/) version 2.2.3 och senare versioner. 

## <a name="next-steps"></a>Nästa steg
* Gå till [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD111) att skapa en FreeBSD-VM.
