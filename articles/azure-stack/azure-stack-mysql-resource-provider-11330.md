---
title: Azure Stack MySQL resource provider 1.1.30.0 viktig information | Microsoft Docs
description: Läs om vad som finns i den senaste Azure Stack MySQL resource provider uppdateringen, inklusive eventuella kända problem och var du hämtar den.
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
ms.date: 01/08/2019
ms.author: jeffgilb
ms.reviewer: georgel
ms.openlocfilehash: a173a21ca3690db4cf5074d067d0e9bbe929e077
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54122354"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>MySQL resource provider 1.1.33.0 viktig information

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Följande versionsinformation innehåller förbättringar och kända problem i MySQL resurs-providerversion 1.1.33.0.

## <a name="build-reference"></a>Skapa referens
Ladda ned MySQL-resursprovidern binära och kör sedan Self-Extractor för att extrahera innehållet till en tillfällig katalog. Resursprovidern har en minsta motsvarande Azure Stack skapa. Den minsta Azure Stack-versionen som krävs för att installera den här versionen av MySQL-resursprovidern som listas nedan:

> |Lägsta version av Azure Stack|MySQL-resurs-providerversion|
> |-----|-----|
> |Version 1811 (1.1811.0.101)|[MySQL RP version 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Tillämpa minsta stöds Azure Stack-uppdatering till integrerade Azure Stack-system eller distribuera den senaste Azure Stack Development Kit (ASDK) innan du distribuerar den senaste versionen av MySQL-resursprovider.

## <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar
Den här versionen av Azure Stack MySQL-resursprovider innehåller följande förbättringar och korrigeringar:

### <a name="fixes"></a>Korrigeringar
- **MySQL resource provider-portaltillägg kan välja fel prenumeration**. MySQL-resursprovider använder Azure Resource Manager-anrop till avgör den första tjänsten administratörsprenumeration ska användas, vilket inte kanske är den *standard Providerprenumeration*. Om detta händer, fungerar MySQL-resursprovider inte normalt. 

- **MySQL värdservern inte innehåller värdbaserad databaser.** Databaser som skapats av användare kan inte visas när du visar klientresurser för MySQL som är värd för servrar.

- **Föregående MySQL resource provider (1.1.30.0) distribution kan misslyckas om TLS 1.2 inte aktiveras**. Uppdatera MySQL-resursprovider 1.1.33.0 för att aktivera TLS 1.2 när du distribuerar resursprovidern, uppdaterar resursprovidern eller roterar hemligheter. 

- **MySQL resource provider hemliga rotation misslyckas**. Problem har åtgärdats vilket resulterar i följande felkod när du roterar hemligheter: ` New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Kända problem 

- **MySQL SKU: er kan ta upp till en timme att bli synliga i portalen**. Det kan ta upp till en timme innan nyligen skapade SKU: er ska vara synlig för användning när du skapar nya MySQL-databaser. 

    **Lösning**: Ingen.

- **Återanvändas MySQL inloggningar**. Försök att skapa en ny MySQL resulterar logga in med samma användarnamn som en befintlig inloggning i samma prenumeration i att återanvända samma inloggning och det befintliga lösenordet. 

    **Lösning**: Använd olika användarnamn när du skapar nya inloggningar i samma prenumeration eller skapa inloggningar med samma användarnamn under olika prenumerationer.

- **Delade MySQL inloggningar orsakar datainkonsekvens**. Om en MySQL-inloggning delas för flera MySQL-databaser i samma prenumeration, orsakar ändra inloggningslösenordet datainkonsekvens.

    **Lösning**: Använd alltid olika inloggningar för olika databaser under samma prenumeration.


### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Kända problem med Cloud administratörerna fungerar Azure Stack
Finns i dokumentationen i den [viktig för Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Nästa steg
[Mer information om MySQL-resursprovider](azure-stack-mysql-resource-provider.md).

[Förbereda för distribution av MySQL-resursprovider](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Uppgradera MySQL-resursprovider från en tidigare version](azure-stack-mysql-resource-provider-update.md). 