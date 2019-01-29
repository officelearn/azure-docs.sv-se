---
title: Azure Stack-verifiering som en tjänst viktig information | Microsoft Docs
description: Viktig information om Azure Stack-verifiering som en tjänst.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 4fcc38d34dd645ddb62acce4577fa145c517684b
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55097908"
---
# <a name="release-notes-for-validation-as-a-service"></a>Viktig information för verifiering som en tjänst

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Den här artikeln innehåller viktig information för Azure Stack-verifiering som en tjänst.

## <a name="version-405"></a>Version 4.0.5
2019 17 januari

-  Disk identifiering Test har uppdaterats till adress storage pool inkonsekvenser. Version: 5.1.14.0  -> 5.1.15.0
-  Azure Stack månatliga Uppdateringsverifiering uppdateras till adress godkända programvara och innehåll verifiering inkonsekvenser. Version: 5.1.14.0  -> 5.1.15.0
-  Verifiering av OEM-tillägget paketet uppdateras för att du utför nödvändiga kontroller *innan* steg för Azure Stack-uppdatering. Version: 5.1.14.0  -> 5.1.15.0
-  Intern felkorrigeringar



## <a name="version-402"></a>Version 4.0.2

2019 januari 7

Om du kör Azure Stack månatliga uppdatera verifiering arbetsflödet och versionen för din OEM-uppdateringspaketet är inte 1810 eller högre, får du ett fel när du kommer till steg för OEM-uppdatering. Detta är ett fel. En korrigering utvecklas. Åtgärderna är följande:

1.  Kör OEM-uppdateringen som vanligt.
2.  Kör Test-AzureStack efter lyckade tillämpning av paketet och spara utdata.
3.  Avbryta testet.
4.  Skicka sparade utdata till VaaSHelp@microsoft.com att ta emot skicka resultaten för körningen.

## <a name="version-402"></a>Version 4.0.2

2018 November 30

- Intern felkorrigeringar

## <a name="version-401"></a>Version 4.0.1

Den 8 oktober 2018

- VaaS krav

    `Install-VaaSPrerequisites` kräver inte längre administratörsautentiseringsuppgifter för molnet. Om du kör den senaste versionen av denna cmdlet finns i [ladda ned och installera agenten](azure-stack-vaas-local-agent.md#download-and-install-the-agent) för reviderade kommandon för att installera nödvändiga komponenter. Nedan visas kommandona:

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>Version 4.0.0

Den 29 augusti 2018

- VaaS krav och VHD-uppdateringar

    `Install-VaaSPrerequisites` kräver nu att molnet administratörsautentiseringsuppgifter för att åtgärda problem vid verifiering av lösningen. I dokumentationen på [ladda ned och installera agenten](azure-stack-vaas-local-agent.md#download-and-install-the-agent) har uppdaterats med följande:

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential "<cloudAdminDomain\username>", (ConvertTo-SecureString "<cloudAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region `
                              -CloudAdminCredentials $CloudAdminCreds
    ```
    > [!NOTE]
    > Den `$CloudAdminCreds` krävs av skriptet för Azure Stack instans verifieras. De är inte Azure Active Directory-autentiseringsuppgifter som används av VaaS-klient.

- Lokala agentuppdatering

    Den tidigare versionen av den lokala agenten är inte kompatibel med den aktuella 4.0.0 versionen av tjänsten. Alla användare måste uppdatera sina lokala agenter. Se [ladda ned och installera agenten](azure-stack-vaas-local-agent.md#download-and-install-the-agent) anvisningar om hur du installerar den senaste agenten.

- Uppdateringen för automatisering av PowerShell

    Ändringar har gjorts till `LaunchVaaSTests` PowerShell-skript som kräver den senaste versionen av scripting paket. Se [starta testet arbetsflödet](azure-stack-vaas-automate-with-powershell.md#launch-the-test-pass-workflow) anvisningar om hur du installerar den senaste versionen av skriptpaketet.

- Verifiering som en tjänst-Portal

  - Paket som loggar meddelanden

    När ett paket för OEM-anpassning skickas som en del av lösningen valideringsarbetsflödet kommer formatet paketet att valideras för att säkerställa att det följer specifikationen publicerade. Om paketet är inte kompatibel, misslyckas körningen. E-postmeddelanden skickas till e-postadress på den registrerade Azure Active Directory-kontakten för klienten.

  - Interaktiv test-kategori

    Den **interaktiv** test kategori har lagts till. De här testerna kan partner för att utnyttja interaktiva, icke-automatiskt Azure Stack-scenarier.

  - Interaktiva funktionen verifiering

    Möjligheten att tillhandahålla fokuserade feedback för vissa funktioner är nu tillgängliga i testet arbetsflödet. Den `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` test kontrolleras om specifika uppdateringar har tillämpats korrekt och samlar in feedback.

## <a name="next-steps"></a>Nästa steg

- [Felsöka verifiering som en tjänst](azure-stack-vaas-troubleshoot.md)
