---
title: XML-meddelanden och platta filer
description: Bearbeta, validera och omvandla XML-meddelanden i Azure Logic Apps med Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792147"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML-meddelanden och platta filer i Azure Logic Apps med Enterprise Integration Pack

I [Azure Logic Apps](logic-apps-overview.md)kan du bearbeta XML-meddelanden som du skickar och ta emot med enterprise integration pack. Om du har använt BizTalk Server innehåller Enterprise Integration Pack liknande funktioner för att omvandla och validera meddelanden, arbeta med platta filer och till och med använda XPath för att berika eller extrahera specifika egenskaper från ett meddelande. Om du inte har tidigare i det här utrymmet expanderar de här funktionerna hur du bearbetar meddelanden i logikappens arbetsflöde. Om du till exempel har ett B2B-scenario (Business-to-Business) och arbetar med specifika XML-scheman kan du använda Enterprise Integration Pack för att förbättra hur ditt företag behandlar dessa meddelanden.

Enterprise Integration Pack innehåller till exempel följande funktioner:

* [XML-validering](logic-apps-enterprise-integration-xml-validation.md): Validera ett inkommande eller utgående XML-meddelande mot ett visst schema.

* [XML-transformering](logic-apps-enterprise-integration-transform.md): Konvertera eller anpassa ett XML-meddelande baserat på dina krav eller en partners krav med hjälp av kartor.

* [Platt filkodning och platt filavkodning:](logic-apps-enterprise-integration-flatfile.md)Koda eller avkoda en platt fil.

  Sap accepterar och skickar till exempel IDOC-filer i platt filformat. Många integrationsplattformar skapar XML-meddelanden, inklusive Logic Apps. Så kan du skapa en logikapp som använder den platta filkodaren för att "konvertera" XML-filer till platta filer.

* [XPath](workflow-definition-language-functions-reference.md#xpath): Berika ett meddelande och extrahera specifika egenskaper från meddelandet. Du kan sedan använda de extraherade egenskaperna för att dirigera meddelandet till ett mål eller en mellanliggande slutpunkt.

## <a name="sample"></a>Exempel

[Distribuera en fullt fungerande logikapp](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (GitHub-exempel) med hjälp av XML-funktionerna i Azure Logic Apps.

## <a name="next-steps"></a>Nästa steg

Läs mer om [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
