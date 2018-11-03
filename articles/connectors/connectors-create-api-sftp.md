---
title: Ansluta till SFTP-konto – Azure Logic Apps | Microsoft Docs
description: Automatisera uppgifter och processer som övervakar, skapa, hantera, skicka och ta emot filer för en SFTP-server via SSH med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.topic: article
tags: connectors
ms.date: 10/26/2018
ms.openlocfilehash: 3dbe40476757ba93f33d39f71c46bf58302b3570
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979462"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Övervaka, skapa och hantera SFTP-filer med hjälp av Azure Logic Apps

Att automatisera uppgifter som att övervaka, skapa, skicka och ta emot filer på en [SFTP Secure File Transfer Protocol ()](https://www.ssh.com/ssh/sftp/) server, som du kan skapa och automatisera arbetsflöden för dataintegrering med hjälp av Azure Logic Apps och SFTP-anslutningsappen. SFTP är ett nätverksprotokoll som tillhandahåller filåtkomst, filöverföring och filhantering via valfri tillförlitlig dataström. Här följer några exempel på uppgifter du kan automatisera: 

* Övervaka när filer läggs till eller ändras.
* Hämta, skapa, kopiera, uppdatera, lista, och ta bort filer.
* Hämta filinnehåll och metadata.
* Extrahera Arkiv till mappar.

Jämfört med den [SFTP-SSH-anslutningen](../connectors/connectors-sftp-ssh.md), SFTP-anslutningsappen kan läsa eller skriva filer upp till 50 MB i storlek om du inte använder [storlekar för att hantera stora meddelanden](../logic-apps/logic-apps-handle-large-messages.md). För filer upp till 1 GB i storlek, Använd den [SFTP-SSH-anslutningen](../connectors/connectors-sftp-ssh.md). För filer som är större än 1 GB, kan du använda den SFTP-SSH connector plus [storlekar för stora meddelanden](../logic-apps/logic-apps-handle-large-messages.md). 

Du kan använda utlösare som övervakar händelser på din SFTP-server och se utdata som är tillgängliga för andra åtgärder. Du kan använda åtgärder som utför olika uppgifter på din SFTP-server. Du kan också ha andra åtgärder i din logikapp använda utdata från SFTP-åtgärder. Om du regelbundet hämta filer från din SFTP-server, kan du exempelvis skicka e-postaviseringar om filerna och sitt innehåll med hjälp av anslutningsappen Office 365 Outlook eller Outlook.com-anslutning.
Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* Din SFTP-adress och konto autentiseringsuppgifter, som låter din logikapp åtkomst till ditt SFTP-konto. Du använder den [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) -protokollet, du måste också åtkomst till en SSH-privata nyckel och SSH-privata nyckel lösenordet. 

  > [!NOTE]
  > 
  > SFTP-anslutningsappen stöder dessa privata nyckel format: OpenSSH, ssh.com och PuTTY
  > 
  > När du skapar din logikapp när du lägger till SFTP-utlösaren eller åtgärden som du vill kan behöver du ange anslutningsinformation för din SFTP-server. 
  > Om du använder en SSH-privata nyckel, se till att du ***kopia*** nyckeln från din SSH fil för privat nyckel och ***klistra in*** nyckeln till anslutningsinformationen, ***inte manuellt ange eller redigera nyckeln***, vilket kan orsaka anslutning att misslyckas. 
  > Mer information finns i senare steg i den här artikeln.

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

1. Ange informationen som krävs för anslutningen.

   > [!IMPORTANT] 
   >
   > När du anger din privata SSH-nyckeln i den **privata SSH-nyckeln** egenskapen, följer du dessa ytterligare steg, som hjälper dig att göra att du anger värdet för fullständig och korrekt för den här egenskapen. 
   > En ogiltig nyckel gör anslutningen misslyckas.
   
   Men du kan använda valfri textredigerare, är här exempel som visar hur du kopiera och klistra in din nyckel genom att använda Notepad.exe som ett exempel på rätt sätt.
    
   1. Öppna din SSH-privata nyckelfilen i en textredigerare. 
   De här stegen kan du använda anteckningar som exempel.

   1. På anteckningar **redigera** menyn och välj **Markera alla**.

   1. Välj **redigera** > **kopiera**.

   1. Klistra in i SFTP utlösaren eller åtgärden som du har lagt till, den *fullständig* nyckeln du kopierade till den **privata SSH-nyckeln** egenskapen, som har stöd för flera rader. 
   ***Kontrollera att du klistra in*** nyckeln. ***Inte manuellt ange eller redigera nyckeln***.

1. När du är klar väljer du att ange anslutningsinformationen **skapa**.

1. Ange informationen som krävs för din valda utlösare eller åtgärd och fortsätt att utveckla logikappens arbetsflöde.

## <a name="trigger-limits"></a>Utlösaren gränser

SFTP utlöser arbete genom att avsöka SFTP-filsystemet och söker efter alla filer som har ändrats sedan den senaste avsökningen. Vissa verktyg kan du bevara tidsstämpel när filerna som ändras. I dessa fall kan behöva du inaktivera den här funktionen så att utlösaren kan arbeta. Här följer några vanliga inställningar:

| SFTP-klienten | Åtgärd | 
|-------------|--------| 
| Winscp | Gå till **alternativ** > **inställningar** > **överföra** > **redigera**  >  **Bevara tidsstämpel** > **inaktivera** |
| FileZilla | Gå till **överföra** > **bevara tidsstämplar av överförda filer** > **inaktivera** | 
||| 

Om en ny fil upptäcks under en utlösare kan utlösaren söker du efter att den nya filen är komplett och inte delvis skriftliga. En fil kan till exempel ha ändringar pågår när utlösaren kontrollerar filservern. För att undvika att returnera en delvis skriftliga fil, noterar utlösaren tidsstämpel för den fil som har de senaste ändringarna, men inte direkt returnerar filen. Utlösaren returnerar filen bara när en avsökning görs servern igen. Det här beteendet kan ibland orsaka en fördröjning som upp till två gånger utlösarens avsökningsintervall. 

## <a name="examples"></a>Exempel

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP-utlösare: när en fil läggs till eller ändras

Den här utlösaren startar en logikappens arbetsflöde när en fil läggs till eller ändras på en SFTP-server. Du kan till exempel lägga till ett villkor som kontrollerar dess innehåll och hur du hämtar innehållet baserat på om innehållet uppfyller ett angivet villkor. Du kan sedan lägga till en åtgärd som hämtar filens innehåll och placerar innehållet i en mapp på SFTP-server. 

**Enterprise exempel**: du kan använda den här utlösaren för att övervaka en SFTP-mapp för nya filer som representerar kundorder. Du kan sedan använda en SFTP-åtgärd som **hämta filinnehåll** så att du hämta orderns innehåll för vidare bearbetning och lagra den ordningen i en order-databas.

### <a name="sftp-action-get-content"></a>SFTP-åtgärd: hämta innehåll

Den här åtgärden hämtar innehållet från en fil på en SFTP-server. Till exempel kan du lägga till utlösaren från föregående exempel och ett villkor som måste uppfylla dess innehåll. Om villkoret är sant, köra den åtgärd som hämtar innehållet. 

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/sftpconnector/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)