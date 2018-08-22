---
title: Konfigurera automatisk skalning av en Azure API Management-instans | Microsoft Docs
description: Det här avsnittet beskriver hur du ställer in automatisk skalning beteende för en Azure API Management-instans.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 1d996469c01640a70b68d70e8c24c617e0defd64
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "40250845"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Skala automatiskt en Azure API Management-instans  

Azure API Management-tjänstinstans kan skala automatiskt baserat på en uppsättning regler. Det här beteendet kan aktiveras och konfigureras via Azure Monitor och stöds bara i **Standard** och **Premium** nivån för Azure API Management-tjänsten.

Artikeln går igenom processen med att konfigurera automatisk skalning och föreslår optimal konfiguration av regler för automatisk skalning.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa stegen från den här artikeln, måste du:

+ Ha en aktiv Azure-prenumeration.
+ Har en Azure API Management-instans. Mer information finns i [skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Förstå begreppet [kapacitet för en Azure API Management-instans](api-management-capacity.md).
+ Förstå [manuell skalning av en Azure API Management-instans](upgrade-and-scale.md), inklusive kostnaden konsekvenser.

## <a name="azure-api-management-autoscale-limitations"></a>Begränsningar för automatisk skalning av Azure API Management

Vissa begränsningar och konsekvenser av skalning beslut måste beaktas innan du konfigurerar automatisk skalning beteende.

+ Automatisk skalning kan endast aktiveras för **Standard** och **Premium** nivån för Azure API Management-tjänsten.
+ Prisnivåer även ange det maximala antalet enheter för en tjänstinstans.
+ Skala processen tar minst 20 minuter.
+ Om tjänsten är låst av en annan åtgärd kan skala begäran misslyckas och automatiskt nya försök.
+ Om en tjänst med flera regioner distributioner, endast enheter i den **primära platsen** kan skalas. Enheter på andra platser kan inte skalas.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Aktivera och konfigurera automatisk skalning för Azure API Management-tjänsten

Följ stegen nedan för att konfigurera automatisk skalning för en Azure API Management-tjänsten:

1. Gå till **övervakaren** instans i Azure-portalen.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Välj **Autoskala** på menyn till vänster.

    ![Azure Monitor autoscale-resurs](media/api-management-howto-autoscale/02.png)

3. Leta upp Azure API Management-tjänsten baserat på filtren i listrutorna.
4. Välj den önskade Azure API Management-tjänstinstansen.
5. Klicka på under nyligen öppnade den **aktivera autoskalning** knappen.

    ![Azure Monitor autoscale enable](media/api-management-howto-autoscale/03.png)

6. I den **regler** klickar du på **+ Lägg till en regel**.

    ![Automatisk skalning i Azure Monitor Lägg till regel](media/api-management-howto-autoscale/04.png)

7. Definiera en ny skala ut-regel.

   En skalbar regeln kan utlösa ett tillägg av en Azure API Management-enhet när måttet Genomsnittlig kapacitet under de senaste 30 minuterna överskrider 80%. Tabellen nedan innehåller konfiguration för sådan regel.

    | Parameter             | Värde             | Anteckningar                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Måttkälla         | Aktuell resurs  | Definiera regeln baserat på den aktuella Azure API Management-Resursmått.                                                                                                                                                                                                     |
    | *Kriterie*            |                   |                                                                                                                                                                                                                                                                                 |
    | Tidsmängd      | Medel           |                                                                                                                                                                                                                                                                                 |
    | Måttnamn           | Kapacitet          | Kapacitet mått är ett mått för Azure API Management som återger användningen av resurser i en Azure API Management-instans.                                                                                                                                                            |
    | Tidsintervallstatistik  | Medel           |                                                                                                                                                                                                                                                                                 |
    | Operator              | Större än      |                                                                                                                                                                                                                                                                                 |
    | Tröskelvärde             | 80%               | Tröskelvärde för Genomsnittlig kapacitet-mått.                                                                                                                                                                                                                                 |
    | Varaktighet (i minuter) | 30                | Timespan att ta medelvärdet kapacitet måttet över är specifik för användningsmönster. Ju längre tidsperioden är, desto jämnare reaktionen kommer att – tillfälliga toppar har mindre effekt på skalbarhet beslutet. Men fördröjs så också skalbar utlösaren. |
    | *Åtgärd*              |                   |                                                                                                                                                                                                                                                                                 |
    | Åtgärd             | Öka antal med |                                                                                                                                                                                                                                                                                 |
    | Antal instanser        | 1                 | Skala ut Azure API Management-instans av 1 enhet.                                                                                                                                                                                                                          |
    | Väntetid (minuter)   | 60                | Det tar minst 20 minuter för tjänsten Azure API Management att skala ut. I de flesta fall förhindrar lågfrekvent nedåt i 60 minuter utlösa många skala ta.                                                                                                  |

8. Klicka på **Lägg till** spara regeln.

    ![Azure Monitor skala ut-regel](media/api-management-howto-autoscale/05.png)

9. Klicka på igen på **+ Lägg till en regel**.

    Den här tiden kan måste en skalningsuppsättning i regeln definieras. Det säkerställer att resurser inte är som gått, om den minskar användningen av API: er.

10. Definiera en ny skala i regeln.

    En skalningsuppsättning i regeln kan utlösa en borttagning av en Azure API Management-enhet när måttet Genomsnittlig kapacitet under de senaste 30 minuterna har lägre än 35%. Tabellen nedan innehåller konfiguration för sådan regel.

    | Parameter             | Värde             | Anteckningar                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Måttkälla         | Aktuell resurs  | Definiera regeln baserat på den aktuella Azure API Management-Resursmått.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Kriterie*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Tidsmängd      | Medel           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Måttnamn           | Kapacitet          | Samma mått som den som används för att skala ut regeln.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Tidsintervallstatistik  | Medel           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operator              | Mindre än         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Tröskelvärde             | 35%               | På liknande sätt att skala ut-regel beroende värdet kraftigt användningsmönster för Azure API Management. |
    | Varaktighet (i minuter) | 30                | Samma värde som det som används för att skala ut regeln.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Åtgärd*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Åtgärd             | Minska antal med | Motsatt vad användes för att skala ut regeln.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Antal instanser        | 1                 | Samma värde som det som används för att skala ut regeln.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Väntetid (minuter)   | 90                | Skala in bör vara konservativ mer än en skala ut, så lågfrekvent ned period får vara längre.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Klicka på **Lägg till** spara regeln.

    ![Med Azure Monitor scale regel](media/api-management-howto-autoscale/06.png)

12. Ange den **maximala** antal Azure API Management-enheter.

    > [!NOTE]
    > Azure API Management har en gräns på enheter som en instans kan skala ut till. Gränsen är beroende av en tjänstnivå.

    ![Med Azure Monitor scale regel](media/api-management-howto-autoscale/07.png)

13. Klicka på **Spara**. Din automatisk skalning har konfigurerats.

## <a name="next-steps"></a>Nästa steg

+ [Hur du distribuerar en Azure API Management-tjänstinstans till flera Azure-regioner](api-management-howto-deploy-multi-region.md)
