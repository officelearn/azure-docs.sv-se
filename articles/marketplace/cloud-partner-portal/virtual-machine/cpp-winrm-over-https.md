---
title: Windows Remote Management via HTTPS för Azure | Azure Marketplace
description: Förklarar hur du konfigurerar en Windows-baserad virtuell dator med Azure som värd, så att den kan fjärrhanteras med PowerShell.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dsindona
ms.openlocfilehash: 88941b334a9c218365bd2d97046dbb6c32d50f35
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142755"
---
# <a name="windows-remote-management-over-https"></a>Windows Remote Management via HTTPS

> [!IMPORTANT]
> Med början den 13 april 2020 kommer vi att börja flytta hanteringen av dina virtuella Azure-datorer till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ instruktionerna i [skapa ett erbjudande för virtuell Azure-dator](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) för att hantera dina migrerade erbjudanden.

I det här avsnittet beskrivs hur du konfigurerar en Windows-baserad virtuell dator med Azure som värd, så att den kan hanteras och distribueras via fjärr anslutning med PowerShell.  Om du vill aktivera PowerShell-fjärrkommunikation måste den virtuella mål datorn exponera en HTTPS-slutpunkt för Windows Remote Management (WinRM).  Mer information om PowerShell-fjärrkommunikation finns i [köra fjärrkommandon](https://docs.microsoft.com/powershell/scripting/learn/remoting/running-remote-commands).  Mer information om WinRM finns i [Windows Remote Management](https://docs.microsoft.com/windows/desktop/WinRM/portal).

Om du har skapat en virtuell dator med någon av de klassiska Azure-metoderna, antingen Azure Service Manager-portalen eller det inaktuella [Azure-Service Management-API](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100)), konfigureras den automatiskt med en WinRM-slutpunkt.  Men om du skapar en virtuell dator med någon av följande "moderna" Azure-metoder kommer din virtuella dator *inte* att konfigureras för WINRM över https.

- Använd [Azure Portal](https://portal.azure.com/), vanligt vis från en godkänd bas, enligt beskrivningen i avsnittet [skapa en Azure-kompatibel virtuell hård disk](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Använda Azure Resource Manager mallar](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Använd antingen Azure PowerShell-eller Azure CLI-kommando tolken.  Exempel finns i [snabb start: skapa en virtuell Windows-dator i Azure med PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) och [snabb start: skapa en virtuell Linux-dator med Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Den här WinRM-slutpunkten krävs också för att köra Certificate Tool Kit för att registrera den virtuella datorn, enligt beskrivningen i [certifiera din virtuella dator avbildning](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

Vanligt vis fjärrhanteras virtuella Linux-datorer via antingen [Azure CLI](https://docs.microsoft.com/cli/azure) -eller Linux-kommandon från en SSH-konsol.  Azure innehåller också flera alternativa metoder för att [köra skript i din virtuella Linux-dator](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  För mer komplexa scenarier finns det ett antal Automation-och integrations lösningar som är tillgängliga för Windows-eller Linux-baserade virtuella datorer.


## <a name="configure-and-deploy-with-winrm"></a>Konfigurera och distribuera med WinRM

WinRM-slutpunkten för en Windows-baserad virtuell dator kan konfigureras under två olika steg i utvecklingen:

- Under skapandet – under distributionen av en virtuell dator till en befintlig virtuell hård disk.  Detta är det bästa sättet för nya erbjudanden.  Den här metoden kräver att ett Azure-certifikat skapas, med hjälp av angivna Azure Resource Manager mallar och att du kör anpassade PowerShell-skript.
- Efter distributionen – på en befintlig virtuell dator som finns på Azure.  Använd den här metoden om du redan har distribuerat en VM-lösning på Azure och behöver aktivera fjärrhantering av fönster för den.  Den här metoden kräver manuella ändringar i Azure Portal och körning av ett skript på den virtuella mål datorn.


## <a name="next-steps"></a>Nästa steg
Om du skapar en ny virtuell dator kan du aktivera WinRM under [distributionen av den virtuella datorn från dess virtuella hård diskar](./cpp-deploy-vm-vhd.md).  Annars kan WinRM aktive ras i en befintlig virtuell dator
