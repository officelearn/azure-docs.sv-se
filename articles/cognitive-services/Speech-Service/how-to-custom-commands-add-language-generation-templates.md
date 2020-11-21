---
title: 'Så här gör du: använda mallar för språk skapande för tal svar – tal tjänst'
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig hur du använder mallar för att skapa språk med anpassade kommandon. Med språkgenerationens mallar kan du anpassa svaren som skickas till klienten och introducera varians i svaren.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 40c5e3474d3992108ef61d34e745bc63c1f7a713
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020954"
---
# <a name="add-language-generation-templates-for-speech-responses"></a>Lägga till mallar för att skapa språk för talsvar

I den här artikeln får du lära dig hur du använder mallar för att skapa språk med anpassade kommandon. Med språkgenerationens mallar kan du anpassa svaren som skickas till klienten och introducera varians i svaren. Anpassningen av språket kan uppnås genom att:

- Användning av mallar för språk skapande
- Användning av anpassningsbara uttryck

## <a name="prerequisites"></a>Förutsättningar

Du måste ha slutfört stegen i följande artiklar:

> [!div class="checklist"]
> * [Så här: skapa program med enkla kommandon](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Gör så här: lägga till parametrar till kommandon](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="language-generation-templates-overview"></a>Översikt över mallar för språk generation

Mallar för anpassade kommandon baseras på BotFramework: s [LG-mallar](/azure/bot-service/file-format/bot-builder-lg-file-format#templates). Eftersom anpassade kommandon skapar en ny LG-mall vid behov (det vill säga för tal svar i parametrar eller åtgärder) behöver du inte ange namnet på LG-mallen. Så i stället för att definiera mallen som:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Du behöver bara definiera bröd texten i mallen utan namnet, enligt följande.

> [!div class="mx-imgBorder"]
> ![exempel på template Editor](./media/custom-commands/template-editor-example.png)


Den här ändringen introducerar variationer i de tal svar som skickas till klienten. För samma uttryck skulle det motsvarande tal svaret slumpmässigt plockas ut från de tillhandahållna alternativen.

Genom att dra nytta av LG-mallar kan du också definiera komplexa tal svar för kommandon med hjälp av anpassade uttryck. Du kan läsa mer om [LG templates-formatet](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) . Anpassade kommandon som standard har stöd för alla funktioner med följande mindre skillnader:

* I entiteterna för LG-mallar visas som $ {entityName}. I anpassade kommandon använder vi inte entiteter, men parametrar kan användas som variabler med någon av dessa representationer $ {parameterName} eller {parameterName}
* Mallens sammansättning och expansion stöds inte i anpassade kommandon. Detta beror på att du aldrig redigerar `.lg` filen direkt, utan bara svaren på automatiskt skapade mallar.
* Anpassade funktioner som matas in av LG stöds inte i anpassade kommandon. Fördefinierade funktioner stöds fortfarande.
* Alternativ (Strict, replaceNull & lineBreakStyle) stöds inte i anpassade kommandon.

## <a name="add-template-responses-to-turnonoff-command"></a>Lägg till mallar svar till kommandot TurnOnOff

Ändra kommandot **TurnOnOff** för att lägga till en ny parameter med följande konfiguration:

| Inställning            | Föreslaget värde       | 
| ------------------ | --------------------- | 
| Name               | `SubjectContext`         | 
| Är global          | avmarkerat             | 
| Obligatorisk           | avmarkerat               | 
| Typ               | Sträng                |
| Standardvärde      | `all` |
| Konfiguration      | Acceptera fördefinierade indatavärden från intern katalog | 
| Fördefinierade indatavärden | `room`, `bathroom`, `all`|

### <a name="modify-completion-rule"></a>Ändra regel för slut för ande

Redigera avsnittet **åtgärder** i den befintliga regeln för slut för ande av **ConfirmationResponse**. I popup-fönstret **Redigera åtgärd** växlar du till redigeraren för **mallar** och ersätter texten med följande exempel.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

**Träna** och **testa** ditt program på följande sätt. Observera variationen av svar på grund av användning av flera alternativ för mallnamnet, och även användning av anpassningsbara uttryck.

* Inmatade: slå på TV
* Utdata: OK, aktivera TV på
* Inmatade: slå på TV
* Utdata: färdig, aktive rad på TV
* Inmatade: Stäng av lamporna
* Utdata: OK, vilket stänger av alla lampor
* Inmatade: inaktivera rums lampor
* Utdata: OK, inaktivera rums belysningen

## <a name="use-custom-voice"></a>Använda anpassad röst

Ett annat sätt att anpassa anpassade kommando svar är att välja en anpassad röst för utdata. Använd följande steg för att växla standard rösten till en anpassad röst.

1. I programmet anpassade kommandon väljer du **Inställningar** i det vänstra fönstret.
1. Välj **anpassad röst** från fönstret i mitten.
1. Välj önskad anpassad eller offentlig röst i tabellen.
1. Välj **Spara**.

> [!div class="mx-imgBorder"]
> ![Exempel på meningar med parametrar](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - För **offentliga röster** är **neurala-typer** bara tillgängliga för vissa regioner. För att kontrol lera tillgängligheten, se [standard-och neurala röster efter region/slut punkt](./regions.md#standard-and-neural-voices).
> - För **anpassade röster** kan de skapas från sidan anpassat röst projekt. Se [Kom igång med anpassad röst](./how-to-custom-voice.md).

Programmet kommer nu att svara i den valda rösten i stället för standard rösten.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Integrera dina anpassade kommandon med hjälp av tal-SDK](./how-to-custom-commands-setup-speech-sdk.md).