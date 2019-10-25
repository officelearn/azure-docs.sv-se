---
title: Ansluta till fil system lokalt – Azure Logic Apps
description: Automatisera aktiviteter och arbets flöden som ansluter till lokala fil system med fil system anslutningen via den lokala datagatewayen i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/13/2019
ms.openlocfilehash: 1b5cf27c49a003042086cd9452f288c7f348d343
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72799696"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Ansluta till lokala fil system med Azure Logic Apps

Med fil Systems anslutningen och Azure Logic Apps kan du skapa automatiserade uppgifter och arbets flöden som skapar och hanterar filer på en lokal fil resurs, till exempel:  

- Skapa, Hämta, lägga till, uppdatera och ta bort filer.
- Visa filer i mappar eller rotmappar.
- Hämta fil innehåll och metadata.

Den här artikeln visar hur du kan ansluta till ett lokalt fil system enligt beskrivningen i det här exempel scenariot: kopiera en fil som laddas upp till Dropbox till en fil resurs och skicka sedan ett e-postmeddelande. För att på ett säkert sätt ansluta och komma åt lokala system använder Logic Apps den [lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md). Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Information om anslutningsspecifika teknisk information finns i referens för [fil system anslutning](/connectors/filesystem/).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Innan du kan ansluta Logi Kap par till lokala system, till exempel fil system servern, måste du [Installera och konfigurera en lokal datagateway](../logic-apps/logic-apps-gateway-install.md). På så sätt kan du ange att din gateway-installation ska användas när du skapar fil system anslutningen från din Logic app.

* Ett [Dropbox-konto](https://www.dropbox.com/)som du kan registrera dig kostnads fritt. Dina kontoautentiseringsuppgifter krävs för att skapa en anslutning mellan din Logic app och ditt Dropbox-konto.

* Åtkomst till den dator som har det fil system som du vill använda. Om du till exempel installerar datagatewayen på samma dator som fil systemet behöver du kontots autentiseringsuppgifter för den datorn.

* Ett e-postkonto från en provider som stöds av Logic Apps, till exempel Office 365 Outlook, Outlook.com eller Gmail. För andra providrar [läser du listan med anslutningsappar här](https://docs.microsoft.com/connectors/). För den här logikappen används ett Office 365 Outlook-konto. Om du använder något annat e-postkonto är de övergripande stegen desamma, men användargränssnittet kan skilja sig något.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). I det här exemplet behöver du en tom Logic-app.

## <a name="add-trigger"></a>Lägg till utlösare

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. Skriv "Dropbox" som filter i rutan Sök. Välj den här utlösaren i listan utlösare: **när en fil skapas**

   ![Välj Dropbox-utlösare](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Logga in med dina autentiseringsuppgifter för Dropbox-kontot och ge åtkomst till Dropbox-data för Azure Logic Apps.

1. Ange den information som krävs för utlösaren.

   ![Dropbox-utlösare](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Lägg till åtgärder

1. Under utlösaren väljer du **Nästa steg**. I rutan Sök anger du "fil system" som filter. I listan åtgärder väljer du den här åtgärden: **Skapa fil**

   ![Hitta fil Systems koppling](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Om du inte redan har en anslutning till fil systemet uppmanas du att skapa en anslutning.

   ![Skapa anslutning](media/logic-apps-using-file-connector/file-system-connection.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   | -------- | -------- | ----- | ----------- |
   | **Anslutningsnamn** | Ja | <*anslutnings namn* > | Det namn som du vill använda för anslutningen |
   | **Rotmapp** | Ja | <*rot-mapp-namn*> | Rotmappen för ditt fil system, till exempel om du har installerat din lokala datagateway, till exempel en lokal mapp på datorn där den lokala datagatewayen är installerad, eller mappen för en nätverks resurs som datorn har åtkomst till. <p>Exempel: `\\PublicShare\\DropboxFiles` <p>Rotmappen är den överordnade huvudmappen som används för relativa sökvägar för alla filrelaterade åtgärder. |
   | **Autentiseringstyp** | Nej | <*autentiserings typ*> | Den typ av autentisering som fil systemet använder, till exempel **Windows** |
   | **Användarnamn** | Ja | <*domän*>\\<*användar namn*> | Användar namnet för den dator där du har fil systemet |
   | **Lösenord** | Ja | <*ditt lösen ord*> | Lösen ordet för datorn där du har fil systemet |
   | **nyckeln** | Ja | <*installerat – Gateway-namn*> | Namnet på din tidigare installerade Gateway |
   |||||

1. När du är klar väljer du **Skapa**.

   Logic Apps konfigurerar och testar anslutningen och kontrollerar att anslutningen fungerar korrekt. Om anslutningen är korrekt konfigurerad visas alternativ för den åtgärd som du tidigare har valt.

1. I åtgärden **Skapa fil** anger du information om hur du kopierar filer från Dropbox till rotmappen i din lokala fil resurs. Om du vill lägga till utdata från föregående steg klickar du i rutorna och väljer från tillgängliga fält när listan med dynamiskt innehåll visas.

   ![Åtgärd för att skapa fil](media/logic-apps-using-file-connector/create-file-filled.png)

1. Nu ska du lägga till en Outlook-åtgärd som skickar ett e-postmeddelande så att rätt användare vet om den nya filen. Ange mottagare, titel och brödtext för e-postmeddelandet. För testning kan du använda din egen e-postadress.

   ![Skicka e-poståtgärd](media/logic-apps-using-file-connector/send-email.png)

1. Spara din logikapp. Testa din app genom att ladda upp en fil till Dropbox.

   Din Logic app ska kopiera filen till din lokala fil resurs och skicka mottagarna ett e-postmeddelande om den kopierade filen.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](/connectors/fileconnector/).

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [ansluter till lokala data](../logic-apps/logic-apps-gateway-connection.md) 
* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
