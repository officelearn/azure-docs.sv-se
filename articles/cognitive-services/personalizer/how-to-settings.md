---
title: Konfigurera Personanpassning
titleSuffix: Azure Cognitive Services
description: Tjänst konfigurationen omfattar hur tjänsten behandlar förmåner, hur ofta tjänsten utforskar, hur ofta modellen omtränas och hur mycket data som lagras.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: d20f81bf7db2e098f2bca674c5540bc067577f30
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75833914"
---
# <a name="configure-personalizer"></a>Konfigurera Personanpassning

Tjänst konfigurationen omfattar hur tjänsten behandlar förmåner, hur ofta tjänsten utforskar, hur ofta modellen omtränas och hur mycket data som lagras.

## <a name="create-personalizer-resource"></a>Skapa en personanpassa resurs

Skapa en personanpassa resurs för varje feedback-slinga.

1. Logga in på [Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Föregående länk tar dig till sidan **skapa** för tjänsten personanpassa.
1. Ange tjänstens namn, Välj en prenumeration, plats, pris nivå och resurs grupp.
1. Välj bekräftelse och välj **skapa**.

<a name="configure-service-settings-in-the-azure-portal"></a>

## <a name="configure-service-in-the-azure-portal"></a>Konfigurera tjänsten i Azure Portal

1. Logga in på [Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Hitta din personanpassa resurs.
1. I avsnittet **resurs hantering** väljer du **konfiguration**.

    Kopiera en av resurs nycklarna från sidan **nycklar** innan du lämnar Azure Portal. Du behöver detta för att kunna använda [PERSONANPASSA SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer).

<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

### <a name="configure-reward-for-the-feedback-loop-based-on-use-case"></a>Konfigurera belöning för feedback-slingan baserat på användnings fall

Konfigurera tjänsten för din feedback-Slings användning av förmåner. Ändringar av följande värden kommer att återställa den aktuella personanpassa modellen och träna den med de senaste 2 dagarna med data:

![Konfigurera belönings värden för feedback-slingan](media/settings/configure-model-reward-settings.png)

|Värde|Syfte|
|--|--|
|Vänte tid för belöning|Anger efter hur lång tid som Personanpassaren samlar in belönings värden för ett rang samtal, från det ögonblick då rang anropet inträffar. Det här värdet anges genom att fråga: "hur länge ska personanpassas vänta på förmåner-samtal?" Alla ersättningar som anländer efter att det här fönstret loggas men inte används för inlärning.|
|Standard belöning|Om inget belönings samtal tas emot av en Personanpassare under fönstret för fördröjnings vänte tid som är associerat med ett rang samtal, tilldelar Personanpassaren standard belöningen. Som standard, och i de flesta fall, är standard belöningen noll.|
|Belönings agg regering|Om flera fördelar tas emot för samma rang-API-anrop används denna agg regerings metod: **Sum** eller **tidigast**. Tidigaste plockning av den tidigaste poängen och tar bort resten. Detta är användbart om du vill ha unika belöningar bland eventuella dubbla samtal. |

När du har ändrat dessa värden måste du se till att välja **Spara**.

### <a name="configure-exploration"></a>Konfigurera utforskning

Anpassningar kan identifiera nya mönster och anpassa sig till användar beteende förändringar över tid genom att utforska alternativ. Värdet för **prospektering** avgör hur stor procent andel rang samtal besvaras med utforskning.

Ändringar av det här värdet kommer att återställa den aktuella personanpassa modellen och träna den med de senaste två dagarnas data.

![Värdet för prospektering avgör hur stor procent andel rang samtal besvaras med utforskning](media/settings/configure-exploration-setting.png)

När du har ändrat det här värdet ska du se till att välja **Spara**.

### <a name="model-update-frequency"></a>Uppdaterings frekvens för modell

Den senaste modellen, som tränas från belönings-API-anrop från varje aktiv händelse, används inte automatiskt av ett personligt rang anrop. **Uppdaterings frekvensen för modellen** anger hur ofta modellen som används av ranknings anropet uppdateras.

Hög modell uppdaterings frekvens är användbart i situationer där du vill spåra ändringar i användar beteenden. Exempel på detta är webbplatser som körs på direktsända nyheter, virus innehåll eller Live Product-budgivning. Du kan använda en frekvens på 15 minuter i dessa scenarier. För de flesta användnings fall är en lägre uppdaterings frekvens effektiv. Uppdaterings frekvensen på en minut är användbar när du felsöker ett programs kod med hjälp av personanpassa, gör demonstrationer eller interaktivt testar maskin inlärnings aspekter.

![Modell uppdaterings frekvens anger hur ofta en ny personanpassa modell ska omtränas.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

När du har ändrat det här värdet ska du se till att välja **Spara**.

### <a name="data-retention"></a>Datakvarhållning

**Data lagrings period** anger hur många dagars personanpassare som ska behålla data loggar. Tidigare data loggar krävs för att utföra [offline-utvärderingar](concepts-offline-evaluation.md)som används för att mäta effektiviteten hos personanpassare och optimera inlärnings policyn.

När du har ändrat det här värdet ska du se till att välja **Spara**.

## <a name="export-the-personalizer-model"></a>Exportera en personanpassa modell

I resurs hanteringens avsnitt för **modell-och inlärnings inställningar**, granska modell skapande och senaste uppdaterade datum och exportera den aktuella modellen. Du kan använda Azure Portal eller anpassade API: er för att exportera en modell fil för arkivering.

![Exportera aktuell personanpassa modell](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Rensa data för din inlärnings slinga

1. I Azure Portal för din personanpassa resurs på sidan **modell-och utbildnings inställningar** väljer du **Rensa data**.
1. Om du vill rensa alla data och återställa inlärnings slingan till det ursprungliga läget markerar du alla tre kryss rutorna.

    ![I Azure Portal rensar du data från en personanpassa resurs.](./media/settings/clear-data-from-personalizer-resource.png)

    |Värde|Syfte|
    |--|--|
    |Loggade anpassnings-och belönings data.|Dessa loggnings data används i offline-utvärderingar. Rensa data om du återställer din resurs.|
    |Återställ anpassnings modellen.|Den här modellen ändras på varje omskolning. Den här frekvensen av utbildning anges i **frekvensen för överförings modeller** på **konfigurations** sidan. |
    |Ange inlärnings principen som standard.|Om du har ändrat inlärnings principen som en del av en offline-utvärdering återställs den till den ursprungliga inlärnings principen.|

1. Välj **Rensa markerade data** för att påbörja clearing processen. Status rapporteras i Azure-meddelanden i det övre högra navigerings fönstret.

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du hanterar en utbildnings princip](how-to-learning-policy.md)
