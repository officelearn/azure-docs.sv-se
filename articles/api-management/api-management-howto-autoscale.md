---
title: Konfigurera automatisk skalning av en Azure API Management-instans | Microsoft-dokument
description: I det här avsnittet beskrivs hur du konfigurerar beteende för automatisk skalning för en Azure API Management-instans.
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
ms.openlocfilehash: 8c1c96fdb1f4f42c7592791881b855f74d411171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70018264"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Skala en Azure API Management-instans automatiskt  

Azure API Management-tjänstinstansen kan skalas automatiskt baserat på en uppsättning regler. Det här problemet kan aktiveras och konfigureras via Azure Monitor och stöds endast i **standard-** och **premiumnivåer** för Azure API Management-tjänsten.

Artikeln går igenom processen att konfigurera automatisk skalning och föreslår optimal konfiguration av regler för automatisk skalning.

> [!NOTE]
> API Management-tjänsten i **förbrukningsnivån** skalar automatiskt baserat på trafiken - utan någon ytterligare konfiguration som behövs.

## <a name="prerequisites"></a>Krav

Om du vill följa stegen i den här artikeln måste du:

+ Har en aktiv Azure-prenumeration.
+ Har en Azure API Management-instans. Mer information finns i [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Förstå begreppet [kapacitet för en Azure API Management-instans](api-management-capacity.md).
+ Förstå [manuell skalningsprocess för en Azure API Management-instans](upgrade-and-scale.md), inklusive kostnadskonsekvenser.

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Begränsningar för automatisk skalning av Azure API Management

Vissa begränsningar och konsekvenser av skalningsbeslut måste beaktas innan du konfigurerar beteende för automatisk skalning.

+ Automatisk skalning kan endast aktiveras för **standard-** och **premiumnivåer** för Azure API Management-tjänsten.
+ Prisnivåer anger också det maximala antalet enheter för en tjänstinstans.
+ Skalningsprocessen tar minst 20 minuter.
+ Om tjänsten är låst av en annan åtgärd misslyckas skalningsbegäran och försöker igen automatiskt.
+ Vid en tjänst med flera regionala distributioner kan endast enheter på den **primära platsen** skalas. Enheter på andra platser kan inte skalas.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Aktivera och konfigurera automatisk skalning för Azure API Management-tjänst

Följ stegen nedan för att konfigurera automatisk skalning för en Azure API Management-tjänst:

1. Navigera till **Övervaka** instans i Azure-portalen.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Välj **Automatisk skalning** på menyn till vänster.

    ![Azure Monitor automatisk skalningsresurs](media/api-management-howto-autoscale/02.png)

3. Hitta din Azure API Management-tjänst baserat på filtren i rullgardinsmenyerna.
4. Välj önskad Azure API Management-tjänstinstans.
5. Klicka på knappen Aktivera **automatisk skalning** i det nyöppnade avsnittet.

    ![Aktivera automatisk skalning i Azure Monitor](media/api-management-howto-autoscale/03.png)

6. Klicka på + **Lägg till en regel**i avsnittet **Regler** .

    ![Azure Monitor-regel för automatisk skalning](media/api-management-howto-autoscale/04.png)

7. Definiera en ny utskalningsregel.

   En utskalningsregel kan till exempel utlösa ett tillägg av en Azure API Management-enhet, när medelkapacitetsmåttet under de senaste 30 minuterna överstiger 80 %. Tabellen nedan innehåller konfiguration för en sådan regel.

    | Parameter             | Värde             | Anteckningar                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Måttkälla         | Aktuell resurs  | Definiera regeln baserat på de aktuella Azure API Management-resursmåtten.                                                                                                                                                                                                     |
    | *Kriterie*            |                   |                                                                                                                                                                                                                                                                                 |
    | Tidsmängd      | Medel           |                                                                                                                                                                                                                                                                                 |
    | Måttnamn           | Kapacitet          | Kapacitetsmått är ett Azure API Management-mått som återspeglar användningen av resurser för en Azure API Management-instans.                                                                                                                                                            |
    | Tidsintervallstatistik  | Medel           |                                                                                                                                                                                                                                                                                 |
    | Operator              | Större än      |                                                                                                                                                                                                                                                                                 |
    | Tröskelvärde             | 80 %               | Tröskelvärdet för det genomsnittliga kapacitetsmåttet.                                                                                                                                                                                                                                 |
    | Varaktighet (i minuter) | 30                | Tidsintervallet för att beräkna medelvärdet för kapacitetsmåttet över är specifikt för användningsmönster. Ju längre tidsperioden är, desto smidigare blir reaktionen - intermittenta spikar kommer att ha mindre effekt på utskalningsbeslutet. Men det kommer också att fördröja utskalningsutlösaren. |
    | *Åtgärd*              |                   |                                                                                                                                                                                                                                                                                 |
    | Åtgärd             | Öka antalet med |                                                                                                                                                                                                                                                                                 |
    | Antal instanser        | 1                 | Skala ut Azure API Management-instansen efter 1 enhet.                                                                                                                                                                                                                          |
    | Väntetid (minuter)   | 60                | Det tar minst 20 minuter för Azure API Management-tjänsten att skala ut. I de flesta fall förhindrar nedkylningsperioden på 60 minuter från att utlösa många utskalningar.                                                                                                  |

8. Klicka på **Lägg till** om du vill spara regeln.

    ![Azure Monitor skala ut regel](media/api-management-howto-autoscale/05.png)

9. Klicka igen på **+ Lägg till en regel**.

    Den här gången måste en regelskala definieras. Det kommer att se till att resurser inte slösas bort när användningen av API:er minskar.

10. Definiera en ny skala i regel.

    En regelskala kan till exempel utlösa en borttagning av en Azure API Management-enhet, när medelkapacitetsmåttet under de senaste 30 minuterna har varit lägre än 35 %. Tabellen nedan innehåller konfiguration för en sådan regel.

    | Parameter             | Värde             | Anteckningar                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Måttkälla         | Aktuell resurs  | Definiera regeln baserat på de aktuella Azure API Management-resursmåtten.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Kriterie*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Tidsmängd      | Medel           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Måttnamn           | Kapacitet          | Samma mått som det som används för skalningsregeln.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Tidsintervallstatistik  | Medel           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operator              | Mindre än         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Tröskelvärde             | 35 %               | På samma sätt som skalningsregeln beror det här värdet i hög grad på användningsmönstren för Azure API Management. |
    | Varaktighet (i minuter) | 30                | Samma värde som det som används för skalningsregeln.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Åtgärd*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Åtgärd             | Minska antalet med | Motsatsen till vad som användes för skala ut regeln.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Antal instanser        | 1                 | Samma värde som det som används för skalningsregeln.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Väntetid (minuter)   | 90                | Skala in bör vara mer konservativ än en skala ut, så nedkylningsperioden bör vara längre.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Klicka på **Lägg till** om du vill spara regeln.

    ![Azure Monitor-skala i regel](media/api-management-howto-autoscale/06.png)

12. Ange det **maximala** antalet Azure API Management-enheter.

    > [!NOTE]
    > Azure API Management har en gräns för enheter som en instans kan skala ut till. Gränsen beror på en tjänstnivå.

    ![Azure Monitor-skala i regel](media/api-management-howto-autoscale/07.png)

13. Klicka på **Spara**. Din automatiska skalning har konfigurerats.

## <a name="next-steps"></a>Nästa steg

+ [Så distribuerar du en Azure API Management-tjänstinstans till flera Azure-regioner](api-management-howto-deploy-multi-region.md)
