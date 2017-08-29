---
title: "Utvärdering av webbaslinjen i säkerhets- och granskningslösningen i Operations Management Suite | Microsoft Docs"
description: "Det här dokumentet beskriver hur du utvärderar webbaslinjen i säkerhets- och granskningslösningen i OMS för att utvärdera säkerhetsbaslinjen och upprätthålla efterlevnaden och säkerheten på alla övervakade webbservrar."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 40b0c6ca933ea02ac9f5fe3bfaaf87a310542a8d
ms.contentlocale: sv-se
ms.lasthandoff: 08/19/2017

---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Utvärdering av säkerhetswebbaslinjen i säkerhets- och granskningslösningen i Operations Management Suite
Det här dokumentet beskriver hur du använder funktionerna för utvärdering av webbaslinjen i säkerhets- och granskningslösningen i OMS för att upprätthålla säkerheten på övervakade resurser.

## <a name="what-is-web-baseline-assessment"></a>Vad är utvärdering av webbaslinjen?
För närvarande tillhandahåller säkerheten i OMS utvärdering av säkerhetsbaslinje för operativsystem. Funktionen söker igenom operativsystemsinställningarna för dina servrar var 24:e timme och identifierar potentiellt sårbara inställningar. Läs [bedömning av baslinjen i Operations Management Suite säkerhet och granska lösningen](https://docs.microsoft.com/azure/operations-management-suite/oms-security-baseline) för mer information om det här.

Målet med utvärderingen av webbaslinjen är att hitta potentiellt sårbara webbserverinställningar. De tre primära källorna för konfiguration av webbaslinje är: .NET, ASP.NET och IIS.  Precis som baslinjeutvärderingen för operativsystemet genomsöker OMS-säkerheten dina webbservrar var 24:e timme och ger en översikt över potentiellt sårbara inställningar deras säkerhetsstatus.  I Internet Information Service (IIS) är konfigurationer mycket anpassningsbara, vilket gör att olika nivåer av webbplatsen och programmet kan åsidosättas. Skannern kontrollerar inställningar på varje nivå för programmet/webbplatsen utöver standardrotnivå. På så sätt blir det enklare för dig att identifiera potentiellt sårbara inställningar och snabbt åtgärda dem, med hjälp av våra rekommendationer för dessa inställningar.

>[!NOTE] 
>Du kan ladda ned de vanliga konfigurationsidentifierare och grundkonfigurationer som används av OMS-säkerhet på den här [sidan](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335?redir=0).


## <a name="web-security-baseline-assessment"></a>Utvärdering av säkerhetsbaslinjen för webben

I den här förhandsversionen kan funktionen nås via alternativet OMS-sökning, och från instrumentpanelen för säkerhets- och granskningslösningen i OMS. Följ stegen nedan för att ställa lämplig fråga:

1. Klicka på panelen **Säkerhet och granskning** på huvudinstrumentpanelen för **Microsoft Operations Management Suite**.
2. På instrumentpanelen **Säkerhet och granskning** kan du se vyn för webbaslinjen jämte den för operativsystembaslinjen.
   
    ![Utvärdering av webbaslinjen i säkerhets- och granskningslösningen i OMS](./media/oms-security-web-baseline/oms-security-web-baseline-fig5.png)

3. I den vänstra rutan visas antalet webbservrar jämfört med säkerhetsbaslinjen, den genomsnittliga procentandelen regler som klarade utvärderingen på alla utvärderade servrar och listan med servrar som har utvärderats.
4. I den högra rutan visas de unika regler som inte klarade utvärderingen efter *allvarlighetsgrad* och *regeltyp*. Om du klickar på någon av reglerna i det högra fönstret visas mer information om regeln. Ett exempel illustreras i bilden nedan. Regeln som utvärderas visas under *Regelinställning*. Fältet *AzId* som är en unik identifierare för varje regel används av Microsoft för att spåra baslinjereglerna. Utöver det kan användare se det *förväntade resultatet* (Microsofts rekommenderade värde), samt annan information om regelns effekt på säkerheten.
    
    ![Fråga](./media/oms-security-web-baseline/oms-security-web-baseline-fig6.png)

5. Du kan skapa egna frågor för att granska resultatet. 

Den första frågan du kan använda är **Sammanfattning av utvärdering av webbaslinje**. I fältet **Begin search here** (Starta sökning här) skriver du den här frågan: Type *=SecurityBaselineSummary BaselineType=Web*. Följande är ett exempel på utdata:

![Frågeresultat](./media/oms-security-web-baseline/oms-security-web-baseline-fig7.png)

>[!NOTE] 
>I den här fråga indikerar varje post en utvärderingssammanfattning på en enskild server.

När du använder **Loggsökning** kan du ange olika frågor för att få mer information om utvärderingen av webbaslinje. Förutom den föregående frågan kan du använda följande i den här förhandsversionen:

**Utvärdering av webbaslinje**: Varje post motsvarar en utvärdering av en webb-baslinjeregel på en enskild server. Den innehåller alla data för en regel som inte klarade utvärderingen, *sökvägen till platsen* där regeln utvärderades, det *förväntade resultatet* och det *faktiska resultatet*.

Fråga: *Type=SecurityBaseline BaselineType=Web AnalyzeResult=Failed*

![Frågeresultat 2](./media/oms-security-web-baseline/oms-security-web-baseline-fig8.png)

**Visa alla resultat för en specifik server**: Den här frågan illustrerar hur du visar resultatet för en specifik server: Fråga: *Type=SecurityBaseline BaselineType=Web Computer=BaselineTestVM1*

![Frågeresultat 3](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

Du kan använda dessa poster/frågor för att skapa dina egna instrumentpaneler, rapporter eller aviseringar. Här är ett exempel på en UI-kontroll som du kan lägga till på din instrumentpanel. Lär dig att visualisera dina data med OMS View Designer [här](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/). Skärmen nedan är ett exempel på hur panelen ser ut när du har gjort den här anpassningen.

![instrumentpanel](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

## <a name="see-also"></a>Se även
I det här dokumentet har du lärt dig hur du utvärderar webbaslinjen i säkerhets- och granskningslösningen i OMS. Mer information om säkerheten i OMS finns i följande artiklar:

* [Översikt över Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Övervaka och svara på säkerhetsaviseringar i säkerhets- och granskningslösningen i Operations Management Suite](oms-security-responding-alerts.md)
* [Övervaka resurser i säkerhets- och granskningslösningen i Operations Management Suite](oms-security-monitoring-resources.md)


