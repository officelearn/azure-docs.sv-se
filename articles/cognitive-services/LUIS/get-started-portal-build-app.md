---
title: 'Snabbstart: Skapa ny app med LUIS-portalen'
titleSuffix: Language Understanding - Azure Cognitive Services
description: I den här snabbstarten skapar du en ny app i LUIS-portalen. Skapa de grundläggande delarna i en app, avsikter och entiteter och sedan testa genom att tillhandahålla en användare uttryck i exemplet i den interaktiva test-panelen för att få förväntade avsikten. Att skapa en app är utan kostnad. Det kräver inte en Azure-prenumeration.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: e97be28261d28c2a72e507adcdac0248691745c7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57783175"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Snabbstart: Skapa en ny app i LUIS-portalen

I den här snabbstarten skapar du en ny app i den [LUIS portal](https://www.luis.ai). Skapa de grundläggande delarna i en app, **avsikter** och **entiteter**, provar genom att tillhandahålla en användare uttryck i exemplet i den interaktiva test-panelen för att få förväntade avsikten.

Att skapa en app är utan kostnad. Det kräver inte en Azure-prenumeration. När du är redo att distribuera din app kan du skapa en Azure-Cognitive Service-resurs och tilldela den till appen. Den här distributionen är i den [nästa Snabbstart](get-started-portal-deploy-app.md).

## <a name="create-app"></a>Skapa app 

1. Öppna den [LUIS portal](https://www.luis.ai) i en webbläsare och logga in. Om det här är din första gången som loggar in, måste du skapa ett kostnadsfritt LUIS portalanvändare konto.

1. Välj **skapar en ny app** från verktygsfältet kontext.

    [![Skapa ny app i LUIS-portalen](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. I popup-fönstret kan du konfigurera appen med följande inställningar och välj sedan **klar**.

    |Inställningsnamn| Värde | Syfte|
    |--|--|--|
    |Namn|`myEnglishApp`|Unikt namn för LUIS-app<br>obligatorisk|
    |Kultur|**Engelska**|Språket i yttranden från användare, **en-us**<br>obligatorisk|
    |Beskrivning|`App made with LUIS Portal`|Beskrivning av app<br>valfri|

    ![Ange nya appinställningar](./media/get-started-portal-build-app/create-new-app-settings.png)


## <a name="create-intent"></a>Skapa avsikt 

När den här appen har skapats är nästa steg att skapa avsikter. Avsikter är ett sätt att kategorisera text från användare. Om du har en personalapp som har två funktioner: först hjälper användare att hitta och gäller för jobb och andra att hitta formulär för jobb, dessa två olika _avsikter_ justeras till följande avsikter:

|Avsikt|Exempeltexten från användare<br>kallas även en _uttryck_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

1. När appen har skapats kan du är på den **avsikter** för den **skapa** avsnittet. Välj **Create new intent** (Skapa ny avsikt). 

    [![Skapa ny avsikt knappen Välj](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Ange namnet på avsikt, `FindForm` och välj **klar**.

    ![Namn på FindForm avsikt](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-example-utterance"></a>Lägg till exempel uttryck 

När du har skapat avsikten är nästa steg att lägga till exempel yttranden. Det här är texten som angetts av användaren i en chattrobot eller andra klientprogram som mappar avsikt att användarens text till en LUIS-avsikt. 

För det här exempelprogrammet `FindForm` avsikt, exempel yttranden ska innehålla formulärnumret för med viktig information inom uttryck att klientprogrammet måste kunna uppfyller av användaren. 

Lägg till följande 15 exempel talade till den `FindForm` avsikt. 

|#|Exempel på yttranden|
|--|--|
|1|Letar du efter hrf 123456|
|2|Var finns personal formuläret hrf-234591?|
|3|hrf 345623 var är den|
|4|Är det möjligt att skicka mig hrf 345794|
|5|Behöver jag hrf 234695 ansöka om ett internt jobb?|
|6|Behöver Mina manager vet jag ansöker om ett jobb med hrf 234091|
|7|Där skickar hrf 234918? Kan jag få ett e-postsvar det togs emot?|
|8|hrf-234555|
|9|När uppdaterades hrf 234987?|
|10|Jag kan använda formuläret hrf-876345 för engineering positioner|
|11|Var en ny version av hrf-765234 skickades in om min öppna req?|
|12|Kan jag använda hrf 234234 för internationella jobb?|
|13|hrf 234598 felstavning|
|14|hrf 234567 kommer redigeras för nya krav|
|15|hrf-123456, hrf-123123, hrf-234567|

Dessa exempel yttranden variera avsiktligt, på följande sätt:

* längd på uttryck
* Skiljetecken
* Word val
* verb Tempus (är, var, kommer att)
* ordföljden

[![Ange exempel yttranden för FindForm avsikten](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

## <a name="create-regular-expression-entity"></a>Skapa entitet för reguljärt uttryck 

För att få formuläret tal som returneras som en del av förutsägelse körningssvar har formuläret som ska markeras som en entitet. Eftersom texten för formuläret är mycket strukturerade, kan det markeras med en entitet för reguljärt uttryck. Skapa entiteten med följande steg. 

1. Välj **entiteter** från den vänstra navigeringsmenyn. 

1. Välj **Skapa ny entitet** på sidan entiteter.

1. Ange namnet `Human Resources Form Number`väljer den **Regex** entitet skriver och anger det reguljära uttrycket `hrf-[0-9]{6}`. Detta matchar de vanliga tecken, `hrf-`, och tillåter exakt 6 siffror. 

    ![Ange entitetsinformation för entiteten reguljärt uttryck](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Välj **Done** (Klar). 

## <a name="add-example-utterances-to-none-intent"></a>Lägg till exempel yttranden NONE avsikt

Den **ingen** avsikt är reserv avsikten och får inte vara tomt. Den här bör ha 1 uttryck för varje 10 i resten av avsikter i appen. 

Den **ingen** avsikts exempel yttranden bör finnas utanför din programdomänen för klienten. 

1. Välj **avsikter** i den vänstra menyn, Välj **ingen** från listan över avsikter.

1. Lägg till följande exempel talade avsikten:

    |Ingen avsiktlig exempel yttranden|
    |--|
    |Skällande hundar är irriterande|
    |Beställ en pizza åt mig|
    |Pingviner i havet|

    Dessa exempel yttranden finns utanför domänen för den här personalapp. Om din personal-domän, inklusive djur, mat eller havet, sedan dessa exempel yttranden inte bör användas för den **ingen** avsikt. 

## <a name="train-the-app"></a>Träna appen

I det övre högra navigeringsfältet, väljer **träna** tillämpas avsikt och entiteten modellen ändras till den aktuella versionen av appen. 

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Titta på entiteten reguljärt uttryck i exempel-uttryck

1. Kontrollera entiteten finns i den **FindForm** avsikt genom att välja **avsikter** i den vänstra menyn, Välj **FindForm** avsikt. 

    Entiteten har markerats där visas i exemplet yttranden. Om du vill se den ursprungliga texten, i stället för entitetsnamn, ändrar du växlingsknappen **entiteter visa** från verktygsfältet.

    [![Alla exempel yttranden som markerats med entiteter](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testa din nya app med interaktiva testfönstret

Använd det interaktiva **Test** fönstret i LUIS-portalen för att verifiera entiteten ska extraheras från nya yttranden som appen inte har sett ännu.

1. Välj **Test** från menyn längst upp till höger.

1. Lägg till en ny uttryck och tryck på RETUR:

    ```Is there a form named hrf-234098```

    ![Testa nya uttryck i Testfönster](./media/get-started-portal-build-app/test-new-utterance.png)

    Upp förutse avsikten är korrekt **FindForm** med över 90% förtroende (0.977) och **personalfrågor formuläret nummer** entitet extraheras med ett värde av hrf 234098. 

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar med den här snabbstarten och du inte är fortsätter till nästa Snabbstart, Välj **Mina appar** från den övre navigeringsmenyn. Sedan markerar appens vänstra kryssrutan i listan och välj **ta bort** från kontexten verktygsfältet ovanför listan. 

[![Ta bort appen från listan över Mina appar](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [2. Distribuera en app](get-started-portal-deploy-app.md)