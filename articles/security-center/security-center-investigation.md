---
title: Undersöka incidenter och aviseringar i Azure Security Center | Microsoft Docs
description: Det här dokumentet hjälper dig att använda undersökningsfunktionen i Azure Security Center för att undersöka incidenter och aviseringar.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a8e894a9-8781-4749-ae8f-8c8e01658566
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: rkarlin
ms.openlocfilehash: 8b2e42759ba2bffef28e1bf74e07bc149027ab7a
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56651781"
---
# <a name="investigate-incidents-and-alerts-in-azure-security-center-preview"></a>Undersöka incidenter och aviseringar i Azure Security Center (förhandsversion)
Det här dokumentet hjälper dig att använda undersökningsfunktionen i Azure Security Center för att undersöka incidenter och aviseringar.

## <a name="what-is-investigation-in-security-center"></a>Vad är undersökning i Security Center?
Med undersökningsfunktionen i Security Center kan du sortera, förstå omfattningen av och spåra rotorsaken till en potentiell [säkerhetsincident](https://docs.microsoft.com/azure/security-center/security-center-incident).

Avsikten är att underlätta undersökningsprocessen genom att länka alla entiteter ([säkerhetsaviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), användare, datorer och incidenter) som ingår i incidenten du undersöker.  Security Center kan göra detta genom att korrelera relevanta data med berörda entiteter och visa korrelationen i ett interaktivt diagram som hjälper dig att navigera bland objekten och visualisera relevant information.


> [!NOTE]
    > * [Anpassade aviseringar](security-center-custom-alert.md) stöds inte i undersökningsfunktionen i Security Center.
    > * Undersökning har endast stöd för aviseringar baserat på data som samlas in från Windows-servrar.
>
>


## <a name="how-investigation-works"></a>Hur fungerar undersökningen?
Undersökningen består av ett diagram som upptar mitten av undersökningsinstrumentpanelen. Diagrammet fokuserar alltid på en specifik entitet och visar entiteterna som är relaterade till den. En entitet kan vara en säkerhetsavisering, användare, dator eller incident.

![Karta](./media/security-center-investigation/security-center-investigation-fig1.png)

Användaren kan gå från en entitet till en annan genom att klicka på den i diagrammet. Diagrammet centreras automatiskt på den valda entiteten och dess relaterade entiteter. Entiteter som inte längre är relevanta kan tas bort från diagrammet.

### <a name="investigation-path"></a>Utredningssökväg
När användaren går till de olika entiteterna hjälper utredningssökvägen dig att hålla reda på undersökningssammanhanget och gör navigeringen snabb. Incidenten som innehåller undersökningsresultaten är alltid den som är längst till vänster i undersökningssökvägen.

![Sökväg](./media/security-center-investigation/security-center-investigation-fig2.png)

### <a name="general-information"></a>Allmän information
När en entitet presenteras i diagrammet visar flikarna mer information om den här entiteten. Fliken **Info** visar allmän information om entiteten från olika tillgängliga informationskällor.

![Allmän information](./media/security-center-investigation/security-center-investigation-fig3.png)

Informationsfliken visar information som är relevant till incidenten som har valts på kartan. Incident är en container som innehåller resultaten av en undersökning. Varje undersökning sker i samband med en incident.

En incident skapas bara när en användare klickar på knappen **Starta undersökning** för en specifik avisering. Grundfunktionen som är tillgänglig för utredaren är att markera entiteter som användare, dator eller avisering. När en entitet markeras som relaterad anges en orsak. Hädanefter visas den här entiteten direkt under incidenten i diagrammet och listan över incidententiteter.

### <a name="entities"></a>Entiteter

På fliken **Entiteter** visas alla relaterade entiteter grupperade efter typ. Det är användbart i två fall: när det finns för många entiteter att visa i diagrammet och när entitetsnamnen är för långa, och det är enklare att granska dem i tabellform.

![Entiteter](./media/security-center-investigation/security-center-investigation-fig4.png)

### <a name="search"></a>Search

På fliken **Sök** visas alla loggtyper som är tillgängliga för entiteten. För varje loggtyp kan du se hur många poster som är tillgängliga. När du klickar på varje loggtyp kommer du till sökskärmen. På sökskärmen kan du förfina sökningen och använda olika sökfunktioner, som att ange aviseringar. I den aktuella versionen är sökfliken bara tillgänglig för användar- och datorentiteter.

![Search](./media/security-center-investigation/security-center-investigation-fig5.png)

### <a name="exploration"></a>Utforskning

På fliken **Utforskning** kan utredaren undersöka data relaterade till olika problem som är relaterade till entiteten. När till exempel en dator undersöks kan listan med processer som utförs på den visas på fliken Utforskning. I vissa fall visar fliken Utforskning data som kan tyda på en misstänkt problem. Utredaren kan undersöka data på fliken eller öppna dem på sökskärmen för att granska stora uppsättningar data och för att använda avancerade sökalternativ som filtrering och export till Excel.

![Utforskning](./media/security-center-investigation/security-center-investigation-fig6.png)

### <a name="timeline"></a>Tidslinje

Det mesta av de data som visas i diagrammet och på de olika flikarna är relevanta under en viss tid. Den här tiden anges med hjälp av tidsomfattningsväljaren uppe till vänster i diagrammet. Utredaren har olika metoder för att välja tidsomfattningen.

![Tidslinje](./media/security-center-investigation/security-center-investigation-fig7.png)

Följande objekt är känsliga för tidsomfattningen:

- Användare-dator-förhållande i diagrammet – bara användare som har loggat in på datorn inom den här tiden visas.
- Vilka aviseringar som visas när du undersöker datorer och användare: bara aviseringar som inträffar inom den tid som visas.
- Fliken Entiteter följer samma logik som diagrammet.

Följande objekt visas oavsett den valda tidsomfattningen:

- När en avisering visas så visas alltid alla entiteter som finns i den, som användare och datorer.
- Om en incident innehåller en entitet så visas den.

> [!NOTE]
> Fliken **Sök** och **Utforskning** visar bara poster inom den här tiden.

## <a name="how-to-perform-an-investigation"></a>Hur utför du en undersökning?

Du kan starta en undersökningen utifrån en säkerhetsincident eller från en avisering – alternativet du väljer varierar beroende på dina behov. Stegen som följer används för att starta en undersökning utifrån en avisering:

1.  Öppna instrumentpanelen **Security Center**.
2.  Klicka på **Säkerhetsaviseringar** och väljer incidenten som du vill undersöka.
3.  På incidentens sida klickar du på knappen **Starta undersökning**, så visas instrumentpanelen **Undersökning**.

    ![Varning](./media/security-center-investigation/security-center-investigation-fig8.png)

4. På den här instrumentpanelen kan du välja entiteten på kartan, så visas den relevanta informationen om den här entiteten till höger på skärmen.

    ![Instrumentpanelen Undersökning](./media/security-center-investigation/security-center-investigation-fig9.png)

Nu kan du utforska entiteterna som ingick i den här incidenten och utforska mer information om var och en av dem.

## <a name="see-also"></a>Se också
I det här dokumentet har du lärt dig hur du använder undersökningsfunktionen i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Felsökningsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Lär dig hur du felsöker vanliga problem i Security Center.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Här finns vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
