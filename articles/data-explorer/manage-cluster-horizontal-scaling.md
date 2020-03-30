---
title: Hantera kluster vågrät skalning (skala ut) för att matcha efterfrågan i Azure Data Explorer
description: I den här artikeln beskrivs steg för att skala ut och skala ut i ett Azure Data Explorer-kluster baserat på förändrade behov.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: ff7420619cffc2287ab8ff6332df605d56329549
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664141"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>Hantera kluster vågrät skalning (skala ut) i Azure Data Explorer för att tillgodose förändrade behov

Att dimensionera ett kluster på rätt sätt är avgörande för prestanda för Azure Data Explorer. En statisk klusterstorlek kan leda till underutnyttjande eller överutnyttjande, varav ingen är idealisk. Eftersom efterfrågan på ett kluster inte kan förutsägas med absolut noggrannhet är det bättre att *skala* ett kluster och lägga till och ta bort kapacitets- och CPU-resurser med förändrade behov. 

Det finns två arbetsflöden för skalning av ett Azure Data Explorer-kluster: 
* Vågrät skalning, även kallad skalning in och ut.
* [Vertikal skalning](manage-cluster-vertical-scaling.md), även kallad skalning upp och ned.
I den här artikeln beskrivs arbetsflödet för vågrät skalning.

## <a name="configure-horizontal-scaling"></a>Konfigurera vågrät skalning

Genom att använda vågrät skalning kan du skala instansantalet automatiskt, baserat på fördefinierade regler och scheman. Så här anger du inställningarna för automatisk skalning för klustret:

1. Gå till din Azure Data Explorer-klusterresurs i Azure-portalen. Under **Inställningar**väljer du **Skala ut**. 

2. I fönstret **Skala ut** väljer du den metod för automatisk skalning som du vill använda: **Manuell skala,** **Optimerad automatisk skalning**eller **Anpassad automatisk skalning**.

### <a name="manual-scale"></a>Manuell skala

Manuell skala är standardinställningen när klustret skapas. Klustret har en statisk kapacitet som inte ändras automatiskt. Du väljer den statiska kapaciteten med hjälp av **fältet Instansantal.** Klustrets skalning finns kvar vid den inställningen tills du gör en ny ändring.

   ![Metod för manuell skalning](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale-preview"></a>Optimerad automatisk skalning (förhandsgranskning)

Optimerad automatisk skalning är den rekommenderade metoden för automatisk skalning. Den här metoden optimerar klusterprestanda och kostnader. Om klustret närmar sig ett tillstånd av underutnyttjande skalas det in. Den här åtgärden sänker kostnaderna men håller prestandanivån. Om klustret närmar sig ett tillstånd av överutnyttjande skalas det ut för att upprätthålla optimal prestanda. Så här konfigurerar du Optimerad automatisk skalning:

1. Välj **Optimerad automatisk skalning**. 

1. Välj ett minsta antal instanser och ett maximalt antal instanser. Klusterskalningsområdet sträcker sig mellan dessa två tal, baserat på belastning.

1. Välj **Spara**.

   ![Optimerad metod för automatisk skalning](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

Optimerad automatisk skalning börjar fungera. Dess åtgärder visas nu i Azure-aktivitetsloggen i klustret.

#### <a name="logic-of-optimized-autoscale"></a>Logik i optimerad automatisk skalning 

**Skala ut**

När klustret närmar sig ett tillstånd av överutnyttjande skalar du ut för att bibehålla optimal prestanda. Skala ut kommer att ske när:
* Antalet klusterinstanser är lägre än det maximala antalet instanser som definierats av användaren.
* Cacheanvändningen är hög i över en timme.
* Processorn är hög i över en timme.
* Intagsutnyttjandet är högt i över en timme.

> [!NOTE]
> Utskalningslogiken tar för närvarande inte hänsyn till inmatningsutnyttjandemåttet. Om det här måttet är viktigt för ditt användningsfall använder du [anpassad automatisk skalning](#custom-autoscale).

**Skala in**

När klustret närmar sig ett tillstånd av underutnyttjande skalar du in för att sänka kostnaderna men bibehåller prestanda. Flera mått används för att verifiera att det är säkert att skala i klustret. Följande regler utvärderas varje timme i 6 timmar innan skala in utförs:
* Antalet instanser är över 2 och över det minsta antalet definierade instanser.
* För att säkerställa att resurser inte överbelastas måste följande mått verifieras innan skala in utförs: 
    * Cacheanvändningen är inte hög
    * CPU är under genomsnittet 
    * Intagsutnyttjandet är lägre än genomsnittet 
    * Användning av direktuppspelning (om streaming intag används) är inte hög
    * Håll vid liv händelser är över ett definierat minimum, bearbetas korrekt, och i tid.
    * Ingen frågebegränsning 
    * Antalet misslyckade frågor ligger under ett definierat minimum.

> [!NOTE]
> Skalan i logik kräver för närvarande en 7-dagars utvärdering innan implementering av optimerad skala i. Denna utvärdering sker en gång var 24:e timme. Om en snabb ändring behövs kan du använda [manuell skala](#manual-scale).

### <a name="custom-autoscale"></a>Anpassad automatisk skalning

Genom att använda anpassad automatisk skalning kan du skala klustret dynamiskt baserat på mått som du anger. Följande bild visar flödet och stegen för att konfigurera anpassad automatisk skalning. Mer information följ bilden.

1. Ange ett namn i rutan *Utskalningsnamn*i rutan **Namn för automatisk skalning.** 

   ![Skalningsregel](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. För **skalningsläge**väljer du **Skala baserat på ett mått**. Det här läget ger dynamisk skalning. Du kan också välja **Skala till ett visst instansantal**.

3. Välj **+ Lägg till en regel**.

4. Ange värden för varje inställning i avsnittet **Skala regel** till höger.

    **Kriterie**

    | Inställning | Beskrivning och värde |
    | --- | --- |
    | **Tidsmängd** | Välj ett aggregeringsvillkor, till exempel **Medel**. |
    | **Måttnamn** | Välj det mått som du vill att skalningsåtgärden ska baseras på, till exempel **Cacheanvändning**. |
    | **Tidsintervallstatistik** | Välj mellan **Medel,** **Minimum**, **Maximum**och **Summa**. |
    | **Operator** | Välj lämpligt alternativ, till exempel **Större än eller lika med**. |
    | **Tröskel** | Välj ett lämpligt värde. För cacheanvändning är till exempel 80 procent en bra utgångspunkt. |
    | **Varaktighet (i minuter)** | Välj en lämplig tid för systemet att se tillbaka när du beräknar mått. Börja med standardvärdet på 10 minuter. |
    |  |  |

    **Åtgärd**

    | Inställning | Beskrivning och värde |
    | --- | --- |
    | **Drift** | Välj lämpligt alternativ för att skala in eller skala ut. |
    | **Antal instanser** | Välj antalet noder eller instanser som du vill lägga till eller ta bort när ett måttvillkor uppfylls. |
    | **Väntetid (minuter)** | Välj ett lämpligt tidsintervall att vänta mellan skalningsåtgärder. Börja med standardvärdet på fem minuter. |
    |  |  |

5. Välj **Lägg till**.

6. I avsnittet **Instansgränser** till vänster anger du värden för varje inställning.

    | Inställning | Beskrivning och värde |
    | --- | --- |
    | **Minimum** | Antalet instanser som klustret inte skalas under, oavsett användning. |
    | **Maximal** | Antalet instanser som klustret inte skalas över, oavsett användning. |
    | **Default** | Standardantalet instanser. Den här inställningen används om det finns problem med att läsa resursmåtten. |
    |  |  |

7. Välj **Spara**.

Du har nu konfigurerat vågrät skalning för ditt Azure Data Explorer-kluster. Lägg till en annan regel för lodrät skalning. Om du behöver hjälp med problem med klusterskalning [öppnar du en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) i Azure-portalen.

## <a name="next-steps"></a>Nästa steg

* [Övervaka Prestanda, hälsotillstånd och användning i Azure Data Explorer med mått](using-metrics.md)
* [Hantera klustervertiklinjeskalning](manage-cluster-vertical-scaling.md) för lämplig storleksändring av ett kluster.
