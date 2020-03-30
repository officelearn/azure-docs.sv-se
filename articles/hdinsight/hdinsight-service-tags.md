---
title: NSG-tjänsttaggar (Network Security Group) för Azure HDInsight
description: Använd HDInsight-tjänsttaggar för att tillåta inkommande trafik till klustret från HDInsight-hälso- och hanteringstjänster, utan att uttryckligen lägga till IP-adresser i dina nätverkssäkerhetsgrupper.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/10/2020
ms.openlocfilehash: a72753d5553e79a8ed28c3afcc7e54af6c2d230c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117231"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>NSG-tjänsttaggar (Network Security Group) för Azure HDInsight

HDInsight-tjänsttaggar för nätverkssäkerhetsgrupper (NSG) är grupper av IP-adresser för hälso- och hanteringstjänster. Dessa grupper hjälper till att minimera komplexiteten för att skapa säkerhetsregeln. [Tjänsttaggar](../virtual-network/security-overview.md#service-tags) är en alternativ metod för att tillåta inkommande trafik från specifika IP-adresser utan att ange var och en av [hanterings-IP-adresserna](hdinsight-management-ip-addresses.md) i nätverkssäkerhetsgrupperna.

Dessa tjänsttaggar skapas och hanteras av HDInsight-tjänsten. Du kan inte skapa ett eget servicetag eller ändra en befintlig tagg. Microsoft hanterar adressprefixen som matchar servicetag och uppdaterar automatiskt servicetaggen när adresserna ändras.

## <a name="getting-started-with-service-tags"></a>Komma igång med tjänsttaggar

Du har två alternativ för att använda tjänsttaggar i nätverkssäkerhetsgrupperna:

1. Använd en enda HDInsight-tjänsttagg – det här alternativet öppnar det virtuella nätverket för alla IP-adresser som HDInsight-tjänsten använder för att övervaka kluster i alla regioner. Det här alternativet är den enklaste metoden, men kanske inte lämpligt om du har restriktiva säkerhetskrav.

1. Använd flera regionala tjänsttaggar – det här alternativet öppnar det virtuella nätverket endast för DE IP-adresser som HDInsight använder i den specifika regionen. Men om du använder flera regioner måste du lägga till flera tjänsttaggar i det virtuella nätverket.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Använda ett enda globalt HDInsight-tjänstmärke

Det enklaste sättet att börja använda tjänsttaggar med HDInsight-klustret är att lägga till den globala taggen `HDInsight` i en regel för nätverkssäkerhetsgrupper.

1. Välj din nätverkssäkerhetsgrupp på [Azure-portalen.](https://portal.azure.com/)

1. Under **Inställningar**väljer du **Regler för inkommande säkerhet**och väljer sedan + Lägg **till**.

1. Välj **Service Tag**i listrutan **Källa** .

1. Välj **HDInsight**i listrutan **Source service tag** .

    ![Azure portal lägga till tjänst tagg](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Den här taggen innehåller IP-adresserna för hälso- och hanteringstjänster för alla regioner där HDInsight är tillgängligt och säkerställer att klustret kan kommunicera med nödvändiga hälso- och hanteringstjänster oavsett var det skapas.

## <a name="use-regional-hdinsight-service-tags"></a>Använda regionala HDInsight-tjänsttaggar

Om alternativ ett inte fungerar eftersom du behöver mer restriktiva behörigheter kan du bara tillåta de tjänsttaggar som gäller för din region. De tillämpliga tjänsttaggarna kan vara en, två eller tre tjänsttaggar, beroende på vilken region klustret skapas.

Om du vill ta reda på vilka tjänsttaggar du ska lägga till för din region läser du följande avsnitt i dokumentet.

### <a name="use-a-single-regional-service-tag"></a>Använda ett enda regionalt servicetag

Om du föredrar servicetag alternativ två och klustret finns i ett av de regioner som anges i den här tabellen, behöver du bara lägga till en enda regional tjänsttagg i nätverkssäkerhetsgruppen.

| Land/region | Region | Tjänsttagg |
| ---- | ---- | ---- |
| Australien | Australien, östra | HDInsight.AustraliaEast |
| &nbsp; | Australien, sydöstra | HDInsight.AustraliaSoutheast |
| &nbsp; | Australien, centrala | HDInsight.AustraliaCentral |
| Kina | Kina Öst 2 | HDInsight.ChinaEast2 |
| &nbsp; | Kina Norra 2 | HDInsight.ChinaNorth2 |
| USA | USA, norra centrala | HDInsight.NorthCentralUS |
| &nbsp; | USA, västra 2 | HDInsight.WestUS2 |
| &nbsp; | USA, västra centrala | HDInsight.WestCentralUS |
| Kanada | Kanada, östra | HDInsight.CanadaEast |
| Brasilien | Brasilien, södra | HDInsight.BrazilSouth |
| Korea | Sydkorea, centrala | HDInsight.KoreaCentral |
| &nbsp; | Sydkorea, södra | HDInsight.KoreaSouth |
| Indien | Indien, centrala | HDInsight.CentralIndia |
| &nbsp; | Indien, södra | HDInsight.SouthIndia |
| Japan | Japan, västra | HDInsight.JapanWest |
| Frankrike | Frankrike, centrala| HDInsight.FranceCentral |
| Storbritannien | Storbritannien, södra | HDInsight.UKSouth |
| Azure Government | USDoD Central   | HDInsight.USDoDCentral |
| &nbsp; | USGov Texas | HDInsight.USGovTexas |
| &nbsp; | UsDoD Öst | HDInsight.USDoDEast |
| &nbsp; | USGov Arizona | HDInsight.USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Använda flera regionala tjänsttaggar

Om du föredrar servicetag alternativ två och regionen där klustret skapas inte listades ovan, måste du tillåta flera regionala tjänsttaggar. Behovet av att använda mer än en beror på skillnader i arrangemanget av resursleverantörer för de olika regionerna.

De återstående regionerna är indelade i grupper baserat på vilka regionala tjänsttaggar de använder.

#### <a name="group-1"></a>Grupp 1

Om klustret skapas i något av regionerna i `HDInsight.WestUS` tabellen `HDInsight.EastUS` nedan tillåter du tjänsttaggarna och förutom den regionala servicetaggen i listan. Regioner i det här avsnittet kräver tre tjänsttaggar.

Om klustret till exempel `East US 2` skapas i regionen måste du lägga till följande tjänsttaggar i nätverkssäkerhetsgruppen:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Land/region | Region | Tjänsttagg |
| ---- | ---- | ---- |
| USA | USA, östra 2 | HDInsight.EastUS2 |
| &nbsp; | USA, centrala | HDInsight.CentralUS |
| &nbsp; | NorthCentral USA | HDInsight. Norra CentralaUS |
| &nbsp; | USA, södra centrala | HDInsight.SouthCentralUS |
| &nbsp; | USA, östra | HDInsight.EastUS |
| &nbsp; | USA, västra | HDInsight.WestUS |
| Japan | Japan, östra | HDInsight.JapanEast |
| Europa | Europa, norra | HDInsight.NorthEurope |
| &nbsp; | Europa, västra| HDInsight.WestEurope |
| Asien | Asien, östra | HDInsight.EastAsia |
| &nbsp; | Sydostasien | HDInsight.Sydöstra |
| Australien | Australien, östra | HDInsight.AustraliaEast |

#### <a name="group-2"></a>Grupp 2

Kluster i regionerna **Kina Norra** och **Kina East**, måste `HDInsight.ChinaNorth` `HDInsight.ChinaEast`tillåta två tjänst taggar: och .

#### <a name="group-3"></a>Grupp 3

Kluster i regionerna **US Gov Iowa** och **US Gov Virginia**, `HDInsight.USGovIowa` `HDInsight.USGovVirginia`måste tillåta två tjänst taggar: och .

#### <a name="group-4"></a>Grupp 4

Kluster i regionerna **Tyskland Central** och **Tyskland Nordöstra**, måste `HDInsight.GermanyCentral` `HDInsight.GermanyNorthEast`tillåta två tjänst taggar: och .

## <a name="next-steps"></a>Nästa steg

- [Nätverkssäkerhetsgrupper - tjänsttaggar](../virtual-network/security-overview.md#security-rules)
- [Skapa virtuella nätverk för Azure HDInsight-kluster](hdinsight-create-virtual-network.md)
