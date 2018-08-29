---
title: XML-meddelanden för B2B enterprise-integration – Azure Logic Apps | Microsoft Docs
description: Bearbeta, verifiera, omvandla och berika XML-meddelanden för B2B-lösningar i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.date: 02/27/2017
ms.openlocfilehash: 5f804bf656b423d6dbe8f5c2ed73ba54d8361000
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124543"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML-meddelanden och flata filer i Azure Logic Apps med Enterprise-Integrationspaket

Med logikappar kan ha du möjlighet att bearbeta XML-meddelanden som du skickar och tar emot. Den här funktionen ingår i Enterprise-Integrationspaketet. För de användarna som har en bakgrund för BizTalk Server ger Enterprise-Integrationspaketet liknande förmågan att omvandla och validera meddelanden kan arbeta med flata filer och även använda XPath för att utöka eller extrahera specifika egenskaper från ett meddelande. 

För de användare som inte har använt det här utrymmet, expandera funktionerna hur du bearbetar meddelanden i ditt arbetsflöde. Till exempel om du är i ett scenario för business-to-business och arbeta med specifika XML-scheman kan du använda Enterprise-Integrationspaketet för att förbättra hur ditt företag bearbetar dessa meddelanden. 

Enterprise-Integrationspaketet innehåller: 

* [XML-verifiering](logic-apps-enterprise-integration-xml-validation.md "Lär dig mer om XML-verifiering för meddelande") -validera en XML-meddelande för inkommande eller utgående mot ett visst schema.
* [XML-transformering](../logic-apps/logic-apps-enterprise-integration-transform.md "Lär dig mer om XML-meddelande transformeringar och kartor") – konvertera eller anpassa en XML-meddelande utifrån dina behov eller kraven för en partner.
* [Fast Filkodning och flatfilsavkodning](logic-apps-enterprise-integration-flatfile.md "Lär dig mer om kodning/flatfilsavkodning") – koda eller avkoda en platt fil. Till exempel SAP accepterar och skickar IDOC-filer i platt format. Många integration plattformar skapa XML-meddelanden, inklusive Logic Apps. Därför kan du skapa en logikapp som använder den flat fil-kodaren ”konvertera” XML-filer till flat-filer. 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) – ett meddelande extrahera information och specifika egenskaper från meddelandet. Du kan sedan använda de extrahera egenskaperna för att dirigera meddelandet till ett mål, eller en mellanliggande slutpunkt.

## <a name="try-it-out"></a>Prova
[Distribuera en fullt fungerande logikapp ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (GitHub-prov) med hjälp av XML-funktionerna i Azure Logic Apps.

## <a name="learn-more"></a>Läs mer
[Mer information om Enterprise-Integrationspaketet](../logic-apps/logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")
