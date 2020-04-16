---
title: 'Självstudiekurs: Integrera med Virtuella Power-agenter - QnA Maker'
titleSuffix: Azure Cognitive Services
description: I den här självstudien kan du förbättra kvaliteten på din kunskapsbas med aktivt lärande. Granska, acceptera eller avvisa eller lägg till utan att ta bort eller ändra befintliga frågor.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 4557dee995c8a01067f7e6ad0e79bb7115b6ecdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402815"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Självstudiekurs: Lägg till kunskapsbasen i Power Virtual Agents
Skapa och utöka en [Power Virtual Agents-robot](https://powervirtualagents.microsoft.com/) för att ge svar från din kunskapsbas.

I den här guiden får du lära dig att:

<!-- green checkmark -->
> [!div class="checklist"]
> * Skapa en power virtual agents bot
> * Skapa ett systemåterboksämne
> * Lägga till QnA Maker som en åtgärd i ett ämne som ett Power Automate-flöde
> * Skapa en Power Automate-lösning
> * Lägga till ett Power Automate-flöde i din lösning
> * Virtuella agenter för publicera energi
> * Testa virtuella power-agenter och få svar från din QnA Maker-kunskapsbas

## <a name="integrate-an-agent-with-a-knowledge-base"></a>Integrera en agent med en kunskapsbas

[Power Virtual Agents](https://powervirtualagents.microsoft.com/) gör det möjligt för team att skapa kraftfulla robotar med hjälp av ett guidat grafiskt gränssnitt utan kod. Du behöver inte datavetarna eller utvecklarna.

I Power Virtual Agents skapar du en agent med en rad ämnen (ämnesområden) för att svara på användarfrågor genom att utföra åtgärder. Om det inte går att hitta ett svar kan ett systemåtergång returnera ett svar.

Konfigurera agenten så att frågan skickas till kunskapsbasen som en del av ett ämnes åtgärd eller som en del av ämnessökvägen *för systemfallback.* De använder båda en åtgärd för att ansluta till din kunskapsbas och returnera ett svar.

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate ansluter `GenerateAnswer` till handling

Om du vill ansluta din agent till din kunskapsbas använder du Power Automate för att skapa åtgärden. Power Automate tillhandahåller ett processflöde som ansluter till `GenerateAnswer` QnA Maker's API.

När du har designa och sparat flödet är det tillgängligt från en Power Automate-lösning. Använd den lösningen som en åtgärd i din agent.

## <a name="connect-an-agent-to-your-knowledge-base"></a>Anslut en agent till din kunskapsbas

Här är en översikt över stegen för att ansluta en agent i Power Virtual Agents till en kunskapsbas i QnA Maker.

* I [QnA](https://www.qnamaker.ai/) Maker-portalen:
    * Bygg och publicera din kunskapsbas.
    * Kopiera information om kunskapsbasen, inklusive ID-, slutpunktsnyckeln för körning och slutpunktsvärden för körning.
* I portalen [Power Virtual Agents:](https://powerva.microsoft.com/)
    * Skapa ett agentämne.
    * Anropa en åtgärd (till Power Automate-flödet).
* I [Power Automate-portalen:](https://us.flow.microsoft.com/)
    * Skapa ett flöde med en koppling till [QnA Maker's GenerateAnswer](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/).
        * QnA Maker publicerade information om kunskapsbasen:
            * Id för kunskapsbas
            * Slutpunktsvärd för QnA Maker-resurs
            * Slutpunktsnyckel för QnA Maker-resurs
        * Indata - användarfråga
        * Output - kunskapsbas svar
    * Skapa en lösning och lägg till flödet.
* Återgå till virtuella power-agenter:
    * Välj lösningens utdata som ett meddelande för ett ämne.

## <a name="create-and-publish-a-knowledge-base"></a>Skapa och publicera en kunskapsbas

1. Följ [snabbstarten](../Quickstarts/create-publish-knowledge-base.md) för att skapa en kunskapsbas. Fyll inte i det sista avsnittet, om att skapa en bot. Använd i stället den här självstudien för att skapa en robot med virtuella power-agenter.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av publicerade kunskapsbasinställningar](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Ange de publicerade kunskapsbasinställningarna som finns på sidan **Inställningar** i [QnA Maker-portalen.](https://www.qnamaker.ai/) Du behöver den här informationen för [Power Automate-steget](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) för att konfigurera QnA Maker-anslutningen. `GenerateAnswer`

1. I QnA Maker-portalen hittar du slutpunktsnyckeln, slutpunktsvärden och kunskapsbas-ID på sidan **Inställningar.**

## <a name="create-an-agent-in-power-virtual-agents"></a>Skapa en agent i Power Virtual Agents

1. [Logga in på Power Virtual Agents](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409). Använd ditt e-postkonto för skolan eller arbetet.
1. Om detta är din första bot, kommer du att vara på **hemsidan** för agenten. Om detta inte är din första bot, välj bot från övre högra området på sidan, och välj **+ Ny Bot**.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av startsidan för Virtuella power-agenter](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

1. Ange dina publicerade kunskapsbasinställningar som finns på sidan **Inställningar** i [QnA Maker-portalen.](https://www.qnamaker.ai/)

## <a name="topics-provided-in-the-bot"></a>Ämnen som finns i bot

Agenten använder ämnessamlingen för att svara på frågor i ditt ämnesområde. I den här självstudien har agenten många ämnen som tillhandahålls för dig, uppdelad i användarämnen och systemämnen.

> [!div class="mx-imgBorder"]
> ![Skärmbild av ämnen som tillhandahålls i agenten](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>Skapa ämnet systemåterback

Även om agenten kan ansluta till din kunskapsbas från alla ämnen, använder den här självstudien *systemets reservämne.* Reservämnet används när agenten inte kan hitta ett svar. Agenten skickar användarens text till QnA `GenerateAnswer` Maker's API, får svaret från din kunskapsbas och visar den för användaren som ett meddelande.

1. I portalen [Power Virtual Agents](https://powerva.microsoft.com/#/) väljer du **Inställningar** (kugghjulsikonen) i det övre högra hörnet. Välj sedan **System Fallback**.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av menyalternativet Power Virtual Agents för System Fallback](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Välj **+ Lägg till** om du vill lägga till ett systemåterföringsämne.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av att lägga till ett reservavsnitt.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. När ämnet har lagts till väljer du **Gå till fallback-ämne** för att skapa reservämnet på redigeringsarbetsytan.

    > [!TIP]
    > Om du behöver återgå till reservavsnittet är det tillgängligt i avsnittet **Ämnen,** som en del av **systemavsnitten.**

## <a name="use-the-authoring-canvas-to-add-an-action"></a>Använda redigeringsarbetsytan för att lägga till en åtgärd

Använd den power virtual agents-redigeringsarbetsytan för att ansluta reservämnet till kunskapsbasen. Avsnittet börjar med den okända användartexten. Lägg till en åtgärd som skickar texten till QnA Maker och visar sedan svaret som ett meddelande. Det sista steget för att visa ett svar hanteras som ett [separat steg](#add-your-solutions-flow-to-power-virtual-agents), senare i den här självstudien.

I det här avsnittet skapas konversationsflödet för reservämnet.

1. Den nya reservåtgärden kanske redan har konversationsflödeselement. Ta bort **alternativet Eskalera** genom att välja **Alternativ-menyn.**

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av reservavsnittet power virtual agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Markera **+** kopplingen som flödar i rutan **Meddelande** och välj sedan **Anropa en åtgärd**.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av Ring en åtgärd](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Välj **skapa ett flöde**. Processen tar dig till Power Automate-portalen.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av Skapa ett flöde](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Skapa ett Power Automate-flöde för att ansluta till din kunskapsbas

I följande procedur skapas ett Power Automate-flöde som:
* Tar den inkommande användartexten och skickar den till QnA Maker.
* Tilldelar QnA Maker-toppsvaret till en variabel och skickar variabeln (det översta svaret) som svar tillbaka till din agent.

1. I **Power Automate**startas **flödesmallen** åt dig. På flödesobjektet **Power Virtual Agents** väljer du **Redigera** för att konfigurera indatavariabeln som kommer från agenten till din kunskapsbas. Den textbaserade indatavariabeln är den textfråga som skickas in från din agent.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av alternativet Power Automate för att konfigurera indatavariabeln som en textsträng](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Lägg till en textinmatning `InputText`och ge `IncomingUserQuestion`variabeln ett namn med en beskrivning av . Det här namnet hjälper till att skilja indatatexten från den utdatatext som du skapar senare.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av power automate-alternativet för att konfigurera indatavariabelns namn och beskrivning](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Välj **+** kopplingen som flödar från rutan **Virtuella Power-agenter** om du vill infoga ett nytt steg i flödet (före **returvärdet till Power Virtual Agent**). Välj sedan **Lägg till en åtgärd**.

1. Sök `Qna` efter för att hitta **QnA Maker-åtgärderna** och välj sedan **Generera svar**.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av Generera svar](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    De anslutningsinställningar som krävs för QnA Maker visas i åtgärden och frågeinställningarna från agenten.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av de anslutningsinställningar som krävs](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Konfigurera åtgärden med kunskapsbas-ID, slutpunktsvärd och slutpunktsnyckel. Dessa finns på sidan **Inställningar** i din kunskapsbas i QnA Maker-portalen.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av publicerade kunskapsbasinställningar](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. Om du vill konfigurera **frågan**markerar du `InputText` textrutan och markerar sedan listan i listan.

1. Om du vill infoga ett nytt **+** steg i flödet markerar du kopplingen som flödar från åtgärdsrutan **Generera svar.** Välj sedan **Lägg till en åtgärd**.

1. Om du vill lägga till en `GenerateAnswer`variabel för att `Initialize variable` fånga svarstexten som returneras från söker du efter och väljer åtgärden.

    Ange namnet på variabeln till `OutgoingQnAAnswer`och välj typen som **Sträng**. Ange inte **värdet**.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av initiering av utdatavariabeln](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Om du vill infoga ett nytt **+** steg i flödet markerar du kopplingen som flödar från rutan **Initiera variabel.** Välj sedan **Lägg till en åtgärd**.

1. Om du vill ställa in hela JSON-svaret på`Apply to each` variabeln söker du efter och väljer åtgärden. `GenerateAnswer` `answers`Välj .

1. Om du bara vill returnera det översta svaret väljer du Lägg till **en åtgärd**i samma Använd för **varje** ruta . Sök efter och välj **Ange variabel**.

    Markera textrutan för **Namn**i **variabeln Ange** och välj sedan **UtgåendeQnAAnswer** i listan.

    Markera textrutan för **Värde**och välj sedan **Svar i** listan.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av inställningen av namn och värde för variabeln](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. Om du vill returnera variabeln (och dess värde) väljer du **returvärdet/flödesobjektet för Power Virtual Agent.** Välj sedan **Redigera** > **Lägg till en utdata**. Markera en textutdatatyp och ange `FinalAnswer`sedan **rubrik** för . **Text** Markera textrutan för **värdet**och markera `OutgoingQnAAnswer` sedan variabeln.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av inställningen av returvärdet](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Spara flödet genom att klicka på **Spara**.

## <a name="create-a-solution-and-add-the-flow"></a>Skapa en lösning och lägg till flödet

För att agenten ska kunna hitta och ansluta till flödet måste flödet inkluderas i en Power Automate-lösning.

1. När du fortfarande är kvar i Power Automate-portalen väljer du **Lösningar** från navigering på vänster sida.

1. Välj **+ Ny lösning**.

1. Ange ett visningsnamn. Listan över lösningar innehåller alla lösningar i din organisation eller skola. Välj en namngivningskonvention som hjälper dig att filtrera till just dina lösningar. Du kan till exempel prefix din e-post till din lösning namn: `jondoe-power-virtual-agent-qnamaker-fallback`.

1. Välj utgivare i listan med alternativ.

1. Acceptera standardvärdena för namnet och versionen.

1. Välj **Skapa** för att slutföra processen.

## <a name="add-your-flow-to-the-solution"></a>Lägg till ditt flöde i lösningen

1. Välj den lösning som du just skapade i listan över lösningar. Det bör vara högst upp på listan. Om den inte är det söker du efter ditt e-postnamn, som är en del av lösningsnamnet.

1. Välj **+ Lägg till befintlig**i lösningen och välj sedan **Flöde** i listan.

1. Hitta flödet och välj sedan **Lägg till** för att slutföra processen. Om det finns många flöden tittar du på kolumnen **Ändrad** för att hitta det senaste flödet.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>Lägg till lösningens flöde i Power Virtual Agents

1. Gå tillbaka till webbläsarfliken med din agent i Power Virtual Agents. Författarduken bör fortfarande vara öppen.

1. Om du vill infoga ett nytt steg i flödet **+** markerar du kopplingen under åtgärdsrutan **Meddelande.** Välj sedan **Anropa en åtgärd**.

1. I den nya åtgärden väljer du indatavärdet **för UnrecognizedTriggerPhrase**. Detta skickar texten från agenten till flödet.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av alternativet Power Virtual Agents för att välja okänd utlösarfras](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Om du vill infoga ett nytt steg i **+** flödet markerar du kopplingen under rutan **Åtgärd.** Välj sedan **Visa ett meddelande**.

1. Skriv meddelandetexten `Your answer is:`. Välj `FinalAnswer` som en kontextvariabel med hjälp av funktionen i verktygsfältet på plats.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av alternativet Power Virtual Agents för att ange meddelandetexten](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. I kontextverktygsfältet väljer du **Spara**om du vill spara information om redigeringsarbetsytan för ämnet.

Så här ser den sista agentduken ut.

> [!div class="mx-imgBorder"]
> ![Skärmbild av arbetsytan för slutagent](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Testa agenten

1. Växla **spår mellan ämnen**i testfönstret . Detta gör att du kan titta på utvecklingen mellan ämnen, liksom inom ett enda ämne.

1. Testa agenten genom att ange användartexten i följande ordning. Redigeringsteplatsen rapporterar de lyckade stegen med en grön bock.

    |Fråga ordning|Testa frågor|Syfte|
    |--|--|--|
    |1|Hello|Starta konversation|
    |2|Lagra timmar|Exempel ämne. Detta är konfigurerat för dig utan ytterligare arbete från din sida.|
    |3|Ja|Som svar på`Did that answer your question?`|
    |4|Utmärkt|Som svar på`Please rate your experience.`|
    |5|Ja|Som svar på`Can I help with anything else?`|
    |6|Vad är en kunskapsbas?|Den här frågan utlöser reservåtgärden, som skickar texten till din kunskapsbas för att svara. Då visas svaret. |

> [!div class="mx-imgBorder"]
> ![Skärmbild av arbetsytan för slutagent](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>Publicera din bot

Om du vill göra agenten tillgänglig för alla medlemmar i din skola eller organisation måste du publicera den.

1. Välj **Publicera**i navigeringen till vänster . Välj sedan **Publicera** på sidan.

1. Prova din bot på demowebbplatsen (leta efter länken under **Publicera).**

    En ny webbsida öppnas med din bot. Fråga bot samma testfråga:`What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av arbetsytan för slutagent](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Dela din bot

Om du vill dela demowebbplatsen konfigurerar du den som en kanal.

1. Välj **Hantera** > **kanaler**i navigeringen till vänster .

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