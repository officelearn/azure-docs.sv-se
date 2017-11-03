---
title: Koda eller avkoda flat-filer i Azure logikappar | Microsoft Docs
description: "Hur du använder filen fil-kodaren eller avkodarens i Enterprise-Integrationspaket i dina logic apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 82152dab-c7ad-43df-b721-596559703be8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; mandia
ms.openlocfilehash: bc3430624844cdeb92958433fba295f67a8ae0ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-enterprise-integration-with-flat-files"></a>Översikt över enterprise integration med flat-filer

Du kanske vill koda XML-innehåll innan du skickar det till ett partnerföretag i ett scenario för business-to-business (B2B). Du kan använda enkla filkodningen koppling för att göra detta i en logikapp. Logikappen som du skapar kan hämta dess XML innehåll från en mängd olika källor, till exempel från en HTTP-begäran-utlösare, från ett annat program eller även från en av många [kopplingar](../connectors/apis-list.md). Mer information om logikappar finns i [logic apps dokumentationen](logic-apps-what-are-logic-apps.md "Lär dig mer om logikappar").  

## <a name="create-the-flat-file-encoding-connector"></a>Skapa enkla filkodningen connector
Följ dessa steg för att lägga till en flat-fil kodning koppling till din logikapp.

1. Skapa en logikapp och [länka till ditt konto integration](logic-apps-enterprise-integration-accounts.md "Lär dig hur du länkar ett integration konto till en logikapp"). Det här kontot innehåller schema som du använder för att koda XML-data.  
2. Lägg till en **begäran - när en HTTP-begäran tas emot** utlösaren till din logikapp.  
   ![Skärmbild av utlösare och välj](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
3. Lägg till flat filkodningen åtgärd, enligt följande:
   
    a. Välj den **plus** tecken.
   
    b. Välj den **lägga till en åtgärd** länk (visas när du har valt på plustecknet).
   
    c. I sökrutan anger *Flat* att filtrera alla åtgärder som du vill använda.
   
    d. Välj den **Flat Filkodning** i listan.   
   ![Skärmbild av Flat fil kodning](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
4. På den **Flat Filkodning** dialogrutan markerar du den **innehåll** textruta.  
   ![Skärmbild av innehåll textruta](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  
5. Välj body-tagg som det innehåll som du vill koda. Body-taggen ska fylla i fältet content.     
   ![Skärmbild av body-tagg](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  
6. Välj den **schemanamnet** listruta och välj det schema som du vill använda för att koda indata innehållet.    
   ![Skärmbild av schemanamnet listruta](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  
7. Spara ditt arbete.   
   ![Skärmbild av spara ikon](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

Nu är du klar med att ställa in din kodning anslutningstjänst flat-fil. I ett verkligt program kanske du vill lagra kodade data i en line-of-business-program, till exempel Salesforce. Eller så kan du skicka att kodade data till en handel partner. Du kan enkelt lägga till en åtgärd för att skicka utdata från åtgärden kodning till Salesforce eller till din handelspartner genom att använda någon av de kopplingar som tillhandahålls.

Nu kan du testa din koppling genom att göra en begäran till HTTP-slutpunkten och inklusive XML-innehåll i brödtexten i begäran.  

## <a name="create-the-flat-file-decoding-connector"></a>Skapa flat fil avkoda connector

> [!NOTE]
> Du måste ha en schemafil redan överförts till integration konto för att slutföra de här stegen.

1. Lägg till en **begäran - när en HTTP-begäran tas emot** utlösaren till din logikapp.  
   ![Skärmbild av utlösare och välj](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
2. Lägg till flat fil avkoda åtgärd, enligt följande:
   
    a. Välj den **plus** tecken.
   
    b. Välj den **lägga till en åtgärd** länk (visas när du har valt på plustecknet).
   
    c. I sökrutan anger *Flat* att filtrera alla åtgärder som du vill använda.
   
    d. Välj den **Flat fil avkoda** i listan.   
   ![Skärmbild av Flat fil avkoda alternativet](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
3. Välj den **innehåll** kontroll. Detta genererar en lista med innehållet från tidigare steg som du kan använda som innehållet för att avkoda. Observera att den *brödtext* från inkommande HTTP begäran är tillgängliga som ska användas som innehållet för att avkoda. Du kan också ange innehåll att avkoda direkt i den **innehåll** kontroll.     
4. Välj den *brödtext* tagg. Observera body-taggen är nu i den **innehåll** kontroll.
5. Välj namnet på det schema som du vill använda för att dekryptera innehållet. Följande skärmbild visar att *OrderFile* är valda schemanamnet. Den här schemanamnet har överförts hänsyn integration tidigare.
   
   ![Skärmbild av Flat fil avkodning av dialogrutan](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png)    
6. Spara ditt arbete.  
   ![Skärmbild av spara ikon](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

Nu är du klar med att ställa in din flat fil avkodning av anslutningen. I ett verkligt program kanske du vill lagra kodade data i ett line-of-business-program, till exempel Salesforce. Du kan enkelt lägga till en åtgärd för att skicka utdata från åtgärden avkodning till Salesforce.

Nu kan du testa din koppling genom att göra en begäran för HTTP-slutpunkten och inkludera det XML-innehåll som du vill avkoda i brödtexten i begäran.  

## <a name="next-steps"></a>Nästa steg
* [Mer information om Enterprise-Integrationspaket](logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket").  

