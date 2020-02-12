---
title: Koda eller avkoda flata filer
description: Koda eller avkoda flata filer för företags integrering med Azure Logic Apps och Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 2d6182ba01507c2fb361628e01bb52e1ea821f44
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152660"
---
# <a name="encode-or-decode-flat-files-with-azure-logic-apps-and-enterprise-integration-pack"></a>Koda eller avkoda flata filer med Azure Logic Apps och Enterprise-integrationspaket

Du kanske vill koda XML-innehåll innan du skickar det till en affärs partner i ett B2B-scenario (Business-to-Business). I en Logic app kan du göra detta med hjälp av den flata fil kodnings anslutningen. Den Logic-app som du skapar kan hämta sitt XML-innehåll från en mängd olika källor, inklusive från en HTTP-begäran, från ett annat program eller till och med från någon av de många [kopplingarna](../connectors/apis-list.md). Mer information om Logic Apps finns i dokumentationen om [Logic Apps](logic-apps-overview.md "Läs mer om Logic Apps").  

## <a name="create-the-flat-file-encoding-connector"></a>Skapa den flata filen kodnings koppling
Följ dessa steg om du vill lägga till en flat fil kodnings koppling i din Logic app.

1. Skapa en Logic-app och [länka den till ditt integrations konto](logic-apps-enterprise-integration-accounts.md "Lär dig att länka ett integrations konto till en Logic app"). Det här kontot innehåller det schema som du ska använda för att koda XML-data.  

1. I Logic Apps designer lägger du till listan **när en HTTP-begäran tas emot** som utlösare för din Logic app.

1. Lägg till den flata fil kodnings åtgärden enligt följande:

   a. Välj **plus** tecknet.

   b. Välj länken **Lägg till en åtgärd** (visas när du har valt plus tecknet).

   c. I rutan Sök anger du *Flat* för att filtrera alla åtgärder till den som du vill använda.

   d. Välj alternativet **flat fil kodning** i listan.   

      ![Skärm bild av alternativ för flat fil kodning](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. I dialog rutan **Flat File encoding** väljer du text rutan **innehåll** .  

   ![Skärm bild av text rutan innehåll](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  

1. Välj bröd text tag gen som det innehåll som du vill koda. Bröd text tag gen fyller i innehålls fältet.     

   ![Skärm bild av body-tagg](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  

1. Markera List rutan **schema namn** och välj det schema som du vill använda för att koda det inmatade innehållet.    

   ![Skärm bild av list rutan schema namn](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  

1. Spara ditt arbete.

   ![Skärm bild av ikonen Spara](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

Nu är du färdig med konfigurationen av den flata fil kodnings anslutningen. I ett verkligt världs program kanske du vill lagra kodade data i ett affärs program, till exempel Salesforce. Eller så kan du skicka dessa kodade data till en handels partner. Du kan enkelt lägga till en åtgärd för att skicka utdata från encoding-åtgärden till Salesforce, eller till din handels partner genom att använda någon av de andra anslutningarna som tillhandahålls.

Nu kan du testa din anslutning genom att göra en begäran till HTTP-slutpunkten och inkludera XML-innehållet i bröd texten i begäran.  

## <a name="create-the-flat-file-decoding-connector"></a>Skapa platt fil avkodnings koppling

> [!NOTE]
> För att slutföra de här stegen måste du ha en schema fil som redan har laddats upp till ditt integrations konto.

1. I Logic Apps designer lägger du till listan **när en HTTP-begäran tas emot** som utlösare för din Logic app.

1. Lägg till den flata fil avkodnings åtgärden enligt följande:

   a. Välj **plus** tecknet.

   b. Välj länken **Lägg till en åtgärd** (visas när du har valt plus tecknet).

   c. I rutan Sök anger du *Flat* för att filtrera alla åtgärder till den som du vill använda.

   d. Välj alternativet **flat fil avkodning** i listan.   

      ![Skärm bild av alternativ för flat fil avkodning](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Välj **innehålls** kontrollen. Detta skapar en lista med innehållet från tidigare steg som du kan använda som innehåll för att avkoda. Observera att *bröd texten* från den inkommande HTTP-begäran är tillgänglig att användas som innehåll att avkoda. Du kan också ange innehållet att avkoda direkt i **innehålls** kontrollen.     

1. Välj *Body* -taggen. Observera att bröd text tag gen nu finns i **innehålls** kontrollen.

1. Välj namnet på det schema som du vill använda för att avkoda innehållet. Följande skärm bild visar att *OrderFile* är det valda schema namnet. Schema namnet hade laddats upp till integrations kontot tidigare.

   ![Skärm bild av dialog rutan flat fil avkodning](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png) 

1. Spara ditt arbete.  

   ![Skärm bild av ikonen Spara](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

Nu är du färdig med konfigurationen av den flata fil avkodnings kopplingen. I ett verkligt världs program kanske du vill lagra de avkodade data i ett branschspecifika program, till exempel Salesforce. Du kan enkelt lägga till en åtgärd för att skicka utdata från avkodnings åtgärden till Salesforce.

Nu kan du testa din anslutning genom att göra en begäran till HTTP-slutpunkten och inkludera det XML-innehåll som du vill avkoda i bröd texten i begäran.  

## <a name="next-steps"></a>Nästa steg
* [Läs mer om Enterprise-integrationspaket](logic-apps-enterprise-integration-overview.md "Läs mer om Enterprise-integrationspaket").  

