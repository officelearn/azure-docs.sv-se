---
title: RosettaNet-meddelanden för B2B-integrering
description: Exchange RosettaNet-meddelanden i Azure Logic Apps med Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 2cc2ac08b9624c1d1d9bee9ce91a7c91189d7f2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792414"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Exchange RosettaNet-meddelanden för B2B-företagsintegrering i Azure Logic Apps

[RosettaNet](https://resources.gs1us.org) är ett ideellt konsortium som har etablerat standardprocesser för att dela affärsinformation. Dessa standarder används ofta för processer i leveranskedjan och är utbredda inom halvledar-, elektronik- och logistikindustrin. RosettaNet-konsortiet skapar och underhåller PiPs (Partner Interface Processes), som tillhandahåller gemensamma affärsprocessdefinitioner för alla RosettaNet-meddelandeutbyten. RosettaNet bygger på XML och definierar meddelanderiktlinjer, gränssnitt för affärsprocesser och implementeringsramverk för kommunikation mellan företag.

I [Azure Logic Apps](../logic-apps/logic-apps-overview.md)hjälper RosettaNet-anslutningen dig att skapa integrationslösningar som stöder RosettaNet-standarder. Anslutningen är baserad på RosettaNet Implementation Framework (RNIF) version 2.0.01. RNIF är ett ramverk för ett öppet nätverksprogram som gör det möjligt för affärspartners att samarbeta köra Pips från RosettaNet. Det här ramverket definierar meddelandestrukturen, behovet av bekräftelser, MIME-kodning (Multipurpose Internet Mail Extensions) och den digitala signaturen.

I anslutningsappen finns följande:

* Koda eller ta emot RosettaNet-meddelanden.
* Avkoda eller skicka RosettaNet-meddelanden.
* Vänta på svar och generering av meddelande om fel.

För dessa funktioner stöder kopplingen alla PIPs som definieras av RNIF 2.0.01. Kommunikation med partnern kan vara synkron eller asynkron.

## <a name="rosettanet-concepts"></a>RosettaNet koncept

Här är några koncept och termer som är unika för RosettaNet-specifikationen och är viktiga när du bygger RosettaNet-baserade integrationer:

* **Pip**

  RosettaNet-organisationen skapar och underhåller PiPs (Partner Interface Processes), som tillhandahåller gemensamma affärsprocessdefinitioner för alla RosettaNet-meddelandeutbyten. Varje PIP-specifikation innehåller en DTD-fil (Document Type Definition) och ett riktlinjedokument för meddelande. DTD-filen definierar meddelandestrukturen för tjänstinnehåll. Meddelanderiktlinjedokumentet, som är en läsbar HTML-fil som kan läsas av människor, anger begränsningar på elementnivå. Tillsammans ger dessa filer en fullständig definition av affärsprocessen.

   PIPs kategoriseras efter en affärsfunktion på hög nivå, eller kluster, och en underfunktion eller ett segment. Till exempel är "3A4" PIP för inköpsorder, medan "3" är funktionen Orderhantering och "3A" är underfunktionen Offert & ordertransaktion. Mer information finns på [RosettaNet-webbplatsen](https://resources.gs1us.org).

* **Åtgärd**

  En del av en PIP, åtgärdsmeddelanden är affärsmeddelanden som utbyts mellan partner.

* **Signal**

   En del av en PIP, signalmeddelanden är bekräftelser som skickas som svar på åtgärdsmeddelanden.

* **Enkel åtgärd och dubbelverkande**

  För en pip med en åtgärd är det enda svaret ett bekräftelsesignalmeddelande. För en PIP med dubbel åtgärd får initieraren ett svarsmeddelande och svarar med en bekräftelse utöver meddelandeflödet för en åtgärd.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration ännu [registrerar du dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) för att lagra ditt avtal och andra B2B-artefakter. Det här integrationskontot måste vara kopplat till din Azure-prenumeration.

* Minst två [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) som definieras i ditt integrationskonto och konfigureras med "DUNS"-kvalificeraren under **Affärsidentiteter**

* En PIP-processkonfiguration, som krävs för att skicka eller ta emot RosettaNet-meddelanden, i ditt integrationskonto. Processkonfigurationen lagrar alla PIP-konfigurationsegenskaper. Du kan sedan referera till den här konfigurationen när du skapar ett avtal med partnern. Om du vill skapa en PIP-processkonfiguration i ditt integrationskonto läser du [Lägga till PIP-processkonfiguration](#add-pip).

* Valfria [certifikat](../logic-apps/logic-apps-enterprise-integration-certificates.md) för kryptering, dekryptering eller signering av meddelanden som du överför till integrationskontot. Certifikat krävs bara om du använder signering eller kryptering.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Lägg till PIP-processkonfiguration

Så här lägger du till en PIP-processkonfiguration i ditt integrationskonto:

1. Leta reda på och öppna ditt integrationskonto i [Azure-portalen.](https://portal.azure.com)

1. Välj **panelen RosettaNet PIP** i fönstret **Översikt.**

   ![Välj RosettaNet-panel](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. Under **RosettaNet PIP**väljer du **Lägg till**. Ange dina PIP-uppgifter.

   ![Lägg till RottaNet PIP-detaljer](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Namn** | Ja | Ditt PIP-namn |
   | **PIP-kod** | Ja | Den tresiffriga PIP-koden. Mer information finns i [RosettaNet PIPs](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **PIP-version** | Ja | PIP-versionsnumret, som är tillgängligt baserat på din valda PIP-kod |
   ||||

   Mer information om dessa PIP-egenskaper finns på [RosettaNets webbplats](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg).

1. När du är klar väljer du **OK**, vilket skapar PIP-konfigurationen.

1. Om du vill visa eller redigera processkonfigurationen markerar du PIP och väljer **Redigera som JSON**.

   Alla processkonfigurationsinställningar kommer från PIP:s specifikationer. Logic Apps fyller de flesta av inställningarna med standardvärden som är de mest använda värdena för dessa egenskaper.

   ![Redigera RosettaNet PIP-konfiguration](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Bekräfta att inställningarna motsvarar värdena i lämplig PIP-specifikation och uppfyller dina affärsbehov. Om det behövs uppdaterar du värdena i JSON och sparar ändringarna.

## <a name="create-rosettanet-agreement"></a>Skapa RosettaNet-avtal

1. I [Azure-portalen](https://portal.azure.com)söker du efter och öppnar ditt integrationskonto, om det inte redan är öppet.

1. Välj panelen **Avtal** i fönstret **Översikt.**

   ![Välj panelen Avtal](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. Under **Avtal**väljer du **Lägg till**. Ange dina avtalsuppgifter.

   ![Lägg till avtalsinformation](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Namn** | Ja | Avtalets namn |
   | **Typ av avtal** | Ja | Välj **RosettaNet**. |
   | **Värdpartner** | Ja | Ett avtal kräver både en värd och gästpartner. Värdpartnern representerar den organisation som konfigurerar avtalet. |
   | **Värdidentitet** | Ja | En identifierare för värdpartnern |
   | **Gästpartner** | Ja | Ett avtal kräver både en värd och gästpartner. Gästpartnern representerar organisationen som gör affärer med värdpartnern. |
   | **Gästidentitet** | Ja | En identifierare för gästpartnern |
   | **Ta emot inställningar** | Varierar | Dessa egenskaper gäller för alla meddelanden som tas emot av värdpartnern |
   | **Skicka inställningar** | Varierar | Dessa egenskaper gäller för alla meddelanden som skickas av värdpartnern |  
   | **RosettaNet PIP referenser** | Ja | PIP-referenserna för avtalet. Alla RosettaNet-meddelanden kräver PIP-konfigurationer. |
   ||||

1. Om du vill konfigurera ditt avtal för att ta emot inkommande meddelanden från gästpartnern väljer du **Ta emot inställningar**.

   ![Ta emot inställningar](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Om du vill aktivera signering eller kryptering för inkommande meddelanden väljer du **Meddelande** under **Meddelanden**eller Meddelande **ska krypteras.**

      | Egenskap | Krävs | Beskrivning |
      |----------|----------|-------------|
      | **Meddelandet ska signeras** | Inga | Signera inkommande meddelanden med det valda certifikatet. |
      | **Certifikat** | Ja, om signering är aktiverat | Certifikatet som ska användas för signering |
      | **Aktivera meddelandekryptering** | Inga | Kryptera inkommande meddelanden med det valda certifikatet. |
      | **Certifikat** | Ja, om kryptering är aktiverat | Certifikatet som ska användas för kryptering |
      ||||

   1. Under varje val väljer du respektive [certifikat](./logic-apps-enterprise-integration-certificates.md), som du tidigare har lagt till i ditt integrationskonto, som ska användas för signering eller kryptering.

1. Om du vill konfigurera ditt avtal för att skicka meddelanden till gästpartnern väljer du **Skicka inställningar**.

   ![Skicka inställningar](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Om du vill aktivera signering eller kryptering för utgående meddelanden väljer du **Aktivera meddelandesignering** respektive **Aktivera meddelandekryptering** under **Meddelanden.** Under varje val väljer du respektive algoritm och [certifikat](./logic-apps-enterprise-integration-certificates.md), som du tidigare har lagt till i ditt integrationskonto, som ska användas för signering eller kryptering.

      | Egenskap | Krävs | Beskrivning |
      |----------|----------|-------------|
      | **Aktivera meddelandesignering** | Inga | Signera utgående meddelanden med den valda signeringsalgoritmen och certifikatet. |
      | **Signeringsalgoritm** | Ja, om signering är aktiverat | Signeringsalgoritmen som ska användas, baserat på det valda certifikatet |
      | **Certifikat** | Ja, om signering är aktiverat | Certifikatet som ska användas för signering |
      | **Aktivera meddelandekryptering** | Inga | Kryptera utgående med den valda krypteringsalgoritmen och certifikatet. |
      | **Krypteringsalgoritm** | Ja, om kryptering är aktiverat | Krypteringsalgoritmen som ska användas, baserat på det valda certifikatet |
      | **Certifikat** | Ja, om kryptering är aktiverat | Certifikatet som ska användas för kryptering |
      ||||

   1. Under **Slutpunkter**anger du de webbadresser som krävs för att skicka åtgärdsmeddelanden och bekräftelser.

      | Egenskap | Krävs | Beskrivning |
      |----------|----------|-------------|
      | **Url till åtgärd** |  Ja | URL:en som ska användas för att skicka åtgärdsmeddelanden. URL:en är ett obligatoriskt fält för både synkrona och asynkrona meddelanden. |
      | **URL för bekräftelse** | Ja | URL:en som ska användas för att skicka bekräftelsemeddelanden. URL:en är ett obligatoriskt fält för asynkrona meddelanden. |
      ||||

1. Om du vill ställa in ditt avtal med RosettaNet PIP-referenser för partners väljer du **RosettaNet PIP-referenser**. Under **PIP-namn**väljer du namnet på din tidigare skapade PIP.

   ![PIP-referenser](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   Ditt val fyller i de återstående egenskaperna, som baseras på den PIP som du har konfigurerat i ditt integrationskonto. Om det behövs kan du ändra **PIP-rollen**.

   ![Vald PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

När du har slutfört de här stegen är du redo att skicka eller ta emot RosettaNet-meddelanden.

## <a name="rosettanet-templates"></a>RosettaNet-mallar

För att påskynda utvecklings- och rekommenderar integrationsmönster kan du använda logikappmallar för avkodning och kodning av RosettaNet-meddelanden. När du skapar en logikapp kan du välja från mallgalleriet i Logic App Designer. Du hittar även dessa mallar i [GitHub-databasen för Azure Logic Apps](https://github.com/Azure/logicapps).

![RosettaNet-mallar](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Ta emot eller avkoda RosettaNet-meddelanden

1. [Skapa en tom logikapp](quickstart-create-first-logic-app-workflow.md).

1. [Länka ditt integrationskonto](logic-apps-enterprise-integration-create-integration-account.md#link-account) till logikappen.

1. Innan du kan lägga till en åtgärd för att avkoda RosettaNet-meddelandet måste du lägga till en utlösare för att starta logikappen, till exempel en begärandeutlösare.

1. När du har lagt till utlösaren väljer du **Nytt steg**.

   ![Lägga till utlösare för begäran](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Skriv "rosettanet" i sökrutan och välj den här åtgärden: **RosettaNet Avkoda**

   ![Hitta och välj åtgärden "RosettaNet Avkoda"](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Ange information om åtgärdens egenskaper:

   ![Ange åtgärdsinformation](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Meddelande** | Ja | RosettaNet-meddelandet att avkoda  |
   | **Rubriker** | Ja | HTTP-huvuden som anger värdena för versionen, som är RNIF-versionen, och svarstypen, som anger kommunikationstypen mellan partnerna och kan vara synkron eller asynkron |
   | **Roll** | Ja | Värdpartnerns roll i PIP |
   ||||

   Från rosettanet avkoda åtgärden, utdata, tillsammans med andra egenskaper, innehåller **Utgående signal**, som du kan välja att koda och återgå till partnern, eller vidta några andra åtgärder på utdata.

## <a name="send-or-encode-rosettanet-messages"></a>Skicka eller koda RosettaNet-meddelanden

1. [Skapa en tom logikapp](quickstart-create-first-logic-app-workflow.md).

1. [Länka ditt integrationskonto](logic-apps-enterprise-integration-create-integration-account.md#link-account) till logikappen.

1. Innan du kan lägga till en åtgärd för att koda RosettaNet-meddelandet måste du lägga till en utlösare för att starta logikappen, till exempel en begärandeutlösare.

1. När du har lagt till utlösaren väljer du **Nytt steg**.

   ![Lägga till utlösare för begäran](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Skriv "rosettanet" i sökrutan och välj den här åtgärden: **RosettaNet Koda**

   ![Hitta och välj åtgärden "RosettaNet Encode"](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Ange information om åtgärdens egenskaper:

   ![Ange åtgärdsinformation](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Meddelande** | Ja | RosettaNet-meddelandet för att koda  |
   | **Värdpartner** | Ja | Värdpartnerns namn |
   | **Gästpartner** | Ja | Gästpartnerns namn |
   | **PIP-kod** | Ja | PIP-koden |
   | **PIP-version** | Ja | PIP-versionen |  
   | **PIP-instansidentitet** | Ja | Den unika identifieraren för detta PIP-meddelande |  
   | **Meddelandetyp** | Ja | Den typ av meddelande som ska kodas |  
   | **Roll** | Ja | Värdpartnerns roll |
   ||||

   Det kodade meddelandet är nu klart att skickas till partnern.

1. Om du vill skicka det kodade meddelandet använder det här exemplet **HTTP-åtgärden,** som har ett nytt namn på "HTTP - Skicka kodat meddelande till partner".

   ![HTTP-åtgärd för att skicka RosettaNet-meddelande](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   Per RosettaNet-standarder betraktas affärstransaktioner som slutförs endast när alla steg som definieras av PIP är klara.

1. När värden skickar det kodade meddelandet till partnern väntar värden på signalen och bekräftelsen. Om du vill utföra den här uppgiften lägger du till **RosettaNet-vänta på** svarsåtgärden.

   ![Lägg till åtgärden "RosettaNet wait for response"](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   Varaktigheten för att vänta och antalet försök baseras på PIP-konfigurationen i ditt integrationskonto. Om svaret inte tas emot genererar den här åtgärden ett meddelande om fel. Om du vill hantera återförsök placerar du alltid **kod-** och **vänta på svarsåtgärder** i en **Till-loop.**

   ![Tills slinga med RosettaNet åtgärder](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du validerar, transformerar och andra meddelandeåtgärder med [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
