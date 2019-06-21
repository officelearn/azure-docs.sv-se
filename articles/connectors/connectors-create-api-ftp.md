---
title: Ansluta till FTP-server – Azure Logic Apps
description: Skapa, övervaka och hantera filer på en FTP-server med Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 66f1d726dcfa1a077abbff0d9f028036db43cc25
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293080"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Skapa, övervaka och hantera FTP-filer med hjälp av Azure Logic Apps

Med Azure Logic Apps och FTP-anslutningsappen kan skapa du automatiserade uppgifter och arbetsflöden som kan skapa, övervaka, skicka och ta emot filerna med ditt konto på en FTP-server, tillsammans med andra åtgärder, till exempel:

* Övervaka när filer läggs till eller ändras.
* Hämta, skapa, kopiera, uppdatera, lista, och ta bort filer.
* Hämta filinnehåll och metadata.
* Extrahera Arkiv till mappar.

Du kan använda utlösare som få svar från FTP-servern och göra utdata som är tillgängliga för andra åtgärder. Du kan använda Kör åtgärder i dina logic apps för att hantera filer på FTP-servern. Du kan också ha andra åtgärder som använder utdata från FTP-åtgärder. Om du regelbundet får filer från FTP-servern kan skicka du e-postmeddelande om dessa filer och sitt innehåll med hjälp av anslutningsappen Office 365 Outlook eller Outlook.com-anslutning. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limits

* FTP-anslutningsappen stöder bara explicit FTP över SSL (FTPS) och är inte kompatibel med implicit FTPS.

* Som standard FTP-åtgärder kan läsa eller skriva filer som är *50 MB eller mindre*. För att hantera filer som är större än 50 MB, FTP-stöd för åtgärder [meddelande storlekar](../logic-apps/logic-apps-handle-large-messages.md). Den **hämta filinnehåll** åtgärden använder implicit storlekar.

* FTP-utlösare stöder inte storlekar. När du begär innehåll, utlösare Markera bara de filer som är 50 MB eller mindre. Om du vill hämta filer större än 50 MB, så det här mönstret:

  * Använda en FTP-utlösare som returnerar filegenskaper, till exempel **när en fil läggs till eller ändras (enbart egenskaper)** .

  * Följ utlösare med FTP **hämta filinnehåll** åtgärd, som läser den fullständiga filen och implicit använder storlekar.

## <a name="how-ftp-triggers-work"></a>Hur FTP utlöser arbete

FTP-utlösare arbete genom att avsöka FTP-filsystemet och söker efter alla filer som har ändrats sedan den senaste avsökningen. Vissa verktyg kan du bevara tidsstämpel när filerna som ändras. I dessa fall kan behöva du inaktivera den här funktionen så att utlösaren kan arbeta. Här följer några vanliga inställningar:

| SFTP-klienten | Åtgärd |
|-------------|--------|
| Winscp | Gå till **alternativ** > **inställningar** > **överföra** > **redigera**  >  **Bevara tidsstämpel** > **inaktivera** |
| FileZilla | Gå till **överföra** > **bevara tidsstämplar av överförda filer** > **inaktivera** |
|||

Om en ny fil upptäcks under en utlösare kan utlösaren söker du efter att den nya filen är komplett och inte delvis skriftliga. En fil kan till exempel ha ändringar pågår när utlösaren kontrollerar filservern. För att undvika att returnera en delvis skriftliga fil, noterar utlösaren tidsstämpel för den fil som har de senaste ändringarna, men inte direkt returnerar filen. Utlösaren returnerar filen bara när en avsökning görs servern igen. Det här beteendet kan ibland orsaka en fördröjning som upp till två gånger utlösarens avsökningsintervall.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Din FTP-värd-adress och konto autentiseringsuppgifter

  FTP-anslutningsappen kräver att FTP-servern kan nås från internet och ställa in att fungera inom *passiva* läge. Dina autentiseringsuppgifter låta din logikapp skapa en anslutning och få åtkomst till FTP-kontot.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill få åtkomst till FTP-kontot. Du kommer igång med en FTP-utlösare [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en FTP-åtgärd, starta din logikapp med en annan utlösare, till exempel, **upprepning** utlösaren.

## <a name="connect-to-ftp"></a>Ansluta till FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. För tom logic apps i sökrutan anger du ”ftp” som filter. Välj utlösaren som du vill under listan över utlösare.

   ELLER

   För befintliga logikappar under det sista steget där du vill lägga till en åtgärd, Välj **nytt steg**, och välj sedan **Lägg till en åtgärd**. I sökrutan anger du ”ftp” som filter. Välj vilken åtgärd du önska under åtgärder.

   Om du vill lägga till en åtgärd mellan stegen, flyttar du pekaren över pilen mellan stegen. Välj plustecknet ( **+** ) som visas och välj **Lägg till en åtgärd**.

1. Ange informationen som krävs för anslutningen och välj sedan **skapa**.

1. Ange informationen som krävs för din valda utlösare eller åtgärd och fortsätt att utveckla logikappens arbetsflöde.

## <a name="examples"></a>Exempel

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>FTP-utlösare: När en fil läggs till eller ändras

Den här utlösaren startar en logikapparbetsflöde när utlösaren identifierar när en fil läggs till eller ändras på en FTP-server. Till exempel kan du lägga till ett villkor som kontrollerar dess innehåll och beslutar om att hämta innehållet, baserat på om innehållet uppfyller ett angivet villkor. Slutligen kan du lägga till en åtgärd som hämtar filens innehåll och placera innehållet i en mapp på SFTP-server.

**Enterprise exempel**: Du kan använda den här utlösaren för att övervaka en FTP-mapp för nya filer som beskriver kundorder. Du kan sedan använda en FTP-åtgärd som **hämta filinnehåll**, så du kan hämta den ordning innehåll för vidare bearbetning och lagra den ordningen i en order-databas.

Här är ett exempel som visar den här utlösaren: **När en fil läggs till eller ändras**

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. För tom logic apps i sökrutan anger du ”ftp” som filter. Under listan över utlösare, väljer du den här utlösaren: **När en arkiverade läggs till eller ändras – FTP**

   ![Hitta och välj FTP-utlösare](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Ange informationen som krävs för anslutningen och välj sedan **skapa**.

   Som standard överför den här kopplingen filer i textformat. Att överföra filer binärt format, till exempel var och när kodning används, Välj **binär Transport**.

   ![Skapa FTP-server-anslutning](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. Bredvid den **mappen** väljer du mappikonen så visas en lista. För att hitta mappen som du vill övervaka för nya eller redigerade filer, väljer du pilen rätvinkliga ( **>** ), bläddra till mappen och välj sedan mappen.

   ![Hitta och Välj mapp som ska övervakas](./media/connectors-create-api-ftp/select-folder.png)  

   Den valda mappen visas i den **mappen** box.

   ![Vald mapp](./media/connectors-create-api-ftp/selected-folder.png)  

Nu när logikappen har en utlösare, lägga till åtgärder som du vill köra när logikappen hittar en ny eller redigerad fil. Du kan lägga till en FTP-åtgärd som hämtar nya eller uppdaterade innehållet för det här exemplet.

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>FTP-åtgärd: Hämta innehåll

Den här åtgärden hämtar innehållet från en fil på en FTP-server när den filen läggs till eller uppdateras. Till exempel kan du lägga till utlösaren från exemplet ovan och en åtgärd som hämtar dess innehåll när den filen läggs till eller redigeras.

Här är ett exempel som visar den här åtgärden: **Hämta innehåll**

1. Under utlösaren eller andra åtgärder, väljer **nytt steg**.

1. I sökrutan anger du ”ftp” som filter. Välj den här åtgärden under åtgärder: **Hämta filinnehåll - FTP**

   ![Välj FTP-åtgärd](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Om du redan har en anslutning till FTP-servern och kontot kan gå till nästa steg. I annat fall anger du informationen som krävs för anslutningen och välj sedan **skapa**.

   ![Skapa FTP-server-anslutning](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Efter den **hämta filinnehåll** åtgärd öppnas klickar du på inuti den **filen** så att den dynamiska innehållslistan visas. Nu kan du välja Egenskaper för utdata från föregående steg. Den dynamiska innehållslistan, väljer du den **filinnehåll** egenskap som har innehållet för filen har lagts till eller uppdaterats.  

   ![Hitta och Välj fil](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   Den **filinnehåll** egenskapen visas nu i den **filen** box.

   ![Valda ”filinnehåll” egenskapen](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Spara din logikapp. Testa ditt arbetsflöde genom att lägga till en fil till FTP-mapp som din logikapp nu övervakar.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska de [anslutningsappens-referenssida](/connectors/ftpconnector/).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
