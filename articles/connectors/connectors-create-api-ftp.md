---
title: Ansluta till FTP-server
description: Automatisera uppgifter och arbetsflöden som skapar, övervakar och hanterar filer på en FTP-server med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: 94ca609d9dc070e6e2b4dc878ecd8dfaf9331ede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648180"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Skapa, övervaka och hantera FTP-filer med hjälp av Azure Logic Apps

Med Azure Logic Apps och FTP-anslutningsappen kan du skapa automatiserade uppgifter och arbetsflöden som skapar, övervakar, skickar och ta emot filer via ditt konto på en FTP-server, tillsammans med andra åtgärder, till exempel:

* Övervaka när filer läggs till eller ändras.
* Hämta, skapa, kopiera, uppdatera, lista och ta bort filer.
* Hämta filinnehåll och metadata.
* Extrahera arkiv till mappar.

Du kan använda utlösare som får svar från FTP-servern och gör utdata tillgängliga för andra åtgärder. Du kan använda körningsåtgärder i logikapparna för att hantera filer på FTP-servern. Du kan också låta andra åtgärder använda utdata från FTP-åtgärder. Om du till exempel regelbundet får filer från FTP-servern kan du skicka e-post om dessa filer och deras innehåll med hjälp av Office 365 Outlook-anslutningen eller Outlook.com-anslutningsappen. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="limitations"></a>Begränsningar

* FTP-anslutningen stöder endast explicit FTP över SSL (FTPS) och är inte kompatibel med implicit FTPS.

* Som standard kan FTP-åtgärder läsa eller skriva filer som är *50 MB eller mindre*. Om du vill hantera filer som är större än 50 MB stöder [FTP-åtgärder meddelandesegmentering](../logic-apps/logic-apps-handle-large-messages.md). Åtgärden **Hämta filinnehåll** använder implicit segmentering.

* FTP-utlösare stöder inte segmentering. När du begär filinnehåll väljer utlösare bara filer som är 50 MB eller mindre. Så här hämtar du filer som är större än 50 MB:

  * Använd en FTP-utlösare som returnerar filegenskaper, till exempel **När en fil läggs till eller ändras (endast egenskaper)**.

  * Följ utlösaren med åtgärden FTP **Get-filinnehåll,** som läser hela filen och implicit använder segmentering.

* Om du har en lokal FTP-server kan du överväga att skapa en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) eller använda [Azure App Service Hybrid-anslutningar](../app-service/app-service-hybrid-connections.md), som båda låter dig komma åt lokala datakällor utan att använda en lokal datagateway.

## <a name="how-ftp-triggers-work"></a>Så här fungerar FTP-utlösare

FTP utlöser arbete genom att avsöka FTP-filsystemet och leta efter alla filer som har ändrats sedan den senaste mätningen. Med vissa verktyg kan du bevara tidsstämpeln när filerna ändras. I dessa fall måste du inaktivera den här funktionen så att utlösaren kan fungera. Här är några vanliga inställningar:

| SFTP-klient | Åtgärd |
|-------------|--------|
| Winscp | Gå > till **Alternativinställningar** > **Preferences** > **Överför** > **redigera** > bevara**tidsstämpel****Inaktivera** |
| Filezilla | Gå till **Överför** > **bevara tidsstämplar för överförda filer** > **Inaktivera** |
|||

När en utlösare hittar en ny fil kontrollerar utlösaren att den nya filen är klar och inte delvis skriven. En fil kan till exempel ha pågående ändringar när utlösaren kontrollerar filservern. Om du vill undvika att returnera en delvis skriven fil noterar utlösaren tidsstämpeln för filen som har de senaste ändringarna, men returnerar inte filen omedelbart. Utlösaren returnerar filen endast när servern avsöks igen. Ibland kan detta orsaka en fördröjning som är upp till dubbelt så många som utlösarens avsökningsintervall.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Din FTP-värdserveradress och kontouppgifter

  FTP-anslutningen kräver att FTP-servern är tillgänglig från internet och konfigureras för att fungera i *passivt* läge. Med dina autentiseringsuppgifter kan logikappen skapa en anslutning och komma åt ditt FTP-konto.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt FTP-konto. Om du vill börja med en FTP-utlösare [skapar du en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en FTP-åtgärd startar du **Recurrence** logikappen med en annan utlösare, till exempel upprepningsutlösaren.

## <a name="connect-to-ftp"></a>Ansluta till FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer.

1. För tomma logikappar anger du `ftp` som filter i sökrutan. Välj den utlösare du vill använda i listan **Utlösare.**

   ELLER

   För befintliga logikappar väljer du **Nytt steg**under det sista steget där du vill lägga till en åtgärd och väljer sedan Lägg till **en åtgärd**. Ange som filter `ftp` i sökrutan. Välj den åtgärd du vill använda i listan **Åtgärder.**

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Markera plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. Ange anslutningsinformation och välj **Skapa**.

1. Ange information för den valda utlösaren eller åtgärden och fortsätt att skapa logikappens arbetsflöde.

## <a name="examples"></a>Exempel

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>Lägga till FTP-utlösare

**När en fil läggs till eller ändras (endast egenskaper)** startar ett logikapparbetsflöde när utlösaren upptäcker att en fil läggs till eller ändras på en FTP-server. Du kan till exempel lägga till ett villkor som kontrollerar filens innehåll och bestämmer om innehållet ska hämtas, baserat på om innehållet uppfyller ett angivet villkor. Slutligen kan du lägga till en åtgärd som hämtar filens innehåll och placera innehållet i en annan mapp på SFTP-servern.

Du kan till exempel använda den här utlösaren för att övervaka en FTP-mapp för nya filer som beskriver kundorder. Du kan sedan använda en FTP-åtgärd, till exempel **Hämta filmetadata** för att hämta egenskaperna för den nya filen, och sedan använda **Hämta filinnehåll** för att hämta innehållet från filen för vidare bearbetning och lagring av order i en orderdatabas.

Här är ett exempel som visar hur du använder **utlösaren När en fil läggs till eller ändras (endast egenskaper).**

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. För tomma logikappar anger du `ftp` som filter i sökrutan. Under listan utlösare väljer du den här utlösaren: **När en arkiverad läggs till eller ändras (endast egenskaper)**

   ![Söka efter och välja FTP-utlösaren](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. Ange nödvändiga uppgifter för anslutningen och välj sedan **Skapa**.

   Som standard överför den här kopplingen filer i textformat. Om du till exempel vill överföra filer i binärt format väljer du **Binär transport**.

   ![Skapa anslutning till FTP-server](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. Markera mappikonen i rutan **Mapp** så att en lista visas. Om du vill söka efter mappen som du vill övervaka för**>** nya eller redigerade filer markerar du pilen för höger vinkel ( ), bläddrar till mappen och väljer sedan mappen.

   ![Söka efter och välja mapp som ska övervakas](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   Den markerade mappen visas i **rutan Mapp.**

   ![Markerad mapp visas i egenskapen "Mapp"](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. Spara din logikapp. Välj **Spara**i designerverktygsfältet .

Nu när logikappen har en utlösare lägger du till de åtgärder som du vill köra när logikappen hittar en ny eller redigerad fil. I det här exemplet kan du lägga till en FTP-åtgärd som hämtar det nya eller uppdaterade innehållet.

<a name="get-content"></a>

### <a name="add-ftp-action"></a>Lägg till FTP-åtgärd

Åtgärden **Hämta filmetadata** hämtar egenskaperna för en fil som finns på FTP-servern och åtgärden **Hämta filinnehåll** hämtar filinnehållet baserat på informationen om filen på FTP-servern. Du kan till exempel lägga till utlösaren från föregående exempel och dessa åtgärder för att hämta filens innehåll efter att filen har lagts till eller redigerats.

1. Under utlösaren eller andra åtgärder väljer du **Nytt steg**.

1. Ange som filter `ftp` i sökrutan. Under åtgärdslistan väljer du den här åtgärden: **Hämta metadata för filer**

   ![Välj åtgärden Hämta metadata för filer](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. Om du redan har en anslutning till ftp-servern och ditt FTP-konto går du vidare till nästa steg. I annat fall anger du nödvändiga information för den anslutningen och väljer sedan **Skapa**.

   ![Skapa FTP-serveranslutning](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. När åtgärden **Hämta filmetadata** visas klickar du i rutan **Arkiv** så att listan med dynamiskt innehåll visas. Du kan nu välja egenskaper för utdata från tidigare steg. I listan med dynamiskt innehåll under **Hämta filmetadata**väljer du egenskapen **Lista över filer id,** som refererar till samlingen där filen lades till eller uppdaterades.

   ![Hitta och välj egenskapen Lista över filer Id](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   Egenskapen **List of Files Id** visas nu i rutan **Arkiv.**

   ![Markerad egenskap för lista över filer Id](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. Lägg nu till den här FTP-åtgärden: **Hämta filinnehåll**

   ![Sök efter och välj åtgärden Hämta filinnehåll](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. När åtgärden **Hämta filinnehåll** visas klickar du i rutan **Arkiv** så att listan med dynamiskt innehåll visas. Du kan nu välja egenskaper för utdata från tidigare steg. I listan med dynamiskt innehåll under **Hämta filmetadata**väljer du egenskapen **Id** som refererar till filen som har lagts till eller uppdaterats.

   ![Hitta och välj egenskapen "Id"](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   Egenskapen **Id** visas nu i rutan **Arkiv.**

   ![Markerad egenskap "Id"](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. Spara din logikapp.

## <a name="test-your-logic-app"></a>Testa logikappen

Om du vill kontrollera att arbetsflödet returnerar det innehåll som du förväntar dig lägger du till en annan åtgärd som skickar innehållet från den uppladdade eller uppdaterade filen.

1. Lägg till en åtgärd som kan skicka filens innehåll under åtgärden **Hämta filinnehåll.** I det här exemplet läggs åtgärden **Skicka ett e-postmeddelande** till Office 365 Outlook.

   ![Lägga till en åtgärd för att skicka e-post](./media/connectors-create-api-ftp/select-send-email-action.png)

1. När åtgärden har visas anger du informationen och inkluderar de egenskaper som du vill testa. Inkludera till exempel egenskapen **Filinnehåll,** som visas i listan med dynamiskt innehåll när du har valt **Visa mer** i avsnittet **Hämta filinnehåll.**

   ![Ange information om e-poståtgärd](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. Spara din logikapp. Om du vill köra och utlösa logikappen väljer du **Kör**i verktygsfältet och lägger sedan till en fil i FTP-mappen som logikappen nu övervakar.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här kopplingen, till exempel utlösare, åtgärder och begränsningar enligt beskrivningen i kopplingens Swagger-fil, finns på [kopplingens referenssida](https://docs.microsoft.com/connectors/ftpconnector/).

> [!NOTE]
> För logikappar i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandegränserna](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
