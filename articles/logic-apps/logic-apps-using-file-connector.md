---
title: Ansluta till filsystem lokalt
description: Automatisera uppgifter och arbetsflöden som ansluter till lokala filsystem med filsystemkopplingen via den lokala datagatewayen i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 01/13/2019
ms.openlocfilehash: 2a00405a2100c3e565ca4f8ea4149540a5199b43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651414"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Ansluta till lokala filsystem med Azure Logic Apps

Med Azure Logic Apps och File System-anslutningsappen kan du skapa automatiserade uppgifter och arbetsflöden som skapar och hanterar filer på en lokal filresurs, till exempel:

- Skapa, hämta, lägga till, uppdatera och ta bort filer.
- Lista filer i mappar eller rotmappar.
- Hämta filinnehåll och metadata.

Den här artikeln visar hur du kan ansluta till ett lokalt filsystem enligt beskrivningen i det här exempelscenariot: kopiera en fil som laddas upp till Dropbox till en filresurs och skicka sedan ett e-postmeddelande. Logikappar använder den [lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md)för att ansluta och komma åt lokala system på ett säkert sätt . Om du inte har tidigare i logikappar läser du [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md) Anslutningsspecifik teknisk information finns i [filsystemanslutningsreferensen](/connectors/filesystem/).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Innan du kan ansluta logikappar till lokala system som filsystemservern måste du [installera och konfigurera en lokal datagateway](../logic-apps/logic-apps-gateway-install.md). På så sätt kan du ange att använda gatewayinstallationen när du skapar filsystemanslutningen från logikappen.

* Ett [Dropbox-konto](https://www.dropbox.com/), som du kan registrera dig gratis. Dina kontouppgifter är nödvändiga för att skapa en anslutning mellan logikappen och ditt Dropbox-konto.

* Åtkomst till den dator som har det filsystem som du vill använda. Om du till exempel installerar datagatewayen på samma dator som filsystemet behöver du kontoautentiseringsuppgifterna för den datorn.

* Ett e-postkonto från en leverantör som stöds av Logic Apps, till exempel Office 365 Outlook, Outlook.com eller Gmail. För andra providrar [läser du listan med anslutningsappar här](https://docs.microsoft.com/connectors/). För den här logikappen används ett Office 365 Outlook-konto. Om du använder något annat e-postkonto är de övergripande stegen desamma, men användargränssnittet kan skilja sig något.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md). I det här exemplet behöver du en tom logikapp.

## <a name="add-trigger"></a>Lägg till utlösare

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. I sökrutan anger du "dropbox" som filter. Välj den här utlösaren i listan utlösare: **När en fil skapas**

   ![Välj Dropbox-utlösare](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Logga in med dina Dropbox-kontouppgifter och ge åtkomst till dina Dropbox-data för Azure Logic Apps.

1. Ange nödvändig information för utlösaren.

   ![Dropbox-utlösare](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Lägga till åtgärder

1. Under utlösaren väljer du **Nästa steg**. Ange "filsystem" som filter i sökrutan. Välj den här åtgärden i åtgärdslistan: **Skapa fil**

   ![Hitta filsystemanslutning](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Om du inte redan har någon anslutning till filsystemet uppmanas du att skapa en anslutning.

   ![Skapa anslutning](media/logic-apps-using-file-connector/file-system-connection.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   | -------- | -------- | ----- | ----------- |
   | **Anslutningsnamn** | Ja | <*anslutningsnamn*> | Namnet du vill använda för anslutningen |
   | **Rotmappen** | Ja | <*rotmapp-namn*> | Rotmappen för filsystemet, till exempel om du har installerat den lokala datagatewayen, till exempel en lokal mapp på datorn där den lokala datagatewayen är installerad, eller mappen för en nätverksresurs som datorn kan komma åt. <p>Exempel: `\\PublicShare\\DropboxFiles` <p>Rotmappen är den överordnade huvudmappen, som används för relativa sökvägar för alla filrelaterade åtgärder. |
   | **Typ av autentisering** | Inga | <*auth-typ*> | Den typ av autentisering som filsystemet använder, till exempel **Windows** |
   | **Användarnamn** | Ja | <*domain*>domän<*username* \\användarnamn> | Användarnamnet för datorn där du har ditt filsystem |
   | **Lösenord** | Ja | <*ditt lösenord*> | Lösenordet för datorn där du har ditt filsystem |
   | **Gateway** | Ja | <*installerat gateway-namn*> | Namnet på den tidigare installerade gatewayen |
   |||||

1. När du är klar väljer du **Skapa**.

   Logic Apps konfigurerar och testar anslutningen och ser till att anslutningen fungerar som den ska. Om anslutningen är korrekt konfigurerad visas alternativ för den åtgärd som du tidigare har markerat.

1. I åtgärden **Skapa fil** anger du information om hur du kopierar filer från Dropbox till rotmappen i den lokala filresursen. Om du vill lägga till utdata från föregående steg klickar du i rutorna och väljer bland tillgängliga fält när listan med dynamiskt innehåll visas.

   ![Skapa filåtgärd](media/logic-apps-using-file-connector/create-file-filled.png)

1. Lägg nu till en Outlook-åtgärd som skickar ett e-postmeddelande så att rätt användare känner till den nya filen. Ange e-postmeddelandets mottagare, titel och brödtext. För testning kan du använda din egen e-postadress.

   ![Åtgärden Skicka ett e-postmeddelande](media/logic-apps-using-file-connector/send-email.png)

1. Spara din logikapp. Testa din app genom att ladda upp en fil till Dropbox.

   Logikappen ska kopiera filen till den lokala filresursen och skicka ett e-postmeddelande till mottagarna om den kopierade filen.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här kopplingen, till exempel utlösare, åtgärder och begränsningar enligt beskrivningen i kopplingens Swagger-fil, finns på [kopplingens referenssida](https://docs.microsoft.com/connectors/fileconnector/).

> [!NOTE]
> För logikappar i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandegränserna](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [ansluter till lokala data](../logic-apps/logic-apps-gateway-connection.md) 
* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
