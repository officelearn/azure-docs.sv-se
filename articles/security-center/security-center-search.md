---
title: Sök i Azure Security Center | Microsoft Docs
description: Lär dig hur Azure Security Center använder Log Analytics-sökningen för att hämta och analysera dina säkerhetsdata.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: ec8c11dcea825dfaab19643e7aefc68a82542fa9
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301539"
---
# <a name="azure-security-center-search"></a>Sök i Azure Security Center
Azure Security Center använder [Log Analytics Sök](../log-analytics/log-analytics-log-searches.md) att hämta och analysera dina säkerhetsdata. Log Analytics tillhandahåller en frågesyntax för att snabbt hämta och konsolidera data. Du kan använda Log Analytics-sökningen för att skapa frågor och analysera insamlade data från Security Center.

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

Se [Log Analytics-frågespråket](../log-analytics/log-analytics-search-reference.md) för mer information om hur du frågar efter data under den valda arbetsytan.

## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig att komma åt Sök i Security Center. Security Center använder Log Analytics-sökningen. Mer information om Log Analytics search finns:

- [Vad är Log Analytics?](../log-analytics/log-analytics-overview.md) – Översikt på Log Analytics
- [Förstå log söker i Log Analytics](../log-analytics/log-analytics-log-search-new.md) – beskriver hur du använder loggsökningar i Log Analytics och tillhandahåller begrepp som du bör känna till innan du skapar en loggsökning
- [Hitta data med loggsökningar i Log Analytics](../log-analytics/log-analytics-log-searches.md) – självstudie om hur du använder loggsökning
- [Log Analytics Sök referens](../log-analytics/log-analytics-search-reference.md) – beskriver frågespråket i Log Analytics

Mer information om Security Center finns:

- [Översikt över Security Center](security-center-intro.md) – beskriver Security Center viktiga funktioner

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
