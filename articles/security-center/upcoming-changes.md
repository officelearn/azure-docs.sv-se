---
title: Viktiga ändringar som kommer till Azure Security Center
description: Kommande ändringar av Azure Security Center som du kan behöva känna till och för vilka du kan behöva planera
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: memildin
ms.openlocfilehash: df863372cbf7abfb6fee145b7d13bb00d8deb074
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380183"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Viktiga kommande ändringar i Azure Security Center

> [!IMPORTANT]
> Informationen på den här sidan avser för hands versions produkter eller funktioner som kan ändras i stort sett innan de släpps kommersiellt. Microsoft lämnar inga åtaganden eller garantier, vare sig uttryckliga eller underförstådda, med avseende på den information som anges här.

På den här sidan får du lära dig om ändringar som planeras för Security Center. Den beskriver planerade ändringar av produkten som kan påverka saker som dina säkra poäng eller arbets flöden.

Om du letar efter den senaste versions informationen hittar du dem i [vad som är nytt i Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planerade ändringar

### <a name="system-updates-should-be-installed-on-your-machines-recommendation-getting-sub-recommendations"></a>"System uppdateringar bör installeras på dina datorer" rekommendationer för att få del rekommendationer

#### <a name="summary"></a>Sammanfattning

| Aspekt | Information |
|---------|---------|
|Meddelande datum | 9 november 2020  |
|Datum för den här ändringen  |  Mid-end november 2020 |
|Påverkan     | Under över gången från den aktuella versionen av den här rekommendationen kan din säkra Poäng ändras. |
|  |  |

Vi publicerar en förbättrad version av **system uppdateringarna bör installeras på din dator** rekommendation. Den nya versionen *ersätter* den aktuella versionen i säkerhets kontrollen tillämpa system uppdateringar och gör följande förbättringar:

- Under rekommendationer för varje saknad uppdatering
- En omdesignad upplevelse på Azure Security Center sidor i Azure Portal
- Omfattande data för rekommendationen från Azure Resource Graph

#### <a name="transition-period"></a>Över gångs period

Det kommer att finnas en över gångs period på 36 timmar (ungefär). För att minimera eventuella eventuella avbrott har vi schemalagt att uppdateringen ska ske under en helg. Under över gången kan dina säkra Poäng påverkas.

#### <a name="redesigned-portal-experience"></a>Omdesignad Portal upplevelse

Rekommendations informations sidan för **System uppdateringar bör installeras på dina datorer** och innehåller en lista över de resultat som visas nedan. När du väljer en enskild sökning öppnas informations fönstret med en länk till reparations informationen och en lista över resurser som påverkas.

:::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Öppna en av under rekommendationerna i Portal upplevelsen för den uppdaterade rekommendationen":::


#### <a name="richer-data-from-azure-resource-graph"></a>Bättre data från Azure Resource Graph

Azure Resource Graph är en tjänst i Azure som är utformad för att tillhandahålla effektiv resurs utforskning. Du kan använda ARG för att fråga i skala över en specifik uppsättning prenumerationer så att du effektivt kan styra din miljö. 

För Azure Security Center kan du använda ARG och [KQL (Kusto Query Language)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) för att fråga efter en mängd säkerhets position data.

Om du frågar den aktuella versionen av **System uppdateringar ska installeras på dina datorer** är den enda tillgängliga informationen från arg att rekommendationen måste åtgärdas på en dator. När den uppdaterade versionen släpps, returnerar följande fråga de system uppdateringar som saknas grupperade efter dator.

```kusto
securityresources
| where type =~ "microsoft.security/assessments/subassessments"
| where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
| where properties.status.code == "Unhealthy"
```

## <a name="next-steps"></a>Nästa steg

För alla nyligen gjorda ändringar av produkten, se [Vad är nytt i Azure Security Center?](release-notes.md).