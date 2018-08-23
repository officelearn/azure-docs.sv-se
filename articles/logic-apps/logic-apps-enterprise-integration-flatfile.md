---
title: Koda eller avkoda flata filer i Azure logic apps | Microsoft Docs
description: Hur du använder fil-kodaren eller avkodaren i Enterprise-Integrationspaketet i logic apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: jeconnoc
editor: ''
ms.assetid: 82152dab-c7ad-43df-b721-596559703be8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; divswa
ms.openlocfilehash: b13e8da04c984456027f152f5af63cfa6604ddc4
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054541"
---
# <a name="overview-of-enterprise-integration-with-flat-files"></a>Översikt över enterprise-integration med flata filer

Du kanske vill koda XML-innehållet innan du skickar den till en affärspartner i ett scenario för business-to-business (B2B). Du kan använda flat fil kodning connector för att göra detta i en logikapp. Logikappen som du skapar kan hämta dess XML innehåll från en mängd olika källor, till exempel från en HTTP-begäran-utlösare, från ett annat program eller även från en av många [anslutningsappar](../connectors/apis-list.md). Läs mer om logic apps, den [dokumentation om logic apps](logic-apps-overview.md "Lär dig mer om Logic apps").  

## <a name="create-the-flat-file-encoding-connector"></a>Skapa flat fil kodning connector
Följ dessa steg för att lägga till en platt fil kodning anslutningen till din logikapp.

1. Skapa en logikapp och [länkar den till ditt integrationskonto](logic-apps-enterprise-integration-accounts.md "Lär dig hur du länkar ett integrationskonto till en logikapp"). Det här kontot innehåller ett schema som du använder för att koda XML-data.  
1. Lägg till en **begäran – när en HTTP-begäran tas emot** utlösare i logikappen.  
   ![Skärmbild av utlösare för att välja](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
1. Lägg till flat fil kodning åtgärden, enligt följande:
   
    a. Välj den **plus** inloggning.
   
    b. Välj den **Lägg till en åtgärd** länk (visas när du har valt på plus-tecknet).
   
    c. I sökrutan anger *fast* att filtrera åtgärderna som ska det som du vill använda.
   
    d. Välj den **fast Filkodning** i listan.   
   ![Skärmbild av Flat fil kodning](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
1. På den **fast Filkodning** dialogrutan den **innehåll** textrutan.  
   ![Skärmbild av innehåll textrutan](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  
1. Välj brödtextstaggen som det innehåll som du vill koda. Brödtextstaggen fylls fältet innehåll.     
   ![Skärmbild av brödtextstaggen](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  
1. Välj den **schemanamn** rutan och välj schemat som du vill använda för att koda indata innehållet.    
   ![Skärmbild av schemanamnet listruta](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  
1. Spara ditt arbete.   
   ![Skärmbild av spara ikon](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

Nu är du klar att konfigurera din kodning flat fil-anslutningsapp. I en verklig tillämpning kanske du vill lagra kodade data i en line-of-business-program, till exempel Salesforce. Eller så kan du skicka att kodade data till en trading partner. Du kan enkelt lägga till en åtgärd för att skicka utdata från åtgärden kodning till Salesforce eller till din handelspartner genom att använda någon av de andra anslutningsappar som tillhandahålls.

Nu kan du testa din anslutningsapp genom att göra en begäran till HTTP-slutpunkten och inklusive XML-innehållet i brödtexten i begäran.  

## <a name="create-the-flat-file-decoding-connector"></a>Skapa flat fil avkodning connector

> [!NOTE]
> Du måste ha en schemafil som redan har överförts till du integrationskonto för att slutföra dessa steg.

1. Lägg till en **begäran – när en HTTP-begäran tas emot** utlösare i logikappen.  
   ![Skärmbild av utlösare för att välja](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
1. Lägg till flat fil avkodning åtgärden, enligt följande:
   
    a. Välj den **plus** inloggning.
   
    b. Välj den **Lägg till en åtgärd** länk (visas när du har valt på plus-tecknet).
   
    c. I sökrutan anger *fast* att filtrera åtgärderna som ska det som du vill använda.
   
    d. Välj den **avkodning av Flat fil** i listan.   
   ![Skärmbild av Flat fil avkodning alternativet](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
1. Välj den **innehåll** kontroll. Detta genererar en lista över innehåll från tidigare steg som du kan använda som innehållet ska avkodas. Observera att den *brödtext* från den inkommande HTTP förfrågan är tillgängliga som ska användas som innehållet ska avkodas. Du kan också ange det innehåll som ska avkoda direkt till den **innehåll** kontroll.     
1. Välj den *brödtext* tagg. Observera brödtextstaggen är nu i den **innehåll** kontroll.
1. Välj namnet på det schema som du vill använda för att dekryptera innehållet. I följande skärmbild visas som *OrderFile* är valda schemanamnet. Den här schemanamn hade tidigare har överförts till integrationskontot.
   
   ![Skärmbild av Flat fil avkodning dialogrutan](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png)    
1. Spara ditt arbete.  
   ![Skärmbild av spara ikon](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

Nu är du klar att konfigurera din flat fil avkodning connector. I en verklig tillämpning kanske du vill lagra Avkodade data i en line-of-business-program till exempel Salesforce. Du kan enkelt lägga till en åtgärd för att skicka utdata från åtgärden avkodning till Salesforce.

Nu kan du testa din anslutningsapp genom att göra en begäran till HTTP-slutpunkten och inkludera det XML-innehåll som du vill att avkoda i brödtexten i begäran.  

## <a name="next-steps"></a>Nästa steg
* [Mer information om Enterprise-Integrationspaketet](logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaketet").  

