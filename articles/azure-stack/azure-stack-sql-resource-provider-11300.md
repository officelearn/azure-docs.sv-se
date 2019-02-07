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
ms.date: 1/09/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 1/09/2019
ms.openlocfilehash: aaa4dc48f237a4647cfde330e40eda91eeb1388d
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768740"
---
# <a name="sql-resource-provider-11300-release-notes"></a>SQL resource provider 1.1.30.0 viktig information

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Följande versionsinformation innehåller förbättringar och kända problem i SQL resurs-providerversion 1.1.30.0.

## <a name="build-reference"></a>Skapa referens
Ladda ned den SQL-resursprovidern binära och kör sedan Self-Extractor för att extrahera innehållet till en tillfällig katalog. Resursprovidern har en minsta motsvarande Azure Stack skapa. Den minsta Azure Stack-versionen som krävs för att installera den här versionen av SQL-resursprovider som listas nedan:

> |Lägsta version av Azure Stack|SQL-resurs-providerversion|
> |-----|-----|
> |Version 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Tillämpa minsta stöds Azure Stack-uppdatering till integrerade Azure Stack-system eller distribuera den senaste Azure Stack Development Kit (ASDK) innan du distribuerar den senaste versionen av SQL-resursprovider.

## <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar
Den här versionen av Azure Stack SQL-resursprovider innehåller följande förbättringar och korrigeringar:

- **Telemetri aktiverat för SQL resource provider distributioner**. Insamling av enhetstelemetri har aktiverats för SQL resource provider-distributioner. Telemetri som samlas in omfattar resource provider distribution, starta och stoppa gånger, avsluta status, avsluta meddelanden och felinformation (om tillämpligt).

- **TLS 1.2-kryptering uppdatering**. Aktiverade TLS 1.2 endast stöd för resource provider kommunikation med interna Azure Stack-komponenter. 

### <a name="fixes"></a>Korrigeringar

- **SQL resource provider Azure Stack PowerShell kompatibiliteten**. SQL-resursprovider har uppdaterats till arbetet med Azure Stack 2018-03-01-hybrid PowerShell-profilen och för att ge kompatibilitet med AzureRM 1.3.0 och senare.

- **SQL-inloggning ändra lösenord-bladet**. Ett problem har åtgärdats där lösenordet kan inte ändras på bladet ändra lösenordet. Borttagna länkar från lösenord ändringsmeddelanden.

- **SQL som är värd för serveruppdateringen inställningar bladet**. Ett problem har åtgärdats där inställningsbladet felaktigt benämnt som ”lösenord”.

## <a name="known-issues"></a>Kända problem 

- **SQL-SKU: er kan ta upp till en timme att bli synliga i portalen**. Det kan ta upp till en timme innan nyligen skapade SKU: er ska vara synlig för användning när du skapar nya SQL-databaser. 

    **Lösning**: Ingen.

- **Återanvändas SQL-inloggningar**. Försök att skapa en ny SQL resulterar logga in med samma användarnamn som en befintlig inloggning i samma prenumeration i att återanvända samma inloggning och det befintliga lösenordet. 

    **Lösning**: Använd olika användarnamn när du skapar nya inloggningar i samma prenumeration eller skapa inloggningar med samma användarnamn under olika prenumerationer.

- **Delade SQL-inloggningar orsakar datainkonsekvens**. Om en SQL-inloggning delas för flera SQL-databaser i samma prenumeration, orsakar ändra inloggningslösenordet datainkonsekvens.

    **Lösning**: Använd alltid olika inloggningar för olika databaser under samma prenumeration.

- **TLS 1.2 supportkraven**. Om du försöker distribuera eller uppdatera SQL-resursprovider från en dator där TLS 1.2 inte har aktiverats, misslyckas åtgärden. Kör följande PowerShell-kommando på datorn som används för att distribuera om eller uppdatera resursprovidern för att kontrollera att TLS 1.2 returneras som stöds:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Om **Tls12** är inte ingår i resultatet av kommandot, TLS 1.2 är inte aktiverat på datorn.

    **Lösning**: Kör följande PowerShell-kommando för att aktivera TLS 1.2 och sedan starta resource provider-distribution eller uppdatera skriptet från samma PowerShell-session:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
- **SQL-resursprovider inte kan lägga till SQL Server Always On-lyssnaren**. När du använder den IP-adressen för lyssnaren på SQL Server alltid på lyssnare, inte kan SQL-resursprovider virtuell dator lösa den lyssnaren värdnamn.

    **Lösning**: Kontrollera att DNS fungerar korrekt för att lösa IP-Adressen för lyssnaren till lyssnaren värdnamn.
    
### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Kända problem med Cloud administratörerna fungerar Azure Stack
Finns i dokumentationen i den [viktig för Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Nästa steg
[Mer information om SQL-resursprovider](azure-stack-sql-resource-provider.md).

[Förbereda för distribution av SQL-resursprovider](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Uppgradera SQL-resursprovider från en tidigare version](azure-stack-sql-resource-provider-update.md). 