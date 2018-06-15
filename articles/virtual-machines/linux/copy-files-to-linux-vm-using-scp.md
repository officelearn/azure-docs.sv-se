---
title: Flytta filer till och från virtuella Azure Linux-datorer med SCP | Microsoft Docs
description: Flytta filer på ett säkert sätt till och från en Linux VM i Azure med hjälp av SCP och en SSH-nyckel.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: danlep
ms.openlocfilehash: 0231e402848e617a46ca70470ba4d3272ace59f7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30904513"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Flytta filer till och från en Linux VM som använder SCP

Den här artikeln visar hur du flyttar filerna från din arbetsstation upp till en Azure Linux-dator eller från en Azure virtuell Linux-dator till din arbetsstation med hjälp av Secure kopia (SCP). Flytta filer mellan din arbetsstation och en Linux VM, snabbt och säkert är viktigt för att hantera dina Azure-infrastrukturen. 

För den här artikeln behöver du Linux VM som distribuerats i Azure med hjälp av [SSH offentliga och privata nyckelfiler](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Du behöver också en SCP-klient för den lokala datorn. Det är byggda på SSH och ingår i Bash standardgränssnittet för de flesta Linux och Mac-datorer och vissa Windows-gränssnitt.

## <a name="quick-commands"></a>Snabbkommandon

Kopiera en fil till Linux VM

```bash
scp file azureuser@azurehost:directory/targetfile
```

Kopiera en fil från Linux VM

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Detaljerad genomgång

Som exempel kan vi flytta en konfigurationsfil för Azure upp till en Linux VM och en loggfilskatalog nedrullningsbara både med SCP och SSH-nycklar.   

## <a name="ssh-key-pair-authentication"></a>SSH-nyckelpar autentisering

SCP använder SSH för transportskiktet. SSH hanterar autentisering på målvärden och flyttas filen i en krypterad som standard med SSH-tunnel. Användarnamn och lösenord kan användas för SSH-autentisering. SSH offentliga och privata nycklar autentisering bör dock som en säkerhetsåtgärd. När anslutningen har autentiserats SSH börjar SCP sedan filen. Med en korrekt konfigurerad `~/.ssh/config` och SSH offentliga och privata nycklar, SCP-anslutning kan upprättas med bara ett servernamn (eller IP-adress). Om du bara har en SSH-nyckeln SCP söker efter den i den `~/.ssh/` directory, och används som standard för att logga in på den virtuella datorn.

Mer information om hur du konfigurerar din `~/.ssh/config` och offentliga och privata nycklar för SSH, se [skapa SSH-nycklar](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>SCP en fil till en Linux-VM

För det första exemplet kopierar vi en Azure-konfigurationsfil upp till en Linux VM som används för att distribuera automation. Säkerhet är viktigt eftersom den här filen innehåller autentiseringsuppgifter för Azure API, bland annat hemligheter. Krypterade tunnel som tillhandahålls av SSH skyddar innehållet i filen.

Följande kommando kopierar du lokalt *.azure/config* filen till en virtuell Azure-dator med FQDN *myserver.eastus.cloudapp.azure.com*. Administratörsanvändarnamnet på Azure VM är *azureuser*. Filen är riktad mot den */home/azureuser/* directory. Ersätt värdena i det här kommandot.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SCP en katalog Linux VM

I det här exemplet kopierar vi en katalog med loggfiler från Linux VM till din arbetsstation. En loggfil kan eller inte kan innehålla känsliga eller hemliga data. Med Tjänstanslutningspunkten innebär dock innehållet i filerna krypteras. Med hjälp av SCP för att överföra filer är det enklaste sättet att hämta loggkatalogen och filerna till din arbetsstation samtidigt säker.

Följande kommando kopierar filer i den */home/azureuser/logs/* på Virtuella Azure till lokala tmp:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

Den `-r` cli flaggan instruerar SCP till rekursivt kopiera filer och kataloger från punkten på katalogen som anges i kommandot.  Observera också att kommandoradssyntaxen liknar en `cp` kopiera kommandot.

## <a name="next-steps"></a>Nästa steg

* [Hantera användare, SSH och kontrollera eller reparera diskar på virtuella Azure Linux-datorer med hjälp av VMAccess-tillägget](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)