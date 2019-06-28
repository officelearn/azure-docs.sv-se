---
title: RosettaNet meddelanden för B2B enterprise-integration – Azure Logic Apps
description: Utbyta RosettaNet meddelanden i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 88e02f3fbbca8007fdf479bb973f50c42a878d6e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333307"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Utbyta RosettaNet meddelanden för B2B enterprise-integration i Azure Logic Apps 

[RosettaNet](https://resources.gs1us.org) är en ideell consortium som har upprättat standardprocesser för delning av företagsinformation. Dessa standarder används ofta för andra och är utökas inom semiconductor, t.ex logistik. RosettaNet consortium skapar och underhåller Partner gränssnittet processer (kärnor), som tillhandahåller gemensamma definitioner av affärsprocessen för alla RosettaNet meddelandeutbyten. RosettaNet baseras på XML och definierar meddelandet riktlinjer gränssnitt för affärsprocesser och implementering ramverk för kommunikation mellan företag.

I [Azure Logic Apps](../logic-apps/logic-apps-overview.md), RosettaNet-anslutning kan du skapa lösningar som har stöd för RosettaNet standarder. Anslutningen är baserad på RosettaNet implementering Framework (RNIF) version 2.0.01. RNIF är ett öppna nätverks-programramverk som gör det möjligt för affärspartners att samarbeta kör RosettaNet kärnor. Det här ramverket definierar strukturen för meddelandet, behovet av bekräftelser Multipurpose Internet Mail Extensions (MIME) kodning och den digitala signaturen.

Mer specifikt innehåller anslutningen dessa funktioner:

* Koda eller ta emot RosettaNet meddelanden.
* Avkoda eller skicka RosettaNet meddelanden.
* Vänta på svar och generering av meddelande med fel.

För de funktionerna stöder anslutningen alla kärnor som definieras av RNIF 2.0.01. Kommunikation med partnern som kan vara synkron eller asynkron.

## <a name="rosettanet-concepts"></a>RosettaNet begrepp

Här följer några begrepp och termer som är unika för RosettaNet-specifikationen och är viktiga när du skapar RosettaNet-baserade integreringar:

* **PIP**

  RosettaNet organisation skapar och underhåller Partner gränssnittet processer (kärnor), som tillhandahåller gemensamma definitioner av affärsprocessen för alla RosettaNet meddelandeutbyten. Varje PIP-specifikation innehåller en dokumentfil typ definition (DTD) och ett meddelande riktlinje dokument. DTD-filen definierar strukturen message service-innehåll. Meddelande-riktlinje dokument, som är ett läsbart HTML-fil, anger element på servernivå begränsningar. De här filerna ger tillsammans en klar definition av affärsprocessen.

   Kärnor kategoriseras efter en övergripande funktioner, eller klustret, och en Underfunktionen eller segment. Till exempel ”3A4” är PIP för inköpsorder är funktionen hantering när ”3” och ”3A” är kvoten & ordning post Underfunktionen. Mer information finns i den [RosettaNet plats](https://resources.gs1us.org).

* **Åtgärd**

  En del av en PIP åtgärdsmeddelanden är business-meddelanden som utbyts mellan partner.

* **Signal**

   En del av en PIP signalen meddelanden är bekräftelser som skickas som svar på åtgärdsmeddelanden.

* **Enskild åtgärd och dubbla åtgärd**

  För en enda åtgärd PIP är endast svaret ett bekräftelsemeddelande signal. För en dubbel-action PIP initieraren tar emot ett svarsmeddelande och svarar med en bekräftelse förutom single-action meddelande flödet.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du inte har en Azure-prenumeration än, [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).

* En [integrationskontot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) för att lagra avtalet och andra B2B-artefakter. Den här integrationskontot måste vara associerad med din Azure-prenumeration.

* Minst två [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) som definieras i ditt integrationskonto och konfigurerad med ”DUNS” kvalificeraren under **Företagsidentiteter**

* En PIP Processkonfiguration, som krävs för att skicka eller ta emot RosettaNet meddelanden i ditt integrationskonto. Konfigurationen av processen lagrar alla egenskaper för PIP-konfiguration. Du kan sedan referera till den här konfigurationen när du skapar ett avtal med partnern. Om du vill skapa en konfiguration för PIP-processen i ditt integrationskonto [lägga till PIP processen configuration](#add-pip).

* Valfritt [certifikat](../logic-apps/logic-apps-enterprise-integration-certificates.md) för kryptering, dekryptering eller signering av meddelanden som du överför till integrationskontot. Certifikat krävs endast om du är Använd signering eller kryptering.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Lägg till PIP Processkonfiguration

Följ dessa steg om du vill lägga till en konfiguration för PIP-processen i ditt integrationskonto:

1. I den [Azure-portalen](https://portal.azure.com), leta upp och öppna ditt integrationskonto.

1. På den **översikt** väljer den **RosettaNet PIP** panelen.

   ![Välj RosettaNet panel](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. Under **RosettaNet PIP**, Välj **Lägg till**. Ange information om din PIP.

   ![Lägg till RosettaNet PIP-information](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Namn** | Ja | PIP-namn |
   | **PIP-kod** | Ja | PIP tresiffrig kod. Mer information finns i [RosettaNet kärnor](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **PIP Version** | Ja | PIP-versionsnummer, vilka är tillgängliga beroende på din valda PIP-kod |
   ||||

   Mer information om de här PIP-egenskaperna finns i [RosettaNet webbplats](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg).

1. När du är klar väljer **OK**, vilket skapar PIP-konfigurationen.

1. Om du vill visa eller redigera konfigurationen av processen, Välj PIP och välj **redigera som JSON**.

   Alla bearbeta configuration inställningar som kommer från den PIP-specifikationer. Logic Apps lägger de flesta inställningar med de standardvärden som är de vanligaste värdena för dessa egenskaper.

   ![Redigera RosettaNet PIP-konfiguration](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Bekräfta att inställningarna för värden i lämpliga PIP-specifikationen och uppfylla dina affärsbehov. Om det behövs uppdaterar du värdet i JSON och spara ändringarna.

## <a name="create-rosettanet-agreement"></a>Skapa RosettaNet avtal

1. I den [Azure-portalen](https://portal.azure.com), hitta och öppna ditt integrationskonto, om inte redan är öppen.

1. På den **översikt** väljer den **avtal** panelen.

   ![Välj panelen avtal](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. Under **avtal**, Välj **Lägg till**. Ange din information.

   ![Lägg till avtalsuppgifter](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Namn** | Ja | Avtalets namn |
   | **Avtalstyp** | Ja | Välj **RosettaNet**. |
   | **Värdpartner** | Ja | Ett avtal kräver både en värdens och gästens partner. Den mottagande partnern representerar organisationen som konfigurerar avtalet. |
   | **Värd-identitet** | Ja | En identifierare för den mottagande partnern |
   | **Gästpartner** | Ja | Ett avtal kräver både en värdens och gästens partner. Gästpartner representerar den organisation som gör affärer med den mottagande partnern. |
   | **Gästidentitet** | Ja | En identifierare för gästpartner |
   | **Ta emot inställningarna** | Varierar | Dessa egenskaper gäller för alla meddelanden som tas emot av den mottagande partnern |
   | **Skicka inställningar** | Varierar | Dessa egenskaper gäller för alla meddelanden som skickas av den mottagande partnern |  
   | **RosettaNet PIP-referenser** | Ja | PIP-referenser för avtalet. Alla RosettaNet meddelanden kräver PIP-konfigurationer. |
   ||||

1. Om du vill konfigurera ditt avtal för att ta emot inkommande meddelanden från gästpartner Välj **ta emot inställningar**.

   ![Ta emot inställningarna](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Aktivera signering eller kryptering för inkommande meddelanden under **meddelanden**väljer **meddelandet ska vara signerat** eller **meddelandet ska krypteras** respektive.

      | Egenskap | Krävs | Beskrivning |
      |----------|----------|-------------|
      | **Meddelandet ska vara signerat** | Nej | Logga inkommande meddelanden i det valda certifikatet. |
      | **Certifikat** | Ja, om signering är aktiverat | Certifikatet som ska användas för signering |
      | **Aktivera meddelandekryptering** | Nej | Kryptera inkommande meddelanden i det valda certifikatet. |
      | **Certifikat** | Ja, om kryptering är aktiverat | Certifikatet som ska användas för kryptering |
      ||||

   1. Under varje val väljer till respektive [certifikat](./logic-apps-enterprise-integration-certificates.md), som du tidigare har lagts till ditt integrationskonto ska användas för signering eller kryptering.

1. Om du vill konfigurera ditt avtal för att skicka meddelanden till gästpartner, Välj **skicka inställningar**.

   ![Skicka inställningar](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Aktivera signering eller kryptering för utgående meddelanden under **meddelanden**väljer **aktivera Meddelandesignering** eller **aktivera meddelandekryptering** respektive. Under varje val väljer algoritmen för respektive och [certifikat](./logic-apps-enterprise-integration-certificates.md), som du tidigare har lagts till ditt integrationskonto ska användas för signering eller kryptering.

      | Egenskap | Krävs | Beskrivning |
      |----------|----------|-------------|
      | **Aktivera Meddelandesignering** | Nej | Logga utgående meddelanden med den valda Signeringsalgoritm och certifikatet. |
      | **Signeringsalgoritm** | Ja, om signering är aktiverat | Signeringsalgoritm för dig, baserat på det valda certifikatet |
      | **Certifikat** | Ja, om signering är aktiverat | Certifikatet som ska användas för signering |
      | **Aktivera meddelandekryptering** | Nej | Kryptera utgående med den valda krypteringsalgoritmen och certifikatet. |
      | **Krypteringsalgoritm** | Ja, om kryptering är aktiverat | Krypteringsalgoritmen som ska använda, baserat på det valda certifikatet |
      | **Certifikat** | Ja, om kryptering är aktiverat | Certifikatet som ska användas för kryptering |
      ||||

   1. Under **slutpunkter**, ange de URL: erna att använda för att skicka åtgärdsmeddelanden och bekräftelser.

      | Egenskap | Krävs | Beskrivning |
      |----------|----------|-------------|
      | **Åtgärdens URL-adress** |  Ja | URL: en ska användas för att skicka åtgärdsmeddelanden. URL: en är ett obligatoriskt fält för både synkrona och asynkrona meddelanden. |
      | **Bekräftelse-URL** | Ja | URL som ska användas för att skicka meddelanden. URL: en är ett obligatoriskt fält för asynkrona meddelanden. |
      ||||

1. Om du vill konfigurera ditt avtal med RosettaNet PIP-referenser för partner, Välj **RosettaNet PIP refererar till**. Under **PIP namn**, väljer du namnet för ditt tidigare skapade PIP.

   ![PIP-referenser](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   Ditt val lägger de övriga egenskaperna som är baserade på PIP som du har konfigurerat i ditt integrationskonto. Om nödvändigt, kan du ändra den **PIP rollen**.

   ![Valda PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

När du har slutfört de här stegen, är du redo att skicka eller ta emot RosettaNet meddelanden.

## <a name="rosettanet-templates"></a>RosettaNet mallar

För att påskynda utvecklingen och rekommenderar integration mönster, kan du använda mallar för logikappar för avkoda och koda RosettaNet meddelanden. När du skapar en logikapp kan välja du från mallgalleriet i Logic App Designer. Du kan också hitta dessa mallar i den [GitHub-lagringsplatsen för Azure Logic Apps](https://github.com/Azure/logicapps).

![RosettaNet mallar](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Ta emot eller avkoda RosettaNet meddelanden

1. [Skapa en tom logikapp](quickstart-create-first-logic-app-workflow.md).

1. [Länka ditt integrationskonto](logic-apps-enterprise-integration-create-integration-account.md#link-account) i logikappen.

1. Innan du kan lägga till en åtgärd för att avkoda meddelandet RosettaNet, måste du lägga till en utlösare för att starta logikappen, till exempel en begäransutlösare.

1. När du lägger till utlösaren väljer **nytt steg**.

   ![Lägg till utlösare för begäran](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Ange ”rosettanet” i sökrutan och välj den här åtgärden: **RosettaNet avkoda**

   ![Hitta och välj ”RosettaNet avkoda” åtgärden](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Ange information för den åtgärden egenskaper:

   ![Ange information om åtgärd](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **meddelande** | Ja | RosettaNet meddelandet att avkoda  |
   | **Headers** | Ja | HTTP-huvuden som anger värden för den versionen, vilket är versionen som RNIF och svarstyp anger kommunikation mellan partner som kan vara synkron eller asynkron |
   | **Roll** | Ja | Rollen för den mottagande partnern i PIP |
   ||||

   Från instruktionen RosettaNet avkoda utdata, tillsammans med andra egenskaper innehåller **utgående signal**, som du kan välja att koda och gå tillbaka till partnern eller vidta andra åtgärder för dessa utdata.

## <a name="send-or-encode-rosettanet-messages"></a>Skicka eller koda RosettaNet meddelanden

1. [Skapa en tom logikapp](quickstart-create-first-logic-app-workflow.md).

1. [Länka ditt integrationskonto](logic-apps-enterprise-integration-create-integration-account.md#link-account) i logikappen.

1. Innan du kan lägga till en åtgärd för att koda RosettaNet meddelandet, måste du lägga till en utlösare för att starta logikappen, till exempel en begäransutlösare.

1. När du lägger till utlösaren väljer **nytt steg**.

   ![Lägg till utlösare för begäran](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Ange ”rosettanet” i sökrutan och välj den här åtgärden: **RosettaNet koda**

   ![Hitta och välj ”RosettaNet koda” åtgärden](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Ange information för den åtgärden egenskaper:

   ![Ange information om åtgärd](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **meddelande** | Ja | RosettaNet-meddelande att koda  |
   | **Värdpartner** | Ja | Värdnamn för partner |
   | **Gästpartner** | Ja | Gästen Partnernamn |
   | **PIP-kod** | Ja | PIP-kod |
   | **PIP-version** | Ja | PIP-version |  
   | **PIP identitet** | Ja | Den unika identifieraren för den här PIP-meddelande |  
   | **Meddelandetyp** | Ja | Typ av meddelande att koda |  
   | **Roll** | Ja | Rollen för den mottagande partnern |
   ||||

   Kodat meddelande är nu klar att skicka till partnern.

1. Om du vill skicka kodade meddelandet, det här exemplet används den **HTTP** ”HTTP - skicka kodade meddelandet till partner” byta namn på åtgärden, vilket är.

   ![HTTP-åtgärd för att skicka RosettaNet meddelande](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   Per RosettaNet standarder anses affärstransaktioner slutförd endast när alla steg som definieras av PIP har slutförts.

1. När värden skickar kodade meddelandet till partner, väntar värden signal och bekräftelse. Om du vill utföra den här uppgiften att lägga till den **RosettaNet väntan på svar** åtgärd.

   ![Lägg till ”RosettaNet väntan på svar”-åtgärd](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   Hur länge ska använda för att vänta på och antalet återförsök baseras på PIP-konfigurationen i ditt integrationskonto. Om du inte fått svar i den här åtgärden genererar ett meddelande med fel. Om du vill hantera återförsök, använda den **koda** och **vänta på svar** åtgärder i en **tills** loop.

   ![Until-loop med RosettaNet åtgärder](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig att verifiera, omvandla och andra meddelandeåtgärder med den [Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
