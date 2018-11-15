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
ms.date: 11/14/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 6354212eb95fbefb217dd5339613d050da55f4ba
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688157"
---
# <a name="mysql-resource-provider-11300--release-notes"></a>MySQL resource provider 1.1.30.0 viktig information

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Följande versionsinformation innehåller förbättringar och kända problem i MySQL resurs-providerversion 1.1.30.0.

## <a name="build-reference"></a>Skapa referens
Ladda ned MySQL-resursprovidern binära och kör sedan Self-Extractor för att extrahera innehållet till en tillfällig katalog. Resursprovidern har en minsta motsvarande Azure Stack skapa. Den minsta Azure Stack-versionen som krävs för att installera den här versionen av MySQL-resursprovidern som listas nedan:

> |Lägsta version av Azure Stack|MySQL-resurs-providerversion|
> |-----|-----|
> |Uppdatering av Azure Stack 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Tillämpa minsta stöds Azure Stack-uppdatering till integrerade Azure Stack-system eller distribuera den senaste Azure Stack Development Kit (ASDK) innan du distribuerar den senaste versionen av MySQL-resursprovider.

## <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar
Den här versionen av Azure Stack MySQL-resursprovider innehåller följande förbättringar och korrigeringar:

- **Telemetri aktiverat för MySQL resource provider distributioner**. Insamling av enhetstelemetri har aktiverats för MySQL resource provider-distributioner. Telemetri som samlas in omfattar resource provider distribution, starta och stoppa gånger, avsluta status, avsluta meddelanden och felinformation (om tillämpligt).

- **TLS 1.2-kryptering uppdatering**. Aktiverade TLS 1.2 endast stöd för resource provider kommunikation med interna Azure Stack-komponenter. 

### <a name="fixes"></a>Korrigeringar

- **MySQL resource provider Azure Stack PowerShell kompatibiliteten**. MySQL-resursprovider har uppdaterats till arbetet med Azure Stack 2018-03-01-hybrid PowerShell-profilen och för att ge kompatibilitet med AzureRM 1.3.0 och senare.

- **MySQL-inloggning ändra lösenord-bladet**. Ett problem har åtgärdats där lösenordet kan inte ändras på bladet ändra lösenordet. Borttagna länkar från lösenord ändringsmeddelanden.

## <a name="known-issues"></a>Kända problem 

- **MySQL SKU: er kan ta upp till en timme att bli synliga i portalen**. Det kan ta upp till en timme innan nyligen skapade SKU: er ska vara synlig för användning när du skapar nya MySQL-databaser. 

    **Lösning**: Ingen.

- **Återanvändas MySQL inloggningar**. Försök att skapa en ny MySQL resulterar logga in med samma användarnamn som en befintlig inloggning i samma prenumeration i att återanvända samma inloggning och det befintliga lösenordet. 

    **Lösning**: använda olika användarnamn när du skapar nya inloggningar i samma prenumeration eller skapa inloggningar med samma användarnamn under olika prenumerationer.


### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Kända problem med Cloud administratörerna fungerar Azure Stack
Finns i dokumentationen i den [viktig för Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Nästa steg
[Mer information om MySQL-resursprovider](azure-stack-mysql-resource-provider.md).

[Förbereda för distribution av MySQL-resursprovider](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Uppgradera MySQL-resursprovider från en tidigare version](azure-stack-mysql-resource-provider-update.md). 