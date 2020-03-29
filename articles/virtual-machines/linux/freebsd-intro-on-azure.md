---
title: Introduktion till FreeBSD på Azure
description: Lär dig mer om hur du använder virtuella FreeBSD-datorer på Azure
author: thomas1206
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: huishao
ms.openlocfilehash: fe64418e254289a29aafd155b92396082bff5b6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945092"
---
# <a name="introduction-to-freebsd-on-azure"></a>Introduktion till FreeBSD på Azure
Den här artikeln innehåller en översikt över hur du kör en virtuell FreeBSD-dator i Azure.

## <a name="overview"></a>Översikt
FreeBSD för Microsoft Azure är ett avancerat datoroperativsystem som används för att driva moderna servrar, stationära datorer och inbäddade plattformar.

Microsoft Corporation gör avbildningar av FreeBSD tillgängliga på Azure med [Azure VM Guest Agent](https://github.com/Azure/WALinuxAgent/) förkonfigurerad. För närvarande erbjuds följande FreeBSD-versioner som avbildningar av Microsoft:

- [FreeBSD 10.4 på Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
- [FreeBSD 11.2 på Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112)
- [FreeBSD 12.0 på Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD120)

Agenten ansvarar för kommunikationen mellan den virtuella freebsd-datorn och Azure-infrastrukturen för åtgärder som att etablera den virtuella datorn vid första användningen (användarnamn, lösenord eller SSH-nyckel, värdnamn osv.) och aktivera funktioner för selektiva VM-tillägg.

När det gäller framtida versioner av FreeBSD, är strategin att hålla sig uppdaterad och göra de senaste utgåvorna tillgängliga strax efter att de publiceras av FreeBSD release engineering team.

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>Skapa en kostnadsfri virtuell dator via Azure CLI på FreeBSD
Först måste du installera [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) men följande kommando på en FreeBSD-dator.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Om bash inte är installerat på din FreeBSD-dator kör du följande kommando före installationen. 

```bash
sudo pkg install bash
```

Om python inte är installerat på din FreeBSD-dator kör du följande kommandon före installationen. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Under installationen tillfrågas `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`du . Om du `y` svarar `/etc/rc.conf` `a path to an rc file to update`och anger som `ERROR: [Errno 13] Permission denied`kan du möta problemet . LÃ¶s problemet genom att bevilja den aktuella `etc/rc.conf`användaren skrivningsrätt mot filen .

Nu kan du logga in på Azure och skapa din FreeBSD VM. Nedan är ett exempel för att skapa en FreeBSD 11.0 VM. Du kan också `--public-ip-address-dns-name` lägga till parametern med ett globalt unikt DNS-namn för en nyligen skapad offentlig IP. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Sedan kan du logga in på din FreeBSD VM via IP-adressen som skrivs ut i utdata från ovanstående distribution. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>VM-tillägg för FreeBSD
Följande stöds VM-tillägg i FreeBSD.

### <a name="vmaccess"></a>VMAccess
[VMAccess-tillägget](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) kan:

* Återställ lösenordet för den ursprungliga sudo-användaren.
* Skapa en ny sudo-användare med det angivna lösenordet.
* Ange den offentliga värdnyckeln med den angivna nyckeln.
* Återställ den offentliga värdnyckeln som tillhandahålls under vm-etablering om värdnyckeln inte anges.
* Öppna SSH-porten (22) och återställ sshd_config om reset_ssh är inställt på true.
* Ta bort den befintliga användaren.
* Kontrollera diskar.
* Reparera en tillagd disk.

### <a name="customscript"></a>CustomScript (Anpassade program)
[CustomScript-tillägget](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) kan:

* Om det tillhandahålls hämtar du anpassade skript från Azure Storage eller extern offentlig lagring (till exempel GitHub).
* Kör startpunktsskriptet.
* Stöd för infogade kommandon.
* Konvertera newline-format i Windows-stil i skal- och Python-skript automatiskt.
* Ta bort strukturlistan i skal- och Python-skript automatiskt.
* Skydda känsliga data i CommandToExecute.

> [!NOTE]
> FreeBSD VM stöder endast CustomScript version 1.x vid det här laget.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Autentisering: användarnamn, lösenord och SSH-nycklar
När du skapar en virtuell FreeBSD-dator med hjälp av Azure-portalen måste du ange ett användarnamn, lösenord eller en offentlig SSH-nyckel.
Användarnamn för att distribuera en virtuell FreeBSD-dator på Azure får inte matcha namn på systemkonton (UID <100) som redan finns i den virtuella datorn ("root", till exempel).
För närvarande stöds endast RSA SSH-nyckeln. En flerrads-SSH-nyckel måste börja med `---- BEGIN SSH2 PUBLIC KEY ----` och sluta med `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>Få superanvändarbehörighet
Användarkontot som anges under distribution av instanser av virtuella datorer på Azure är ett privilegierat konto. Paketet med sudo installerades i den publicerade FreeBSD-avbildningen.
När du har loggat in via det här användarkontot kan du köra kommandon som root med hjälp av kommandosyntaxen.

```
$ sudo <COMMAND>
```

Du kan också hämta ett `sudo -s`rotskal med hjälp av .

## <a name="known-issues"></a>Kända problem
[Azure VM Guest Agent](https://github.com/Azure/WALinuxAgent/) version 2.2.2 har ett känt [problem](https://github.com/Azure/WALinuxAgent/pull/517) som orsakar etableringsfelet för FreeBSD VM på Azure. Korrigeringen fångades av [Azure VM Guest Agent](https://github.com/Azure/WALinuxAgent/) version 2.2.3 och senare versioner. 

## <a name="next-steps"></a>Nästa steg
* Gå till [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112) för att skapa en kostnadsfri virtuell dator för freeBSD.
