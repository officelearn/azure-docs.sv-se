---
title: 'Gör så här: använda anpassade kommandon med anpassad röst tal tjänst'
titleSuffix: Azure Cognitive Services
description: I den här artikeln anger du utdata-rösten för ett anpassat kommandon-program.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: sausin
ms.openlocfilehash: b1e488653f210089ee5ed6757fb2f3d1e4fb0fe4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87294549"
---
# <a name="use-custom-commands-with-custom-voice"></a>Använda anpassade kommandon med Anpassad röst

I den här artikeln får du lära dig hur du väljer en anpassad röst för utdata för ett anpassat kommando program.

## <a name="select-a-custom-voice"></a>Välj en anpassad röst

1. I programmet anpassade kommandon väljer du **Inställningar** i det vänstra fönstret.
1. Välj **anpassad röst** från fönstret i mitten.
1. Välj önskad anpassad eller offentlig röst i tabellen.
1. Välj **Spara**.

> [!div class="mx-imgBorder"]
> ![Exempel på meningar med parametrar](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - För **offentliga röster**är **neurala-typer** bara tillgängliga för vissa regioner. För att kontrol lera tillgängligheten, se [standard-och neurala röster efter region/slut punkt](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
> - För **anpassade röster**kan de skapas från sidan anpassat röst projekt. Se [Kom igång med anpassad röst](./how-to-custom-voice.md).

Programmet kommer nu att svara i den valda rösten i stället för standard rösten.
