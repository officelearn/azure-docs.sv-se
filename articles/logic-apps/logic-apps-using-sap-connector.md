---
title: Ansluta till en lokal SAP-system i Logikappar i Azure | Microsoft Docs
description: "Ansluta till en lokal SAP-system från logik app-arbetsflödes lokala datagateway"
services: logic-apps
author: padmavc
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 3fea93f558d5a4ef62550fd1f6486903cb812930
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-an-on-premises-sap-system-from-logic-apps-with-the-sap-connector"></a>Ansluta till en lokal SAP-system från logikappar med SAP-koppling 

Lokala datagateway kan du hantera data och säker åtkomst till resurser som finns lokalt. Det här avsnittet visar hur du kan ansluta logikappar till en lokal SAP-system. I det här exemplet logikappen begär en IDOC över HTTP och skickar svaret tillbaka.    

> [!NOTE]
> Aktuella begränsningar: 
> - Din logikapp timeout om alla steg som krävs för svaret inte slutförs inom den [tidsgränsen för begäran](./logic-apps-limits-and-config.md). I det här scenariot kan begäranden blockeras. 
> - Filväljaren visar inte alla tillgängliga fält. I det här scenariot kan du manuellt lägga till sökvägar.

## <a name="prerequisites"></a>Krav

- Installera och konfigurera senast [lokala datagateway](https://www.microsoft.com/download/details.aspx?id=53127) version 1.15.6150.1 eller senare. [Hur du ansluter till lokala datagateway i en logikapp](http://aka.ms/logicapps-gateway) innehåller stegen. Gatewayen måste installeras på en lokal dator innan du kan fortsätta.

- Hämta och installera det senaste SAP-klientbiblioteket på samma dator där du installerade datagateway. Använd någon av följande SAP-versioner: 
    - SAP-Server
        - SAP-Server som har stöd för .NET-anslutningen (NCo) 3.0
 
    - SAP-klient
        - SAP .NET Connector (NCo) 3.0

## <a name="add-triggers-and-actions-for-connecting-to-your-sap-system"></a>Lägg till utlösare och åtgärder för att ansluta till SAP-system

SAP-koppling har åtgärder, men inte utlösare. Vi har därför använda en annan utlösare i början av arbetsflödet. 

1. Lägg till begäranden/svar-utlösare och välj sedan **nytt steg**.

2. Välj **lägga till en åtgärd**, och välj sedan SAP-koppling genom att skriva `SAP` i sökfältet:    

     ![Välj SAP-programserver eller SAP Message Server](media/logic-apps-using-sap-connector/sap-action.png)

3. Välj [ **SAP-programserver** ](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) eller [ **SAP Message Server**](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm), baserat på din SAP-konfiguration. Om du inte har en befintlig anslutning, uppmanas du att skapa en.

   1. Välj **Anslut via lokala datagateway**, och ange information för SAP-system:   

       ![Lägg till anslutningssträng SAP](media/logic-apps-using-sap-connector/picture2.png)  

   2. Under **Gateway**väljer du en befintlig gateway, eller om du vill installera en ny gateway **installera gatewayen**.

        ![Installera en ny gateway](media/logic-apps-using-sap-connector/install-gateway.png)
  
   3. När du har angett alla detaljer Välj **skapa**. 
   Logic Apps konfigurerar och testar anslutningen, för att se till att anslutningen inte fungerar korrekt.

4. Ange ett namn för din SAP-anslutning.

5. De olika alternativen för SAP är nu tillgängliga. Välj i listan för att hitta IDOC-kategori. Eller manuellt ange i sökvägen och välj HTTP-svar i den **brödtext** fält:

     ![SAP-åtgärd](media/logic-apps-using-sap-connector/picture3.png)

6. Lägg till instruktionen för att skapa en **HTTP-svar**. Svarsmeddelandet ska från SAP-utdata.

7. Spara din logikapp. Testa den genom att skicka en IDOC via HTTP-utlösaren URL. När IDOC som skickas, vänta på svar från logikappen:   

     > [!TIP]
     > Kolla hur man [övervaka dina Logikappar](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Nu när SAP-kopplingen har lagts till i din logikapp, kan du börja utforska andra funktioner:

- BAPI
- RFC

## <a name="get-help"></a>Få hjälp

I [Azure Logic Apps-forumet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) kan du ställa frågor, besvara frågor och se vad andra Azure Logic Apps-användare håller på med.

På [webbplatsen för Azure Logic Apps-användarfeedback](http://aka.ms/logicapps-wish) kan du hjälpa till med att förbättra Azure Logic Apps och anslutningsapparna genom att rösta på förslag eller komma med egna förslag på förbättringar.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du validera, transformera och andra BizTalk-liknande funktioner i den [Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md). 
- [Ansluta till lokala data](../logic-apps/logic-apps-gateway-connection.md) från logikappar
