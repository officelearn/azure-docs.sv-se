---
title: Skapa ett automatiserat integrerings arbets flöde
description: Snabb start – Bygg ditt första automatiserade arbets flöde med hjälp av Azure Logic Apps för system integrering och EAI-lösningar (Enterprise Application Integration)
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/23/2020
ms.openlocfilehash: c40bec80d9f61cf46221cbfe7dde80f3a7b46f6f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "89658310"
---
# <a name="quickstart-create-your-first-automated-integration-workflow-by-using-azure-logic-apps---azure-portal"></a>Snabb start: skapa ditt första automatiserade integrerings arbets flöde med hjälp av Azure Logic Apps-Azure Portal

I den här snabb starten beskrivs de grundläggande allmänna koncepten bakom hur du skapar ditt första arbets flöde med hjälp av [Azure Logic Apps](logic-apps-overview.md), till exempel skapa en tom Logic-app, lägga till en utlösare och en åtgärd och sedan testa din Logic app. I den här snabb starten skapar du en Logic-app som regelbundet kontrollerar en webbplats RSS-flöde för nya objekt. Om det finns ett nytt objekt skickar logikappen ett e-postmeddelande för vart och ett. När du är klar ser logikappen ut som det här arbetsflödet på en hög nivå:

![Konceptuell bild som visar ett exempel på ett Logic app-arbetsflöde med hög nivå.](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

I det här scenariot behöver du en Azure-prenumeration eller [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ett e-postkonto från en tjänst som stöds av Azure Logic Apps, till exempel Office 365 Outlook, Outlook.com eller Gmail. För andra e-posttjänster som stöds [granskar du kopplings listan här](/connectors/). I det här exemplet använder Logic-appen ett arbets-eller skol konto. Om du använder en annan e-posttjänst är de övergripande allmänna stegen desamma, men användar gränssnittet kan skilja sig något.

> [!IMPORTANT]
> Om du vill använda Gmail Connector kan endast företags konton i G-Suite använda den här anslutningen utan begränsning i Logic Apps. Om du har ett Gmail-konto kan du använda den här anslutningen med endast vissa Google-godkända tjänster, eller så kan du [skapa en Google-klient som används för autentisering med din Gmail-anslutning](/connectors/gmail/#authentication-and-bring-your-own-application). Mer information finns i [principer för data säkerhet och sekretess för Google Connectors i Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

## <a name="create-your-logic-app"></a>Skapa en logikapp

1. Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto.

1. I rutan Azure Portal Sök anger `logic apps` du och väljer **Logic Apps**.

   ![Skärm bild som visar Azure Portal sökruta med "Logic Apps" som Sök villkor och "Logic Apps" som det valda Sök resultatet.](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. På sidan **Logic Apps** väljer du **Lägg till**.

   ![Skärm bild som visar listan över Logic Apps och markerad knapp, "Lägg till".](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. I fönstret **Logic app** anger du information om din Logi Kap par som visas nedan.

   ![Skärm bild som visar fönstret för att skapa logiska appar med information om nya Logic-appar.](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Namn** | <*Logic – App-Name*> | Din Logic Apps namn, som måste vara unikt i flera regioner och får bara innehålla bokstäver, siffror, bindestreck ( `-` ), under streck ( `_` ), parenteser ( `(` , `)` ) och punkter ( `.` ). I det här exemplet används "min-First-Logic-app". |
   | **Prenumeration** | <*Azure-prenumeration-namn*> | Ditt Azure-prenumerations namn |
   | **Resursgrupp** | <*Azure-resurs-grupp-namn*> | Namnet på Azure- [resurs gruppen](../azure-resource-manager/management/overview.md), som måste vara unik mellan regioner och används för att organisera relaterade resurser. I det här exemplet används "My-First-LA-RG". |
   | **Plats** | <*Azure-region*> | Den region där du vill lagra information om din Logic Apps. I det här exemplet används "västra USA". |
   | **Log Analytics** | Av | Behåll inställningen **Av** för diagnostisk loggning. |
   ||||

1. När du är klar väljer du **Granska + skapa**. Bekräfta informationen som du har angett och välj **skapa**.

1. När Azure har distribuerat din app väljer **du gå till resurs**.

   ![Skärm bild som visar resurs distributions sida och markerad knapp för "gå till resurs".](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Eller så kan du hitta och välja din Logic-app genom att skriva namnet i sökrutan.

   Logic Apps Designer öppnas och det visas en sida med en introduktionsvideo och vanliga utlösare. Under **Mallar** väljer du **Tom logikapp**.

   ![Skärm bild som visar Logic Apps designer Template Gallery och vald mall, "Tom Logic app".](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Lägg därefter till en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som utlöses när ett nytt RSS-flödesobjekt kommer. Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när ett särskilt villkor uppfylls. Varje gång utlösaren utlöses skapar Azure Logic Apps-motorn en Logic App-instans som startar och kör ditt arbets flöde.

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Lägg till RSS-utlösaren

1. I **Logic App Designer**väljer du **alla**under sökrutan.

1. Du hittar RSS-anslutningen genom att ange i rutan Sök `rss` . I listan utlösare väljer du RSS-utlösaren **när ett feed-objekt publiceras**.

   ![Skärm bild som visar Logic Apps designer med "RSS" i sökrutan och den valda utlösaren "när ett feed-objekt publiceras".](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Ange informationen för utlösaren enligt beskrivningen i det här steget:

   ![Skärm bild som visar Logic Apps designer med inställningar för RSS-utlösare, inklusive RSS-URL, frekvens och intervall.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **RSS-feedens webbadress** | <*RSS-feed-URL*> | Länken för det RSS-flöde som du vill övervaka. I det här exemplet används RSS-flödet för wallets gata i `https://feeds.a.dj.com/rss/RSSMarketsMain.xml` , men om du vill kan du använda en egen URL för RSS-feed. |
   | **Intervall** | 1 | Antalet intervaller som ska förflyta mellan kontrollerna |
   | **Frekvens** | Minut | Tidsenhet för varje intervall mellan kontroller |
   ||||

   Tillsammans definierar intervall och frekvens schemat för logikappens utlösare. Logikappen kontrollerar feeden varje minut.

1. Om du vill komprimera utlösnings informationen för tillfället klickar du i Utlösarens namn List.

   ![Skärm bild som visar Logic Apps designer med en komprimerad Logic app-form.](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

Logikappen har nu lanserats men gör inget annat än att kontrollera RSS-flödet. Därför ska vi lägga till en åtgärd som svarar när utlösaren utlöses.

## <a name="add-the-send-email-action"></a>Lägg till åtgärden "skicka e-post"

Lägg nu till en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) som skickar ett e-postmeddelande när ett nytt objekt visas i RSS-flödet.

1. Under **när ett flödes objekt publiceras** utlösare väljer du **nytt steg**.

   ![Skärm bild som visar Logic Apps designer med "nytt steg".](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. Under **Välj en åtgärd** och sökrutan väljer du **alla**.

1. I sökrutan anger `send an email` du så att du kan hitta anslutningar som erbjuder den här åtgärden. Om du vill filtrera åtgärds listan till en speciell app eller tjänst kan du välja att först använda appen eller tjänsten.

   Om du till exempel använder ett arbets-eller skol konto från Microsoft och vill använda Office 365 Outlook, väljer du **office 365 Outlook**. Eller, om du använder en personlig Microsoft-konto, kan du välja Outlook.com. Det här exemplet fortsätter med Office 365 Outlook:

   ![Skärm bild som visar Logic Apps designer och valt Office 365 Outlook Connector.](./media/quickstart-create-first-logic-app-workflow/select-connector.png)

   Nu kan du enkelt hitta och välja den åtgärd som du vill använda, till exempel `send an email` :

   ![Skärm bild som visar Logic Apps designer och lista med filtrerade åtgärder.](./media/quickstart-create-first-logic-app-workflow/filtered-actions-list.png)

1. Om din valda e-postanslutning kräver att du autentiserar din identitet, slutför du det steget nu för att skapa en anslutning mellan din Logic app och e-posttjänsten.

   > [!NOTE]
   > I det här exemplet kan du autentisera din identitet manuellt. Men kopplingar som kräver autentisering skiljer sig åt i de autentiseringstyper som de stöder. Du har också alternativ för att ställa in hur du vill hantera autentisering. När du till exempel använder Azure Resource Manager mallar för distribution kan du Parameterisera och förbättra säkerheten för indata som du vill ändra ofta eller enkelt, till exempel anslutnings information. Mer information finns i de här ämnena:
   >
   > * [Mallparametrar för distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [Auktorisera OAuth-anslutningar](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Autentisera åtkomst med hanterade identiteter](../logic-apps/create-managed-service-identity.md)
   > * [Autentisera anslutningar för Logic app-distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. I åtgärden **Skicka ett e-postmeddelande** anger du den information som ska tas med i e-postmeddelandet.

   1. Ange mottagarens e-postadress i fältet **Till**. I test syfte kan du använda din e-postadress.

      För närvarande kan du listan **Lägg till dynamiskt innehåll** som visas. När du klickar i vissa redigerings rutor visas den här listan och visar tillgängliga utdata från föregående steg som du kan använda som indata för den aktuella åtgärden.

   1. I rutan **Ämne** anger du den här texten med ett avslutande blanksteg: `New RSS item: `

      ![Skärm bild som visar Logic Apps designer med åtgärden "skicka e-post" och markören i egenskaps rutan "ämne".](./media/quickstart-create-first-logic-app-workflow/send-email-subject.png)

   1. I listan **Lägg till dynamiskt innehåll** väljer du **feed-rubrik**, som är utdata från utlösaren, "när ett feed-objekt publiceras", vilket gör att RSS-objektets rubrik är tillgänglig för användning.

      ![Skärm bild som visar Logic Apps designer med åtgärden "skicka e-post" och markören i egenskaps rutan "ämne" med en öppen dynamisk innehålls lista och valda utdata, "feed-rubrik".](./media/quickstart-create-first-logic-app-workflow/send-email-subject-dynamic-content.png)

      > [!TIP]
      > I listan med dynamiskt innehåll, om inga utdata visas från utlösaren "när ett feed-objekt publiceras", bredvid åtgärdens rubrik, väljer du **Läs mer**.
      > 
      > ![Skärm bild som visar Logic Apps designer med öppnat dynamisk innehålls lista och "se mer" valt för utlösaren.](./media/quickstart-create-first-logic-app-workflow/dynamic-content-list-see-more-actions.png)

      När du är klar ser ämnet ut som i det här exemplet:

      ![Skärm bild som visar Logic Apps designer med åtgärden "Skicka ett e-postmeddelande" och ett exempel på e-post som omfattas av egenskapen "feed title".](./media/quickstart-create-first-logic-app-workflow/send-email-feed-title.png)

      Om en For each-loop (för varje) visas i designern har du valt en token för en matris, till exempel token **categories-item**. För sådana typer av token lägger designern automatiskt till den här loopen omkring åtgärden som refererar till token. På så sätt kan din logikappsåtgärd utföra samma åtgärd på varje element i matrisen. Om du vill ta bort loopen väljer du **ellipserna** (**...**) på loopens namn list och väljer sedan **ta bort**.

   1. I **brödtextrutan** anger du texten och väljer dessa token för e-postmeddelandets brödtext. Tryck på Skift + Enter för att lägga till tomma rader i en redigeringsruta.

      ![Skärm bild som visar Logic Apps designer med åtgärden "Skicka ett e-postmeddelande" och valda egenskaper i rutan "brödtext".](./media/quickstart-create-first-logic-app-workflow/send-email-body.png)

      | Egenskap | Beskrivning |
      |----------|-------------|
      | **Flödesrubrik** | Objektets rubrik |
      | **Flödet publicerat den** | Objektets publiceringsdatum och -tid |
      | **Flödets primära länk** | Objektets webbadress |
      |||

1. Spara logikappen.

Testa därefter logikappen.

## <a name="run-your-logic-app"></a>Kör logikappen

Om du vill starta din Logic app manuellt går du till verktygsfältet i designern och väljer **Kör**. Eller vänta tills logikappen har kontrollerat RSS-flödet baserat på ditt angivna schema (varje minut).

![Skärm bild som visar Logic Apps designer med knappen Kör som är markerad i Design verktygsfältet.](./media/quickstart-create-first-logic-app-workflow/run-logic-app-test.png)

Om RSS-flödet innehåller nya objekt skickar din logikapp ett e-postmeddelande för varje nytt objekt. Annars väntar logikappen tills nästa intervall innan kontrollen utförs på nytt. Om du inte får e-post kan du titta i mappen Skräppost.

Här är ett exempel på ett e-postmeddelande som den här logikappen skickar.

![Skärm bild som visar exempel på e-post som visas när ett nytt RSS-flöde visas.](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Tekniskt, när utlösaren kontrollerar RSS-flödet och hittar nya objekt, utlöses utlösaren och Azure Logic Apps motor skapar en instans av ditt Logic app-arbetsflöde som kör åtgärderna i arbets flödet. Om utlösaren inte hittar nya objekt utlöses den inte, och den ”hoppar över” att instansiera arbetsflödet.

Grattis, nu har du skapat och kört din första Logic-app med Azure Portal.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen som innehåller logikappen och alla relaterade resurser när du inte längre behöver dem.

1. I rutan Azure Search anger du `resource groups` och väljer sedan **resurs grupper**.

   ![Skärm bild som visar Azure Portal sökruta med Sök termen, "resurs grupper".](./media/quickstart-create-first-logic-app-workflow/find-resource-groups.png)

1. Hitta och välj din Logic Apps resurs grupp. I **översikts** fönstret väljer du **ta bort resurs grupp**.

   ![Skärm bild som visar Azure Portal med vald resurs grupp och knapp för "ta bort resurs grupp".](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. När fönstret bekräftelse visas anger du namnet på resurs gruppen och väljer **ta bort**.

   ![Skärm bild som visar Azure Portal med bekräftelse fönstret och det angivna resurs grupps namnet som ska tas bort.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> När du tar bort en logikapp instantieras inga nya körningar. Alla pågående och väntande körningar avbryts. Om du har flera tusen körningar kan det ta relativt lång tid att avbryta dem.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du först en logikapp som söker efter RSS-uppdateringar baserat på ditt angivna schema (varje minut) och vidtar åtgärder (skickar e-post) när det finns uppdateringar. Om du vill lära dig mer fortsätter du med den här självstudien där du får skapa mer avancerade schemabaserade arbetsflöden:

> [!div class="nextstepaction"]
> [Kontrollera trafik med en schemabaserad logikapp](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
