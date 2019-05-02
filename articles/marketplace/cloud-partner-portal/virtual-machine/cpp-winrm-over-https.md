---
title: Windows Remote Management via HTTPS för Azure | Azure Marketplace
description: Beskriver hur du konfigurerar en Azure-värdbaserade, Windows-baserad virtuell dator så att den kan hanteras via en fjärranslutning med PowerShell.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: pabutler
ms.openlocfilehash: fb661a2705d437d1f40ceebcad7e759c2a78540f
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938227"
---
# <a name="windows-remote-management-over-https"></a>Windows Remote Management via HTTPS

Det här avsnittet beskrivs hur du konfigurerar en Azure-värdbaserade, Windows-baserad virtuell dator så att den kan hanteras och distribueras via en fjärranslutning med PowerShell.  Om du vill aktivera PowerShell-fjärrkommunikation, måste den Virtuella måldatorn exponera en Windows Remote Management (WinRM) HTTPS-slutpunkt.  Mer information om PowerShell-fjärrkommunikation finns i [köra fjärrkommandon](https://docs.microsoft.com/powershell/scripting/core-powershell/running-remote-commands?view=powershell-6).  Läs mer om WinRM [Windows Remote Management](https://docs.microsoft.com/windows/desktop/WinRM/portal).

Om du har skapat en virtuell dator med någon av ”klassiska” Azure metoder – Azure Service Manager-portalen eller den inaktuella [Azure Service Management API](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))– och sedan konfigureras automatiskt med en WinRM-slutpunkt.  Men om du skapar en virtuell dator med hjälp av någon av följande ”modern” Azure närmar sig sedan den virtuella datorn kommer *inte* konfigureras för WinRM via HTTPS.  

- Med hjälp av den [Azure-portalen](https://portal.azure.com/), vanligtvis från en godkänd basen som beskrivs i avsnittet [skapa en Azure-kompatibel VHD](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Med hjälp av Azure Resource Manager-mallar](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Med hjälp av Azure PowerShell eller Azure CLI-kommandogränssnittet.  Exempel finns i [snabbstarten: Skapa en Windows-dator i Azure med PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) och [snabbstarten: Skapa en Linux-dator med Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Den här slutpunkten för WinRM är också krävs för att köra Certifieringspaket för verktyget för onboarding av den virtuella datorn, enligt beskrivningen i [certifiera din avbildning](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

Däremot vanligtvis virtuella Linux-datorer via fjärranslutning hanteras med hjälp av antingen [Azure CLI](https://docs.microsoft.com/cli/azure) eller Linux-kommandon från en SSH-konsolen.  Azure tillhandahåller också flera alternativa metoder för att [köra skript i din Linux-VM](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  För mer komplicerade scenarier kan finns det ett antal lösningar för automatisering och integrering för Windows - eller Linux-baserade virtuella datorer.


## <a name="configure-and-deploy-with-winrm"></a>Konfigurera och distribuera med WinRM

WinRM-slutpunkten för en windows-baserad virtuell dator kan konfigureras under två olika faser av dess utveckling:

- När du skapar - under distributionen av en virtuell dator till en befintlig virtuell Hårddisk.  Det här är den bästa lösningen för nya erbjudanden.  Den här metoden kräver att skapa ett Azure-certifikat, med hjälp av angivna Azure Resource Manager-mallar och köra anpassade PowerShell-skript. 
- Efter distributionen – på en befintlig virtuell dator på Azure.  Använd den här metoden om du redan har en VM-lösningen distribueras på Azure och måste du aktivera fjärrhantering i fönstret för den.  Den här metoden kräver manuella ändringar i Azure-portalen och körningen av ett skript på den Virtuella måldatorn. 


## <a name="next-steps"></a>Nästa steg
Om du skapar en ny virtuell dator, kan du aktivera WinRM under [distribution av en virtuell dator från dess virtuella hårddiskar](./cpp-deploy-vm-vhd.md).  I annat fall kan WinRM aktiveras i en befintlig virtuell dator  
