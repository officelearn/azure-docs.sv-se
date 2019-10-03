---
title: Konfigurera inställningar – Personanpassare
titleSuffix: Azure Cognitive Services
description: Tjänst konfigurationen omfattar hur tjänsten behandlar förmåner, hur ofta tjänsten utforskar, hur ofta modellen omtränas och hur mycket data som lagras.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: diberry
ms.openlocfilehash: bad581fbc53292b5a7c25157ef839e07f33e131e
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827885"
---
# <a name="personalizer-settings"></a>Inställningar för personanpassa

Tjänst konfigurationen omfattar hur tjänsten behandlar förmåner, hur ofta tjänsten utforskar, hur ofta modellen omtränas och hur mycket data som lagras.

## <a name="create-personalizer-resource"></a>Skapa en personanpassa resurs

Skapa en personanpassa resurs för varje feedback-slinga. 

1. Logga in på [Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Föregående länk tar dig till sidan **skapa** för tjänsten personanpassa. 
1. Ange tjänstens namn, Välj en prenumeration, plats, pris nivå och resurs grupp.
1. Välj bekräftelse och välj **skapa**.

## <a name="configure-service-settings-in-the-azure-portal"></a>Konfigurera tjänst inställningar i Azure Portal

1. Logga in på [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Hitta din personanpassa resurs. 
1. I avsnittet **resurs hantering** väljer du **Inställningar**.

    Kopiera en av resurs nycklarna från sidan **nycklar** innan du lämnar Azure Portal. Du behöver detta för att kunna använda [PERSONANPASSA SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer).

### <a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>Konfigurera belönings inställningar för feedback-slingan baserat på användnings fall

Konfigurera tjänstens inställningar för din feedback-Slings användning av förmåner. Ändringar av följande inställningar kommer att återställa den aktuella personanpassa modellen och träna den med de senaste två dagarna med data:

![Konfigurera belönings inställningarna för feedback-slingan](media/settings/configure-model-reward-settings.png)

|Inställning|Syfte|
|--|--|
|Väntetid för ersättning|Anger efter hur lång tid som Personanpassaren samlar in belönings värden för ett rang samtal, från det ögonblick då rang anropet inträffar. Det här värdet anges genom att fråga: "Hur lång tid ska personanpassa att vänta på belönings samtal?" Alla ersättningar som anländer efter att det här fönstret loggas men inte används för inlärning.|
|Standardersättning|Om inget belönings samtal tas emot av en Personanpassare under fönstret för fördröjnings vänte tid som är associerat med ett rang samtal, tilldelar Personanpassaren standard belöningen. Som standard, och i de flesta fall, är standard belöningen noll.|
|Belöna sammansättning|Om flera fördelar tas emot för samma rang-API-anrop används denna agg regerings metod: **Sum** eller **tidigast**. Tidigaste plockning av den tidigaste poängen och tar bort resten. Detta är användbart om du vill ha unika belöningar bland eventuella dubbla samtal. |

När du har ändrat de här inställningarna ska du se till att välja **Spara**.

### <a name="exploration-setting"></a>Utforsknings inställning 

Anpassningar kan identifiera nya mönster och anpassa sig till användar beteende förändringar över tid genom att utforska alternativ. Inställningen **för att avgöra** hur stor procent andel rang samtal besvaras med utforskning. 

Ändringar av den här inställningen återställer den aktuella personanpassa modellen och tränar den med de senaste två dagarnas data.

![Inställningen för att avgöra hur stor procent andel rang samtal besvaras med utforskning](media/settings/configure-exploration-setting.png)

När du har ändrat den här inställningen, se till att välja **Spara**.

### <a name="model-update-frequency"></a>Modellens uppdateringsfrekvens

Den senaste modellen, som tränas från belönings-API-anrop från varje aktiv händelse, används inte automatiskt av ett personligt rang anrop. **Uppdaterings frekvensen för modellen** anger hur ofta modellen som används av ranknings anropet uppdateras. 

Hög modell uppdaterings frekvens är användbart i situationer där du vill spåra ändringar i användar beteenden. Exempel på detta är webbplatser som körs på direktsända nyheter, virus innehåll eller Live Product-budgivning. Du kan använda en frekvens på 15 minuter i dessa scenarier. För de flesta användnings fall är en lägre uppdaterings frekvens effektiv. Uppdaterings frekvensen på en minut är användbar när du felsöker ett programs kod med hjälp av personanpassa, gör demonstrationer eller interaktivt testar maskin inlärnings aspekter.

![Modell uppdaterings frekvens anger hur ofta en ny personanpassa modell ska omtränas.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

När du har ändrat den här inställningen, se till att välja **Spara**.

### <a name="data-retention"></a>Datakvarhållning

**Data lagrings period** anger hur många dagars personanpassare som ska behålla data loggar. Tidigare data loggar krävs för att utföra [offline-utvärderingar](concepts-offline-evaluation.md)som används för att mäta effektiviteten hos personanpassare och optimera inlärnings policyn.

När du har ändrat den här inställningen, se till att välja **Spara**.

## <a name="export-the-personalizer-model"></a>Exportera en personanpassa modell

I resurs hanteringens avsnitt för **modell och princip**, granska modell skapande och senaste uppdaterade datum och exportera den aktuella modellen. Du kan använda Azure Portal eller anpassade API: er för att exportera en modell fil för arkivering. 

![Exportera aktuell personanpassa modell](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Importera och exportera utbildnings princip

Från resurs hanteringens avsnitt för **modell och princip**importerar du en ny utbildnings princip eller exporterar den aktuella inlärnings principen.
Du kan hämta learning policy-filer från tidigare exporter eller ladda ned de optimerade principerna som upptäcks under en offline-utvärdering. Om du gör manuella ändringar av de här filerna påverkas maskin inlärnings prestanda och precisionen för offline-utvärderingar, och Microsoft kan inte skriva ut för att ge maskin inlärning och utvärderingar, eller tjänst undantag som orsakas av manuellt redigerade principer.

## <a name="clear-data-for-your-learning-loop"></a>Rensa data för din inlärnings slinga

1. I Azure Portal för din personanpassa resurs går du till sidan **modell och princip** och väljer **Rensa data**.
1. Om du vill rensa alla data och återställa inlärnings slingan till det ursprungliga läget markerar du alla tre kryss rutorna.

    ![I Azure Portal rensar du data från en personanpassa resurs.](./media/settings/clear-data-from-personalizer-resource.png)

    |Inställning|Syfte|
    |--|--|
    |Loggade anpassnings-och belönings data.|Dessa loggnings data används i offline-utvärderingar. Rensa data om du återställer din resurs.|
    |Återställ anpassnings modellen.|Den här modellen ändras på varje omskolning. Den här frekvensen av utbildning anges i **frekvensen för överförings modeller** på sidan **Inställningar** . |
    |Ange inlärnings principen som standard.|Om du har ändrat inlärnings principen som en del av en offline-utvärdering återställs den till den ursprungliga inlärnings principen.|

1. Välj **Rensa markerade data** för att påbörja clearing processen. Status rapporteras i Azure-meddelanden i det övre högra navigerings fönstret. 

## <a name="next-steps"></a>Nästa steg

<!--
[How to use the Personalizer container](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
-->
[Lär dig mer om regions tillgänglighet](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
