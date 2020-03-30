---
title: Flytta filer till och från virtuella Azure Linux-datorer med SCP
description: Flytta filer till och från en Virtuell Linux-dator på ett säkert sätt med SCP och ett SSH-nyckelpar.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.topic: article
ms.date: 07/12/2017
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: a0837790b70de42073338bf085ee0f3976b866f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969610"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Flytta filer till och från en Virtuell Linux-dator med SCP

Den här artikeln visar hur du flyttar filer från din arbetsstation upp till en Virtuell Azure Linux-dator eller från en Virtuell Azure Linux-dator till din arbetsstation med Secure Copy (SCP). Att flytta filer mellan din arbetsstation och en virtuell Linux-dator, snabbt och säkert, är avgörande för att hantera din Azure-infrastruktur. 

I den här artikeln behöver du en Virtuell Linux-dator som distribueras i Azure med [SSH-offentliga och privata nyckelfiler](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Du behöver också en SCP-klient för den lokala datorn. Den är byggd ovanpå SSH och ingår i standard Bash skal av de flesta Linux och Mac-datorer och vissa Windows-skal.

## <a name="quick-commands"></a>Snabbkommandon

Kopiera en fil upp till Linux-vm

```bash
scp file azureuser@azurehost:directory/targetfile
```

Kopiera en fil ned från Linux-vm

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Detaljerad genomgång

Som exempel flyttar vi en Azure-konfigurationsfil upp till en Virtuell Linux-dator och drar ner en loggfilskatalog, både med SCP- och SSH-nycklar.   

## <a name="ssh-key-pair-authentication"></a>SSH-autentisering av nyckelpar

SCP använder SSH för transportskiktet. SSH hanterar autentiseringen på målvärden och flyttar filen i en krypterad tunnel som tillhandahålls som standard med SSH. För SSH-autentisering kan användarnamn och lösenord användas. SSH-autentisering av offentliga och privata nyckeler rekommenderas dock som en säkerhetspraxis. När SSH har autentiserat anslutningen börjar SCP sedan kopiera filen. Med hjälp av `~/.ssh/config` en korrekt konfigurerad och SSH offentliga och privata nycklar, kan SCP-anslutningen upprättas genom att bara använda ett servernamn (eller IP-adress). Om du bara har en SSH-nyckel letar `~/.ssh/` SCP efter den i katalogen och använder den som standard för att logga in på den virtuella datorn.

Mer information om hur `~/.ssh/config` du konfigurerar offentliga och privata nycklar för din och SSH finns i [Skapa SSH-nycklar](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>SCP en fil till en Virtuell Linux-dator

I det första exemplet kopierar vi en Azure-konfigurationsfil upp till en Virtuell Linux-dator som används för att distribuera automatisering. Eftersom den här filen innehåller Azure API-autentiseringsuppgifter, som innehåller hemligheter, är säkerhet viktigt. Den krypterade tunneln som tillhandahålls av SSH skyddar innehållet i filen.

Följande kommando kopierar den lokala *AZURE/config-filen* till en Virtuell Azure-dator med FQDN *myserver.eastus.cloudapp.azure.com*. Administratörsanvändarnamnet på den virtuella Azure-datorn är *azureuser*. Filen är riktad till katalogen */home/azureuser/.* Ersätt dina egna värden i det här kommandot.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SCP en katalog från en Virtuell Linux-dator

I det här exemplet kopierar vi en katalog med loggfiler från Linux VM ner till din arbetsstation. En loggfil kan innehålla känsliga eller hemliga data eller inte. Med hjälp av SCP säkerställs dock att innehållet i loggfilerna krypteras. Använda SCP för att överföra filerna är det enklaste sättet att få loggkatalogen och filer ner till din arbetsstation samtidigt som du är säker.

Följande kommando kopierar filer i katalogen */home/azureuser/logs/* på Azure VM till den lokala /tmp-katalogen:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

Flaggan `-r` instruerar SCP att rekursivt kopiera filer och kataloger från den punkt i katalogen som anges i kommandot.  Observera också att kommandoradssyntaxen `cp` liknar ett kopieringskommando.

## <a name="next-steps"></a>Nästa steg

* [Hantera användare, SSH och kontrollera eller reparera diskar på virtuella Azure Linux-datorer med VMAccess-tillägget](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
