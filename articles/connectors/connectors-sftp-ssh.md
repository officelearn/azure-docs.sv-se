---
title: Ansluta till SFTP-server med SSH
description: Automatisera uppgifter som övervakar, skapar, hanterar, skickar och ta emot filer för en SFTP-server med hjälp av SSH- och Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, klam, logicappspm
ms.topic: article
ms.date: 03/7/2020
tags: connectors
ms.openlocfilehash: d4ab7425c967d3a176c0a576d0be38ece1701b8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128406"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Övervaka, skapa och hantera SFTP-filer med hjälp av SSH- och Azure Logic Apps

Om du vill automatisera uppgifter som övervakar, skapar, skickar och ta emot filer på en [SFTP-server (Secure File Transfer Protocol)](https://www.ssh.com/ssh/sftp/) med hjälp av [SSH-protokollet (Secure Shell)](https://www.ssh.com/ssh/protocol/) kan du skapa och automatisera integrationsarbetsflöden med hjälp av Azure Logic Apps och SFTP-SSH-anslutningen. SFTP är ett nätverksprotokoll som ger filåtkomst, filöverföring och filhantering via valfri betrodd dataström. Här är några exempel på uppgifter som du kan automatisera:

* Övervaka när filer läggs till eller ändras.
* Hämta, skapa, kopiera, byta namn på, uppdatera, lista och ta bort filer.
* Skapa mappar.
* Hämta filinnehåll och metadata.
* Extrahera arkiv till mappar.

Du kan använda utlösare som övervakar händelser på SFTP-servern och gör utdata tillgängliga för andra åtgärder. Du kan använda åtgärder som utför olika uppgifter på SFTP-servern. Du kan också låta andra åtgärder i logikappen använda utdata från SFTP-åtgärder. Om du till exempel regelbundet hämtar filer från SFTP-servern kan du skicka e-postaviseringar om dessa filer och deras innehåll med hjälp av Office 365 Outlook-anslutningen eller Outlook.com-anslutningsappen. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Om du vill skilja mellan SFTP-SSH-anslutningen och SFTP-anslutningen läser du avsnittet [Jämför SFTP-SSH jämfört med SFTP](#comparison) senare i det här avsnittet.

## <a name="limits"></a>Begränsningar

* SFTP-SSH-åtgärder som stöder [segmentering](../logic-apps/logic-apps-handle-large-messages.md) kan hantera filer upp till 1 GB, medan SFTP-SSH-åtgärder som inte stöder segmentering kan hantera filer upp till 50 MB. Även om standardsegmentstorleken är 15 MB kan den här storleken ändras dynamiskt, från 5 MB och gradvis öka till den maximala 50 MB, baserat på faktorer som nätverksfördröjning, serversvarstid och så vidare.

  > [!NOTE]
  > För logikappar i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandegränserna](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

  Du kan åsidosätta det här adaptiva beteendet när du [anger en konstant segmentstorlek](#change-chunk-size) som ska användas i stället. Den här storleken kan variera från 5 MB till 50 MB. Anta till exempel att du har en 45 MB-fil och ett nätverk som kan stödja filstorleken utan svarstid. Adaptiv chunking resulterar i flera samtal, snarare att ett samtal. Om du vill minska antalet samtal kan du prova att ange en segmentstorlek på 50 MB. I olika scenario, om din logikapp är time out, till exempel när du använder 15 MB segment, kan du prova att minska storleken till 5 MB.

  Segmentstorlek är associerad med en anslutning, vilket innebär att du kan använda samma anslutning för åtgärder som stöder segmentering och sedan för åtgärder som inte stöder segmentering. I det här fallet varierar segmentstorleken för åtgärder som inte stöder segmentering från 5 MB till 50 MB. Den här tabellen visar vilka SFTP-SSH-åtgärder som stöder segmentering:

  | Åtgärd | Stöd för segmentering | Stöd för åsidosättning av segmentstorlek |
  |--------|------------------|-----------------------------|
  | **Kopiera fil** | Inga | Inte tillämpligt |
  | **Skapa fil** | Ja | Ja |
  | **Skapa mapp** | Inte tillämpligt | Inte tillämpligt |
  | **Ta bort panel** | Inte tillämpligt | Inte tillämpligt |
  | **Extrahera arkiv till mapp** | Inte tillämpligt | Inte tillämpligt |
  | **Hämta filinnehåll** | Ja | Ja |
  | **Hämta filinnehåll med sökvägen** | Ja | Ja |
  | **Hämta metadata för filer** | Inte tillämpligt | Inte tillämpligt |
  | **Hämta filmetadata med sökvägen** | Inte tillämpligt | Inte tillämpligt |
  | **Lista filer i mappen** | Inte tillämpligt | Inte tillämpligt |
  | **Byta namn på fil** | Inte tillämpligt | Inte tillämpligt |
  | **Uppdatera fil** | Inga | Inte tillämpligt |
  ||||

* SFTP-SSH-utlösare stöder inte meddelandesegmentering. När du begär filinnehåll väljer utlösare bara filer som är 15 MB eller mindre. Om du vill hämta filer som är större än 15 MB följer du det här mönstret i stället:

  1. Använd en SFTP-SSH-utlösare som bara returnerar filegenskaper, till exempel **När en fil läggs till eller ändras (endast egenskaper)**.

  1. Följ utlösaren med åtgärden SFTP-SSH **Hämta filinnehåll,** som läser hela filen och implicit använder meddelandesegmentering.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Jämför SFTP-SSH jämfört med SFTP

Här är andra viktiga skillnader mellan SFTP-SSH-kontakten och SFTP-kontakten där SFTP-SSH-anslutningen har följande funktioner:

* Använder [SSH.NET-biblioteket](https://github.com/sshnet/SSH.NET), som är ett SSH-bibliotek (Secure Shell) med öppen källkod som stöder .NET.

* Tillhandahåller åtgärden **Skapa mapp** som skapar en mapp vid den angivna sökvägen på SFTP-servern.

* Tillhandahåller åtgärden **Byt namn på fil** som byter namn på en fil på SFTP-servern.

* Cachelagrar anslutningen till SFTP-servern *i upp till 1 timme,* vilket förbättrar prestanda och minskar antalet försök att ansluta till servern. Om du vill ange varaktigheten för det här cachelagringsbeteendet redigerar du egenskapen [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) i SSH-konfigurationen på SFTP-servern.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Din SFTP-serveradress och kontouppgifter, som gör att logikappen kan komma åt ditt SFTP-konto. Du behöver också tillgång till en SSH privat nyckel och SSH privat nyckel lösenord. Om du vill använda segmentering när du laddar upp stora filer behöver du både läs- och skrivbehörighet för rotmappen på SFTP-servern. Annars får du ett "401 Obehörigt" fel.

  > [!IMPORTANT]
  >
  > SFTP-SSH-anslutningen stöder *endast* dessa privata nyckelformat, algoritmer och fingeravtryck:
  >
  > * **Privata nyckelformat:** RSA (Rivest Shamir Adleman) och DSA (Digital Signature Algorithm) i både OpenSSH- och ssh.com-format. Om din privata nyckel är i PuTTY-filformat (.ppk) konverterar du först [nyckeln till openssh-filformatet (.pem).](#convert-to-openssh)
  >
  > * **Krypteringsalgoritmer**: DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC och AES-256-CBC
  >
  > * **Fingeravtryck**: MD5
  >
  > När du har lagt till SFTP-SSH-utlösaren eller åtgärden som du vill i logikappen måste du ange anslutningsinformation för SFTP-servern. När du anger den privata SSH-nyckeln för den här anslutningen ***ska du inte ange eller redigera nyckeln manuellt,*** vilket kan leda till att anslutningen misslyckas. Se i stället till att du ***kopierar nyckeln*** från den privata SSH-filen och ***klistrar in*** nyckeln i anslutningsinformationen. 
  > Mer information finns i avsnittet [Anslut till SFTP med SSH](#connect) senare i den här artikeln.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt SFTP-konto. Om du vill börja med en SFTP-SSH-utlösare [skapar du en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en SFTP-SSH-åtgärd startar du **Recurrence** logikappen med en annan utlösare, till exempel upprepningsutlösaren.

## <a name="how-sftp-ssh-triggers-work"></a>Så här fungerar SFTP-SSH-utlösare

SFTP-SSH utlöser arbete genom att avsöka SFTP-filsystemet och leta efter alla filer som har ändrats sedan den senaste omröstningen. Med vissa verktyg kan du bevara tidsstämpeln när filerna ändras. I dessa fall måste du inaktivera den här funktionen så att utlösaren kan fungera. Här är några vanliga inställningar:

| SFTP-klient | Åtgärd |
|-------------|--------|
| Winscp | Gå > till **Alternativinställningar** > **Preferences** > **Överför** > **redigera** > bevara**tidsstämpel****Inaktivera** |
| Filezilla | Gå till **Överför** > **bevara tidsstämplar för överförda filer** > **Inaktivera** |
|||

När en utlösare hittar en ny fil kontrollerar utlösaren att den nya filen är klar och inte delvis skriven. En fil kan till exempel ha pågående ändringar när utlösaren kontrollerar filservern. Om du vill undvika att returnera en delvis skriven fil noterar utlösaren tidsstämpeln för filen som har de senaste ändringarna, men returnerar inte filen omedelbart. Utlösaren returnerar filen endast när servern avsöks igen. Ibland kan detta orsaka en fördröjning som är upp till dubbelt så många som utlösarens avsökningsintervall.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Konvertera PuTTY-baserad nyckel till OpenSSH

Om din privata nyckel är i PuTTY-format, som använder filnamnstillägget .ppk (PuTTY Private Key), konverterar du först nyckeln till OpenSSH-formatet, som använder filnamnstillägget .pem (Privacy Enhanced Mail).

### <a name="unix-based-os"></a>Unix-baserat operativsystem

1. Om PuTTY-verktygen inte redan är installerade på datorn gör du det nu, till exempel:

   `sudo apt-get install -y putty`

1. Kör det här kommandot, som skapar en fil som du kan använda med SFTP-SSH-kopplingen:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Ett exempel:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows OS

1. Om du inte redan har gjort det [hämtar du det senaste PuTTY Generator -verktyget (puttygen.exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)och startar sedan verktyget.

1. På den här skärmen väljer du **Läs in**.

   ![Välj "Ladda"](./media/connectors-sftp-ssh/puttygen-load.png)

1. Bläddra till din privata nyckelfil i PuTTY-format och välj **Öppna**.

1. Välj **Exportera OpenSSH-tangenten**på **menyn Konverteringar** .

   ![Välj "Exportera OpenSSH-tangenten"](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Spara den privata nyckelfilen med filnamnstillägget. `.pem`

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Anslut till SFTP med SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. För tomma logikappar anger du `sftp ssh` som filter i sökrutan. Markera den utlösare du vill använda under listan utlösare.

   ELLER

   För befintliga logikappar väljer du **Nytt steg**under det sista steget där du vill lägga till en åtgärd . Ange som filter `sftp ssh` i sökrutan. Välj den åtgärd du vill använda under åtgärdslistan.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Markera plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. Ange nödvändiga uppgifter för din anslutning.

   > [!IMPORTANT]
   >
   > När du anger din SSH-privata nyckel i egenskapen **SSH private key** följer du dessa ytterligare steg, som hjälper dig att ange det fullständiga och korrekta värdet för den här egenskapen. En ogiltig nyckel gör att anslutningen misslyckas.

   Även om du kan använda vilken textredigerare som helst är det här exempelstegen som visar hur du kopierar och klistrar in nyckeln korrekt med hjälp av Notepad.exe som exempel.

   1. Öppna filen SSH-privat nyckel i en textredigerare. I de här stegen används Anteckningar som exempel.

   1. Välj Markera alla **på** Redigera antecknings- **meny.**

   1. Välj **Redigera** > **kopia**.

   1. I SFTP-SSH-utlösaren eller åtgärden du har lagt till klistrar du in *den fullständiga* nyckeln som du kopierade till egenskapen **SSH private key,** som stöder flera rader.  ***Se till att du klistrar in*** nyckeln. ***Ange eller redigera inte nyckeln manuellt***.

1. När du är klar med att ange anslutningsinformationen väljer du **Skapa**.

1. Ange nu nödvändig information för den valda utlösaren eller åtgärden och fortsätt att bygga logikappens arbetsflöde.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Åsidosätt segmentstorlek

Om du vill åsidosätta standarduppadaptivt beteende som segmentering använder kan du ange en konstant segmentstorlek från 5 MB till 50 MB.

1. I åtgärdens övre högra hörn väljer du ellipsknappen (**...**) och väljer sedan **Inställningar**.

   ![Öppna SFTP-SSH-inställningar](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. Ange **Content Transfer**ett heltalsvärde från **Chunk size** `5` till `50`exempel: 

   ![Ange segmentstorlek som ska användas i stället](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. När du är klar väljer du **Klar**.

## <a name="examples"></a>Exempel

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - SSH-utlösare: När en fil läggs till eller ändras

Den här utlösaren startar ett logikapparbetsflöde när en fil läggs till eller ändras på en SFTP-server. Du kan till exempel lägga till ett villkor som kontrollerar filens innehåll och hämtar innehållet baserat på om innehållet uppfyller ett angivet villkor. Du kan sedan lägga till en åtgärd som hämtar filens innehåll och placera innehållet i en mapp på SFTP-servern.

**Företagsexempel**: Du kan använda den här utlösaren för att övervaka en SFTP-mapp för nya filer som representerar kundorder. Du kan sedan använda en SFTP-åtgärd som **Hämta filinnehåll** så att du får orderns innehåll för vidare bearbetning och lagring av ordern i en orderdatabas.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP - SSH-åtgärd: Hämta innehåll med sökvägen

Den här åtgärden hämtar innehållet från en fil på en SFTP-server. Du kan till exempel lägga till utlösaren från föregående exempel och ett villkor som filens innehåll måste uppfylla. Om villkoret är sant kan åtgärden som hämtar innehållet köras.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här kopplingen, till exempel utlösare, åtgärder och begränsningar enligt beskrivningen i kopplingens Swagger-fil, finns på [kopplingens referenssida](https://docs.microsoft.com/connectors/sftpwithssh/).

> [!NOTE]
> För logikappar i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandegränserna](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
