---
title: Felsöka tjänst anslutning Windows Virtual Desktop – Azure
description: Så här löser du problem när du konfigurerar klient anslutningar i en Windows-klient för virtuella skriv bord.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7bf05fe039de2ab9e25495f9e2652fde8fac34e1
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747704"
---
# <a name="windows-virtual-desktop-service-connections"></a>Anslutningar till virtuella Windows-datorer

>[!IMPORTANT]
>Det här innehållet gäller för våren 2020-uppdateringen med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder den virtuella Windows-datorn med version 2019 utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).
>
> Den virtuella Windows-skrivbordets våren 2020-uppdateringen är för närvarande en offentlig för hands version. Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. 
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Använd den här artikeln för att lösa problem med klient anslutningar för virtuella Windows-datorer.

## <a name="provide-feedback"></a>Ge feedback

Du kan ge oss feedback och diskutera Windows Virtual Desktop-tjänsten med produkt teamet och andra aktiva community-medlemmar i [Tech-communityn för Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Användaren ansluter men inget visas (ingen feed)

En användare kan starta fjärr skrivbords klienter och kan autentisera, men användaren ser inga ikoner i webb identifierings flödet.

Bekräfta att användaren som rapporterar problemen har tilldelats program grupper genom att använda den här kommando raden:

```PowerShell
Get-AzRoleAssignment -SignInName <userupn>
```

Bekräfta att användaren har loggat in med rätt autentiseringsuppgifter.

Om webb klienten används kontrollerar du att det inte finns några problem med cachelagrade autentiseringsuppgifter.

## <a name="next-steps"></a>Nästa steg

- En översikt över fel sökning av virtuella Windows-datorer och eskalerade spår finns i [fel söknings översikt, feedback och support](troubleshoot-set-up-overview.md).
- Om du vill felsöka problem när du skapar en Windows Virtual Desktop-miljö och en adresspool i en Windows Virtual Desktop-miljö kan du läsa mer i [miljö-och värd bassäng](troubleshoot-set-up-issues.md).
- Information om hur du felsöker problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [konfiguration av Session Host-dator](troubleshoot-vm-configuration.md).
- Information om hur du felsöker problem när du använder PowerShell med Windows Virtual Desktop finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Information om hur du går igenom en fel söknings kurs finns i [Självstudier: Felsöka distributioner av Resource Manager-mallar](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
