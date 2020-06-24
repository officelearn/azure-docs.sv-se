---
title: 'Gör så här: Konfigurera parametern som externa entiteter-entitet'
titleSuffix: Azure Cognitive Services
description: I den här artikeln förklarar vi hur du konfigurerar en sträng parameter för att referera till katalogen som exponeras över en webb slut punkt.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: sausin
ms.openlocfilehash: 5417ff3b53526adbaeba2b9df58694ad495475d3
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307955"
---
# <a name="add-configurations-to-commands-parameters"></a>Lägg till konfigurationer i parametrar för kommandon

I den här artikeln får du lära dig mer om konfiguration av avancerad parameter, inklusive:

 - Hur parameter värden kan tillhöra en uppsättning som definierats externt till anpassade kommando program
 - Lägga till verifierings satser i värdet för parametrarna

## <a name="prerequisites"></a>Krav

Du måste ha slutfört stegen i följande artiklar:

> [!div class="checklist"]
> * [Så här: skapa program med enkla kommandon](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Gör så här: lägga till parametrar till kommandon](./how-to-custom-commands-add-parameters-to-commands.md)


## <a name="configure-parameter-as-external-catalog-entity"></a>Konfigurera parametern som extern katalog-entitet

I det här avsnittet konfigurerar du sträng typ parametrar för att referera till externa kataloger som är värdbaserade över en webb slut punkt. På så sätt kan du uppdatera den externa katalogen oberoende utan att göra ändringar i programmet för anpassade kommandon. Den här metoden är användbar i fall där katalog posterna kan vara stora i tal.

Återanvänd parametern **SubjectDevice** från kommandot **TurnOnOff** . Den aktuella konfigurationen för den här parametern **accepterar fördefinierade indata från den interna katalogen**. Detta avser en statisk lista över enheter som definieras i parameter konfigurationen. Vi vill flytta ut det här innehållet till en extern data källa som kan uppdateras oberoende av varandra.

Börja med att lägga till en ny webb slut punkt. Gå till avsnittet **webb slut punkter** i det vänstra fönstret och Lägg till en ny webb slut punkt med följande konfiguration.

| Inställningen | Föreslaget värde |
|----|----|
| Name | `getDevices` |
| URL | `https://aka.ms/speech/cc-sampledevices` |
| Metod | HÄMTA |


Om det föreslagna värdet för URL inte fungerar för dig måste du konfigurera och vara värd för en enkel webb slut punkt som returnerar en JSON-enhet som består av en lista över de enheter som kan kontrol leras. Webb slut punkten ska returnera en JSON-formaterad på följande sätt:
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```


Gå sedan till sidan parameter inställningar för **SubjectDevice** och ändra egenskaperna till följande.

| Inställningen | Föreslaget värde |
| ----| ---- |
| Konfiguration | Acceptera fördefinierade indata från extern katalog |                               
| Katalog slut punkt | getDevices |
| Metod | HÄMTA |

Välj sedan **Spara**.

> [!IMPORTANT]
> Du kan inte se ett alternativ för att konfigurera en parameter att acceptera indata från en extern katalog om du inte har angett webb slut punkten i avsnittet **webb slut punkt** i det vänstra fönstret.

### <a name="try-it-out"></a>Prova nu

Välj **träna** och vänta på slut för ande av utbildning. När inlärningen är klar väljer du **testa** och prova några interaktioner.

* Inmatade: Aktivera
* Utdata: vilken enhet vill du styra?
* Inmatade: lampor
* Utdata: OK, aktivera indikeringar

> [!NOTE]
> Observera hur du kan styra alla enheter som finns på webb slut punkten nu. Du måste fortfarande träna programmet för att testa de nya ändringarna och publicera programmet på nytt.

## <a name="add-validation-to-parameters"></a>Lägg till validering i parametrar

**Valideringar** är tillämpliga för vissa parameter typer som gör att du kan konfigurera begränsningar för parameterns värde och fråga efter korrigering om värden inte faller inom begränsningarna. En fullständig lista över parameter typer som utökar validerings konstruktionen finns i [referenser](./custom-commands-references.md)

Testa verifieringar med kommandot **SetTemperature** . Använd följande steg för att lägga till en validering för **temperatur** parametern.

1. Välj kommandot **SetTemperature** i det vänstra fönstret.
1. Välj **temperatur** i fönstret i mitten.
1. Välj **Lägg till en verifiering** i den högra rutan.
1. I fönstret **ny validering** konfigurerar du verifiering enligt följande och väljer **skapa**.


    | Parameter konfiguration | Föreslaget värde | Beskrivning |
    | ---- | ---- | ---- |
    | Minvärde | `60` | För Number-parametrar kan det minsta värdet som denna parameter anta |
    | Maxvärde | `80` | För Number-parametrar kan det högsta värdet som den här parametern anta |
    | Haveri svar |  Enkel redigerare > första varianten >`Sorry, I can only set temperature between 60 and 80 degrees` | Fråga om du vill ange ett nytt värde om valideringen Miss lyckas |

    > [!div class="mx-imgBorder"]
    > ![Lägg till en intervall validering](media/custom-commands/add-validations-temperature.png)

### <a name="try-it-out"></a>Prova nu

1. Välj **träna** ikon överst i den högra rutan.

1. När inlärningen är klar väljer du **test** och prova några interaktioner:

    - Inmatade: Ange temperatur till 72 grader
    - Utdata: OK, ställa in temperatur på 72 grader
    - Inmatade: Ange temperatur till 45 grader
    - Utdata: det går bara att ange temperatur mellan 60 och 80 grader
    - Inmatade: gör det till 72 grader i stället
    - Utdata: OK, ställa in temperatur på 72 grader

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gör så här: Lägg till interaktions regler](./how-to-custom-commands-add-interaction-rules.md)
