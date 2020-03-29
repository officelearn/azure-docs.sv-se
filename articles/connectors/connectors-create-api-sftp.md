---
title: Ansluta till SFTP-konto
description: Automatisera uppgifter och processer som övervakar, skapar, hanterar, skickar och ta emot filer för en SFTP-server via SSH med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: divswa, klam, logicappspm
ms.topic: article
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: d0da98070fa8da5403677e1a67bda75456c74d80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789281"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Övervaka, skapa och hantera SFTP-filer med hjälp av Azure Logic Apps

> [!IMPORTANT]
> Använd [SFTP-SSH-kontakten](../connectors/connectors-sftp-ssh.md) när SFTP-kontakten är föråldrad. Du kan inte längre välja SFTP-utlösare och åtgärder i Logic App Designer.

Om du vill automatisera uppgifter som övervakar, skapar, skickar och ta emot filer på en [SFTP-server (Secure File Transfer Protocol)](https://www.ssh.com/ssh/sftp/) kan du skapa och automatisera integrationsarbetsflöden med hjälp av Azure Logic Apps och SFTP-anslutningen. SFTP är ett nätverksprotokoll som ger filåtkomst, filöverföring och filhantering via valfri betrodd dataström. Här är några exempel på uppgifter som du kan automatisera:

* Övervaka när filer läggs till eller ändras.
* Hämta, skapa, kopiera, uppdatera, lista och ta bort filer.
* Hämta filinnehåll och metadata.
* Extrahera arkiv till mappar.

Du kan använda utlösare som övervakar händelser på SFTP-servern och gör utdata tillgängliga för andra åtgärder. Du kan använda åtgärder som utför olika uppgifter på SFTP-servern. Du kan också låta andra åtgärder i logikappen använda utdata från SFTP-åtgärder. Om du till exempel regelbundet hämtar filer från SFTP-servern kan du skicka e-postaviseringar om dessa filer och deras innehåll med hjälp av Office 365 Outlook-anslutningen eller Outlook.com-anslutningsappen. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Begränsningar

SFTP-anslutningen hanterar endast filer som är *50 MB eller mindre* och stöder inte [meddelandesegmentering](../logic-apps/logic-apps-handle-large-messages.md). För större filer använder du [SFTP-SSH-anslutningen](../connectors/connectors-sftp-ssh.md). För skillnader mellan SFTP-kontakten och SFTP-SSH-anslutningen läser du [Jämför SFTP-SSH jämfört med SFTP](../connectors/connectors-sftp-ssh.md#comparison) i SFTP-SSH-artikeln.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Din SFTP-serveradress och kontouppgifter, som gör att logikappen kan komma åt ditt SFTP-konto. Om du vill använda [SSH-protokollet (Secure Shell)](https://www.ssh.com/ssh/protocol/) behöver du också åtkomst till en privat SSH-nyckel och SSH-lösenordet för privata nyckel.

  > [!NOTE]
  >
  > SFTP-anslutningen stöder dessa privata nyckelformat: OpenSSH, ssh.com och PuTTY
  >
  > När du skapar logikappen måste du ange anslutningsinformation för SFTP-servern när du har lagt till den SFTP-utlösare eller åtgärd du vill använda. 
  > Om du använder en privat SSH-nyckel kontrollerar du att du ***kopierar*** nyckeln från den privata SSH-nyckelfilen och ***klistrar in*** nyckeln i ***anslutningsinformationen, Ange eller redigera inte nyckeln manuellt***, vilket kan leda till att anslutningen misslyckas. 
  > Mer information finns i de senare stegen i den här artikeln.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt SFTP-konto. Om du vill börja med en SFTP-utlösare [skapar du en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en SFTP-åtgärd startar du **Recurrence** logikappen med en annan utlösare, till exempel upprepningsutlösaren.

## <a name="how-sftp-triggers-work"></a>Så här fungerar SFTP-utlösare

SFTP utlöser arbete genom att avsöka SFTP-filsystemet och leta efter alla filer som har ändrats sedan den senaste mätningen. Med vissa verktyg kan du bevara tidsstämpeln när filerna ändras. I dessa fall måste du inaktivera den här funktionen så att utlösaren kan fungera. Här är några vanliga inställningar:

| SFTP-klient | Åtgärd |
|-------------|--------|
| Winscp | Gå > till **Alternativinställningar** > **Preferences** > **Överför** > **redigera** > bevara**tidsstämpel****Inaktivera** |
| Filezilla | Gå till **Överför** > **bevara tidsstämplar för överförda filer** > **Inaktivera** |
|||

När en utlösare hittar en ny fil kontrollerar utlösaren att den nya filen är klar och inte delvis skriven. En fil kan till exempel ha pågående ändringar när utlösaren kontrollerar filservern. Om du vill undvika att returnera en delvis skriven fil noterar utlösaren tidsstämpeln för filen som har de senaste ändringarna, men returnerar inte filen omedelbart. Utlösaren returnerar filen endast när servern avsöks igen. Ibland kan detta orsaka en fördröjning som är upp till dubbelt så många som utlösarens avsökningsintervall.

## <a name="connect-to-sftp"></a>Anslut till SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. För tomma logikappar anger du "sftp" som filter i sökrutan. Markera den utlösare du vill använda under listan utlösare.

   ELLER

   För befintliga logikappar väljer du **Nytt steg**under det sista steget där du vill lägga till en åtgärd . I sökrutan anger du "sftp" som filter. Välj den åtgärd du vill använda under åtgärdslistan.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. Ange nödvändiga uppgifter för din anslutning.

   > [!IMPORTANT]
   >
   > När du anger din SSH-privata nyckel i egenskapen **SSH private key** följer du dessa ytterligare steg, som hjälper dig att ange det fullständiga och korrekta värdet för den här egenskapen. 
   > En ogiltig nyckel gör att anslutningen misslyckas.

   Även om du kan använda vilken textredigerare som helst är det här exempelstegen som visar hur du kopierar och klistrar in nyckeln korrekt med hjälp av Notepad.exe som exempel.

   1. Öppna filen SSH-privat nyckel i en textredigerare. I de här stegen används Anteckningar som exempel.

   1. Välj Markera alla **på** Redigera antecknings- **meny.**

   1. Välj **Redigera** > **kopia**.

   1. I SFTP-utlösaren eller åtgärden som du har lagt till klistrar du in *den fullständiga* nyckeln som du kopierade till egenskapen **SSH private key,** som stöder flera rader. ***Se till att du klistrar in*** nyckeln. ***Ange eller redigera inte nyckeln manuellt***.

1. När du är klar med att ange anslutningsinformationen väljer du **Skapa**.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa logikappens arbetsflöde.

## <a name="examples"></a>Exempel

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP-utlösare: När en fil läggs till eller ändras

Den här utlösaren startar ett logikapparbetsflöde när en fil läggs till eller ändras på en SFTP-server. Du kan till exempel lägga till ett villkor som kontrollerar filens innehåll och hämtar innehållet baserat på om innehållet uppfyller ett angivet villkor. Du kan sedan lägga till en åtgärd som hämtar filens innehåll och placera innehållet i en mapp på SFTP-servern.

**Företagsexempel**: Du kan använda den här utlösaren för att övervaka en SFTP-mapp för nya filer som representerar kundorder. Du kan sedan använda en SFTP-åtgärd som **Hämta filinnehåll** så att du får orderns innehåll för vidare bearbetning och lagring av ordern i en orderdatabas.

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>SFTP-åtgärd: Hämta innehåll

Den här åtgärden hämtar innehållet från en fil på en SFTP-server. Du kan till exempel lägga till utlösaren från föregående exempel och ett villkor som filens innehåll måste uppfylla. Om villkoret är sant kan åtgärden som hämtar innehållet köras.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI-beskrivning (tidigare Swagger) finns i kopplingens [referenssida](/connectors/sftpconnector/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
