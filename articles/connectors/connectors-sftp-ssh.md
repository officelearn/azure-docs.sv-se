---
title: Ansluta till SFTP-konto från Azure Logic Apps | Microsoft Docs
description: Automatisera uppgifter och arbetsflöden som övervakar, skapa, hantera, skicka och ta emot filer för en SFTP-server med Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
tags: connectors
ms.date: 09/24/2018
ms.openlocfilehash: cfee4f06479d2504b88f4a5d5a0a2417154e3b03
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47065118"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps-and-sftp-ssh-connector"></a>Övervaka, skapa och hantera SFTP-filer med Azure Logic Apps och SFTP-SSH-anslutningen

Med Azure Logic Apps och den SFTP-SSH-anslutningen kan du kan skapa automatiserade uppgifter och arbetsflöden som övervakar, skapa, skicka och ta emot filerna med ditt konto på en [SFTP](https://www.ssh.com/ssh/sftp/) server, tillsammans med andra åtgärder, till exempel:

* Övervaka när filer läggs till eller ändras.
* Hämta, skapa, kopiera, byta namn på, uppdatera, lista, och ta bort filer.
* Skapa mapp.
* Hämta filinnehåll och metadata.
* Extrahera Arkiv till mappar.

Du kan använda utlösare som få svar från din SFTP-server och se utdata som är tillgängliga för andra åtgärder. Du kan använda åtgärder i dina logic apps för att utföra uppgifter med filer på din SFTP-server. Du kan också ha andra åtgärder som använder utdata från SFTP-åtgärder. Till exempel om du regelbundet hämta filer från din SFTP-server, kan du skicka e-postmeddelande om dessa filer och sitt innehåll med hjälp av anslutningsappen Office 365 Outlook eller Outlook.com-anslutning.
Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="sftp-ssh-versus-sftp"></a>SFTP-SSH jämfört med SFTP

Här följer några viktiga skillnader mellan den SFTP-SSH-anslutningen och [SFTP](../connectors/connectors-create-api-sftp.md) connector. Den SFTP-SSH-anslutningen innehåller följande funktioner:

* Använder den <a href="https://github.com/sshnet/SSH.NET" target="_blank"> **SSH.NET** </a> biblioteket, vilket är ett bibliotek med öppen källkod SSH (Secure Shell) för .NET.

* Har stöd för stora filer upp till **1 GB**. Anslutningen kan läsa eller skriva filer som är upp till 1 GB i storlek.

* Innehåller den **skapa mapp** som skapar en mapp på den angivna sökvägen på SFTP-server.

* Innehåller den **Byt namn på filen** åtgärden, byter namn på en fil på SFTP-servern.

* Cachelagrar anslutningen till SFTP-servern, vilket förbättrar prestanda och minskar antalet anslutningsförsök på servern. 

  Du kan styra den varaktighet som används för att cachelagra anslutningen genom att ställa in den <a href="http://man.openbsd.org/sshd_config#ClientAliveInterval" target="_blank"> **ClientAliveInterval** </a> egenskapen på din SFTP-server. 

## <a name="how-trigger-polling-works"></a>Hur utlösa avsökningen fungerar

SFTP-SSH utlösare fungerar genom att avsöka SFTP-filsystemet och söker efter alla filer som har ändrats sedan den senaste avsökningen. Vissa verktyg kan du bevara tidsstämpel när filerna som ändrar, så i dessa fall kan du behöva inaktivera den här funktionen för utlösaren ska fungera. Här följer några vanliga inställningar:

| SFTP-klienten | Åtgärd | 
|-------------|--------| 
| Winscp | Alternativ → inställningar... → Överföring → redigera... Inaktivera → Preserve tidsstämpel → |
| FileZilla | Inaktivera överföring → Preserve tidsstämplar av överförda filer → | 
||| 

Om en ny fil upptäcks under en utlösare kan utlösaren söker du efter att den nya filen är komplett och inte delvis skriftliga. En fil kan till exempel ha ändringar pågår när utlösaren kontrollerar filservern. För att undvika att returnera en delvis skriftliga fil, noterar utlösaren tidsstämpel för den fil som har de senaste ändringarna, men inte direkt returnerar filen. Utlösaren returnerar filen bara när en avsökning görs servern igen. Det här beteendet kan ibland orsaka en fördröjning som upp till två gånger utlösarens avsökningsintervall. 

När du begär filinnehåll utlösaren inte att hämta filer som är större än 50 MB. För att hämta upp filer som är större än 50 MB, så det här mönstret:

* Använda en utlösare som returnerar filegenskaper, till exempel **när en fil läggs till eller ändras (enbart egenskaper)**. 
* Följ utlösare med en åtgärd som läser den fullständiga filen, till exempel **hämta filinnehåll med hjälp av sökvägen**.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* Din SFTP värd postadressen och kontonamnet autentiseringsuppgifter, vilka tillåter din logikapp för att skapa en anslutning och få åtkomst till ditt SFTP-konto.

  Kopiera och klistra in hela innehållet för den privata SSH-nyckeln i den **privata SSH-nyckeln** egenskapen genom att följa multiline-format. 
  Här är exempel som visar hur du ger den privata SSH-nyckeln med hjälp av Notepad.exe:
    
  1. Öppna den SSH-privata nyckelfilen i Notepad.exe
  2. På den **redigera** menyn och välj **Markera alla**.
  3. Välj **redigera** > **kopiera**.
  4. När du skapar anslutningen, i den **privata SSH-nyckeln** egenskapen, klistra in nyckeln. Manuellt inte redigera den **privata SSH-nyckeln** egenskapen.

     Anslutningen används SSH.NET-biblioteket, som stöder dessa SSH privat nyckel format och endast MD5 fingeravtrycksläsare:

     * RSA 
     * DSA

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt SFTP-konto. Du kommer igång med en SFTP-utlösare [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en SFTP-åtgärd, starta din logikapp med en annan utlösare, till exempel, **upprepning** utlösaren.

## <a name="connect-to-sftp"></a>Ansluta till SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. För tom logic apps i sökrutan anger du ”sftp” som filter. Välj utlösaren som du vill under listan över utlösare. 

   ELLER

   För befintliga logikappar under det sista steget där du vill lägga till en åtgärd, Välj **nytt steg**. 
   I sökrutan anger du ”sftp” som filter. 
   Välj vilken åtgärd du önska under åtgärder.

   Om du vill lägga till en åtgärd mellan stegen, flyttar du pekaren över pilen mellan stegen. 
   Välj plustecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.

1. Ange informationen som krävs för anslutningen och välj sedan **skapa**.

1. Ange informationen som krävs för din valda utlösare eller åtgärd och fortsätt att utveckla logikappens arbetsflöde.

## <a name="examples"></a>Exempel

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP-utlösare: när en fil läggs till eller ändras

Den här utlösaren startar en logikapparbetsflöde när utlösaren identifierar när en fil läggs till eller ändras på en SFTP-server. Till exempel kan du lägga till ett villkor som kontrollerar dess innehåll och beslutar om att hämta innehållet, baserat på om innehållet uppfyller ett angivet villkor. Slutligen kan du lägga till en åtgärd som hämtar filens innehåll och placera innehållet i en mapp på SFTP-server. 

**Enterprise exempel**: du kan använda den här utlösaren för att övervaka en SFTP-mapp för nya filer som representerar kundorder. Du kan sedan använda en SFTP-åtgärd som **hämta filinnehåll**, så du kan hämta den ordning innehåll för vidare bearbetning och lagra den ordningen i en order-databas.

### <a name="sftp-action-get-content"></a>SFTP-åtgärd: hämta innehåll

Den här åtgärden hämtar innehållet från en fil på en SFTP-server. Till exempel kan du lägga till utlösaren från föregående exempel och ett villkor som måste uppfylla dess innehåll. Om villkoret är sant, köra den åtgärd som hämtar innehållet. 

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/sftpconnector/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)