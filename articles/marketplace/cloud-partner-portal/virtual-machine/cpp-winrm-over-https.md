---
title: Windows Remote Management via HTTPS för Azure | Azure Marketplace
description: Förklarar hur du konfigurerar en Windows-baserad virtuell dator med Azure-värd så att den kan hanteras på distans med PowerShell.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dsindona
ms.openlocfilehash: b2a4bb107309894a7180e0a4585cdba6f04d1bee
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273043"
---
# <a name="windows-remote-management-over-https"></a>Windows Remote Management via HTTPS

> [!IMPORTANT]
> Från och med den 13 april 2020 börjar vi flytta hanteringen av dina Azure Virtual Machine-erbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Skapa ett Azure Virtual Machine-erbjudande](https://aka.ms/CreateAzureVMoffer) för att hantera dina migrerade erbjudanden.

I det här avsnittet beskrivs hur du konfigurerar en Windows-baserad virtuell dator som är värd för Azure så att den kan hanteras och distribueras på distans med PowerShell.  Om du vill aktivera PowerShell-omvärdering måste måldatorn exponera en HTTPS-slutpunkt (Windows Remote Management) HTTPS.  Mer information om PowerShell-ommotning finns i [Köra fjärrkommandon](https://docs.microsoft.com/powershell/scripting/learn/remoting/running-remote-commands).  Mer information om WinRM finns i [Windows Remote Management](https://docs.microsoft.com/windows/desktop/WinRM/portal).

Om du har skapat en virtuell dator med en av de "klassiska" Azure-metoderna – antingen Azure Service Manager Portal eller det inaktuella [Azure Service Management API–](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))konfigureras den automatiskt med en WinRM-slutpunkt.  Men om du skapar en virtuell dator med någon av följande "moderna" Azure-metoder, kommer din virtuella dator *inte* att konfigureras för WinRM via HTTPS.

- Använda [Azure-portalen](https://portal.azure.com/), vanligtvis från en godkänd bas, enligt beskrivningen i avsnittet [Skapa en Azure-kompatibel VIRTUELLD](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Använda Azure Resource Manager-mallarna](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Använda kommandoskalet Azure PowerShell eller Azure CLI.  Exempel finns i [Snabbstart: Skapa en virtuell Windows-dator i Azure med PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) och [Snabbstart: Skapa en virtuell Linux-dator med Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Den här WinRM-slutpunkten krävs också för att köra certifieringsverktygspaketet för introduktion av den virtuella datorn, enligt beskrivningen i [Certifiera din VM-avbildning](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

Däremot hanteras vanligtvis virtuella Linux-datorer med azure [CLI-](https://docs.microsoft.com/cli/azure) eller Linux-kommandon från en SSH-konsol.  Azure innehåller också flera alternativa metoder för att [köra skript i din Virtuella Linux-dator](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  För mer komplexa scenarier finns det ett antal automatiserings- och integrationslösningar tillgängliga för Windows- eller Linux-baserade virtuella datorer.


## <a name="configure-and-deploy-with-winrm"></a>Konfigurera och distribuera med WinRM

WinRM-slutpunkten för en windowsbaserad virtuell dator kan konfigureras under två olika stadier av dess utveckling:

- Under skapandet - under distributionen av en virtuell dator till en befintlig virtuell hårddisk.  Detta är det bästa tillvägagångssättet för nya erbjudanden.  Den här metoden kräver att ett Azure-certifikat skapas, med hjälp av medföljande Azure Resource Manager-mallar och köra anpassade PowerShell-skript.
- Efter distribution - på en befintlig virtuell dator som finns på Azure.  Använd den här metoden om du redan har en VM-lösning som distribueras på Azure och behöver aktivera Windows Fjärrhantering för den.  Den här metoden kräver manuella ändringar i Azure-portalen och körningen av ett skript på måldatorn.


## <a name="next-steps"></a>Nästa steg
Om du skapar en ny virtuell dator kan du aktivera WinRM under [distributionen av den virtuella datorn från dess virtuella hårddiskar](./cpp-deploy-vm-vhd.md).  Annars kan WinRM aktiveras i en befintlig virtuell dator
