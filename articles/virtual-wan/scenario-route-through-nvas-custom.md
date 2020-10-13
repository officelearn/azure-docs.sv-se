---
title: 'Scenario: dirigera trafik via NVA med anpassade inställningar'
titleSuffix: Azure Virtual WAN
description: Det här scenariot hjälper dig att dirigera trafik via NVA med en annan NVA för Internet-baserad trafik.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: e1cf9faeab60264d491539256828151e496ade8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267507"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>Scenario: dirigera trafik genom NVA – anpassad (för hands version)

När du arbetar med virtuell WAN-routning för virtuella WAN finns det några tillgängliga scenarier. I det här NVA-scenariot (Network Virtual installation) är målet att dirigera trafik genom en NVA för kommunikation mellan virtuella nätverk och grenar och använda en annan NVA för Internet-baserad trafik. Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Design

I det här scenariot använder vi namngivnings konventionen:

* "Tjänstens VNet" för virtuella nätverk där användare har distribuerat en NVA (VNet 4 i **bild 1**) för att inspektera trafik som inte är Internet.
* "DMZ VNet" för virtuella nätverk där användare har distribuerat en NVA som ska användas för att kontrol lera Internet-bundit trafik (VNet 5 i **bild 1**).
* "NVA ekrar" för virtuella nätverk som är anslutna till ett NVA VNet (VNet 1, VNet 2 och VNet 3 i **bild 1**).
* "Hubbar" för Microsoft-hanterade virtuella WAN-nav.

Följande anslutnings mat ris sammanfattar de flöden som stöds i det här scenariot:

**Anslutnings mat ris**

| Från          | Till:|*NVA pinnar*|*Service VNet*|*DMZ VNet*|*Grenar statiska*|
|---|---|---|---|---|---|
| **NVA pinnar**| &#8594;|      X |            X |   Peering |    Statisk    |
| **Service VNet**| &#8594;|    X |            X |      X    |      X       |
| **DMZ VNet** | &#8594;|       X |            X |      X    |      X       |
| **Grenar** | &#8594;|  Statisk |            X |      X    |      X       |

Var och en av cellerna i anslutnings matrisen beskriver om en virtuell WAN-anslutning ("från"-sidan av flödet, rad rubrikerna) lär sig ett måltema ("till"-sidan i flödet, kolumn rubrikerna i kursiv stil) för ett särskilt trafikflöde. Ett "X" innebär att anslutningen tillhandahålls internt av Virtual WAN och "static" innebär att anslutningen tillhandahålls av Virtual WAN med statiska vägar. Vi går igenom detalj över de olika raderna:

* NVA pinnar:
  * Ekrar kommer att komma åt andra ekrar direkt över virtuella WAN-hubbar.
  * Ekrar får anslutning till grenar via en statisk väg som pekar på tjänstens VNet. De bör inte lära sig vissa prefix från grenarna (annars skulle de vara mer exakta och åsidosätta sammanfattningen).
  * Ekrar kommer att skicka Internet trafik till DMZ VNet via en direkt VNet-peering.
* Delar
  * Grenar får ekrar via en statisk routning som pekar på tjänstens VNet. De bör inte lära sig vissa prefix från virtuella nätverk som åsidosätter den sammanfattade statiska vägen.
* Tjänstens VNet kommer att likna ett VNet för delade tjänster som behöver kunna bli tillgängligt från varje VNet och varje gren.
* DMZ VNet behöver inte ha någon anslutning via det virtuella WAN-nätverket eftersom den enda trafik som stöds kommer att komma över direkta VNet-peering. Vi kommer dock att använda samma anslutnings modell som för DMZ VNet för att förenkla konfigurationen.

Därför ger vår anslutnings mat ris tre distinkta anslutnings mönster, som översätts till tre väg tabeller. Associationerna till de olika virtuella nätverk är följande:

* NVA pinnar:
  * Associerad routningstabell: **RT_V2B**
  * Sprider till routningstabeller: **RT_V2B** och **RT_SHARED**
* NVA virtuella nätverk (internt och Internet):
  * Associerad routningstabell: **RT_SHARED**
  * Sprider till routningstabeller: **RT_SHARED**
* Delar
  * Associerad routningstabell: **standard**
  * Sprider till routningstabeller: **RT_SHARED** och **standard**

Vi behöver dessa statiska vägar för att säkerställa att VNet-till-gren-och gren-till-VNet-trafik går genom NVA i tjänstens VNet (VNet 4):

| Beskrivning | Routningstabell | Statisk väg              |
| ----------- | ----------- | ------------------------- |
| Grenar    | RT_V2B      | 10.2.0.0/16-> vnet4conn  |
| NVA pinnar  | Default     | 10.1.0.0/16-> vnet4conn  |

Nu vet Virtual WAN vilken anslutning som ska skicka paketen till, men anslutningen måste veta vad du ska göra när du tar emot dessa paket: det är här som anslutnings väg tabellerna används.

| Beskrivning | Anslutning | Statisk väg            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16-> 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16-> 10.4.0.5 |

Nu bör allt vara på plats.

Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Arkitektur

I **bild 1**finns det en hubb, **hubb 1**.

* **Hubb 1** är direkt ansluten till NVA virtuella nätverk **VNet 4** och **VNet 5**.

* Trafik mellan virtuella nätverk 1, 2 och 3 och grenar (VPN/ER/P2S) förväntas gå via **VNet 4 NVA** 10.4.0.5.

* All Internet bindnings trafik från virtuella nätverk 1, 2 och 3 förväntas gå via **VNet 5 NVA** 10.5.0.5.

**Bild 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Bild 1":::

## <a name="workflow"></a><a name="workflow"></a>Arbetsflöde

Om du vill konfigurera routning via NVA följer du stegen nedan:

1. För att Internet-bunditnde trafik ska gå via VNet 5 behöver du virtuella nätverk 1, 2 och 3 för att ansluta via VNet-peering till VNet 5 direkt. Du behöver också en UDR som kon figurer ATS i virtuella nätverk för 0.0.0.0/0 och nästa hopp 10.5.0.5. För närvarande tillåter inte Virtual WAN en nästa hopp-NVA i den virtuella hubben för 0.0.0.0/0.

1. I Azure Portal navigerar du till den virtuella hubben och skapar en anpassad väg tabell **RT_Shared** som kommer att lära sig vägar via spridning från alla virtuella nätverk-och förgrenings anslutningar. I **bild 2**visas detta som en tom anpassad väg tabell **RT_Shared**.

   * **Vägar:** Du behöver inte lägga till några statiska vägar.

   * **Association:** Välj virtuella nätverk 4 och 5, vilket innebär att virtuella nätverk 4 och 5 anslutningar är kopplade till routningstabellen **RT_Shared**.

   * **Spridning:** Eftersom du vill att alla grenar och VNet-anslutningar ska sprida sina vägar dynamiskt till den här routningstabellen väljer du grenar och alla virtuella nätverk.

1. Skapa en anpassad routningstabell **RT_V2B** för att dirigera trafik från virtuella nätverk 1, 2 och 3 till grenar.

   * **Vägar:** Lägg till en sammanställd statisk väg post för grenar (VPN/ER/P2S) (10.2.0.0/16 i **bild 2**) med nästa hopp som VNet 4-anslutningen. Du måste också konfigurera en statisk väg i VNet 4-anslutning för förgrenings-prefix och ange nästa hopp som ska vara den speciella IP-adressen för NVA i VNet 4.

   * **Association:** Välj alla virtuella nätverk 1, 2 och 3. Detta innebär att VNet-anslutningarna 1, 2 och 3 associeras med denna routningstabell och kan lära sig vägar (statiskt och dynamiskt via spridning) i den här routningstabellen.

   * **Spridning:** Anslutningar sprider vägar till routningstabeller. Om du väljer virtuella nätverk 1, 2 och 3 aktive ras spridning av vägar från virtuella nätverk 1, 2 och 3 till den här väg tabellen. Det finns inget behov av att sprida vägar från förgrenings anslutningar till RT_V2B, medan grenen VNet-trafik går via NVA i VNet 4.
  
1. Redigera standard väg tabellen **DefaultRouteTable**.

   Alla VPN-, ExpressRoute-och användares VPN-anslutningar är kopplade till standard väg tabellen. Alla VPN-, ExpressRoute-och användares VPN-anslutningar sprider vägar till samma uppsättning routningstabeller.

   * **Vägar:** Lägg till en sammanställd statisk väg post för virtuella nätverk 1, 2 och 3 (10.1.0.0/16 i **bild 2**) med nästa hopp som VNet 4-anslutningen. Du måste också konfigurera en statisk väg i VNet 4-anslutning för VNet 1, 2 och 3 aggregerade prefix och ange nästa hopp som ska vara den speciella IP-adressen för NVA i VNet 4.

   * **Association:** Se till att alternativet för grenar (VPN/ER/P2S) är markerat, och se till att lokala gren anslutningar är kopplade till *defaultroutetable*.

   * **Spridningen från:** Se till att alternativet för grenar (VPN/ER/P2S) är markerat, och se till att lokala anslutningar sprider vägar till *defaultroutetable*.

**Bild 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Bild 1" lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
