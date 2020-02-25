---
title: Hantera vågrätt kluster skalning (skala ut) för att matcha efter frågan i Azure Datautforskaren
description: I den här artikeln beskrivs hur du skalar ut och skalar i ett Azure Datautforskaren-kluster baserat på ändring efter frågan.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: d0c9fe9ebd040ee59ae8717e95fd1911eaef61be
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560464"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>Hantera vågrätt kluster skalning (skala ut) i Azure Datautforskaren för att hantera ändring efter frågan

Att ändra storlek på ett kluster är på lämpligt sätt avgörande för Azure Datautforskarens prestanda. En statisk kluster storlek kan leda till användning eller över-användning, men ingen av dem är idealisk. Eftersom efter frågan på ett kluster inte kan förutsägas med absolut noggrannhet är det bättre att *skala* ett kluster, lägga till och ta bort kapacitets-och CPU-resurser med justerbar efter frågan. 

Det finns två arbets flöden för skalning av ett Azure Datautforskaren-kluster: 
* Horisontell skalning, även kallat skalning in och ut.
* [Vertikal skalning](manage-cluster-vertical-scaling.md), även kallat skalning upp och ned.
I den här artikeln beskrivs arbets flödet för horisontell skalning.

## <a name="configure-horizontal-scaling"></a>Konfigurera vågrät skalning

Genom att använda vågrät skalning kan du skala antalet instanser automatiskt, baserat på fördefinierade regler och scheman. Ange inställningarna för autoskalning för klustret:

1. I Azure Portal går du till Azure Datautforskaren kluster resursen. Under **Inställningar**väljer du **skala ut**. 

2. I fönstret **skala ut** väljer du den metod för autoskalning som du vill använda: **manuell skalning**, **optimerad autoskalning**eller **anpassad autoskalning**.

### <a name="manual-scale"></a>Manuell skalning

Manuell skalning är standardinställningen när klustret skapas. Klustret har en statisk kapacitet som inte ändras automatiskt. Du väljer den statiska kapaciteten med hjälp av **antalet instans antal** . Klustrets skalning förblir i den inställningen tills du gör en ny ändring.

   ![Metod för manuell skalning](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale-preview"></a>Optimerad autoskalning (för hands version)

Optimerad autoskalning är den rekommenderade automatiska skalnings metoden. Med den här metoden optimeras kluster prestanda och-kostnader. Om klustret närmar sig ett tillstånd med under användning skalas det i. Den här åtgärden sänker kostnaderna men behåller prestanda nivån. Om klustret närmar sig ett tillstånd för överförbrukning kommer det att skalas ut för att upprätthålla optimala prestanda. Så här konfigurerar du optimerad autoskalning:

1. Välj **optimerad autoskalning**. 

1. Välj ett minsta antal instanser och maximalt antal instanser. Klustrets intervall för automatisk skalning mellan de två talen, baserat på belastning.

1. Välj **Spara**.

   ![Optimerad autoskalning-metod](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

Optimerad autoskalning börjar fungera. Dess åtgärder visas nu i Azures aktivitets logg i klustret.

#### <a name="logic-of-optimized-autoscale"></a>Logik för optimerad autoskalning 

**Skala ut**

När klustret närmar sig ett tillstånd för överförbrukning kan du skala ut för att upprätthålla optimala prestanda. Skala ut sker när:
* Antalet kluster instanser är lägre än det maximala antalet instanser som definierats av användaren.
* Användningen av cacheminnet är hög i över en timme.
* PROCESSORN är hög i mer än en timme.

> [!NOTE]
> Skala ut-logiken tar för närvarande inte hänsyn till användnings måttet. Om det här måttet är viktigt för ditt användnings fall använder du [anpassad autoskalning](#custom-autoscale).

**Skala i**

När klustret närmar sig ett tillstånd med under användningen kan du skala in till lägre kostnader men upprätthålla prestanda. Flera mått används för att kontrol lera att det är säkert att skala i klustret. Följande regler utvärderas dagligen i 7 dagar innan skalning i utförs:
* Antalet instanser är över 2 och över det minsta antalet definierade instanser.
* För att säkerställa att det inte finns någon överlagring av resurser måste följande mått verifieras innan skalning i utförs: 
    * Användningen av cacheminnet är inte hög
    * CPU är under genomsnittet 
    * Förbruknings användningen är under genomsnittet 
    * Strömnings förbrukning (om streaming används) är inte hög
    * Keep Alive-händelser är över ett definierat minimum, bearbetat korrekt och i tid.
    * Ingen fråga begränsning 
    * Antalet misslyckade frågor är under ett definierat minimum.

> [!NOTE]
> Skalan i Logic kräver för närvarande en 7-dagars utvärdering innan implementering av optimerad skalning i. Denna utvärdering sker var 24: e timme. Om en snabb ändring krävs använder du [manuell skalning](#manual-scale).

### <a name="custom-autoscale"></a>Anpassad autoskalning

Med hjälp av anpassad autoskalning kan du skala klustret dynamiskt baserat på mått som du anger. Följande bild visar flödet och stegen för att konfigurera anpassad autoskalning. Mer information finns i bilden.

1. I rutan **namn på inställning för autoskalning** anger du ett namn, till exempel *utskalning: cache-användning*. 

   ![Skalnings regel](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. I **skalnings läge**väljer du **skala baserat på ett mått**. Det här läget ger dynamisk skalning. Du kan också välja **skala till ett angivet instans antal**.

3. Välj **+ Lägg till en regel**.

4. I avsnittet **skalnings regel** till höger anger du värden för varje inställning.

    **Kriterie**

    | Inställning | Beskrivning och värde |
    | --- | --- |
    | **Tids mängd** | Välj ett agg regerings kriterium, till exempel **Average**. |
    | **Mått namn** | Välj det mått som du vill att skalnings åtgärden ska baseras på, till exempel **användningen av cachen**. |
    | **Statistik för tids kornig het** | Välj mellan **genomsnitt**, **lägsta**, **högsta**och **Sum**. |
    | **Operator** | Välj lämpligt alternativ, till exempel **större än eller lika**med. |
    | **Fastställd** | Välj ett lämpligt värde. Till exempel för cachelagring är 80 procent en lämplig start punkt. |
    | **Varaktighet (i minuter)** | Välj en lämplig tid för systemet att se tillbaka när du beräknar mått. Börja med standard 10 minuter. |
    |  |  |

    **Åtgärd**

    | Inställning | Beskrivning och värde |
    | --- | --- |
    | **Åtgärd** | Välj lämpligt alternativ för att skala in eller skala ut. |
    | **Antal instanser** | Välj det antal noder eller instanser som du vill lägga till eller ta bort när ett mått villkor uppfylls. |
    | **Låg frekvent (minuter)** | Välj ett lämpligt tidsintervall för att vänta mellan skalnings åtgärder. Börja med standardvärdet på fem minuter. |
    |  |  |

5. Välj **Lägg till**.

6. Ange värden för varje inställning i avsnittet **instans gränser** till vänster.

    | Inställning | Beskrivning och värde |
    | --- | --- |
    | **Lägst** | Antalet instanser som klustret inte skalar nedan, oavsett användning. |
    | **Maximihalter** | Antalet instanser som klustret inte skalar över, oavsett användning. |
    | **Standard** | Standard antalet instanser. Den här inställningen används om det är problem med att läsa resurs måtten. |
    |  |  |

7. Välj **Spara**.

Du har nu konfigurerat horisontell skalning för ditt Azure Datautforskaren-kluster. Lägg till en annan regel för vertikal skalning. Om du behöver hjälp med problem med kluster skalning [öppnar du en support förfrågan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) i Azure Portal.

## <a name="next-steps"></a>Nästa steg

* [Övervaka Azure Datautforskaren prestanda, hälsa och användning med mått](using-metrics.md)
* [Hantera vertikal kluster skalning](manage-cluster-vertical-scaling.md) för lämplig storlek på ett kluster.
