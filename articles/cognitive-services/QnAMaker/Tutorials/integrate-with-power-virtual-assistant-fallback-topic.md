---
title: 'Självstudie: integrera med virtuella energi agenter – QnA Maker'
description: I den här självstudien får du bättre kvalitet på din kunskaps bas med aktiv inlärning. Granska, acceptera eller avvisa eller Lägg till utan att ta bort eller ändra befintliga frågor.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 11/09/2020
ms.openlocfilehash: 322db4e1535e763f4c3e7c87afaa370471ba0b66
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376306"
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
    * Sök efter _skapa svar med QNA Maker_ -mall
    * Använd mallen för att konfigurera flödet så att det använder [QNA Maker GenerateAnswer](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/).
        * QnA Maker publicerad information om kunskaps bas:
            * Kunskaps bas-ID
            * QnA Maker resurs slut punkts värd
            * Resurs slut punkts nyckel för QnA Maker
        * Fråga om indata-användare
        * Utdata – kunskaps bas svar
    * Skapa en lösning och Lägg till flödet eller Lägg till flödet i en befintlig lösning.
* Återgå till virtuella energi agenter:
    * Välj lösningens utdata som ett meddelande för ett ämne.

## <a name="create-and-publish-a-knowledge-base"></a>Skapa och publicera en kunskapsbas

1. Följ [snabb](../Quickstarts/create-publish-knowledge-base.md) starten för att skapa en kunskaps bas. Slutför inte det sista avsnittet om hur du skapar en bot. Använd i stället den här självstudien för att skapa en robot med virtuella energi agenter.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av publicerade kunskaps bas inställningar](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Ange dina publicerade kunskaps bas inställningar på sidan **Inställningar** i [QNA Maker](https://www.qnamaker.ai/) portalen. Du behöver den här informationen för att [Automatisera energi steget](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) för att konfigurera din QNA Maker- `GenerateAnswer` anslutning.

1. På sidan **Inställningar** i QNA Maker Portal söker du efter slut punkts nyckel, slut punkts värd och kunskaps bas-ID.

## <a name="create-an-agent-in-power-virtual-agents"></a>Skapa en agent i virtuella energi agenter

1. [Logga in på virtuella energi agenter](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409). Använd ditt e-postkonto för skolan eller arbetet.

1. Om det här är din första robot kommer du att finnas på agentens **Start** sida. Om det inte är din första bot väljer du roboten från det övre högra hörnet på sidan och väljer **+ ny robot**.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av start sidan för Power Virtual agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="topics-provided-in-the-bot"></a>Ämnen som finns i bot

Agenten använder ämnes samlingen för att besvara frågor i ditt ämnes område. I den här självstudien har agenten många ämnen som tillhandahålls, delas in i användar ämnen och system ämnen.

Välj **ämnen** i det vänstra navigerings fältet för att se de avsnitt som du hittar i bot.

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

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/delete-escalate-action-using-option-menu.png" alt-text="En del skärm bild av konversations flödet med alternativet ta bort markerat.":::

1. Välj den **+** koppling som flödar till **meddelande** rutan och välj sedan **anropa en åtgärd**.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png" alt-text="Delvis skärm bild av anrop till en åtgärd.":::

1. Välj **skapa ett flöde**. Processen tar dig till Energis par portalen.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av skapa ett flöde](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)


    Automatisk start öppnas i en ny mall. Du kommer inte att använda den här nya mallen.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-flow-initial-template.png" alt-text="En skärm bild av en skärm som automatiseras med nya flödes mal len.":::

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Skapa ett energi automatiserat flöde för att ansluta till din kunskaps bas

> [!NOTE]
> För närvarande stöder inte Power automate-mallen QnA Maker Managed (Preview) slut punkter. Om du vill lägga till en QnA Maker hanterad (för hands version) kunskaps bas för att automatisera det här steget, och lägga till slut punkterna manuellt. 

Följande procedur skapar ett energi spar flöde som:
* Tar den inkommande användar texten och skickar den till QnA Maker.
* Returnerar det översta svaret tillbaka till agenten.

1. I **Automatisera energi spar läge** väljer du **mallar** i det vänstra navigerings fältet. Om du tillfrågas om du vill lämna webb läsar sidan accepterar du lämna.

1. På sidan mallar söker du efter mallen **skapa svar med QNA Maker** och väljer sedan mallen. Den här mallen innehåller alla steg som krävs för att anropa QnA Maker med dina kunskaps bas inställningar och returnera det främsta svaret.

1. På den nya skärmen för QnA Maker flödet väljer du **Fortsätt**.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-qna-flow-template-continue.png" alt-text="En del skärm bild av QnA Maker Template-flöde med knappen Fortsätt markerad.":::

1. Välj rutan skapa **svars** åtgärd och fyll i QNA Maker inställningar från ett tidigare avsnitt med rubriken [skapa och publicera en kunskaps bas](#create-and-publish-a-knowledge-base). Din **tjänst värd** i följande avbildning refererar till din kunskaps bas värds **värd** och har formatet `https://YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker` .


    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fill-in-generate-answer-settings.png" alt-text="En del skärm bild av QnA Maker Template-flöde med generera svar (förhands granskning) markerat.":::

1. Spara flödet genom att klicka på **Spara**.

## <a name="create-a-solution-and-add-the-flow"></a>Skapa en lösning och Lägg till flödet

För att agenten ska kunna hitta och ansluta till flödet måste flödet inkluderas i en automatiserad energi lösning.

1. Medan du fortfarande finns i den automatiserade portalen väljer du **lösningar** från den vänstra navigeringen.

1. Välj **+ Ny lösning**.

1. Ange ett visningsnamn. Listan med lösningar innehåller alla lösningar i din organisation eller skola. Välj en namngivnings konvention som hjälper dig att filtrera efter enbart dina lösningar. Du kan till exempel använda ett prefix för ditt e-postmeddelande till lösningens namn: `jondoe-power-virtual-agent-qnamaker-fallback` .

1. Välj utgivare i listan med alternativ.

1. Acceptera standardvärdena för namn och version.

1. Slutför processen genom att välja **skapa** .

## <a name="add-your-flow-to-the-solution"></a>Lägg till ditt flöde i lösningen

1. I listan med lösningar väljer du den lösning som du nyss skapade. Den ska vara överst i listan. Om den inte är det kan du söka efter ditt e-postnamn, som är en del av lösnings namnet.

1. I lösningen väljer du **+ Lägg till befintlig** och väljer sedan **flöde** i listan.

1. Hitta ditt flöde från listan **utanför lösningar** och välj sedan **Lägg till** för att slutföra processen. Om det finns många flöden tittar du på kolumnen **ändrad** för att hitta det senaste flödet.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>Lägg till din lösnings flöde till virtuella energi agenter

1. Gå tillbaka till fliken webbläsare med agenten i virtuella energi agenter. Redigerings arbets ytan bör fortfarande vara öppen.

1. Om du vill infoga ett nytt steg i flödet, ovanför rutan **meddelande** åtgärd, väljer du **+** kopplingen. Välj sedan **anropa en åtgärd**.

1. I popup-fönstret **flöde** väljer du det nya flödet med namnet **generera svar med QNA Maker kunskaps bas..**.. Den nya åtgärden visas i flödet.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-flow-after-adding-action.png" alt-text="En del skärm bild av den virtuella PowerPivot-agentens ämne konversations arbets yta efter tillägg av QnA Maker flöde.":::

1. Om du vill ställa in variabeln till den QnA Maker åtgärden korrekt väljer du **Välj en variabel** och sedan **robot. UnrecognizedTriggerPhrase**.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-selection-action-input.png" alt-text="En del skärm bild av den virtuella PowerPivot-agentens ämne konversations arbets yta väljer indatamängd.":::


1. Om du vill ange den utgående variabeln till åtgärden QnA Maker väljer du **UnrecognizedTriggerPhrase** i **meddelande** åtgärden och väljer sedan ikonen för att infoga en variabel, `{x}` och väljer sedan **FinalAnswer**.

1. I verktygsfältet kontext väljer du **Spara** för att spara arbets ytans redigerings information för ämnet.

Så här ser den sista agent arbets ytan ut.

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar den sista agent arbets ytan med utlösare, åtgärd och sedan meddelande avsnitt.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Testa agenten

1. Växla **spår mellan ämnen** i rutan test. På så sätt kan du se förloppet mellan ämnena och i ett enda ämne.

1. Testa agenten genom att ange användar texten i följande ordning. Redigerings arbets ytan rapporterar de slutförda stegen med en grön bock markering.

    |Fråge ordning|Test frågor|Syfte|
    |--|--|--|
    |1|Hello|Starta konversation|
    |2|Store-timmar|Exempel ämne. Detta är konfigurerat för dig utan ytterligare arbete på din sida.|
    |3|Yes|Som svar på `Did that answer your question?`|
    |4|Utmärkt|Som svar på `Please rate your experience.`|
    |5|Yes|Som svar på `Can I help with anything else?`|
    |6|Hur kan jag förbättra data flödes prestandan för fråga förutsägelser?|Den här frågan utlöser återställnings åtgärden, som skickar texten till din kunskaps bas för att svara. Sedan visas svaret. de gröna bock markeringarna för de enskilda åtgärderna indikerar att det är klart för varje åtgärd.|

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png" alt-text="Skärm bild av Chat-roboten med arbets ytan som visar grön bock markering för lyckade åtgärder.":::

## <a name="publish-your-bot"></a>Publicera din robot

Om du vill att agenten ska vara tillgänglig för alla medlemmar i din skola eller organisation måste du publicera den.

1. Välj **publicera** i navigerings fönstret till vänster. Välj sedan **publicera** på sidan.

1. Prova din robot på demo webbplatsen (leta efter länken under **publicera** ).

    En ny webb sida öppnas med din robot. Fråga roboten samma test fråga: `How can I improve the throughput performance for query predictions?`

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av den slutliga agent arbets ytan](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Dela din robot

Om du vill dela demo webbplatsen konfigurerar du den som en kanal.

1. Välj **Hantera** kanaler i navigeringen till vänster  >  **Channels**.

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