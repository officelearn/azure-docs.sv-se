---
title: Avvikelse identifierings tjänst för avgiftsbelagda fakturerings Microsoft Azure Marketplace
description: Beskriver hur avvikelse identifiering fungerar, när meddelanden skickas och vad du kan göra med dem, samt support alternativ.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 06/10/2020
ms.openlocfilehash: 909b818c60932f5b1d6071ea1ccf0ee4c76173b4
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92280534"
---
# <a name="anomaly-detection-service-for-metered-billing"></a>Tjänst för att identifiera avvikelser vid förbrukningsbaserad fakturering

Med [Marketplace för avläsning](marketplace-metering-service-apis-faq.md) av program vara kan du skapa erbjudanden i det kommersiella Marketplace-programmet som debiteras enligt andra enheter än standard enheter. Med avgiftsbelagd fakturering skickar du användnings händelser för kundens användning till Microsoft och vi förbereder faktureringen baserat på den användningen.

Felaktiga användnings data kan komma från olika orsaker, t. ex. buggar, fel konfigurationer i förbruknings spårningen eller bedrägerier. Felaktiga användnings data leder till felaktiga kund avgifter och fakturerings tvister.

För att minska risken använder vår avvikelse identifierings tjänst Machine Learning-algoritmer för att fastställa det normala fakturerings beteendet för mätningen, analysera den avgiftsbelagda fakturerings användningen och upptäcka avvikelser med minimal inblandning av användaren.

Du får ett meddelande om avvikelser upptäcks i din avgiftsbelagda fakturerings användning. Det ger dig möjlighet att undersöka och meddela oss om en avvikelse bekräftas vara ett reellt problem, där punkt åtgärder kan vidtas för att lösa kundens fakturerings problem proaktivt.

Förutom plötsliga toppar, DIP-och trend ändringar i den avgiftsbelagda fakturerings användningen, har vår modell också konto för säsongs effekter. Eftersom den avgiftsbelagda faktureringen kommunicerar via överförbruknings data kan vår modell också hantera långa perioder av data som saknas.

Följande är exempel på avvikelse identifierings resultat. Det förväntade intervallet visas som ett gult band. Acceptabel fakturerings användning för mätare visas som gröna stjärnor på bandet. Fakturerings användning utanför bandet visas som en röd punkt.  

Avvikelser identifierade utanför en förutsägbar trend:

![Visar avvikelser identifierade utanför en förutsägbar trend.](media/anomaly-1.png)

Avvikelser identifierade utanför en återkommande cyklisk trend:

![Visar avvikelser identifierade utanför en återkommande cyklisk trend.](media/anomaly-2.png)

Avvikelser identifierade i en uppåt-trend:

![Visar avvikelser identifierade i en uppåt-trend.](media/anomaly-3.png)

## <a name="how-anomaly-detection-service-works"></a>Så här fungerar avvikelse identifierings tjänsten

Avvikelse identifiering aktive ras automatiskt för all avgiftsbelagd fakturerings användning. När du skickar användnings händelser till Microsoft skapar avvikelse identifierings tjänsten en modell med förväntade värden baserat på tidigare användnings data. Den här modellen körs varje vecka.

Avvikelse identifierings funktioner på per mätare och per kund nivå. Det innebär att varje mätare med varje kund har en modell som har tränats baserat på den här kundens tidigare användnings mönster för den här mätaren.

Modellen fungerar genom att skapa retroaktiva konfidens intervall. Tids serie prognosen är en generaliserad additiv modell bestående av en trend förutsägelse del och en säsongs beroende del. Eftersom modellen formuleras som en Regressions uppgift kan den hantera långa perioder av data som saknas. Om en observation faller utanför de förväntade konfidens intervallen innebär det att observationen inte kan förklaras baserat på historiska mönster i den avgiftsbelagda faktureringen och kan därför vara en avvikelse.

## <a name="anomaly-detection-notification"></a>Meddelande om avvikelse identifiering

Vi skickar meddelanden om avvikelse identifieringar varje vecka. Den innehåller alla avvikelser som identifierats för alla mätare och kunder. Det här e-postmeddelandet skickas till **teknik** -och **support** kontakter som tillhandahålls när du skapade erbjudandet.

Du förväntas undersöka om identifierade avvikelser är verkliga problem och kontakta Microsoft för att rapportera felaktig användning (se avsnittet support nedan).

Om du bekräftar att identifierade avvikelser är normal användning krävs ingen ytterligare åtgärd från dig. Men om en avvikelse representerar potentiellt hög ekonomisk risk kan vi kontakta dig för att bekräfta användningen.  

## <a name="when-and-how-to-get-support"></a>När och hur man får support

Om du har skickat den felaktiga användningen till Microsoft och detta gjorde, eller kommer att bli underdebiterad till kunden, kommer Microsoft inte att initiera någon faktura till kunden under rapporterad användning eller betala dig för den användningen. Du får själv stå för kostnaderna som beror på underrapporteringen.

Om något av följande fall gäller kan du öppna ett supportärende och begära återbetalning eller fakturajustering för dina kunder:

- Du bekräftade att ett av de avvikelser som vi hittade är ett verkligt problem och att den felaktiga användningen skulle leda till **överkostnad** av kunden.
- Du upptäcker att du har skickat felaktig användning till oss och att den felaktiga användningen skulle leda till **överkostnad** av kunden.
- Du vill begära en åter betalning för avgiften för kundens avgiftsbelagda fakturerings användning.

Så här skickar du en biljett:

1. Gå till support sidan. Skriv "felaktig användning" i **rutan berätta om ditt ärende**.
2. I avsnittet support, i list rutan med Sök resultat, väljer du något av följande:
    - **Kommersiell marknads**  >  plats **Avgiftsbelagd fakturering**  >  **Fel användning har skickats för Azure-program**, eller
    - **Kommersiell marknads**  >  plats **Avgiftsbelagd fakturering**  >  **Fel användning har skickats för SaaS-erbjudandet**
3. Under **Nästa steg**väljer du knappen **Granska lösningar** för att logga in på Partner Center för att skicka in ett support ärende.

Fler support alternativ för utgivare finns i [Support för programmet för kommersiella marknads platser i Partner Center](../support.md).

## <a name="next-step"></a>Nästa steg

- Lär dig mer om [API för avläsning av marknads](marketplace-metering-service-apis.md)plats.
