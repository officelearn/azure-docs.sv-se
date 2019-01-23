---
title: Hantera integration konto artefaktmetadata – Azure Logic Apps | Microsoft Docs
description: Lägga till eller hämta artefaktmetadata för från integrationskonton i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 01/17/2019
ms.openlocfilehash: 5ebdf45bec4e7cfceb75354af40c7a21c22c6eef
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446790"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Hantera artefaktmetadata i konton för integrering med Azure Logic Apps och Enterprise-Integrationspaket

Du kan definiera anpassade metadata för artefakter i integrationskonton och få dem under körning för din logikapp att använda. Du kan till exempel ange metadata för artefakter, till exempel partners, avtal, scheman och maps - alla lagra metadata med hjälp av nyckel / värde-par. 

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

* En grundläggande [integrationskontot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som har artefakterna där du vill lägga till metadata, till exempel: 

  * [Partner](logic-apps-enterprise-integration-partners.md)
  * [Avtal](logic-apps-enterprise-integration-agreements.md)
  * [schemat](logic-apps-enterprise-integration-schemas.md)
  * [Karta](logic-apps-enterprise-integration-maps.md)

* En logikapp som är kopplad till metadata för integration-kontot och artefakt som du vill använda. Lär dig mer om logikappen inte är redan länkat måste [så här länkar du logikappar till integrationskonton](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

  Om du inte har en logikapp ännu kan du lära dig [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Lägg till utlösare och åtgärder som du vill använda för att hantera artefaktmetadata. Eller om du vill testa saker, lägger du till en utlösare som **begära** eller **HTTP** i logikappen.

## <a name="add-metadata-to-artifacts"></a>Lägga till metadata artefakter

1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure Portal</a> med autentiseringsuppgifterna för ditt Azure-konto. Hitta och öppna ditt integrationskonto.

1. Välj artefakten där du vill lägga till metadata och välj **redigera**. Ange metadata-information för den artefakten, till exempel:

   ![Ange metadata](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. När du är klar väljer du **OK**.

1. Välj för att visa dessa metadata i JavaScript Object Notation (JSON)-definitionen för integrationskontot **redigera som JSON** så att JSON-redigerare öppnas: 

   ![JSON för partner metadata](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Hämta artefaktmetadata för

1. Öppna logikappen som är länkad till integrationskontot som du vill i Azure-portalen. 

1. Om du vill lägga till steg för att hämta metadata under utlösaren eller senaste åtgärd i arbetsflödet i Logic App Designer välja **nytt steg** > **Lägg till en åtgärd**. 

1. I sökrutan anger du ”integrationskontot”. Under sökrutan väljer du **Alla**. Välj den här åtgärden från åtgärdslistan över: **Integrering sökning efter Integrationskontoartefakt - Integrationskonto**

   ![Välj ”Integration sökning efter Integrationskontoartefakt”](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Ange den här informationen för artefakten du vill söka efter:

   | Egenskap  | Krävs | Värde | Beskrivning | 
   |----------|---------|-------|-------------| 
   | **Typ av artefakt** | Ja | **Schemat**, **kartan**, **Partner**, **avtal**, eller en anpassad typ | Typen för artefakten du vill | 
   | **Artefaktnamn** | Ja | <*sammansättningsartefaktens namn*> | Namnet på den artefakt som du vill | 
   ||| 

   Anta exempelvis att du vill hämta metadata för en handel partner-artefakt:

   ![Välj typ av artefakt och ange sammansättningsartefaktens namn](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Lägg till den åtgärd som du vill för att hantera dessa metadata, till exempel:

   1. Under den **integrering sökning efter Integrationskontoartefakt** åtgärd, Välj **nästa steg**, och välj **Lägg till en åtgärd**. 

   1. I sökrutan anger du ”http”. Under sökrutan väljer **Built-ins**, och välj den här åtgärden: **HTTP - HTTP**

      ![Lägg till HTTP-åtgärd](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Ange information för artefaktmetadata som du vill hantera. 

      Anta exempelvis att du vill hämta den `routingUrl` metadata som har lagts till tidigare i det här avsnittet. Här följer de egenskapsvärden som du kan ange: 

      | Egenskap  | Krävs | Värde | Beskrivning | 
      |----------|----------|-------|-------------| 
      | **Metod** | Ja | <*operation-to-run*> | HTTP-åtgärd ska köras på artefakten. Den här HTTP-åtgärden använder exempelvis den **hämta** metod. | 
      | **URI** | Ja | <*metadata-location*> | Åtkomst till den `routingUrl` metadata värde från artefakten som du hämtade, du kan använda ett uttryck, till exempel: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Headers** | Nej | <*huvud-värden*> | Alla rubriker matar ut från utlösaren som du vill skicka till HTTP-åtgärder. Till exempel att skicka in utlösarens `headers` egenskapsvärdet: du kan använda ett uttryck, till exempel: <p>`@triggeroutputs()['headers']` | 
      | **Brödtext** | Nej | <*body-content*> | Annat innehåll som du vill skicka via HTTP-åtgärden `body` egenskapen. Det här exemplet överför artefakten `properties` värden till HTTP-åtgärder: <p>1. Klicka i den **brödtext** egenskapen så att den dynamiska innehållslistan visas. Om inga egenskaper visas väljer du **mer**. <br>2. Från den dynamiska innehållslistan under **integrering sökning efter Integrationskontoartefakt**väljer **egenskaper**. | 
      |||| 

      Exempel:

      ![Ange värden och uttryck för HTTP-åtgärd](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. Kontrollera informationen för HTTP-åtgärden genom att visa logikappens JSON-definition. Välj Logic App Designer-verktygsfältet **Kodvyer** så att appens JSON-definition visas till exempel:

      ![Logic app JSON-definition](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      När du växlar tillbaka till Logic App Designer visas alla uttryck som du använde nu åtgärdats, till exempel:

      ![Löst uttryck i Logic App Designer](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Nästa steg

* [Mer information om avtalen](logic-apps-enterprise-integration-agreements.md)
