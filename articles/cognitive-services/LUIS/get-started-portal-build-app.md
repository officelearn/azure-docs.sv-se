---
title: 'Snabbstart: Skapa en ny app i LUIS-portalen'
titleSuffix: Language Understanding - Azure Cognitive Services
description: I den här snabbstarten skapar du en ny app i LUIS-portalen. Skapa de grundläggande delarna i en app, avsikter och entiteter. Testa appen genom att tillhandahålla en användare uttryck i exemplet i den interaktiva test-panelen för att få förväntade avsikten. Att skapa en app är utan kostnad. Det kräver inte en Azure-prenumeration.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 2e4ff7dc97e3ee72336bd4c081caf1aa1a62bc56
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146567"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Snabbstart: Skapa en ny app i LUIS-portalen

I den här snabbstarten skapar du en ny app den [LUIS portal](https://www.luis.ai). Först skapar du de grundläggande delarna i en app, **avsikter**, och **entiteter**. Sedan kan du testa appen genom att tillhandahålla en användare uttryck i exemplet i den interaktiva test-panelen för att få förväntade avsikten.

Att skapa en app är kostnadsfritt och kräver inte en Azure-prenumeration. När du är redo att distribuera din app, visas den [Snabbstart för att distribuera en app](get-started-portal-deploy-app.md). Den visar hur du skapar en Azure-Cognitive Service-resurs och tilldela den till appen.

## <a name="create-an-app"></a>Skapa en app

1. Öppna den [LUIS portal](https://www.luis.ai) i en webbläsare och logga in. Om det är första gången du loggar in, måste du skapa ett kostnadsfritt LUIS portalanvändare konto.

1. Välj **skapar en ny app** från verktygsfältet kontext.

   [![Skapa ny app i LUIS-portalen](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. I popup-fönstret kan du konfigurera appen med följande inställningar och välj sedan **klar**.

   |Inställningsnamn| Värde | Syfte|
   |--|--|--|
   |Namn|`myEnglishApp`|Unikt namn för LUIS-app<br>obligatorisk|
   |Kultur|**Engelska**|Språket i yttranden från användare, **en-us**<br>obligatorisk|
   |Beskrivning|`App made with LUIS Portal`|Beskrivning av app<br>valfri|
   | | | |

   ![Ange nya appinställningar](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Skapa avsikter

När LUIS-app har skapats kan behöva du skapa avsikter. Avsikter är ett sätt att kategorisera text från användare. En personalapp kan till exempel ha två funktioner. Hjälper användare att:

 1. Hitta och gäller för jobb
 1. Hitta formulär för jobb

Appen är två olika _avsikter_ justeras till följande avsikter:

|Avsikt|Exempeltexten från användare<br>kallas även en _uttryck_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Om du vill skapa avsikter, gör du följande:

1. När appen har skapats kan du är på den **avsikter** för den **skapa** avsnittet. Välj **Create new intent** (Skapa ny avsikt).

   [![Skapa ny avsikt knappen Välj](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Ange namnet på avsikt, `FindForm`, och välj sedan **klar**.

   ![Namn på FindForm avsikt](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-an-example-utterance"></a>Lägg till en exempel-uttryck

Du kan lägga till exempel yttranden när du har skapat avsikter. Exempel yttranden är text som en användare anger i en chattrobot eller andra klientprogram. De mappar avsikt att användarens text till en LUIS-avsikt.

För det här exempelprogrammet `FindForm` avsikt, exempel yttranden ska innehålla formulärnumret för. Klientprogrammet måste det tal som formuläret ska uppfylla av användaren, så det är viktigt att inkludera den i uttryck.

[![Ange exempel yttranden för FindForm avsikten](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

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

Av utformningen kan variera dessa exempel yttranden på följande sätt:

* längd på uttryck
* Skiljetecken
* Word val
* verb Tempus (är, var, kommer att)
* ordföljden



## <a name="create-a-regular-expression-entity"></a>Skapa en entitet för reguljärt uttryck

För att returnera antalet formuläret i förutsägelse körningssvar måste formatet vara markerad som en entitet. Eftersom texten för formuläret är mycket strukturerade, kan du markera den med hjälp av en entitet för reguljärt uttryck. Skapa entiteten med följande steg:

1. Välj **entiteter** på menyn till vänster.

1. Välj **Skapa ny entitet** på den **entiteter** sidan.

1. Ange namnet `Human Resources Form Number`väljer den **Regex** entitet skriver och anger det reguljära uttrycket `hrf-[0-9]{6}`. Den här posten matchar bokstäver, `hrf-`, och tillåter exakt 6 siffror.

   ![Ange entitetsinformation för entiteten reguljärt uttryck](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Välj **Done** (Klar).

## <a name="add-example-utterances-to-the-none-intent"></a>Lägga till exempelyttranden i avsikten Ingen

Den **ingen** avsikt är reserv avsikten och får inte vara tomt. Den här ska innehålla ett uttryck för varje 10 exempel yttranden som du har lagt till för de andra metoderna för appen.

Den **ingen** avsikts exempel yttranden bör finnas utanför din programdomänen för klienten.

1. Välj **avsikter** från den vänstra menyn och välj sedan **ingen** från listan över avsikter.

1. Lägg till följande exempel talade avsikten:

   |Ingen avsiktlig exempel yttranden|
   |--|
   |Skällande hundar är irriterande|
   |Beställ en pizza åt mig|
   |Pingviner i havet|

   Dessa exempel yttranden finns utanför domänen för den här personalapp. Om din personal-domän är djur, mat eller havet så bör du använda olika exempel yttranden för den **ingen** avsikt.

## <a name="train-the-app"></a>Träna appen

I menyn i det övre högra hörnet, väljer **träna** tillämpas avsikt och entiteten modellen ändras till den aktuella versionen av appen.

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Titta på entiteten reguljärt uttryck i exempel-uttryck

1. Kontrollera entiteten finns i den **FindForm** avsikt genom att välja **avsikter** menyn till vänster. Välj sedan **FindForm** avsikt.

   Entiteten har markerats där visas i exemplet yttranden. Om du vill se den ursprungliga texten i stället för entitetsnamnet växla **entiteter visa** från verktygsfältet.

   [![Alla exempel yttranden som markerats med entiteter](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testa din nya app med interaktiva testfönstret

Använd det interaktiva **Test** fönstret i LUIS-portalen för att verifiera att entiteten ska extraheras från nya yttranden som appen inte har sett ännu.

1. Välj **Test** från menyn längst upp till höger.

1. Lägg till en ny uttryck och tryck sedan på RETUR:

   ```Is there a form named hrf-234098```

   ![Testa nya uttryck i Testfönster](./media/get-started-portal-build-app/test-new-utterance.png)

   Upp förutse avsikten är korrekt **FindForm** med över 90% förtroende (0.977). Den **personalfrågor formuläret nummer** entitet extraheras med ett värde av hrf 234098.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabbstarten och inte fortsätter till nästa Snabbstart, Välj **Mina appar** från den övre navigeringsmenyn. Välj sedan appens vänstra kryssrutan från listan och välj **ta bort** från kontexten verktygsfältet ovanför listan.

[![Ta bort appen från listan över Mina appar](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [2. Distribuera en app](get-started-portal-deploy-app.md)
