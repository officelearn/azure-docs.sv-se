---
title: Ansluta till filsystem lokalt - Azure Logic Apps | Microsoft Docs
description: "Ansluta till lokalt filsystem från logik app arbetsflöden via lokala datagateway och filsystemet connector"
keywords: filsystem, lokalt
services: logic-apps
author: derek1ee
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: LADocs; deli
ms.openlocfilehash: 32ab5be41a8dee3b1f2c0b1bde076c0d1a844bdd
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>Ansluta till lokalt filsystem från logikappar med filsystemet connector

För att hantera data och säker åtkomst till lokala resurser, kan dina logic apps använda lokala datagateway. Den här artikeln visar hur du kan ansluta till ett filsystem lokalt via den här enkla Exempelscenario: kopiera en fil som överförs till Dropbox till en filresurs och sedan skicka ett e-postmeddelande.

## <a name="prerequisites"></a>Förutsättningar

* Ladda ned senaste [lokala datagateway](https://www.microsoft.com/download/details.aspx?id=53127).

* Installera och ställa in senaste lokala datagateway, version 1.15.6150.1 eller senare. Anvisningar finns i avsnittet [Anslut till datakällor på lokala](http://aka.ms/logicapps-gateway). Du måste installera gatewayen på en lokal dator innan du kan fortsätta med de här stegen.

* Grundläggande kunskaper om [skapa logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>Lägga till utlösare och åtgärder för att ansluta till filsystemet

1. Skapa en tom logikapp. Lägg till den här utlösaren som det första steget: **Dropbox - när en fil har skapats** 

2. Välj under utlösare, **+ nästa steg** > **lägga till en åtgärd**. 

3. I sökrutan anger du ”file system” som filter. När du ser alla åtgärder för kopplingen filsystem, Välj den **filsystem - skapa fil** åtgärd. 

   ![Sök efter filen connector](media/logic-apps-using-file-connector/search-file-connector.png)

4. Om du inte redan har en anslutning till din dator, uppmanas du att skapa en anslutning. 

5. Välj **Anslut via lokala datagateway**. Konfigurera anslutningen som anges i tabellen när du anslutningens egenskaper visas.

   ![Konfigurera anslutning](media/logic-apps-using-file-connector/create-file.png)

   | Inställning | Beskrivning |
   | ------- | ----------- |
   | **Rotmappen** | Ange rotmappen för din dator. Du kan ange en lokal mapp på datorn där lokala datagateway har installerats eller mappen kan vara en nätverksresurs som datorn kan komma åt. <p>**Tips:** rotmappen är den viktigaste överordnade mappen som används för relativa sökvägar för alla fil-relaterade åtgärder. | 
   | **Autentiseringstyp** | Typ av autentisering som används av filsystemet | 
   | **Användarnamn** | Ange ditt användarnamn {*domän*\\*användarnamn*} för din tidigare installerade gateway. | 
   | **Lösenord** | Ange lösenordet för ditt tidigare installerade gateway. | 
   | **Gateway** | Välj din tidigare installerade gateway. | 
   ||| 

6. När du har angett anslutningsinformationen väljer **skapa**. 

   Logic Apps konfigurerar och testar anslutningen, se till att anslutningen inte fungerar korrekt. 
   Om anslutningen har konfigurerats korrekt visas alternativ för den åtgärd som du valde tidigare. 
   Filen system koppling är nu redo för användning.

7. Ställ in den **skapa fil** åtgärd för att kopiera filer från Dropbox till rotmappen för din lokala filresurs.

   ![Skapa filen åtgärd](media/logic-apps-using-file-connector/create-file-filled.png)

8. Lägg till ett Outlook-åtgärd som skickar ett e-postmeddelande så att relevant användarna vet om den nya filen när den här åtgärden för att kopiera filen. Ange mottagare, titel och brödtexten i e-postmeddelandet. 

   I den **dynamiskt innehåll** lista, kan du utdata från filen-anslutningen så att du kan lägga till mer information i e-postmeddelandet.

   ![Skicka e-åtgärd](media/logic-apps-using-file-connector/send-email.png)

9. Spara din logikapp. Testa din app genom att överföra en fil till Dropbox. Filen ska kopieras till filresursen lokalt och du får ett e-postmeddelande om åtgärden.

Grattis, du har nu en fungerande logikapp som kan ansluta till det lokala filsystemet. 

Försök att utforska andra funktioner som kopplingen erbjuder:

- Skapa fil
- Listar filer i mappen
- Lägg till filen
- Ta bort fil
- Hämta filinnehåll
- Hämta filinnehåll med hjälp av sökväg
- Hämta filmetadata
- Hämta metadata för fil med hjälp av sökväg
- Listar filer i rotmappen
- Uppdatera filen

## <a name="view-the-swagger"></a>Visa swagger

Finns det [swagger information](/connectors/fileconnector/). 

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* För att förbättra Azure Logic Apps och kopplingar, rösta eller skicka in förslag på den [Azure Logic Apps User Voice plats](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Ansluta till lokala data](../logic-apps/logic-apps-gateway-connection.md) 
* [Övervaka dina logikappar](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Enterprise-integrering för B2B-scenarier](../logic-apps/logic-apps-enterprise-integration-overview.md)
