---
title: Service tag gen för nätverks säkerhets gruppen (NSG) för Azure HDInsight
description: Använd HDInsight-tjänstens taggar för att tillåta inkommande trafik till ditt kluster från HDInsight-tjänster för hälso-och hanterings tjänster utan att uttryckligen lägga till IP-adresser i dina nätverks säkerhets grupper.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/19/2019
ms.openlocfilehash: 7e3ce33bdf0773ababe5eb190877a9288c094c5c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74187090"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>Service tag gen för nätverks säkerhets gruppen (NSG) för Azure HDInsight

HDInsight Service-taggar för nätverks säkerhets grupper (NSG: er) är grupper med IP-adresser för hälso-och hanterings tjänster. Dessa grupper bidrar till att minimera komplexiteten vid skapande av säkerhets regler. [Service Taggar](../virtual-network/security-overview.md#service-tags) tillhandahåller en alternativ metod för att tillåta inkommande trafik från vissa IP-adresser utan att ange var och en av [hanterings-IP-adresserna](hdinsight-management-ip-addresses.md) i dina nätverks säkerhets grupper.

Dessa service märken skapas och hanteras av HDInsight-tjänsten. Du kan inte skapa en egen service tag eller ändra en befintlig tagg. Microsoft hanterar de adressprefix som matchar tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresserna ändras.

## <a name="getting-started-with-service-tags"></a>Komma igång med service märken

Du har två alternativ för att använda tjänst Taggar i dina nätverks säkerhets grupper:

1. Använd en enda tagg för HDInsight-tjänsten – det här alternativet öppnar ditt virtuella nätverk till alla IP-adresser som HDInsight-tjänsten använder för att övervaka kluster i alla regioner. Det här alternativet är den enklaste metoden, men det kanske inte är lämpligt om du har begränsade säkerhets krav.

1. Använd flera regionala service märken – med det här alternativet öppnas det virtuella nätverket bara de IP-adresser som HDInsight använder i den aktuella regionen. Om du använder flera regioner måste du dock lägga till flera service märken i det virtuella nätverket.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Använd en enda global HDInsight Service-tagg

Det enklaste sättet att börja använda service märken med ditt HDInsight-kluster är att lägga till den globala taggen `HDInsight` i en regel för nätverks säkerhets grupp. Instruktioner för hur du lägger till tjänst Taggar i nätverks säkerhets gruppen finns i [säkerhets grupper: service Taggar](../virtual-network/security-overview.md#service-tags).

Den här taggen innehåller IP-adresserna för hälso-och hanterings tjänster för alla regioner där HDInsight är tillgängligt, och säkerställer att klustret kan kommunicera med nödvändiga hälso-och hanterings tjänster oavsett var de skapas.

## <a name="use-regional-hdinsight-service-tags"></a>Använda regionala HDInsight Service-Taggar

Om alternativet inte fungerar eftersom du behöver mer restriktiva behörigheter kan du bara tillåta de service märken som gäller för din region. Lämpliga service märken kan vara en, två eller tre tjänst etiketter, beroende på den region där klustret skapas.

Om du vill ta reda på vilka service märken som ska läggas till för din region läser du följande avsnitt i dokumentet.

### <a name="use-a-single-regional-service-tag"></a>Använd en enda regional service tag

Om du föredrar service tag-alternativet två, och klustret finns i någon av de regioner som anges i den här tabellen, behöver du bara lägga till en enda regional service-tagg i nätverks säkerhets gruppen.

| Country | Region | Service tag |
| ---- | ---- | ---- |
| Australien | Östra Australien | HDInsight. AustraliaEast |
| &nbsp; | Sydöstra Australien | HDInsight. AustraliaSoutheast |
| &nbsp; | Australien, centrala | HDInsight. AustraliaCentral |
| Kina | Kina, östra 2 | HDInsight. ChinaEast2 |
| &nbsp; | Kina, norra 2 | HDInsight. ChinaNorth2 |
| USA | Norra centrala USA | HDInsight. Usanorracentrala |
| &nbsp; | Västra USA 2 | HDInsight. WestUS2 |
| &nbsp; | Västra centrala USA | HDInsight. WestCentralUS |
| Kanada | Östra Kanada | HDInsight. Indien |
| Brasilien | Södra Brasilien | HDInsight. centrala |
| Korea | Sydkorea, centrala | HDInsight. centrala |
| &nbsp; | Sydkorea, södra | HDInsight. Koreasödra |
| Indien | Indien, centrala | HDInsight. Kanada |
| &nbsp; | Indien, södra | HDInsight. Australienöstra |
| Japan | Västra Japan | HDInsight. Japanvästra |
| Frankrike | Frankrike, centrala| HDInsight. FranceCentral |
| Storbritannien | Storbritannien, södra | HDInsight. UKSouth |
| Azure Government (Fairfax) | USDoD Central   | HDInsight. USDoDCentral |
| &nbsp; | USGov Texas | HDInsight. USGovTexas |
| &nbsp; | UsDoD, öst | HDInsight. USDoDEast |

### <a name="use-multiple-regional-service-tags"></a>Använda flera regionala tjänst etiketter

Om du föredrar service tag-alternativet två, och den region där klustret skapas inte listas ovan, så måste du tillåta flera regionala service märken. Behovet av att använda mer än ett beror på skillnaderna i consensusöverenskommelsen för resurs leverantörer i de olika regionerna.

De återstående regionerna är indelade i grupper baserat på vilka regionala tjänst Taggar de använder.

#### <a name="group-1"></a>Grupp 1

Om klustret har skapats i någon av regionerna i tabellen nedan kan du tillåta service tag `HDInsight.WestUS` och `HDInsight.EastUS` utöver den regionala service tag gen som anges. Regioner i det här avsnittet kräver tre tjänst etiketter.

Om klustret till exempel skapas i `East US 2` region, måste du lägga till följande service taggar till nätverks säkerhets gruppen:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Country | Region | Service tag |
| ---- | ---- | ---- |
| USA | USA, östra 2 | HDInsight. EastUS2 |
| &nbsp; | Centrala USA | HDInsight. centrala |
| &nbsp; | NorthCentral oss | HDInsight. Usanorracentrala |
| &nbsp; | Södra centrala USA | HDInsight. Usasödracentrala |
| &nbsp; | East US | HDInsight. öst |
| &nbsp; | Västra USA | HDInsight. väst |
| Japan | Östra Japan | HDInsight. Japanöstra |
| Europa | Norra Europa | HDInsight. Europanorra |
| &nbsp; | Västra Europa| HDInsight. WestEurope |
| Asien | Östasien | HDInsight. Asienöstra |
| &nbsp; | Sydostasien | HDInsight. Sydostasien |
| Australien | Östra Australien | HDInsight. AustraliaEast |

#### <a name="group-2"></a>Grupp 2

Kluster i regionerna **Kina, norra** och **Kina, östra**måste tillåta två service märken: `HDInsight.ChinaNorth` och `HDInsight.ChinaEast`.

#### <a name="group-3"></a>Grupp 3

Kluster i regionerna **US gov, Iowa** och **US gov, Virginia**måste tillåta två service märken: `HDInsight.USGovIowa` och `HDInsight.USGovVirginia`.

#### <a name="group-4"></a>Grupp 4

Kluster i regionerna **Tyskland, centrala** och **Tyskland nordöstra**, måste tillåta två service märken: `HDInsight.GermanyCentral` och `HDInsight.GermanyNorthEast`.

## <a name="next-steps"></a>Nästa steg

* [Nätverks säkerhets grupper – tjänst etiketter](../virtual-network/security-overview.md#security-rules)
* [Skapa virtuella nätverk för Azure HDInsight-kluster](hdinsight-create-virtual-network.md)
