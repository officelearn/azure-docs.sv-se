---
title: "Sök i Azure Security Center | Microsoft Docs"
description: "Lär dig hur Azure Security Center använder logganalys-sökning för att hämta och analysera säkerhetsdata med."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 513c98237a322dabd6b2bf13443e8998ca843b1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-search"></a>Sök i Azure Security Center
Azure Security Center använder [logganalys söka](../log-analytics/log-analytics-log-searches.md) att hämta och analysera säkerhetsdata med. Logganalys innehåller ett frågespråk för att snabbt hämta och konsolidera data. Du kan utnyttja logganalys-sökning för att skapa frågor och analysera data som samlats in från Security Center.

Sökningen är tillgängliga i både kostnadsfri nivå och Standardnivå av Security Center.  Data som är tillgängliga i loggen-sökningar är beroende av den nivå som tillämpas på arbetsytan.  Se Security Center [sida med priser](../security-center/security-center-pricing.md) för mer information.


> [!NOTE]
> Security Center sparas inte säkerhetsdata för en arbetsyta under den kostnadsfria nivån. Du kan skicka en mängd olika loggar till en arbetsyta under den kostnadsfria nivån och söka på dessa data men sökresultaten innehåller inte data från Security Center. Security Center sparar bara data till en arbetsyta under standardnivån.
>
>

## <a name="access-search"></a>Åtkomst-sökning
1. Välj under huvudmenyn Security Center **Sök**.

  ![Välj loggen sökning][1]

2. Security Center visas alla arbetsytor under dina Azure-prenumerationer. Välj en arbetsyta. (Om du har en enda arbetsyta, visas inte den här arbetsytan selector.)

  ![Välj en arbetsyta][2]

3. **Logga Sök** öppnas. Ange den här exempelfråga för att fråga efter mer data under den valda arbetsytan:

  SecurityEvent | Om händelse-ID == 4625 | Sammanfatta count() av TargetAccount

  Resultatet visas alla konton som gick inte att logga in (händelse 4625).

  ![Sökresultat][3]

Se [Log Analytics-frågespråket](../log-analytics/log-analytics-search-reference.md) för mer information om hur du fråga efter data under den valda arbetsytan.

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du öppnar sökfunktionen i Security Center. Security Center använder logganalys sökning. Mer information om logganalys search finns:

- [Vad är Log Analytics?](../log-analytics/log-analytics-overview.md) – Översikt på logganalys
- [Förstå loggen söker i logganalys](../log-analytics/log-analytics-log-search-new.md) – beskriver hur loggen sökningar används i logganalys och begrepp som du bör känna till innan du skapar en logg-sökning
- [Hitta data med hjälp av loggen sökningar i logganalys](../log-analytics/log-analytics-log-searches.md) – självstudiekurs på loggen sökning
- [Logganalys söka referens](../log-analytics/log-analytics-search-reference.md) – beskriver frågespråket i logganalys

Mer information om Security Center finns:

- [Översikt över Security Center](security-center-intro.md) – beskriver Security Center viktiga funktioner

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
