---
title: Ansluta till SFTP-servern med SSH
description: Automatisera aktiviteter som övervakar, skapar, hanterar, skickar och tar emot filer för en SFTP-server med hjälp av SSH och Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, klam, logicappspm
ms.topic: article
ms.date: 06/18/2019
tags: connectors
ms.openlocfilehash: 3370eea8909f30563babcf2a84f727ba51f67e29
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77647643"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Övervaka, skapa och hantera SFTP-filer med hjälp av SSH och Azure Logic Apps

Om du vill automatisera aktiviteter som övervakar, skapar, skickar och tar emot filer på en [säker File Transfer Protocol-server (SFTP)](https://www.ssh.com/ssh/sftp/) med hjälp av SSH-protokollet [(Secure Shell)](https://www.ssh.com/ssh/protocol/) kan du skapa och automatisera integrerings arbets flöden med hjälp av Azure Logic Apps och SFTP-SSH-anslutaren. SFTP är ett nätverksprotokoll som ger filåtkomst, filöverföring och filhantering via valfri betrodd dataström. Här följer några exempel på uppgifter som du kan automatisera:

* Övervaka när filer läggs till eller ändras.
* Hämta, skapa, kopiera, byta namn på, uppdatera, lista och ta bort filer.
* Skapa mappar.
* Hämta fil innehåll och metadata.
* Extrahera arkiv till mappar.

Du kan använda utlösare som övervakar händelser på din SFTP-server och göra utdata tillgängliga för andra åtgärder. Du kan använda åtgärder som utför olika uppgifter på din SFTP-server. Du kan också använda andra åtgärder i din Logic-app för att använda utdata från SFTP-åtgärder. Om du till exempel regelbundet hämtar filer från din SFTP-server kan du skicka e-postaviseringar om filerna och deras innehåll med hjälp av Office 365 Outlook Connector eller Outlook.com Connector. Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Mer skillnader mellan SFTP-SSH-anslutningsprogrammet och SFTP-anslutningen finns i avsnittet [jämföra SFTP – SSH och SFTP](#comparison) senare i det här avsnittet.

## <a name="limits"></a>Begränsningar

* Som standard kan SFTP och SSH-åtgärder läsa eller skriva filer som är *1 GB eller mindre* , men bara i *15 MB* -segment i taget. För att hantera filer som är större än 15 MB har SFTP-SSH-åtgärder stöd för [meddelande segment](../logic-apps/logic-apps-handle-large-messages.md), förutom åtgärden Kopiera fil, som bara kan hantera 15 MB filer. Åtgärden **Hämta fil innehåll** använder implicit meddelande segment.

* SFTP – SSH-utlösare stöder inte segment. När du begär fil innehåll väljer utlösare endast filer som är 15 MB eller mindre. Om du vill hämta filer som är större än 15 MB följer du detta mönster i stället:

  * Använd en SFTP-SSH-utlösare som returnerar fil egenskaper, till exempel **när en fil läggs till eller ändras (endast egenskaper)** .

  * Följ utlösaren med åtgärden SFTP-SSH **Get File Content** , som läser den fullständiga filen och använder implicit meddelande segment.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Jämför SFTP – SSH kontra SFTP

Här följer några andra viktiga skillnader mellan SFTP-SSH-anslutningen och SFTP-anslutningen där SFTP-SSH-anslutningen har dessa funktioner:

* Använder [SSH.net-biblioteket](https://github.com/sshnet/SSH.NET), som är ett SSH-bibliotek med öppen källkod som stöder .net.

* Som standard kan SFTP och SSH-åtgärder läsa eller skriva filer som är *1 GB eller mindre* , men bara i *15 MB* -segment i taget.

  För att hantera filer som är större än 15 MB kan SFTP-SSH-åtgärder använda [meddelande segment](../logic-apps/logic-apps-handle-large-messages.md). Åtgärden Kopiera fil stöder dock endast 15 MB filer eftersom den åtgärden inte stöder meddelande segment. SFTP – SSH-utlösare stöder inte segment. Om du vill överföra stora filer behöver du både Läs-och Skriv behörighet för rotmappen på din SFTP-server.

* Tillhandahåller åtgärden **Skapa mapp** , som skapar en mapp på den angivna sökvägen på SFTP-servern.

* Innehåller åtgärden **Byt namn på fil** , som byter namn på en fil på SFTP-servern.

* Cachelagrar anslutningen till SFTP-servern *i upp till 1 timme*, vilket förbättrar prestandan och minskar antalet försök att ansluta till servern. Om du vill ställa in varaktigheten för den här funktionen för cachelagring redigerar du egenskapen [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) i SSH-konfigurationen på din SFTP-server.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Dina SFTP-server-och kontoautentiseringsuppgifter som låter din Logic app komma åt ditt SFTP-konto. Du måste också ha åtkomst till en privat SSH-nyckel och lösen ordet för den privata SSH-nyckeln. Om du vill använda segment vid överföring av stora filer behöver du både Läs-och Skriv behörighet för rotmappen på din SFTP-server. Annars får du fel meddelandet "401 obehörig".

  > [!IMPORTANT]
  >
  > SFTP-SSH-anslutaren stöder *endast* dessa format för privata nycklar, algoritmer och finger avtryck:
  >
  > * **Privata nyckel format**: RSA (Rivest Shamir Adleman) och DSA-nycklar (Digital Signature Algorithm) i både OpenSSH-och SSH.com-format. Om din privata nyckel är i fil formatet SparaTillFil (. PPK), måste [du först konvertera nyckeln till fil formatet openssh (. pem)](#convert-to-openssh).
  >
  > * **Krypteringsalgoritmer**: des-EDE3-CBC, des-EDE3-CFB, des-CBC, AES-128-CBC, AES-192-CBC och aes-256-CBC
  >
  > * **Finger avtryck**: MD5
  >
  > När du har lagt till den SFTP-SSH-utlösare eller åtgärd som du vill använda i din Logic-app måste du ange anslutnings information för din SFTP-server. När du anger din privata SSH-nyckel för den här anslutningen kan du ***inte manuellt ange eller redigera nyckeln***, vilket kan leda till att anslutningen Miss fungerar. Kontrol lera i stället att du ***kopierar nyckeln*** från filen med din privata SSH-nyckel och ***klistrar in*** nyckeln i anslutnings informationen. 
  > Mer information finns i avsnittet [ansluta till SFTP med SSH](#connect) senare i den här artikeln.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill komma åt ditt SFTP-konto. [Skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md)för att starta med en SFTP-SSH-utlösare. Om du vill använda en SFTP-SSH-åtgärd startar du din Logic-app med en annan utlösare, till exempel utlösaren **upprepning** .

## <a name="how-sftp-ssh-triggers-work"></a>Så här fungerar SFTP – SSH-utlösare

SFTP – SSH-utlösare fungerar genom att avsöka SFTP-filsystemet och leta efter en fil som har ändrats sedan den senaste avsökningen. Med vissa verktyg kan du bevara tidsstämpeln när filerna ändras. I dessa fall måste du inaktivera den här funktionen så att utlösaren kan fungera. Här följer några vanliga inställningar:

| SFTP-klient | Åtgärd |
|-------------|--------|
| WinSCP | Gå till **alternativen** > **inställningar** > **överföra** > **Redigera** > **bevara tidsstämpel** > **inaktivera** |
| FileZilla | Gå till **överför** > **bevara tidsstämplar för överförda filer** > **inaktivera** |
|||

När en utlösare hittar en ny fil, kontrollerar utlösaren att den nya filen är fullständig och inte delvis skriven. En fil kan till exempel ha ändringar som pågår när utlösaren kontrollerar fil servern. För att undvika att returnera en delvis skriven fil, noterar utlösaren tidsstämpeln för filen som har nyligen gjorda ändringar, men returnerar inte omedelbart den filen. Utlösaren returnerar filen endast när servern avsöks igen. Ibland kan det här problemet orsaka en fördröjning som är upp till två gånger utlösaren för avsöknings intervall.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Konvertera den SparaTillFil-baserade nyckeln till OpenSSH

Om den privata nyckeln är i formatet SparaTillFil, som använder fil namns tillägget. PPK (SparaTillFil-privat nyckel), måste du först konvertera nyckeln till OpenSSH-formatet, som använder fil namns tillägget. pem (Privacy Enhanced Mail).

### <a name="unix-based-os"></a>UNIX-baserat OS

1. Om SparaTillFil-verktygen inte redan är installerade på systemet, gör du det nu, till exempel:

   `sudo apt-get install -y putty`

1. Kör det här kommandot, som skapar en fil som du kan använda med SFTP-SSH-anslutningen:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Exempel:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows-operativsystem

1. Om du inte redan har gjort det kan du [Hämta det senaste verktyget SparaTillFil-generatorn (PuTTYgen. exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)och sedan starta verktyget.

1. Välj **load**på den här skärmen.

   ![Välj load (Load)](./media/connectors-sftp-ssh/puttygen-load.png)

1. Bläddra till din privata nyckel fil i formatet SparaTillFil och välj **Öppna**.

1. Från menyn **konverteringar** väljer du **Exportera openssh nyckel**.

   ![Välj "Exportera OpenSSH key"](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Spara den privata nyckel filen med fil namns tillägget `.pem`.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Ansluta till SFTP med SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. För tomma Logi Kap par anger du "SFTP SSH" som filter i rutan Sök. Välj den utlösare som du vill använda under listan utlösare.

   ELLER

   För befintliga Logic Apps, under det sista steget där du vill lägga till en åtgärd, väljer du **nytt steg**. I rutan Sök anger du "SFTP SSH" som filter. Under listan åtgärder väljer du den åtgärd som du vill använda.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. Ange nödvändig information för anslutningen.

   > [!IMPORTANT]
   >
   > När du anger den privata SSH-nyckeln i egenskapen för den **privata SSH-nyckeln** följer du dessa ytterligare steg, som hjälper dig att ange det fullständiga och korrekta värdet för den här egenskapen. En ogiltig nyckel gör att anslutningen Miss fungerar.

   Även om du kan använda valfri text redigerare är det exempel steg som visar hur du kopierar och klistrar in nyckeln på rätt sätt med notepad. exe som exempel.

   1. Öppna din privata SSH-nyckel fil i en text redigerare. I dessa steg används anteckningar som exempel.

   1. I **redigerings** menyn i Anteckningar väljer du **Markera alla**.

   1. Välj **redigera** > **Kopiera**.

   1. I SFTP-SSH-utlösare eller åtgärd som du har lagt till klistrar du in den *fullständiga* nyckeln som du kopierade i egenskapen **SSH Private Key** , som stöder flera rader.  ***Se till att klistra in*** nyckeln. ***Ange eller redigera inte nyckeln manuellt***.

1. När du är klar med att ange anslutnings informationen väljer du **skapa**.

1. Ange den information som krävs för den valda utlösaren eller åtgärden och fortsätt att skapa din Logic app-arbetsflöde.

## <a name="examples"></a>Exempel

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP – SSH-utlösare: när en fil läggs till eller ändras

Den här utlösaren startar ett Logic app-arbetsflöde när en fil läggs till eller ändras på en SFTP-server. Du kan till exempel lägga till ett villkor som kontrollerar filens innehåll och hämtar innehållet baserat på om innehållet uppfyller ett angivet villkor. Du kan sedan lägga till en åtgärd som hämtar filens innehåll och placerar innehållet i en mapp på SFTP-servern.

**Enterprise-exempel**: du kan använda den här utlösaren för att övervaka en SFTP-mapp för nya filer som representerar kund order. Du kan sedan använda en SFTP-åtgärd, till exempel **Hämta fil innehåll** , så att du kan hämta Beställningens innehåll för ytterligare bearbetning och lagra den i en order databas.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP – SSH-åtgärd: hämta innehåll med hjälp av sökväg

Den här åtgärden hämtar innehållet från en fil på en SFTP-server. Du kan till exempel lägga till utlösaren från föregående exempel och ett villkor som filens innehåll måste uppfylla. Om villkoret är sant kan åtgärden som hämtar innehållet köras.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här anslutningen, till exempel utlösare, åtgärder och begränsningar som beskrivs av kopplingens Swagger-fil finns på [kopplingens referens sida](https://docs.microsoft.com/connectors/sftpwithssh/).

> [!NOTE]
> För logi Kap par i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandets gränser](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
