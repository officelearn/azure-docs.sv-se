---
title: Anslut till FTP-Server
description: Skapa, övervaka och hantera filer på en FTP-server med Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: c7b8c1ac94fd35a4a0cb30ad32d8c6ce39edc058
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789777"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Skapa, övervaka och hantera FTP-filer med hjälp av Azure Logic Apps

Med Azure Logic Apps och FTP-anslutningen kan du skapa automatiserade uppgifter och arbets flöden som skapar, övervakar, skickar och tar emot filer via ditt konto på en FTP-server, tillsammans med andra åtgärder, till exempel:

* Övervaka när filer läggs till eller ändras.
* Hämta, skapa, kopiera, uppdatera, lista och ta bort filer.
* Hämta fil innehåll och metadata.
* Extrahera arkiv till mappar.

Du kan använda utlösare som får svar från FTP-servern och göra utdata tillgängliga för andra åtgärder. Du kan använda kör åtgärder i dina Logi Kap par för att hantera filer på din FTP-server. Du kan också använda andra åtgärder för att använda utdata från FTP-åtgärder. Om du till exempel regelbundet hämtar filer från FTP-servern kan du skicka e-post om filerna och deras innehåll med hjälp av Office 365 Outlook Connector eller Outlook.com Connector. Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Begränsningar

* FTP-anslutningen har endast stöd för explicit FTP över SSL (FTPS) och är inte kompatibel med implicit FTPS.

* FTP-åtgärder kan som standard läsa eller skriva filer som är *50 MB eller mindre*. För att hantera filer som är större än 50 MB stöder FTP-åtgärder [meddelande segment](../logic-apps/logic-apps-handle-large-messages.md). Åtgärden **Hämta fil innehåll** använder implicit segment.

* FTP-utlösare stöder inte segment. När du begär fil innehåll väljer utlösare endast filer som är 50 MB eller mindre. Följ det här mönstret om du vill hämta filer som är större än 50 MB:

  * Använd en FTP-utlösare som returnerar fil egenskaper, till exempel **när en fil läggs till eller ändras (endast egenskaper)** .

  * Följ utlösaren med åtgärden **Hämta fil innehåll** i FTP, som läser den fullständiga filen och som implicit använder segment.

## <a name="how-ftp-triggers-work"></a>Så här fungerar FTP-utlösare

FTP-utlösare fungerar genom att avsöka FTP-filsystemet och leta efter en fil som har ändrats sedan den senaste avsökningen. Med vissa verktyg kan du bevara tidsstämpeln när filerna ändras. I dessa fall måste du inaktivera den här funktionen så att utlösaren kan fungera. Här följer några vanliga inställningar:

| SFTP-klient | Åtgärd |
|-------------|--------|
| WinSCP | Gå till **alternativen** > **inställningar** > **överföra** > **Redigera** > **bevara tidsstämpel** > **inaktivera** |
| FileZilla | Gå till **överför** > **bevara tidsstämplar för överförda filer** > **inaktivera** |
|||

När en utlösare hittar en ny fil, kontrollerar utlösaren att den nya filen är fullständig och inte delvis skriven. En fil kan till exempel ha ändringar som pågår när utlösaren kontrollerar fil servern. För att undvika att returnera en delvis skriven fil, noterar utlösaren tidsstämpeln för filen som har nyligen gjorda ändringar, men returnerar inte omedelbart den filen. Utlösaren returnerar filen endast när servern avsöks igen. Ibland kan det här problemet orsaka en fördröjning som är upp till två gånger utlösaren för avsöknings intervall.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Din FTP-värds server adress och kontoautentiseringsuppgifter

  FTP-anslutningen kräver att FTP-servern är tillgänglig från Internet och att den fungerar i *passivt* läge. Dina autentiseringsuppgifter gör att din Logic app kan skapa en anslutning och komma åt ditt FTP-konto.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic-app där du vill komma åt ditt FTP-konto. Starta med en FTP-utlösare genom att [skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en FTP-åtgärd startar du din Logic-app med en annan utlösare, till exempel utlösaren **upprepning** .

## <a name="connect-to-ftp"></a>Anslut till FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. För tomma Logi Kap par anger du "FTP" som filter i rutan Sök. Välj den utlösare som du vill använda under listan utlösare.

   ELLER

   För befintliga Logic Apps, under det sista steget där du vill lägga till en åtgärd, väljer du **nytt steg**och sedan **Lägg till en åtgärd**. I rutan Sök anger du "FTP" som filter. Under listan åtgärder väljer du den åtgärd som du vill använda.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet ( **+** ) som visas och välj **Lägg till en åtgärd**.

1. Ange nödvändig information för anslutningen och välj sedan **skapa**.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa din Logic Apps-arbetsflöde.

## <a name="examples"></a>Exempel

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>FTP-utlösare: när en fil läggs till eller ändras

Den här utlösaren startar ett Logic app-arbetsflöde när utlösaren upptäcker när en fil läggs till eller ändras på en FTP-server. Du kan till exempel lägga till ett villkor som kontrollerar filens innehåll och bestämmer om innehållet ska hämtas baserat på om innehållet uppfyller ett visst villkor. Slutligen kan du lägga till en åtgärd som hämtar filens innehåll och lägga till innehållet i en mapp på SFTP-servern.

**Enterprise-exempel**: du kan använda den här utlösaren för att övervaka en FTP-mapp för nya filer som beskriver kund beställningar. Du kan sedan använda en FTP-åtgärd, till exempel **Hämta fil innehåll**, så att du kan hämta Beställningens innehåll för ytterligare bearbetning och lagra den i en order databas.

Här är ett exempel som visar den här utlösaren: **när en fil läggs till eller ändras**

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. För tomma Logi Kap par anger du "FTP" som filter i rutan Sök. Välj den här utlösaren under listan utlösare: **när ett arkiverat har lagts till eller ändrats – FTP**

   ![Sök efter och välj FTP-utlösare](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Ange nödvändig information för anslutningen och välj sedan **skapa**.

   Som standard överför den här anslutningen filer i text format. Om du vill överföra filer i binärformat, till exempel WHERE och när kodning används, väljer du **binär transport**.

   ![Skapa anslutning till FTP-Server](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. Bredvid rutan **mapp** väljer du mappikonen så att en lista visas. Om du vill hitta den mapp som du vill övervaka för nya eller redigerade filer väljer du högerpilen ( **>** ), bläddrar till mappen och väljer sedan mappen.

   ![Sök efter och välj mapp att övervaka](./media/connectors-create-api-ftp/select-folder.png)  

   Den valda mappen visas i rutan **mapp** .

   ![Vald mapp](./media/connectors-create-api-ftp/selected-folder.png)  

Nu när din Logic app har en utlösare lägger du till de åtgärder som du vill köra när din Logi Kap par söker efter en ny eller redige rad fil. I det här exemplet kan du lägga till en FTP-åtgärd som hämtar det nya eller uppdaterade innehållet.

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>FTP-åtgärd: hämta innehåll

Den här åtgärden hämtar innehållet från en fil på en FTP-server när filen läggs till eller uppdateras. Du kan t. ex. lägga till utlösaren från föregående exempel och en åtgärd som hämtar filens innehåll när filen har lagts till eller redigerats.

Här är ett exempel som visar den här åtgärden: **Hämta innehåll**

1. Under utlösaren eller andra åtgärder väljer du **nytt steg**.

1. I rutan Sök anger du "FTP" som filter. Under listan åtgärder väljer du den här åtgärden: **Hämta fil innehåll – FTP**

   ![Välj FTP-åtgärd](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Om du redan har en anslutning till FTP-servern och kontot går du till nästa steg. Annars anger du nödvändig information för anslutningen och väljer sedan **skapa**.

   ![Skapa anslutning till FTP-Server](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. När åtgärden **Hämta fil innehåll** öppnas klickar du i rutan **fil** så att listan med dynamiskt innehåll visas. Nu kan du välja egenskaper för utdata från föregående steg. I listan med dynamiskt innehåll väljer du egenskapen **fil innehåll** , som innehåller innehållet för den tillagda eller uppdaterade filen.  

   ![Sök efter och välj fil](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   Egenskapen **fil innehåll** visas nu i rutan **fil** .

   ![Vald egenskap för fil innehåll](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Spara din logikapp. Om du vill testa arbets flödet lägger du till en fil i FTP-mappen som din Logic app nu övervakar.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i [kopplingens referens sida](/connectors/ftpconnector/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)