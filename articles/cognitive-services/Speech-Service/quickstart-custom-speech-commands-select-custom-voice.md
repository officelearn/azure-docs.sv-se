---
title: 'Snabb start: Använd anpassade kommandon med anpassad röst (för hands version) – tal tjänst'
titleSuffix: Azure Cognitive Services
description: I den här artikeln anger du utdata-rösten för ett anpassat kommandon-program.
services: cognitive-services
author: anhoang
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: anhoang
ms.openlocfilehash: c2b9b4d51e89975d988ed94bf85695bd8a1cc770
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872458"
---
# <a name="quickstart-use-custom-commands-with-custom-voice-preview"></a>Snabb start: Använd anpassade kommandon med anpassad röst (förhands granskning)

I [föregående artikel](./quickstart-custom-speech-commands-create-parameters.md)skapade vi ett nytt anpassat kommando-projekt för att svara på kommandon med parametrar.

I den här artikeln väljer vi en anpassad röst för utdata för det program som vi har skapat.

## <a name="select-a-custom-voice"></a>Välj en anpassad röst

1. Öppna projektet som [vi skapade tidigare](./quickstart-custom-speech-commands-create-parameters.md)
1. Välj **Inställningar** i den vänstra rutan
1. Välj **anpassad röst** från fönstret i mitten
1. Välj önskad anpassad eller offentlig röst i tabellen
1. Välj **Spara**

> [!div class="mx-imgBorder"]
> ![Exempel på meningar med parametrar](media/custom-speech-commands/select-custom-voice.png)

> [!NOTE]
> - För **offentliga röster**är **neurala-typer** bara tillgängliga för vissa regioner. För att kontrol lera tillgängligheten, se [standard-och neurala röster efter region/slut punkt](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
> - För **anpassade röster**kan de skapas från sidan anpassat röst projekt. Se [Kom igång med anpassad röst](./how-to-custom-voice.md).

Programmet kommer nu att svara i den valda rösten i stället för standard rösten.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Snabb start: ansluta till ett anpassat kommando program med talet SDK (för hands version)](./quickstart-custom-speech-commands-speech-sdk.md)

