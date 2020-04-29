---
title: 'Självstudie: integrera med virtuella energi agenter – QnA Maker'
titleSuffix: Azure Cognitive Services
description: I den här självstudien får du bättre kvalitet på din kunskaps bas med aktiv inlärning. Granska, acceptera eller avvisa eller Lägg till utan att ta bort eller ändra befintliga frågor.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 4557dee995c8a01067f7e6ad0e79bb7115b6ecdb
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81402815"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Självstudie: Lägg till din kunskaps bas för virtuella energi agenter
Skapa och utöka en [Power Virtual agents](https://powervirtualagents.microsoft.com/) -robot för att ge svar från din kunskaps bas.

I den här guiden får du lära dig att:

<!-- green checkmark -->
> [!div class="checklist"]
> * Skapa en robot för virtuella energi agenter
> * Skapa ett ämne för system återställning
> * Lägg till QnA Maker som en åtgärd i ett ämne som ett energi automatiserat flöde
> * Skapa en energi automatiserad lösning
> * Lägg till ett energi automatiserat flöde till din lösning
> * Publicera virtuella energi agenter
> * Testa kraftfulla virtuella agenter och få ett svar från din QnA Maker kunskaps bas

## <a name="integrate-an-agent-with-a-knowledge-base"></a>Integrera en agent med en kunskaps bas

Med [virtuella energi agenter](https://powervirtualagents.microsoft.com/) kan team skapa kraftfulla robotar med hjälp av ett guidat grafiskt gränssnitt utan kod. Du behöver inte data experter eller utvecklare.

I virtuella energi agenter skapar du en agent med en serie ämnen (ämnes områden), för att besvara användar frågor genom att utföra åtgärder. Om det inte går att hitta ett svar kan system reserven returnera ett svar.

Konfigurera agenten så att den skickar frågan till din kunskaps bas som en del av ämnets åtgärd eller som en del av *system reservens* ämnes Sök väg. Båda använder en åtgärd för att ansluta till din kunskaps bas och returnera ett svar.

## <a name="power-automate-connects-to-generateanswer-action"></a>Automatisk anslutning ansluter till `GenerateAnswer` åtgärd

Om du vill ansluta agenten till din kunskaps bas använder du automatisk start för att skapa åtgärden. Power automatisering tillhandahåller ett process flöde, som ansluter till QnA Makers `GenerateAnswer` API.

När du har konstruerat och sparat flödet är det tillgängligt från en automatiserad lösning. Använd lösningen som en åtgärd i agenten.

## <a name="connect-an-agent-to-your-knowledge-base"></a>Anslut en agent till din kunskaps bas

Här är en översikt över stegen för att ansluta en agent i virtuella energi agenter till en kunskaps bas i QnA Maker.

* I [QNA Maker](https://www.qnamaker.ai/) -portalen:
    * Bygg och publicera din kunskaps bas.
    * Kopiera din kunskaps bas information, inklusive ID, runtime-slutpunktens slut punkts nyckel och värd för körnings slut punkten.
* I portalen för [virtuella energi agenter](https://powerva.microsoft.com/) :
    * Bygg ett agent-ämne.
    * Anropa en åtgärd (för att starta automatiskt flöde).
* I [Power automatiserad](https://us.flow.microsoft.com/) Portal:
    * Bygg ett flöde med en koppling till [QNA Maker GenerateAnswer](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/).
        * QnA Maker publicerad information om kunskaps bas:
            * Kunskaps bas-ID
            * QnA Maker resurs slut punkts värd
            * Resurs slut punkts nyckel för QnA Maker
        * Fråga om indata-användare
        * Utdata – kunskaps bas svar
    * Skapa en lösning och Lägg till flödet.
* Återgå till virtuella energi agenter:
    * Välj lösningens utdata som ett meddelande för ett ämne.

## <a name="create-and-publish-a-knowledge-base"></a>Skapa och publicera en kunskapsbas

1. Följ [snabb](../Quickstarts/create-publish-knowledge-base.md) starten för att skapa en kunskaps bas. Slutför inte det sista avsnittet om hur du skapar en bot. Använd i stället den här självstudien för att skapa en robot med virtuella energi agenter.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av publicerade kunskaps bas inställningar](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Ange dina publicerade kunskaps bas inställningar på sidan **Inställningar** i [QNA Maker](https://www.qnamaker.ai/) portalen. Du behöver den här informationen för att [Automatisera energi steget](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) för att konfigurera din `GenerateAnswer` QNA Maker-anslutning.

1. På sidan **Inställningar** i QNA Maker Portal söker du efter slut punkts nyckel, slut punkts värd och kunskaps bas-ID.

## <a name="create-an-agent-in-power-virtual-agents"></a>Skapa en agent i virtuella energi agenter

1. [Logga in på virtuella energi agenter](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409). Använd ditt e-postkonto för skolan eller arbetet.
1. Om det här är din första robot kommer du att finnas på agentens **Start** sida. Om det inte är din första bot väljer du roboten från det övre högra hörnet på sidan och väljer **+ ny robot**.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av start sidan för Power Virtual agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

1. Ange dina publicerade kunskaps bas inställningar, som finns på sidan **Inställningar** i [QNA Maker](https://www.qnamaker.ai/) portalen.

## <a name="topics-provided-in-the-bot"></a>Ämnen som finns i bot

Agenten använder ämnes samlingen för att besvara frågor i ditt ämnes område. I den här självstudien har agenten många ämnen som tillhandahålls, delas in i användar ämnen och system ämnen.

> [!div class="mx-imgBorder"]
> ![Skärm bild av ämnen som finns i agenten](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>Skapa avsnittet system reserv

Även om agenten kan ansluta till din kunskaps bas från ett ämne, använder den här självstudien *system reserv* avsnittet. Återställnings avsnittet används när agenten inte kan hitta något svar. Agenten skickar användarens text till QnA Maker s `GenerateAnswer` API, tar emot svaret från din kunskaps bas och visar den för användaren som ett meddelande.

1. Välj **Inställningar** (kugg hjuls ikonen) i det övre högra hörnet i portalen för [virtuella energi agenter](https://powerva.microsoft.com/#/) . Välj sedan **system återställning**.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av meny alternativet Power Virtual agents för system reserven](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Välj **+ Lägg** till för att lägga till ett avsnitt om system återställning.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar hur du lägger till ett reserv ämne.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. När du har lagt till avsnittet väljer du **gå till återställnings avsnittet** för att redigera återställnings avsnittet på redigerings arbets ytan.

    > [!TIP]
    > Om du behöver gå tillbaka till återställnings avsnittet är det tillgängligt i avsnittet **ämnen** som en del av **system** ämnena.

## <a name="use-the-authoring-canvas-to-add-an-action"></a>Använd arbets ytan redigering för att lägga till en åtgärd

Använd arbets ytan redigerare virtuella agenter för att ansluta återställnings avsnittet till din kunskaps bas. Avsnittet börjar med den okända användar texten. Lägg till en åtgärd som skickar texten till QnA Maker och visar sedan svaret som ett meddelande. Det sista steget i att visa ett svar hanteras som ett [separat steg](#add-your-solutions-flow-to-power-virtual-agents)senare i den här självstudien.

I det här avsnittet skapas ett konversations flöde för återställnings ämnet.

1. Den nya återställnings åtgärden kanske redan har konversations flödes element. Ta bort objektet **eskalera** genom att välja menyn **alternativ** .

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av återställnings ämne för Power Virtual agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Välj den **+** koppling som flödar från **meddelande** rutan och välj sedan **anropa en åtgärd**.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av anropet till en åtgärd](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Välj **skapa ett flöde**. Processen tar dig till Energis par portalen.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av skapa ett flöde](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Skapa ett energi automatiserat flöde för att ansluta till din kunskaps bas

Följande procedur skapar ett energi spar flöde som:
* Tar den inkommande användar texten och skickar den till QnA Maker.
* Tilldelar QnA Maker Top svar till en variabel och skickar variabeln (Top Response) som svar till din agent.

1. I **energi spar läge**startas **flödes mal len** automatiskt. På objektet **Power Virtual agents** Flow väljer du **Redigera** för att konfigurera den angivna variabeln som kommer från agenten till kunskaps basen. Den textbaserade indata-variabeln är den text fråga som användaren skickat från agenten.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av alternativet automatiserat energi alternativ för att konfigurera din Invariabel som en text sträng](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Lägg till ett text flöde och namnge variabeln `InputText`med en beskrivning av `IncomingUserQuestion`. Det här namnet hjälper till att skilja inmatnings texten från den utgående text som du skapar senare.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av alternativet automatiserat energi alternativ för att konfigurera variabelns namn och beskrivning](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Välj den **+** koppling som flödar från rutan **virtuella energi agenter** för att infoga ett nytt steg i flödet (innan **returvärdet till den virtuella Power agenten**). Välj sedan **Lägg till en åtgärd**.

1. Sök efter `Qna` **QNA Maker** åtgärder och välj sedan **skapa svar**.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av skapa svar](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    De nödvändiga anslutnings inställningarna för QnA Maker visas i åtgärden och frågeinställningar från agenten.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av de anslutnings inställningar som krävs](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Konfigurera åtgärden med ditt kunskaps bas-ID, slut punkts värd och slut punkts nyckel. De finns på sidan **Inställningar** i din kunskaps bas i QNA Maker portalen.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av publicerade kunskaps bas inställningar](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. Om du vill konfigurera **frågan**markerar du text rutan och väljer sedan `InputText` från listan.

1. Om du vill infoga ett nytt steg i flödet väljer du **+** den koppling som flödar från rutan **skapa svars** åtgärd. Välj sedan **Lägg till en åtgärd**.

1. Om du vill lägga till en variabel för att avbilda svars texten som returneras från `GenerateAnswer`söker `Initialize variable` du efter och väljer åtgärden.

    Ange namnet på variabeln till `OutgoingQnAAnswer`och Välj typen som **sträng**. Ange inte **värdet**.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av att initiera utdata-variabeln](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Om du vill infoga ett nytt steg i flödet väljer du **+** den koppling som flödar från åtgärds rutan **initiera variabel** . Välj sedan **Lägg till en åtgärd**.

1. Om du vill ange hela JSON-svaret för kunskaps basen i variabeln söker du`Apply to each` efter och väljer åtgärden. `GenerateAnswer` `answers`Välj.

1. Om du bara vill returnera det översta svaret väljer du **Lägg till en åtgärd**i rutan samma **gäller för varje** . Sök efter och välj **ange variabel**.

    I rutan **ange variabel** väljer du text rutan för **namn**och väljer sedan **OutgoingQnAAnswer** i listan.

    Välj text rutan för **värde**och välj **svars svar** från listan.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av inställning av namn och värde för variabeln](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. Om du vill returnera variabeln (och dess värde), väljer du **RETUR värde (n) till flödet objekt för virtuell agent för PowerPivot** . Välj sedan **Redigera** > **Lägg till utdata**. Välj en **text** Utdatatyp och ange **rubriken** för `FinalAnswer`. Välj text rutan för **värdet**och välj sedan `OutgoingQnAAnswer` variabeln.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av inställning av retur värde](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Spara flödet genom att klicka på **Spara**.

## <a name="create-a-solution-and-add-the-flow"></a>Skapa en lösning och Lägg till flödet

För att agenten ska kunna hitta och ansluta till flödet måste flödet inkluderas i en automatiserad energi lösning.

1. Medan du fortfarande finns i den automatiserade portalen väljer du **lösningar** från den vänstra navigeringen.

1. Välj **+ Ny lösning**.

1. Ange ett visningsnamn. Listan med lösningar innehåller alla lösningar i din organisation eller skola. Välj en namngivnings konvention som hjälper dig att filtrera efter enbart dina lösningar. Du kan till exempel använda ett prefix för ditt e-postmeddelande till `jondoe-power-virtual-agent-qnamaker-fallback`lösningens namn:.

1. Välj utgivare i listan med alternativ.

1. Acceptera standardvärdena för namn och version.

1. Slutför processen genom att välja **skapa** .

## <a name="add-your-flow-to-the-solution"></a>Lägg till ditt flöde i lösningen

1. I listan med lösningar väljer du den lösning som du nyss skapade. Den ska vara överst i listan. Om den inte är det kan du söka efter ditt e-postnamn, som är en del av lösnings namnet.

1. I lösningen väljer du **+ Lägg till befintlig**och väljer sedan **flöde** i listan.

1. Hitta ditt flöde och välj sedan **Lägg till** för att slutföra processen. Om det finns många flöden tittar du på kolumnen **ändrad** för att hitta det senaste flödet.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>Lägg till din lösnings flöde till virtuella energi agenter

1. Gå tillbaka till fliken webbläsare med agenten i virtuella energi agenter. Redigerings arbets ytan bör fortfarande vara öppen.

1. Om du vill infoga ett nytt steg i flödet går du till rutan **meddelande** åtgärd och väljer **+** kopplingen. Välj sedan **anropa en åtgärd**.

1. I den nya åtgärden väljer du indatavärdet för **UnrecognizedTriggerPhrase**. Detta skickar texten från agenten till flödet.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av alternativet för virtuella energi agenter för att välja okänd utlösnings fras](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Om du vill infoga ett nytt steg i flödet går du till rutan **åtgärd** och väljer **+** kopplingen. Välj sedan **Visa ett meddelande**.

1. Ange meddelande texten, `Your answer is:`. Välj `FinalAnswer` som en Sammanhangs variabel genom att använda funktionen i verktygsfältet på plats.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av alternativet för virtuella energi agenter för att ange meddelande texten](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. I verktygsfältet kontext väljer du **Spara**för att spara arbets ytans redigerings information för ämnet.

Så här ser den sista agent arbets ytan ut.

> [!div class="mx-imgBorder"]
> ![Skärm bild av den slutliga agent arbets ytan](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Testa agenten

1. Växla **spår mellan ämnen**i rutan test. På så sätt kan du se förloppet mellan ämnena och i ett enda ämne.

1. Testa agenten genom att ange användar texten i följande ordning. Redigerings arbets ytan rapporterar de slutförda stegen med en grön bock markering.

    |Fråge ordning|Test frågor|Syfte|
    |--|--|--|
    |1|Hello|Starta konversation|
    |2|Store-timmar|Exempel ämne. Detta är konfigurerat för dig utan ytterligare arbete på din sida.|
    |3|Ja|Som svar på`Did that answer your question?`|
    |4|Utmärkt|Som svar på`Please rate your experience.`|
    |5|Ja|Som svar på`Can I help with anything else?`|
    |6|Vad är en kunskaps bas?|Den här frågan utlöser återställnings åtgärden, som skickar texten till din kunskaps bas för att svara. Sedan visas svaret. |

> [!div class="mx-imgBorder"]
> ![Skärm bild av den slutliga agent arbets ytan](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>Publicera din robot

Om du vill att agenten ska vara tillgänglig för alla medlemmar i din skola eller organisation måste du publicera den.

1. Välj **publicera**i navigerings fönstret till vänster. Välj sedan **publicera** på sidan.

1. Prova din robot på demo webbplatsen (leta efter länken under **publicera**).

    En ny webb sida öppnas med din robot. Fråga roboten samma test fråga:`What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av den slutliga agent arbets ytan](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Dela din robot

Om du vill dela demo webbplatsen konfigurerar du den som en kanal.

1. Välj **Hantera** > **kanaler**i navigeringen till vänster.

1. Välj **Demo webbplats** från listan kanaler.

1. Kopiera länken och välj **Spara**. Klistra in länken på din Demo webbplats i ett e-postmeddelande till dina skol-eller organisations medlemmar.

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med kunskaps basen tar du bort QnA Maker-resurserna i Azure Portal.

## <a name="next-step"></a>Nästa steg

[Få analyser om din kunskapsbas](../How-To/get-analytics-knowledge-base.md)

Läs mer om:
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [QNA Maker koppling](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) och [inställningarna för anslutningen](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)