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
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 8b0c7bf97592309d68313ef7cc2a919f7aa1c324
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49644972"
---
# <a name="release-notes-for-validation-as-a-service"></a>Viktig information för verifiering som en tjänst

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Den här artikeln innehåller viktig information för Azure Stack-verifiering som en tjänst.

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

    `Install-VaaSPrerequisites` kräver nu att molnet administratörsautentiseringsuppgifter för att åtgärda problem vid Paketverifiering av. I dokumentationen på [ladda ned och installera agenten](azure-stack-vaas-local-agent.md#download-and-install-the-agent) har uppdaterats med följande:

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

    När ett paket för OEM-anpassning skickas som en del av paketet valideringsarbetsflödet kommer formatet paketet att valideras för att säkerställa att det följer specifikationen publicerade. Om paketet är inte kompatibel, misslyckas körningen. E-postmeddelanden skickas till e-postadress på den registrerade Azure Active Directory-kontakten för klienten.

  - Interaktiv test-kategori

    Den **interaktiv** test kategori har lagts till. De här testerna kan partner för att utnyttja interaktiva, icke-automatiskt Azure Stack-scenarier.

  - Interaktiva funktionen verifiering

    Möjligheten att tillhandahålla fokuserade feedback för vissa funktioner är nu tillgängliga i testet arbetsflödet. Den `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` test kontrolleras om specifika uppdateringar har tillämpats korrekt och samlar in feedback.

## <a name="next-steps"></a>Nästa steg

- [Felsöka verifiering som en tjänst](azure-stack-vaas-troubleshoot.md)