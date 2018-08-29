---
title: Anslut till filsystem lokalt – Azure Logic Apps | Microsoft Docs
description: Automatisera uppgifter och arbetsflöden som ansluter till lokala filsystem med anslutningsappen för filsystem via den lokala datagatewayen i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: 41dd8ad721329c4c4d2761c9e4a37c640251dac3
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125286"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Ansluta till lokala filsystem med Azure Logic Apps

Med anslutningsappen för filsystem och Azure Logic Apps kan du kan skapa automatiserade uppgifter och arbetsflöden som skapar och hanterar filer på en lokal fil delar, till exempel:  

- Skapa, hämta, lägga till, uppdatera och ta bort filer
- Lista filer i mappar eller rotmappar.
- Hämta filinnehåll och metadata.

Den här artikeln visar hur du kan ansluta till ett lokalt filsystem som beskrivs i det här scenariot: kopiera en fil som har överförts till Dropbox till en filresurs och sedan skicka ett e-postmeddelande. På ett säkert sätt ansluta och få åtkomst till lokala system, logic apps används den [lokal datagateway](../logic-apps/logic-apps-gateway-connection.md). Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* Innan du kan ansluta logikappar till lokala system, till exempel filservern för system, måste du [installera och konfigurera en lokal datagateway](../logic-apps/logic-apps-gateway-install.md). På så sätt kan du ange om du vill använda din gatewayinstallationen när du skapar filen system-anslutning från din logikapp.

* En [Drobox konto](https://www.dropbox.com/) och dina autentiseringsuppgifter

  Dina autentiseringsuppgifter för tillåta din logikapp för att skapa en anslutning och komma åt ditt konto för Drobox. 

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md). I det här exemplet behöver du en tom logikapp.

## <a name="add-trigger"></a>Lägg till utlösare

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. I sökrutan anger du ”dropbox” som filter. Välj den här utlösaren från listan över utlösare: **när en fil skapas** 

   ![Välj Dropbox-utlösare](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Logga in med dina autentiseringsuppgifter för Dropbox-konto och auktorisera åtkomst till dina data i Dropbox för Azure Logic Apps. 

1. Ange nödvändig information för utlösaren.

   ![Dropbox-utlösare](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Lägga till åtgärder

1. Under utlösaren väljer **nästa steg**. I sökrutan anger du ”file system” som filter. Välj den här åtgärden från åtgärdslistan över: **skapa fil - filsystem**

   ![Hitta anslutningsappen för filsystem](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Om du inte redan har en anslutning till ditt filsystem, uppmanas du att skapa en anslutning.

   ![Skapa anslutning](media/logic-apps-using-file-connector/file-system-connection.png)

   | Egenskap  | Krävs | Värde | Beskrivning | 
   | -------- | -------- | ----- | ----------- | 
   | **Anslutningsnamn** | Ja | <*Anslutningens namn*> | Du ett namn för anslutningen | 
   | **Rotmapp för vyer** | Ja | <*rot mappnamn*> | Rotmapp för ditt filsystem, till exempel en lokal mapp på datorn där den lokala datagatewayen har installerats eller mappen för en nätverksresurs som datorn kan komma åt. <p>Exempel: `\\PublicShare\\DropboxFiles` <p>Rotmappen är huvudsakliga överordnad mapp, som används för relativa sökvägar för alla filrelaterade åtgärder. | 
   | **Autentiseringstyp** | Nej | <*autentiseringstyp*> | Vilken typ av autentisering som ditt filsystem använder, till exempel **Windows** | 
   | **Användarnamn** | Ja | <*domän*>\\<*användarnamn*> | Användarnamn för din tidigare installerade datagateway | 
   | **Lösenord** | Ja | <*ditt lösenord*> | Lösenordet för din tidigare installerade datagateway | 
   | **Gateway** | Ja | <*installerade gatewaynamn*> | Namn för din tidigare installerade gateway | 
   ||| 

1. När du är klar väljer du **Skapa**. 

   Logic Apps konfigurerar och testar anslutningen, se till att anslutningen fungerar korrekt. 
   Om anslutningen är korrekt konfigurerad, visas alternativ för den åtgärd som du valde tidigare. 

1. I den **skapa fil** åtgärden, anger du informationen för att kopiera filer från Dropbox till rotmappen i den lokala filresursen. Klicka i rutorna för att lägga till utdata från föregående steg och välj från tillgängliga fält när listan med dynamiskt innehåll visas.

   ![Skapa filåtgärd](media/logic-apps-using-file-connector/create-file-filled.png)

1. Lägg nu till en Outlook-åtgärd som skickar ett e-postmeddelande så att rätt användare veta om den nya filen. Ange mottagare, rubriken och brödtexten i e-postmeddelandet. För testning bör använda du din egen e-postadress.

   ![E-poståtgärd](media/logic-apps-using-file-connector/send-email.png)

1. Spara din logikapp. Testa din app genom att ladda upp en fil till Dropbox. 

   Logikappen bör kopiera filen till den lokala filresursen och skicka ett e-postmeddelande för mottagarna om den kopierade filen.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/fileconnector/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* För att förbättra Azure Logic Apps och kopplingar, rösta på förslag eller komma med på den [Azure Logic Apps User Voice-webbplatsen](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [ansluta till lokala data](../logic-apps/logic-apps-gateway-connection.md) 
* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
