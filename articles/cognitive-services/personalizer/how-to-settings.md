---
title: Konfigurera Personanpassning
description: Tjänstkonfigurationen innehåller hur tjänsten behandlar belöningar, hur ofta tjänsten utforskar, hur ofta modellen tränas om och hur mycket data som lagras.
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: ac31a9f907defeb44dbd4748a4395d3aec34d30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219361"
---
# <a name="configure-personalizer-learning-loop"></a>Konfigurera Personalizer-inlärningsslinga

Tjänstkonfigurationen innehåller hur tjänsten behandlar belöningar, hur ofta tjänsten utforskar, hur ofta modellen tränas om och hur mycket data som lagras.

Konfigurera utbildningsloopen på **sidan Konfiguration,** i Azure-portalen för den Personalizer-resursen.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="configure-rewards-for-the-feedback-loop"></a>Konfigurera belöningar för feedbackloopen

Konfigurera tjänsten för din utbildningsloop användning av belöningar. Ändringar av följande värden återställer den aktuella Personalizer-modellen och tränar om den med de senaste 2 dagarnas data.

> [!div class="mx-imgBorder"]
> ![Konfigurera belöningsvärdena för feedbackloopen](media/settings/configure-model-reward-settings.png)

|Värde|Syfte|
|--|--|
|Väntetid för belöning|Anger hur länge Personalizer samlar in belöningsvärden för ett rank-samtal, från det ögonblick då rank-samtalet inträffar. Det här värdet ställs in genom att fråga: "Hur länge ska Personalizer vänta på belöningssamtal?" Eventuell belöning som anländer efter det här fönstret loggas men inte användas för lärande.|
|Standardbelöning|Om inget belöningssamtal tas emot av Personalizer under fönstret Belöningstid som är kopplat till ett rankningssamtal, tilldelas Personalizer standardbelöningen. Som standard, och i de flesta fall är standardbelöningen noll (0).|
|Belöningsaggregering|Om flera belöningar tas emot för samma Rank API-anrop används den här aggregeringsmetoden: **summa** eller **tidigast**. Tidigast plockar den tidigaste poängen emot och kasserar resten. Detta är användbart om du vill ha en unik belöning bland eventuellt dubbla samtal. |

När du har ändrat dessa värden ska du se till att välja **Spara**.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>Konfigurera utforskning så att inlärningsslingan kan anpassas

Anpassning kan upptäcka nya mönster och anpassa sig till förändringar i användarbeteendet över tid genom att utforska alternativ istället för att använda den tränade modellens förutsägelse. **Prospekteringsvärdet** avgör hur stor procentandel av Rank-samtal som besvaras med utforskning.

Ändringar av det här värdet återställer den aktuella Personalizer-modellen och tränar om den med de senaste 2 dagarnas data.

![Prospekteringsvärdet avgör hur stor procentandel av Rank-samtalen som besvaras med prospektering](media/settings/configure-exploration-setting.png)

När du har ändrat det här värdet kontrollerar du att du väljer **Spara**.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>Konfigurera modelluppdateringsfrekvens för modellutbildning

**Modelluppdateringsfrekvensen** anger hur ofta modellen tränas.

|Frekvensinställning|Syfte|
|--|--|
|1 minut|Uppdateringsfrekvenser på en minut är användbara när du **felsöker** ett programs kod med Personalizer, gör demonstrationer eller interaktivt testar maskininlärningsaspekter.|
|15 minuter|Hög modell uppdatering frekvenser är användbara för situationer där du vill **noga spåra ändringar** i användarbeteenden. Exempel är webbplatser som körs på direktsända nyheter, viralt innehåll eller budgivning med liveprodukter. Du kan använda en 15-minuters frekvens i dessa scenarier. |
|1 timme|För de flesta användningsfall är en lägre uppdateringsfrekvens effektiv.|

![Modelluppdateringsfrekvensen anger hur ofta en ny Personalizer-modell tränas om.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

När du har ändrat det här värdet kontrollerar du att du väljer **Spara**.

## <a name="data-retention"></a>Datakvarhållning

**Datalagringsperioden** anger hur många dagar Personalizer behåller dataloggar. Tidigare dataloggar krävs för att utföra [offlineutvärderingar](concepts-offline-evaluation.md), som används för att mäta effektiviteten hos Personalizer och optimera utbildningspolitiken.

När du har ändrat det här värdet kontrollerar du att du väljer **Spara**.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>Inställningar som inkluderar återställning av modellen

Följande åtgärder omfattar en omedelbar omskolning av modellen med de senaste 2 dagarnas data.

* Belöning
* Utforskning

Om du vill [rensa](how-to-manage-model.md) alla data använder du sidan **Modell- och utbildningsinställningar ** .

## <a name="next-steps"></a>Nästa steg

[Läs om hur du hanterar din modell](how-to-manage-model.md)
