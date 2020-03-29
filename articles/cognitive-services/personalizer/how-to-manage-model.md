---
title: Hantera modell- och inlärningsinställningar - Personalizer
description: Den maskininlärda modellen och inlärningsinställningarna kan exporteras för säkerhetskopiering i ditt eget källkontrollsystem.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c544d058019c8d507f65dc6bfd854376b364abd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624301"
---
# <a name="how-to-manage-model-and-learning-settings"></a>Så här hanterar du modell- och inlärningsinställningar

Den maskininlärda modellen och inlärningsinställningarna kan exporteras för säkerhetskopiering i ditt eget källkontrollsystem.

## <a name="export-the-personalizer-model"></a>Exportera Personalizer-modellen

I avsnittet Resurshantering för **modell- och utbildningsinställningar**granskar du skapandet av modeller och senast uppdaterade datum och exporterar den aktuella modellen. Du kan använda Azure-portalen eller Personalizer-API:erna för att exportera en modellfil för arkivering.

![Exportera aktuell personalizer-modell](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Rensa data för din utbildningsloop

1. På sidan **Modell- och inlärningsinställningar** på sidan Modell- och utbildningsinställningar i Azure-portalen väljer du **Rensa data**på sidan Modell- och utbildningsinställningar .
1. Om du vill rensa alla data och återställa inlärningsloopen till det ursprungliga tillståndet markerar du alla tre kryssrutor.

    ![Rensa data från Personalizer-resursen i Azure-portalen.](./media/settings/clear-data-from-personalizer-resource.png)

    |Värde|Syfte|
    |--|--|
    |Loggade anpassnings- och belöningsdata.|Den här loggningsdata används i offlineutvärderingar. Rensa data om du återställer resursen.|
    |Återställ Personalizer-modellen.|Denna modell ändras på varje omskolning. Den här utbildningsfrekvensen anges i **uppladdningsmodellfrekvensen** på **sidan Konfiguration.** |
    |Ange att utbildningsprincipen ska standard.|Om du har ändrat utbildningsprincipen som en del av en offlineutvärdering återställs den till den ursprungliga utbildningsprincipen.|

1. Välj **Rensa markerade data** för att påbörja clearingprocessen. Status rapporteras i Azure-meddelanden i navigeringen längst upp till höger.

## <a name="import-a-new-learning-policy"></a>Importera en ny utbildningspolitik

[Inställningarna för inlärningsprincipen](concept-active-learning.md#understand-learning-policy-settings) bestämmer _modellutbildningens hyperparametrar._ Gör en [offlineutvärdering](how-to-offline-evaluation.md) för att hitta en ny inlärningsprincip.

1. Öppna [Azure-portalen](https://portal.azure.com)och välj din Personalizer-resurs.
1. Välj **Modell- och utbildningsinställningar** i avsnittet **Resurshantering.**
1. För **inställningarna för importinlärning** väljer du den fil som du skapade med det JSON-format som anges ovan och väljer sedan **knappen Ladda upp.**

    Vänta på meddelandet om att utbildningsprincipen har överförts.

## <a name="export-a-learning-policy"></a>Exportera en utbildningspolitik

1. Öppna [Azure-portalen](https://portal.azure.com)och välj din Personalizer-resurs.
1. Välj **Modell- och utbildningsinställningar** i avsnittet **Resurshantering.**
1. För **inställningarna för Importera inlärning** väljer du knappen **Exportera utbildningsinställningar.** Då sparas `json` filen på den lokala datorn.

## <a name="next-steps"></a>Nästa steg

[Läs om hur du hanterar en utbildningspolitik](how-to-manage-model.md)
