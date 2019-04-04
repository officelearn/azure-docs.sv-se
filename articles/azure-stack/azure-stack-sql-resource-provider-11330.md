---
title: Azure Stack SQL resource provider 1.1.30.0 viktig information | Microsoft Docs
description: Läs om vad som finns i den senaste Azure Stack resource provider uppdatering för SQL, inklusive eventuella kända problem och var du hämtar den.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: ae5a824fbd96a9a76eb18811a46bfc17afa15073
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879360"
---
# <a name="sql-resource-provider-11330-release-notes"></a>SQL resource provider 1.1.33.0 viktig information

*Gäller för Integrerade Azure Stack-system och Azure Stack Development Kit*

Följande versionsinformation innehåller förbättringar och kända problem i SQL resurs-providerversion 1.1.33.0.

## <a name="build-reference"></a>Skapa referens
Ladda ned den SQL-resursprovidern binära och kör sedan Self-Extractor för att extrahera innehållet till en tillfällig katalog. Resursprovidern har en minsta motsvarande Azure Stack skapa. Den minsta Azure Stack-versionen som krävs för att installera den här versionen av SQL-resursprovider som listas nedan:

> |Lägsta version av Azure Stack|SQL-resurs-providerversion|
> |-----|-----|
> |Version 1808 (1.1808.0.97)|[SQL RP version 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Tillämpa minsta stöds Azure Stack-uppdatering till integrerade Azure Stack-system eller distribuera den senaste Azure Stack Development Kit (ASDK) innan du distribuerar den senaste versionen av SQL-resursprovider.

## <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar
Den här versionen av Azure Stack SQL-resursprovider innehåller följande förbättringar och korrigeringar:

### <a name="fixes"></a>Korrigeringar
- **SQL resource provider-portaltillägg kan välja fel prenumeration**. SQL-resursprovider använder Azure Resource Manager-anrop till avgör den första tjänsten administratörsprenumeration ska användas, vilket inte kanske är den *standard Providerprenumeration*. Om detta händer, fungerar SQL-resursprovider inte normalt. 

- **SQL-värdserver innehåller inte värdbaserade databaser.** Databaser som skapats av användare kan inte visas när du visar klientresurser för SQL som är värd för servrar.

- **Tidigare SQL provider (1.1.30.0) resursdistributionen kan misslyckas om TLS 1.2 inte aktiveras**. Uppdatera SQL-resursprovider 1.1.33.0 för att aktivera TLS 1.2 när du distribuerar resursprovidern, uppdaterar resursprovidern eller roterar hemligheter. 

- **SQL resource provider hemliga rotation misslyckas**. Problem har åtgärdats vilket resulterar i följande felkod när du roterar hemligheter:
`New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Kända problem 

- **SQL-SKU: er kan ta upp till en timme att bli synliga i portalen**. Det kan ta upp till en timme innan nyligen skapade SKU: er ska vara synlig för användning när du skapar nya SQL-databaser. 

    **Lösning**: Ingen.

- **Återanvändas SQL-inloggningar**. Försök att skapa en ny SQL resulterar logga in med samma användarnamn som en befintlig inloggning i samma prenumeration i att återanvända samma inloggning och det befintliga lösenordet. 

    **Lösning**: Använd olika användarnamn när du skapar nya inloggningar i samma prenumeration eller skapa inloggningar med samma användarnamn under olika prenumerationer.

- **Delade SQL-inloggningar orsakar datainkonsekvens**. Om en SQL-inloggning delas för flera SQL-databaser i samma prenumeration, orsakar ändra inloggningslösenordet datainkonsekvens.

    **Lösning**: Använd alltid olika inloggningar för olika databaser under samma prenumeration.

- **SQL-resursprovider inte kan lägga till SQL Server Always On-lyssnaren**. När du använder den IP-adressen för lyssnaren på SQL Server alltid på lyssnare, inte kan SQL-resursprovider virtuell dator lösa den lyssnaren värdnamn.

    **Lösning**: Kontrollera att DNS fungerar korrekt för att lösa IP-Adressen för lyssnaren till lyssnaren värdnamn.

### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Kända problem med Cloud administratörerna fungerar Azure Stack
Finns i dokumentationen i den [viktig för Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Nästa steg
[Mer information om SQL-resursprovider](azure-stack-sql-resource-provider.md).

[Förbereda för distribution av SQL-resursprovider](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Uppgradera SQL-resursprovider från en tidigare version](azure-stack-sql-resource-provider-update.md). 