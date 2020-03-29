---
title: Hantera artefaktersmetadata för integrationskonto
description: Lägga till eller hämta artefaktmetadata från integrationskonton i Azure Logic Apps med Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/17/2019
ms.openlocfilehash: bc119f1ce8efb821781dabfb9dd259cc5c8d9c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792466"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Hantera artefaktmetadata i integrationskonton med Azure Logic Apps och Enterprise Integration Pack

Du kan definiera anpassade metadata för artefakter i integrationskonton och hämta metadata under körning för din logikapp att använda. Du kan till exempel ange metadata för artefakter, till exempel partner, avtal, scheman och kartor – alla lagrar metadata med hjälp av nyckelvärdespar. 

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

* Ett grundläggande [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som har de artefakter där du vill lägga till metadata, till exempel: 

  * [Partner](logic-apps-enterprise-integration-partners.md)
  * [Avtal](logic-apps-enterprise-integration-agreements.md)
  * [Schema](logic-apps-enterprise-integration-schemas.md)
  * [Karta](logic-apps-enterprise-integration-maps.md)

* En logikapp som är länkad till det integrationskonto och artefaktmetadata som du vill använda. Om logikappen inte redan är länkad kan du läsa om hur du [länkar logikappar till integrationskonton](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

  Om du inte har någon logikapp ännu kan du läsa om hur du [skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Lägg till utlösaren och de åtgärder som du vill använda för att hantera artefaktmetadata. Om du bara vill prova saker kan du lägga till en utlösare som **Begär** eller **HTTP** i logikappen.

## <a name="add-metadata-to-artifacts"></a>Lägga till metadata i artefakter

1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure Portal</a> med dina Azure-kontoautentiseringsuppgifter. Hitta och öppna ditt integrationskonto.

1. Markera den artefakt där du vill lägga till metadata och välj **Redigera**. Ange metadatainformation för den artefakten, till exempel:

   ![Ange metadata](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. När du är klar väljer du **OK**.

1. Om du vill visa dessa metadata i JSON-definitionen (JavaScript Object Notation) för integrationskontot väljer du **Redigera som JSON** så att JSON-redigeraren öppnas: 

   ![JSON för partnermetadata](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Hämta artefaktmetadata

1. Öppna logikappen som är länkad till det integrationskonto du vill ha i Azure-portalen. 

1. Om du lägger till steget för att hämta metadata under utlösaren eller den senaste åtgärden i arbetsflödet i Logic App Designer väljer du **Nytt steg** > **Lägg till en åtgärd**. 

1. Skriv "integrationskonto" i sökrutan. Under sökrutan väljer du **Alla**. Välj den här åtgärden i åtgärdslistan: **Integrationskontoartefaktsökning - Integrationskonto**

   ![Välj "Uppslag på artefakt för integrationskonto"](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Ange den här informationen för den artefakt som du vill hitta:

   | Egenskap | Krävs | Värde | Beskrivning | 
   |----------|---------|-------|-------------| 
   | **Artefakttyp** | Ja | **Schema,** **Karta,** **Partner,** **Avtal**eller en anpassad typ | Typen för den artefakt du vill använda | 
   | **Artefaktnamn** | Ja | <*artefaktnamn*> | Namnet på den artefakt du vill ha | 
   ||| 

   Anta till exempel att du vill hämta metadata för en handelspartnerartefakt:

   ![Välj artefakttyp och ange artefaktnamn](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Lägg till den åtgärd som du vill använda för att hantera metadata, till exempel:

   1. Under åtgärden **Uppslagning av integrationskontot** väljer du **Nästa steg**och väljer Lägg till **en åtgärd**. 

   1. Skriv "http" i sökrutan. Under sökrutan väljer du **Inbyggda**och väljer den här åtgärden: **HTTP - HTTP**

      ![Lägg till HTTP-åtgärd](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Ange information för de artefaktmetadata som du vill hantera. 

      Anta till exempel att du `routingUrl` vill hämta de metadata som har lagts till tidigare i det här avsnittet. Här är egenskapsvärdena som du kan ange: 

      | Egenskap | Krävs | Värde | Beskrivning | 
      |----------|----------|-------|-------------| 
      | **Metod** | Ja | <*operation-till-kör*> | HTTP-åtgärden som ska köras på artefakten. Den här HTTP-åtgärden använder till exempel **GET-metoden.** | 
      | **Uri** | Ja | <*metadata-plats*> | Om du `routingUrl` vill komma åt metadatavärdet från artefakten som du hämtade kan du använda ett uttryck, till exempel: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Rubriker** | Inga | <*rubrikvärden*> | Alla huvudutdata från utlösaren som du vill skicka till HTTP-åtgärden. Om du till exempel vill `headers` skicka in utlösarens egenskapsvärde: du kan använda ett uttryck, till exempel: <p>`@triggeroutputs()['headers']` | 
      | **Brödtext** | Inga | <*kroppsinnehåll*> | Allt annat innehåll som du vill skicka `body` genom HTTP-åtgärdens egenskap. I det här exemplet `properties` skickas artefaktens värden till HTTP-åtgärden: <p>1. Klicka inuti egenskapen **Body** så att listan med dynamiskt innehåll visas. Om inga egenskaper visas väljer du **Visa mer**. <br>2. Välj **Egenskaper**under Granskning av **integrationskontoartefakter under Integrationskontoartefakt.** | 
      |||| 

      Ett exempel:

      ![Ange värden och uttryck för HTTP-åtgärd](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. Om du vill kontrollera informationen som du angav för HTTP-åtgärden kan du visa logikappens JSON-definition. I verktygsfältet Logikappdesigner väljer du **Kodvyn** så att appens JSON-definition visas, till exempel:

      ![JSON-definition av logikapp](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      När du har växlat tillbaka till Logic App Designer visas alla uttryck som du har använt nu lösta, till exempel:

      ![Lösta uttryck i Logic App Designer](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Nästa steg

* [Läs mer om avtal](logic-apps-enterprise-integration-agreements.md)
