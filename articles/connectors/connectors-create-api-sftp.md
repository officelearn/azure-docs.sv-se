---
title: Ansluta till SFTP-konto – Azure Logic Apps
description: Automatisera uppgifter och processer som övervakar, skapa, hantera, skicka och ta emot filer för en SFTP-server via SSH med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/18/2019
tags: connectors
ms.openlocfilehash: 5b80339e90947b7cc45b1edb5df00652a13291ac
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293463"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Övervaka, skapa och hantera SFTP-filer med hjälp av Azure Logic Apps

Att automatisera uppgifter som att övervaka, skapa, skicka och ta emot filer på en [SFTP Secure File Transfer Protocol ()](https://www.ssh.com/ssh/sftp/) server, som du kan skapa och automatisera arbetsflöden för dataintegrering med hjälp av Azure Logic Apps och SFTP-anslutningsappen. SFTP är ett nätverksprotokoll som tillhandahåller filåtkomst, filöverföring och filhantering via valfri tillförlitlig dataström. Här följer några exempel på uppgifter du kan automatisera:

* Övervaka när filer läggs till eller ändras.
* Hämta, skapa, kopiera, uppdatera, lista, och ta bort filer.
* Hämta filinnehåll och metadata.
* Extrahera Arkiv till mappar.

Du kan använda utlösare som övervakar händelser på din SFTP-server och se utdata som är tillgängliga för andra åtgärder. Du kan använda åtgärder som utför olika uppgifter på din SFTP-server. Du kan också ha andra åtgärder i din logikapp använda utdata från SFTP-åtgärder. Om du regelbundet hämta filer från din SFTP-server, kan du exempelvis skicka e-postaviseringar om filerna och sitt innehåll med hjälp av anslutningsappen Office 365 Outlook eller Outlook.com-anslutning. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limits

SFTP-anslutningsappen hanterar endast filer som är *50 MB eller mindre* och stöder inte [meddelande storlekar](../logic-apps/logic-apps-handle-large-messages.md). På större filer kan använda den [SFTP-SSH-anslutningen](../connectors/connectors-sftp-ssh.md). Skillnaderna mellan SFTP-anslutningsappen och den SFTP-SSH-anslutningen, granska [jämför SFTP-SSH jämfört med SFTP](../connectors/connectors-sftp-ssh.md#comparison) i SFTP-SSH-artikeln.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Din SFTP-adress och konto autentiseringsuppgifter, som låter din logikapp åtkomst till ditt SFTP-konto. Du använder den [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) -protokollet, du måste också åtkomst till en SSH-privata nyckel och SSH-privata nyckel lösenordet.

  > [!NOTE]
  >
  > SFTP-anslutningsappen stöder formaten för privat nyckel: OpenSSH och ssh.com PuTTY
  >
  > När du skapar din logikapp när du lägger till SFTP-utlösaren eller åtgärden som du vill kan behöver du ange anslutningsinformation för din SFTP-server. 
  > Om du använder en SSH-privata nyckel, se till att du ***kopia*** nyckeln från din SSH fil för privat nyckel och ***klistra in*** nyckeln till anslutningsinformationen, ***inte manuellt ange eller redigera nyckeln***, vilket kan orsaka anslutning att misslyckas. 
  > Mer information finns i senare steg i den här artikeln.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt SFTP-konto. Du kommer igång med en SFTP-utlösare [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en SFTP-åtgärd, starta din logikapp med en annan utlösare, till exempel, **upprepning** utlösaren.

## <a name="how-sftp-triggers-work"></a>Hur SFTP utlöser arbete

SFTP utlöser arbete genom att avsöka SFTP-filsystemet och söker efter alla filer som har ändrats sedan den senaste avsökningen. Vissa verktyg kan du bevara tidsstämpel när filerna som ändras. I dessa fall kan behöva du inaktivera den här funktionen så att utlösaren kan arbeta. Här följer några vanliga inställningar:

| SFTP-klienten | Åtgärd |
|-------------|--------|
| Winscp | Gå till **alternativ** > **inställningar** > **överföra** > **redigera**  >  **Bevara tidsstämpel** > **inaktivera** |
| FileZilla | Gå till **överföra** > **bevara tidsstämplar av överförda filer** > **inaktivera** |
|||

Om en ny fil upptäcks under en utlösare kan utlösaren söker du efter att den nya filen är komplett och inte delvis skriftliga. En fil kan till exempel ha ändringar pågår när utlösaren kontrollerar filservern. För att undvika att returnera en delvis skriftliga fil, noterar utlösaren tidsstämpel för den fil som har de senaste ändringarna, men inte direkt returnerar filen. Utlösaren returnerar filen bara när en avsökning görs servern igen. Det här beteendet kan ibland orsaka en fördröjning som upp till två gånger utlösarens avsökningsintervall.

## <a name="connect-to-sftp"></a>Ansluta till SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. För tom logic apps i sökrutan anger du ”sftp” som filter. Välj utlösaren som du vill under listan över utlösare.

   ELLER

   För befintliga logikappar under det sista steget där du vill lägga till en åtgärd, Välj **nytt steg**. I sökrutan anger du ”sftp” som filter. Välj vilken åtgärd du önska under åtgärder.

   Om du vill lägga till en åtgärd mellan stegen, flyttar du pekaren över pilen mellan stegen. Välj plustecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. Ange informationen som krävs för anslutningen.

   > [!IMPORTANT]
   >
   > När du anger din privata SSH-nyckeln i den **privata SSH-nyckeln** egenskapen, följer du dessa ytterligare steg, som hjälper dig att göra att du anger värdet för fullständig och korrekt för den här egenskapen. 
   > En ogiltig nyckel gör anslutningen misslyckas.

   Men du kan använda valfri textredigerare, är här exempel som visar hur du kopiera och klistra in din nyckel genom att använda Notepad.exe som ett exempel på rätt sätt.

   1. Öppna din SSH-privata nyckelfilen i en textredigerare. De här stegen kan du använda anteckningar som exempel.

   1. På anteckningar **redigera** menyn och välj **Markera alla**.

   1. Välj **redigera** > **kopiera**.

   1. Klistra in i SFTP utlösaren eller åtgärden som du har lagt till, den *fullständig* nyckeln du kopierade till den **privata SSH-nyckeln** egenskapen, som har stöd för flera rader. ***Kontrollera att du klistra in*** nyckeln. ***Inte manuellt ange eller redigera nyckeln***.

1. När du är klar väljer du att ange anslutningsinformationen **skapa**.

1. Ange informationen som krävs för din valda utlösare eller åtgärd och fortsätt att utveckla logikappens arbetsflöde.

## <a name="examples"></a>Exempel

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP-utlösare: När en fil läggs till eller ändras

Den här utlösaren startar en logikappens arbetsflöde när en fil läggs till eller ändras på en SFTP-server. Du kan till exempel lägga till ett villkor som kontrollerar dess innehåll och hur du hämtar innehållet baserat på om innehållet uppfyller ett angivet villkor. Du kan sedan lägga till en åtgärd som hämtar filens innehåll och placerar innehållet i en mapp på SFTP-server.

**Enterprise exempel**: Du kan använda den här utlösaren för att övervaka en SFTP-mapp för nya filer som representerar kundorder. Du kan sedan använda en SFTP-åtgärd som **hämta filinnehåll** så att du hämta orderns innehåll för vidare bearbetning och lagra den ordningen i en order-databas.

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>SFTP-åtgärd: Hämta innehåll

Den här åtgärden hämtar innehållet från en fil på en SFTP-server. Till exempel kan du lägga till utlösaren från föregående exempel och ett villkor som måste uppfylla dess innehåll. Om villkoret är sant, köra den åtgärd som hämtar innehållet.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/sftpconnector/).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
