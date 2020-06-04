---
title: Hantera modell-och inlärnings inställningar – Personanpassare
description: Datorns modell-och utbildnings inställningar kan exporteras för säkerhets kopiering i ditt eget käll kontroll system.
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 59af5a64ea5752ec03fb44df91440e8d395e3782
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344364"
---
# <a name="how-to-manage-model-and-learning-settings"></a>Hantera modell-och inlärnings inställningar

Datorns modell-och utbildnings inställningar kan exporteras för säkerhets kopiering i ditt eget käll kontroll system.

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

## <a name="import-a-new-learning-policy"></a>Importera en ny utbildnings princip

Inställningarna för [inlärnings principen](concept-active-learning.md#understand-learning-policy-settings) bestämmer modell utbildningens _Egenskaper_ . Utför en [offline-utvärdering](how-to-offline-evaluation.md) för att hitta en ny inlärnings princip.

1. Öppna [Azure Portal](https://portal.azure.com)och välj din personanpassa resurs.
1. Välj **modell-och inlärnings inställningar** i avsnittet **resurs hantering** .
1. För inställningarna för att **Importera inlärning** väljer du den fil som du skapade med JSON-formatet som anges ovan och väljer sedan knappen **överför** .

    Vänta på meddelandet att inlärnings principen har laddats upp.

## <a name="export-a-learning-policy"></a>Exportera en utbildnings princip

1. Öppna [Azure Portal](https://portal.azure.com)och välj din personanpassa resurs.
1. Välj **modell-och inlärnings inställningar** i avsnittet **resurs hantering** .
1. För inställningarna för att **Importera inlärning** väljer du knappen **Exportera inlärnings inställningar** . Detta sparar `json` filen på den lokala datorn.

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du hanterar en utbildnings princip](how-to-manage-model.md)
