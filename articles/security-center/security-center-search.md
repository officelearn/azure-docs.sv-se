---
title: Azure Security Center Sök | Microsoft Docs
description: Lär dig hur Azure Security Center använder Azure Monitor loggar Sök för att hämta och analysera säkerhets data.
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
ms.openlocfilehash: c88e2025444aa2fd83e05fdff97ea640ceefaa04
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662917"
---
# <a name="azure-security-center-search-retired"></a>Azure Security Center Sök (dras tillbaka)

> [!NOTE]
> Security Center Sök instrument panelen har dragits tillbaka den 31 juli 2019. Mer information och alternativa tjänster finns i [dra tillbaka Security Center funktioner (2019 juli)](security-center-features-retirement-july2019.md#menu_search).

Azure Security Center använder [Azure Monitor loggar Sök](../log-analytics/log-analytics-log-searches.md) för att hämta och analysera säkerhets data. Azure Monitor-loggar innehåller ett frågespråk för att snabbt hämta och konsolidera data. Från Security Center kan du utnyttja Azure Monitor loggar Sök för att skapa frågor och analysera insamlade data.

Sök är tillgängligt på både den kostnads fria nivån och standard nivån för Security Center.  Vilka data som är tillgängliga i logg sökningen beror på vilken nivå nivå som används på din arbets yta.  Mer information finns på sidan med Security Center [prissättning](../security-center/security-center-pricing.md) .


> [!NOTE]
> Security Center sparar inte säkerhets data för en arbets yta under den kostnads fria nivån. Du kan skicka flera olika loggar till en arbets yta under den kostnads fria nivån och söka efter dessa data, men Sök resultaten omfattar inte data från Security Center. Security Center sparar bara data till en arbets yta under standard nivån.
>
>

## <a name="access-search"></a>Åtkomsts ökning
1. Under Security Center huvud menyn väljer du **Sök**.

   ![Välj loggs ökning][1]

2. Security Center visar en lista över alla arbets ytor under dina Azure-prenumerationer. Välj en arbetsyta. (Om du bara har en arbets yta visas inte den här arbets ytans väljare.)

   ![Välj en arbetsyta][2]

3. **Loggs ökningen** öppnas. Om du vill fråga efter mer data under den valda arbets ytan anger du den här exempel frågan:

   SecurityEvent | där EventID = = 4625 | sammanfatta antal () efter TargetAccount

   Resultatet visar alla konton som inte kunde logga in (händelse 4625).

   ![Sökresultat][3]

Mer information om hur du frågar efter data på den valda arbets ytan finns i [Kusto frågespråk](../log-analytics/log-analytics-search-reference.md) .

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du kommer åt sökning i Security Center. Security Center använder Azure Monitor loggar Sök. Mer information om Sök i Azure Monitor loggar finns i:

- [Vad är Azure Monitor loggar?](../log-analytics/log-analytics-overview.md) – Översikt över Azure Monitor loggar
- [Förstå loggs ökningar i Azure Monitor loggar](../log-analytics/log-analytics-log-search-new.md) – beskriver hur loggs ökningar används i Azure Monitor loggar och ger begrepp som bör förstås innan en loggs ökning skapas
- [Hitta data med loggs ökningar i Azure Monitor loggar](../log-analytics/log-analytics-log-searches.md) – självstudier om att använda loggs ökning
- [Sök referens för Kusto](../log-analytics/log-analytics-search-reference.md) – beskriver frågespråket i Azure Monitor loggar

I följande avsnitt kan du lära dig mer om Security Center:

- [Översikt över Security Center](security-center-intro.md) – beskriver viktiga funktioner i Security Center

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
