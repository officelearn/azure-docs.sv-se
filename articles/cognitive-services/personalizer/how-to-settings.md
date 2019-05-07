---
title: Konfigurera inställningar
titleSuffix: Azure Cognitive Services
description: Tjänstkonfigurationen innehåller hur tjänsten behandlar belöningar, hur ofta tjänsten utforskar, hur ofta modellen modellkomponenten och hur mycket data lagras.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: bce260d2a6b897d3e45b231df4eecd7ecb31ba31
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143249"
---
# <a name="personalizer-settings"></a>Personalizer inställningar

Tjänstkonfigurationen innehåller hur tjänsten behandlar belöningar, hur ofta tjänsten utforskar, hur ofta modellen modellkomponenten och hur mycket data lagras.

## <a name="create-personalizer-resource"></a>Skapa Personalizer resurs

Skapa en Personalizer resurs för varje feedbackloop. 

1. Logga in på [Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Den föregående länken tar dig till den **skapa** för tjänsten Personlizer. 
1. Ange tjänstnamnet, Välj en prenumeration, plats, prisnivå, och resursgruppen.
1. Välj bekräftelsen och välj **skapa**.

## <a name="configure-service-settings-in-the-azure-portal"></a>Konfigurera inställningar för tjänsten i Azure portal

1. Logga in på [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Hitta Personalizer-resursen. 
1. I den **resurshantering** väljer **inställningar**.

    Innan de lämnar Azure-portalen, kan du kopiera en av dina resursnycklar från den **nycklar** sidan. Du behöver detta för att använda den [Personalizer SDK](https://aka.ms/personalizer-dotnet-sdk-docs).

### <a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>Konfigurera inställningar för ersättning för feedbackloopen baserat på användningsfall

Konfigurera inställningar för den tjänsten för din feedback-loop användning av fördelarna. Ändringar av följande inställningar kommer återställa den aktuella Personalizer-modellen och omtrimma med de senaste 2 dagarna av data:

![Konfigurera inställningar för ersättning för feedback-loop](media/settings/configure-model-reward-settings.png)

|Inställning|Syfte|
|--|--|
|Väntetid för ersättning|Anger hur lång tid under vilken Personalizer samlar in räkning värden för rangordnas samtal, med början från den tidpunkt då rangordnas anropet sker. Det här värdet anges genom att be: ”Hur länge ska Personalizer vänta på att alla anrop”? Alla trafik som kommer efter det här fönstret kommer logga men inte använts för utbildning.|
|Standard-utmärkelse|Om ingen ersättning samtal tas emot med Personalizer under utmärkelse vänta tidsperioden som är kopplad till en rangordning anropa, Personalizer tilldelar som standard-utmärkelse. Som standard och i de flesta fall är standard-utmärkelse noll.|
|Trafik-aggregering|Om flera belöningar tas emot samma rangordning API anropa används den här sammansättningsmetod: **summan** eller **tidigaste**. Tidigaste hämtar den tidigaste poäng och ignorerar resten. Detta är användbart om du vill att unika trafik mellan eventuellt duplicerade anrop. |

När du har ändrat dessa inställningar, se till att välja **spara**.

### <a name="exploration-setting"></a>Inställningen för Kunskapsutveckling 

Anpassning kan identifiera nya mönster och anpassa sig till användaren beteendeförändringar över tid genom att utforska alternativ. Den **utforskning** inställningen bestämmer vilken procentandel av rangordnas samtal besvaras med utforskning. 

Ändringar i den här inställningen kommer återställa den aktuella Personalizer-modellen och träna om den med de senaste 2 dagarna av data.

![Utforskning-inställningen bestämmer vilken procentandel av rangordnas samtal besvaras med utforskning](media/settings/configure-exploration-setting.png)

När du har ändrat den här inställningen, se till att välja **spara**.

### <a name="model-update-frequency"></a>Uppdateringsfrekvensen för modellen

**Modellera uppdateringsfrekvensen** anger hur ofta en ny modell för Personalizer modellkomponenten. 

![Uppdateringsfrekvensen för modellen anger hur ofta en ny modell för Personalizer modellkomponenten.](media/settings/configure-model-update-frequency-settings.png)

När du har ändrat den här inställningen, se till att välja **spara**.

### <a name="data-retention"></a>Datakvarhållning

**Datalagringsperiod** anger hur många dagar Personalizer håller dataloggar. Senaste data loggar som krävs för att utföra [offline utvärderingar](concepts-offline-evaluation.md), som används för att mäta effektiviteten i Personalizer och optimera Learning principen.

När du har ändrat den här inställningen, se till att välja **spara**.

## <a name="export-the-personalizer-model"></a>Exportera Personalizer modellen

Från den resurshantering avsnittet **modell och principen**, granska modellskapandet och datum för senaste uppdatering och exportera den aktuella modellen.

![Exportera den aktuella Personalizer modellen](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Importera och exportera learning princip

Från den resurshantering avsnittet **modell och principen**, importera en ny princip för learning eller exportera den aktuella learning-principen.

## <a name="next-steps"></a>Nästa steg

[Hur du använder Personalizer behållare](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
