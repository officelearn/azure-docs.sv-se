---
title: Flytta filer till och från virtuella Linux-datorer med SCP | Microsoft Docs
description: Flytta filer på ett säkert sätt till och från en Linux-VM i Azure med hjälp av SCP och en SSH-nyckelpar.
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
ms.subservice: disks
ms.openlocfilehash: 7d5b2d2ee7e7320fb8bf91c8a62a0f46c403c977
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55459561"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Flytta filer till och från en Linux VM med hjälp av SCP

Den här artikeln visar hur du flytta filer från din arbetsstation upp till en virtuell Linux-dator eller från en virtuell Linux-dator till din arbetsstation, med hjälp av SCP (Secure Copy). Flytta filer mellan din arbetsstation och en Linux-VM, snabbt och säkert, är viktigt för att hantera dina Azure-infrastrukturen. 

För den här artikeln behöver du en virtuell Linux-dator som har distribuerats i Azure med hjälp av [SSH offentliga och privata nyckelfiler](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Du behöver också en SCP-klient för din lokala dator. Det är byggt på SSH och ingår i standard Bash-gränssnitt för de flesta Linux och Mac-datorer och vissa Windows-gränssnitt.

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

Som exempel kan vi flytta en Azure-konfigurationsfil upp till en Linux VM och nedrullningsbara en loggfilskatalog båda använder SCP och SSH-nycklar.   

## <a name="ssh-key-pair-authentication"></a>SSH-nyckelpar-autentisering

SCP använder SSH för transportlagret. SSH hanterar autentisering på målvärden och flyttas till filen i en krypterad tunnel som tillhandahålls som standard med SSH. Användarnamn och lösenord kan användas för SSH-autentisering. SSH offentliga och privata nyckelautentisering rekommenderas dock som en säkerhetsåtgärd. När anslutningen har autentiserats SSH börjar SCP sedan kopiera filen. Med hjälp av en korrekt konfigurerad `~/.ssh/config` och SSH-nycklar för offentliga och privata, SCP-anslutning kan upprättas genom att bara använda ett servernamn (eller IP-adress). Om du bara har en SSH-nyckel SCP söker efter den i den `~/.ssh/` directory, och används som standard för att logga in på den virtuella datorn.

Mer information om hur du konfigurerar din `~/.ssh/config` och offentliga och privata nycklar för SSH, se [skapa SSH-nycklar](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>SCP en fil till en Linux VM

För det första exemplet kopierar vi en Azure-konfigurationsfil upp till en Linux VM som används för att distribuera automation. Säkerhet är viktigt eftersom den här filen innehåller Azure API-autentiseringsuppgifter, bland annat hemligheter. Krypterad tunnel som tillhandahålls av SSH skyddar innehållet i filen.

I följande kopieras lokalt *.azure/config* filen till en Azure-dator med FQDN *myserver.eastus.cloudapp.azure.com*. Ett administratörsanvändarnamn för virtuella Azure-datorn är *azureuser*. Filen är riktad mot den */home/azureuser/* directory. Ange egna värden i det här kommandot.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SCP en katalog från en Linux VM

I det här exemplet kopierar vi en katalog med loggfiler från Linux-VM till din arbetsstation. En loggfil kan eller inte kan innehålla känsliga eller hemliga data. Med hjälp av SCP innebär dock innehållet i filerna krypteras. Med hjälp av SCP för att överföra filer är det enklaste sättet att hämta loggkatalogen och filerna till din arbetsstation när du också är säker.

I följande kopieras filer i den */home/azureuser/logs/* katalogen på Azure-VM till lokala tmp:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

Den `-r` cli-flaggan instruerar SCP till rekursivt kopiera filer och kataloger från punkten på katalogen som anges i kommandot.  Observera också att kommandoradssyntaxen liknar en `cp` kopiera kommandot.

## <a name="next-steps"></a>Nästa steg

* [Hantera användare, SSH och kontrollera eller reparera diskar på virtuella Linux-datorer med hjälp av VMAccess-tillägget](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
