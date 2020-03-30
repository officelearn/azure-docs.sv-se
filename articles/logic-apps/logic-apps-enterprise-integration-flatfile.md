---
title: Koda eller avkoda platta filer
description: Koda eller avkoda platta filer för företagsintegrering med Azure Logic Apps och Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 2d6182ba01507c2fb361628e01bb52e1ea821f44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152660"
---
# <a name="encode-or-decode-flat-files-with-azure-logic-apps-and-enterprise-integration-pack"></a>Koda eller avkoda platta filer med Azure Logic Apps och Enterprise Integration Pack

Du kanske vill koda XML-innehåll innan du skickar det till en affärspartner i ett B2B-scenario (Business-to-Business). I en logikapp kan du använda den platta filkodningsappen för att göra detta. Logikappen som du skapar kan hämta xml-innehåll från en mängd olika källor, bland annat från en HTTP-begärandeutlösare, från ett annat program eller till och med från en av de många [anslutningsapparna](../connectors/apis-list.md). Mer information om logikappar finns i dokumentationen för [logikappar](logic-apps-overview.md "Läs mer om Logic-appar").  

## <a name="create-the-flat-file-encoding-connector"></a>Skapa den platta filkodningsappen
Följ dessa steg för att lägga till en platt filkodningsappar i logikappen.

1. Skapa en logikapp och [länka den till ditt integrationskonto](logic-apps-enterprise-integration-accounts.md "Lär dig att länka ett integrationskonto till en Logic-app"). Det här kontot innehåller det schema som du ska använda för att koda XML-data.  

1. Lägg till **utlösaren När en HTTP-begäran tas emot** i logikappen i Logic App Designer.

1. Lägg till den platta filkodningsåtgärden enligt följande:

   a. Välj **plustecknet.**

   b. Markera länken **Lägg till en åtgärd** (visas när du har valt plustecknet).

   c. I sökrutan anger du *Platt* för att filtrera alla åtgärder till den du vill använda.

   d. Välj alternativet **Filkodning** i listan.   

      ![Skärmbild av alternativet Filkodning](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Markera textrutan **Innehåll** i dialogrutan **Platt filkodning.**  

   ![Skärmbild av textrutan Innehåll](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  

1. Markera brödtexttaggen som det innehåll som du vill koda. Brödtexttaggen fyller i innehållsfältet.     

   ![Skärmbild av kroppstagg](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  

1. Markera listrutan **Schemanamn** och välj det schema som du vill använda för att koda indatainnehållet.    

   ![Skärmbild av listrutan Schemanamn](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  

1. Spara ditt arbete.

   ![Skärmbild av ikonen Spara](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

Nu är du klar med att konfigurera den platta filkodningskopplingen. I ett verkligt program kanske du vill lagra kodade data i ett affärsprogram, till exempel Salesforce. Eller så kan du skicka kodade data till en handelspartner. Du kan enkelt lägga till en åtgärd för att skicka utdata från kodningsåtgärden till Salesforce, eller till din handelspartner, genom att använda någon av de andra kopplingarna som tillhandahålls.

Du kan nu testa anslutningen genom att göra en begäran till HTTP-slutpunkten och inkludera XML-innehållet i brödtexten i begäran.  

## <a name="create-the-flat-file-decoding-connector"></a>Skapa den platta filkodningskopplingen

> [!NOTE]
> För att kunna utföra de här stegen måste du ha en schemafil som redan har överförts till ditt integrationskonto.

1. Lägg till **utlösaren När en HTTP-begäran tas emot** i logikappen i Logic App Designer.

1. Lägg till den platta filavkodningsåtgärden enligt följande:

   a. Välj **plustecknet.**

   b. Markera länken **Lägg till en åtgärd** (visas när du har valt plustecknet).

   c. I sökrutan anger du *Platt* för att filtrera alla åtgärder till den du vill använda.

   d. Välj alternativet **Avkodning av blankfil** i listan.   

      ![Skärmbild av alternativet Avkodning av platta filer](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Markera **innehållskontrollen.** Då skapas en lista över innehållet från tidigare steg som du kan använda som innehåll för att avkoda. Observera att *brödtexten* från den inkommande HTTP-begäran är tillgänglig för att användas som innehåll för avkodning. Du kan också ange innehållet för att avkoda direkt i **innehållskontrollen.**     

1. Markera *Body* body-taggen. Lägg märke till att brödtexttaggen nu finns i **innehållskontrollen.**

1. Välj namnet på det schema som du vill använda för att avkoda innehållet. Följande skärmbild visar att *OrderFile* är det valda schemanamnet. Det här schemanamnet hade tidigare överförts till integrationskontot.

   ![Skärmbild av dialogrutan Avkodning av platt fil](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png) 

1. Spara ditt arbete.  

   ![Skärmbild av ikonen Spara](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

Nu är du klar med att konfigurera den platta filkodningskopplingen. I ett verkligt program kanske du vill lagra de avkodade data i ett affärsprogram som Salesforce. Du kan enkelt lägga till en åtgärd för att skicka utdata från avkodningsåtgärden till Salesforce.

Du kan nu testa kopplingen genom att göra en begäran till HTTP-slutpunkten och inkludera det XML-innehåll som du vill avkoda i brödtexten för begäran.  

## <a name="next-steps"></a>Nästa steg
* [Läs mer om Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Läs mer om Enterprise Integration Pack").  

