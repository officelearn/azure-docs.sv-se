---
title: 'Självstudiekurs: Integrera med Power Virtual Agent - QnA Maker'
description: I den här självstudien kan du förbättra kvaliteten på din kunskapsbas med aktivt lärande. Granska, acceptera eller avvisa, lägg till utan att ta bort eller ändra befintliga frågor.
ms.topic: tutorial
ms.date: 03/11/2020
ms.openlocfilehash: 283667c587e395a1d712f82f3385582b4c5c3227
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398879"
---
# <a name="tutorial-add-knowledge-base-to-power-virtual-agent"></a>Självstudiekurs: Lägg till kunskapsbas i Power Virtual Agent
Skapa och utöka en [Power Virtual Agent-robot](https://powervirtualagents.microsoft.com/) för att ge svar från din kunskapsbas.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Skapa virtuell energiagent
> * Skapa tillbakafallsämne för system
> * Lägg till QnA Maker som åtgärd i ämnet som Power Automate-flöde
> * Skapa Power Automate-lösning
> * Lägg till Power Automate-flöde till lösning
> * Den virtuella agenten Publicera energi
> * Test Power Virtual Agent, få svar från QnA Maker kunskapsbas

## <a name="integrate-a-power-virtual-agent-with-a-knowledge-base"></a>Integrera en Virtuell Power-agent med en kunskapsbas

[Power Virtual Agents](https://powervirtualagents.microsoft.com/) gör det möjligt för team att enkelt skapa kraftfulla robotar med hjälp av ett guidat grafiskt gränssnitt utan kod utan behov av datavetare eller utvecklare.

En Power Virtual Agent skapas med en rad ämnen (ämnesområden), för att svara på användarfrågor genom att utföra åtgärder. Om det inte går att hitta ett svar kan ett systemåtergång returnera ett svar.

Konfigurera agenten så att frågan skickas till kunskapsbasen som en del av ett ämnes åtgärd eller som en del av ämnessökvägen **för systemfallback.** De båda använder samma mekanism för en åtgärd för att ansluta till din kunskapsbas och returnera ett svar.

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate ansluter till GenerateAnswer-åtgärden

Om du vill ansluta din agent till din kunskapsbas använder du Power Automate för att skapa åtgärden. Power Automate tillhandahåller ett **processflöde**som ansluter till QnA Maker's GenerateAnswer API.

När **flödet** har utformats och sparats är det tillgängligt från en Power **Automate-lösning**.  När GenerateAnswer-flödet har lagts till i en lösning använder du den lösningen som en åtgärd i din agent.

## <a name="process-steps-to-connect-an-agent-to-your-knowledge-base"></a>Bearbeta steg för att ansluta en agent till din kunskapsbas

Följande steg presenteras som en översikt som hjälper dig att förstå hur stegen relaterar till målet att ansluta en Power Virtual Agent till en QnA Maker-kunskapsbas.

Steg för att använda en Power Virtual agent med QnA Maker:
* I [QnA Maker](https://www.qnamaker.ai/) portal
    * Skapa och publicera kunskapsbas
    * Kopiera kunskapsbasinformation, inklusive kunskapsbas-ID, slutpunktsnyckel för körning och slutpunktsvärd för körning.
* I [Power Virtual Agent-portalen](https://powerva.microsoft.com/)
    * Skapa agentämne
    * Anropa en åtgärd (till Power Automate Flow)
* I [Power Automate-portalen](https://us.flow.microsoft.com/)
    * Skapa ett flöde med anslutning till [QnA Maker's GenerateAnswer](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)
        * QnA Maker publicerade information om kunskapsbasen
            * Id för kunskapsbas
            * Slutpunktsvärd för QnA Maker-resurs
            * Slutpunktsnyckel för QnA Maker-resurs
        * Indata - användarfråga
        * Output - kunskapsbas svar
    * Skapa lösning och lägga till flöde
* Återgå till Power Virtual Agent
    * Välj lösningens utdata som meddelande för ämne

## <a name="create-and-publish-a-knowledge-base"></a>Skapa och publicera en kunskapsbas

1. Följ [snabbstarten](../Quickstarts/create-publish-knowledge-base.md) för att skapa en kunskapsbas. Fyll inte i det sista avsnittet för att skapa en robot. Den här självstudien ersätter det sista avsnittet i snabbstarten eftersom den här självstudien använder Power Virtual Agent för att skapa en bot, i stället för Bot Framework-roboten i snabbstarten.

    > [!div class="mx-imgBorder"]
    > ![Ange dina publicerade kunskapsbasinställningar som finns på **Inställningar** påhttps://www.qnamaker.ai/) portalen [QnA Maker](](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Du behöver den här informationen för [Power Automate-steget](#create-power-automate-flow-to-connect-to-your-knowledge-base) för att konfigurera QnA Maker GenerateAnswer-anslutningen.

1. Hitta slutpunktsnyckeln, slutpunktsvärden och kunskapsbas-ID:t på sidan **Inställningar** i QnA Maker-portalen.

## <a name="create-power-virtual-agent"></a>Skapa virtuell energiagent

1. [Logga in](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409) på Power Virtual Agent med ditt e-postkonto för skolan eller arbetet.
1. Om detta är din första bot, bör du vara på **hemsidan** för agenten. Om detta inte är din första Power Virtual Agent, välj bot från övre högra navigering och välj **+ Ny Bot**.

    > [!div class="mx-imgBorder"]
    > ![Ange dina publicerade kunskapsbasinställningar som finns på **Inställningar** påhttps://www.qnamaker.ai/) portalen [QnA Maker](](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="several-topics-are-provided-in-the-bot"></a>Flera ämnen finns i bot

Agenten använder ämnessamlingen för att svara på frågor i ditt ämnesområde. I den här självstudien har agenten många ämnen som tillhandahålls för dig, indelade i **användarämnen** och **systemämnen**.

> [!div class="mx-imgBorder"]
> ![Agenten använder ämnessamlingen för att svara på frågor i ditt ämnesområde. I den här självstudien har agenten många ämnen som tillhandahålls för indelade i ** Användarämnen ** och ** System ämnen **.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-power-virtual-agents-system-fallback-topic"></a>Skapa power virtual agents system reservämne

Agenten kan ansluta till din kunskapsbas från vilket ämne som helst, men den här självstudien använder avsnittet System **Fallback.** Reservämnet används när agenten inte kan hitta ett svar. Agenten skickar användarens text till QnA Maker's GenerateAnswer API, får svaret från din kunskapsbas och visar den tillbaka till användaren som ett meddelande.

1. Välj sidan **Inställningar** längst upp till höger i navigeringen i portalen [Power Virtual Agents.](https://powerva.microsoft.com/#/) Ikonen för den här sidan är växeln. Välj **System-återställning**.

    > [!div class="mx-imgBorder"]
    > ![Menyalternativ för Power Virtual agent för System Fallback](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. I **fönstret** Inställningar för popup väljer du + **Lägg till** för att lägga till ett systemfallsavsnitt.

    > [!div class="mx-imgBorder"]
    > ![Lägg till reservämne i fönstret Inställningar.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. När ämnet har lagts till väljer du **Gå till fallback-ämne** för att skapa fallet Fallback på redigeringsarbetsytan.

    > [!TIP]
    > Om du behöver återgå till fallet Återgång är det tillgängligt i avsnittet **Ämnen,** som en del av **systemavsnitten.**

## <a name="use-authoring-canvas-to-add-an-action"></a>Använda redigeringsarbetsytan för att lägga till en åtgärd

Använd den power-virtuella agenter som skapar arbetsytan för att ansluta reservavsnittet till din kunskapsbas. Avsnittet börjar med den **okända användartexten**. Lägg till en åtgärd som skickar texten till QnA Maker och visar sedan svaret som ett meddelande. Det sista steget för att visa ett svar hanteras som ett [separat steg](#add-solutions-flow-to-power-virtual-agent) senare i den här självstudien.

I det här avsnittet skapas konversationsflödet för reservämnet.

1. Den nya återgångsåtgärden kanske redan har konversationsflödeselement. Ta bort **alternativet Eskalera** genom att välja Alternativ-menyn.

    > [!div class="mx-imgBorder"]
    > ![Starta återställningsåtgärd med utlösarfraser](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Markera **+** kopplingen som flödar i rutan **Meddelande** och välj sedan **Anropa en åtgärd**.

    > [!div class="mx-imgBorder"]
    > ![Anropa en åtgärd](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Välj **skapa ett flöde**. Processen tar dig till **Power Automate**, en annan webbläsarbaserad portal.

    > [!div class="mx-imgBorder"]
    > ![Anropa en åtgärd](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-power-automate-flow-to-connect-to-your-knowledge-base"></a>Skapa Power Automate Flow för att ansluta till din kunskapsbas

I följande procedur skapas ett **Power Automate-flöde** som:
* tar den inkommande användartexten
* skickar den till QnA Maker
* tilldelar QnA Maker-toppsvaret till en variabel
* skickar variabeln (det översta svaret) som svar tillbaka till din agent

1. I **Power Automate**startas **flödesmallen** åt dig. På flödesobjektet **Power Virtual Agents** väljer du **Redigera** för att konfigurera indatavariabeln som kommer från agenten till din kunskapsbas. Den textbaserade indatavariabeln är den textfråga som skickas in från din agent.

    > [!div class="mx-imgBorder"]
    > ![Konfigurera indatavariabeln som en textsträng](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Lägg till en textinmatning och ge variabeln `InputText` ett namn med en beskrivning av `IncomingUserQuestion`. Det här namnet hjälper till att skilja indatatexten från den utdatatext som du skapar senare.

    > [!div class="mx-imgBorder"]
    > ![Lägg till en textinmatning och namnge variabeln "InputText" med en beskrivning av "UserQuestion"](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Markera **+** kopplingen som flödar från rutan **Virtuella Power-agenter** om du vill infoga ett nytt steg i flödet (före **returvärdet till Power Virtual Agent**) och välj sedan Lägg till en **åtgärd**.

1. Sök `Qna` efter för att hitta **QnA Maker-åtgärderna** och välj sedan **Generera svar**.

    > [!div class="mx-imgBorder"]
    > ![Sök efter "Qna" för att hitta **QnA Maker** åtgärder, välj sedan **Generera svar**](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    De tre (3) nödvändiga anslutningsinställningarna för QnA Maker visas i åtgärden och frågeinställningarna från Power Virtual Agent.

    > [!div class="mx-imgBorder"]
    > ![Anslutningsinställningarna för QnA Maker visas i åtgärden.](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Konfigurera åtgärden med kunskapsbas-ID, slutpunktsvärd och slutpunktsnyckel. Dessa finns på sidan **Inställningar** i din kunskapsbas i QnA Maker-portalen.

    > [!div class="mx-imgBorder"]
    > ![Ange dina publicerade kunskapsbasinställningar som finns på **Inställningar** påhttps://www.qnamaker.ai/) portalen [QnA Maker](](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. Om du vill konfigurera **frågan**markerar du `InputText` textrutan och markerar sedan listan i listan.

1. Om du vill infoga ett nytt **+** steg i flödet markerar du kopplingen som flödar från åtgärdsrutan **Generera svar** och väljer sedan **Lägg till en åtgärd**.

1. Om du vill lägga till en variabel för att fånga svarstexten som returneras från GenerateAnswer söker du efter och väljer `Initialize variable` åtgärden.

    Ange namnet på variabeln till `OutgoingQnAAnswer`och välj typen som **Sträng**. Ange inte **värdet**.

    > [!div class="mx-imgBorder"]
    > ![Ange namnet på variabeln till "QnAAnswer" och välj typen som **String**](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Om du vill infoga ett nytt **+** steg i flödet markerar du kopplingen som flödar från rutan **Initiera variabel** och väljer sedan **Lägg till en åtgärd**.

1. Om du vill ställa in hela JSON-svaret på`Apply to each` variabeln söker du efter och väljer åtgärden. Välj GenerateAnswer `answers`.

1. Om du bara vill returnera det översta svaret väljer du Lägg till **en åtgärd**i samma Använd för **varje** ruta . Sök efter och välj **Ange variabel**.

    Markera textrutan för **Namn**i **variabeln Ange** och välj sedan **UtgåendeQnAAnswer** i listan.

    Markera textrutan för **Värde**och välj sedan **Svar i** listan.

    > [!div class="mx-imgBorder"]
    > ![Ange namn och värde för variabeln](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. Om du vill returnera variabeln (och dess värde) väljer du **returvärdet till flödesobjektet För verklig agent** och väljer sedan **Redigera**och sedan Lägga till **en utdata**. Markera en textutmatningstyp `FinalAnswer`och ange sedan **rubrik** för . **Text** Markera textrutan för **värde**och `OutgoingQnAAnswer` välj sedan variabeln.

    > [!div class="mx-imgBorder"]
    > ![Ange returvärdet](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Välj **Spara** om du vill spara flödet.

## <a name="create-solution-and-add-flow"></a>Skapa lösning och lägga till flöde

För att Power Virtual Agent ska kunna hitta och ansluta till flödet måste flödet inkluderas i en Power Automate-lösning.

1. När du fortfarande är kvar i Power Automate-portalen väljer du **Lösningar** från navigering på vänster sida.

1. Välj **+ Ny lösning**.

1. Ange ett visningsnamn. Listan över lösningar innehåller alla lösningar i din organisation eller skola. Välj en namngivningskonvention som hjälper dig att filtrera till just `jondoe-power-virtual-agent-qnamaker-fallback`dina lösningar, till exempel att prefixa din e-post till ditt lösningsnamn: .

1. Välj utgivare i listan med alternativ.

1. Acceptera standardvärdena för namnet och versionen.

1. Välj **Skapa** för att slutföra processen.

## <a name="add-flow-to-solution"></a>Lägg till flöde i lösning

1. Välj den lösning som du just skapade i listan över lösningar. Det bör vara högst upp på listan. Om den inte är det söker du efter ditt e-postnamn, som är en del av lösningsnamnet.

1. Välj **+ Lägg till befintlig**i lösningen och välj sedan **Flöde** i listan.

1. Hitta ditt flöde och välj sedan **Lägg till** för att slutföra processen. Om det finns många flöden tittar du på kolumnen **Ändrad** för att hitta det senaste flödet.

## <a name="add-solutions-flow-to-power-virtual-agent"></a>Lägg till lösningens flöde i Power Virtual Agent

1. Gå tillbaka till webbläsarfliken med din Virtuella Power-agent. Författarduken bör fortfarande vara öppen.

1. Markera **+** kopplingen under åtgärdsrutan **Meddelande** om du vill infoga ett nytt steg i flödet och välj sedan **Anropa en åtgärd**.

1. I den nya åtgärden väljer du indatavärdet **för UnrecognizedTriggerPhrase**. Detta skickar texten från agenten till flödet.

    > [!div class="mx-imgBorder"]
    > ![I den nya åtgärden väljer du indatavärdet för **UnrecognizedTriggerPhrase**.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Markera **+** kopplingen under rutan **Åtgärd** om du vill infoga ett nytt steg i flödet och välj sedan **Visa ett meddelande**.

1. Ange meddelandetexten `Your answer is:`och `FinalAnswer` välj som en kontextvariabel med hjälp av funktionen i verktygsfältet på plats.

    > [!div class="mx-imgBorder"]
    > ![Skriv in meddelandetexten och "FinalAnswer" från Power Automate Flow.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. Välj **Spara** i kontextverktygsfältet om du vill spara information om redigeringsarbetsytan för ämnet.

Den sista duken visas nedan.

> [!div class="mx-imgBorder"]
> ![Arbetsyta med slutagent](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-power-virtual-agent"></a>Virtuell testkraftagent

1. Växla **spår mellan ämnen**i testfönstret . Detta gör att du kan titta på utvecklingen mellan ämnen samt i ett enda ämne.

1. Testa agenten genom att ange användartexten i den ordning som anges nedan. Redigeringsteplatsen rapporterar de lyckade stegen med en grön bock.

|Fråga ordning|Testa frågor|Syfte|
|--|--|--|
|1|Hello|Starta konversation|
|2|Lagra timmar|Exempel ämne - konfigurerad för dig utan ytterligare arbete från din sida.|
|3|Ja|Som svar på`Did that answer your question?`|
|4|Utmärkt|Som svar på`Please rate your experience.`|
|5|Ja|Som svar på`Can I help with anything else?`|
|6|Vad är en kunskapsbas?|Den här frågan utlöser reservåtgärden, som skickar texten till din kunskapsbas för att svara, då visas svaret. |

> [!div class="mx-imgBorder"]
> ![Arbetsyta med slutagent](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>Publicera din bot

För att göra agenten tillgänglig för alla medlemmar i din skola eller organisation måste du publicera den.

1. Välj **Publicera** från vänsternavigeringen och välj sedan **Publicera** på sidan.

1. Prova din bot på demowebbplatsen, som tillhandahålls som en länk under **knappen Publicera** .

    En ny webbsida öppnas med din bot. Fråga bot samma testfråga:`What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Arbetsyta med slutagent](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Dela din bot

Konfigurera den som en kanal för att dela demowebbplatsen.

1. Välj **Hantera** sedan **Kanaler** från vänsternavigeringen.

1. Välj **Demo webbplats** från kanallistan.

1. Kopiera länken och välj **Spara**. Klistra in länken på din demowebbplats i ett e-postmeddelande till dina skol- eller organisationsmedlemmar.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med kunskapsbasen tar du bort QnA Maker-resurserna i Azure-portalen.

## <a name="next-step"></a>Nästa steg

[Få analyser om din kunskapsbas](../How-To/get-analytics-knowledge-base.md)

Läs mer om:
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [QnA Maker-kontakten](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) och [inställningarna för kontakten](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)