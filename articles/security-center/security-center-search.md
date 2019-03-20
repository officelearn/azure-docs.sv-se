---
title: Sök i Azure Security Center | Microsoft Docs
description: Lär dig hur Azure Security Center använder Azure Monitor-loggar search för att hämta och analysera dina säkerhetsdata.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: rkarlin
ms.openlocfilehash: 6cbf3d70bd835ce1b838b19c93507f7d9487a418
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58074513"
---
# <a name="azure-security-center-search"></a>Sök i Azure Security Center
Azure Security Center använder [Azure Monitor loggar search](../log-analytics/log-analytics-log-searches.md) att hämta och analysera dina säkerhetsdata. Azure Monitor-loggar tillhandahåller en frågesyntax för att snabbt hämta och konsolidera data. Du kan använda Azure Monitor-loggar sökning för att skapa frågor och analysera insamlade data från Security Center.

Search är tillgängliga i både den kostnadsfria nivån och standardnivån i Security Center.  Data som är tillgängliga i dina sökningar i loggen är beroende av den nivå som tillämpas på din arbetsyta.  Se de Säkerhetscenter [prissättningssidan](../security-center/security-center-pricing.md) för mer information.


> [!NOTE]
> Security Center sparas inte säkerhetsdata för en arbetsyta under den kostnadsfria nivån. Du kan skicka en mängd loggar till en arbetsyta under den kostnadsfria nivån och söka på dessa data men sökresultaten innehåller inte data från Security Center. Security Center sparar bara data till en arbetsyta under Standard-nivån.
>
>

## <a name="access-search"></a>Åtkomst-sökning
1. Huvudmenyn i Security Center, Välj **Search**.

   ![Välj loggsökning][1]

2. Security Center visar en lista över alla arbetsytor under dina Azure-prenumerationer. Välj en arbetsyta. (Om du har endast en arbetsyta, visas inte den här Väljaren.)

   ![Välj en arbetsyta][2]

3. **Loggsöknings-** öppnas. Om du vill fråga efter mer data under den valda arbetsytan, anger du den här exempelfråga:

   SecurityEvent | Om händelse-ID == 4625 | Sammanfatta antal() efter TargetAccount

   Resultatet visas alla konton som inte gick att logga in (händelse 4625).

   ![Sökresultat][3]

Se [Kusto-frågespråket](../log-analytics/log-analytics-search-reference.md) för mer information om hur du frågar efter data under den valda arbetsytan.

## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig att komma åt Sök i Security Center. Security Center använder search som Azure Monitor-loggar. Mer information om Azure Monitor-loggar search finns:

- [Vad är Azure Monitor-loggar?](../log-analytics/log-analytics-overview.md) – Översikt över Azure Monitor-loggar
- [Förstå log söker i Azure Monitor-loggar](../log-analytics/log-analytics-log-search-new.md) – beskriver hur du använder loggsökningar i Azure Monitor-loggar och tillhandahåller begrepp som du bör känna till innan du skapar en loggsökning
- [Hitta data med hjälp av sökningar i loggen i Azure Monitor-loggar](../log-analytics/log-analytics-log-searches.md) – självstudie om hur du använder loggsökning
- [Kusto-sökreferens](../log-analytics/log-analytics-search-reference.md) – beskriver frågespråket i Azure Monitor-loggar

I följande avsnitt kan du lära dig mer om Security Center:

- [Översikt över Security Center](security-center-intro.md) – beskriver Security Center viktiga funktioner

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
