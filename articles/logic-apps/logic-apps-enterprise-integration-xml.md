---
title: Arbeta med XML-meddelanden i dina arbetsflöden - Azure Logic Apps | Microsoft Docs
description: Bearbeta, verifiera, transformera och utöka XML-meddelanden i logikappar och business-scenarier med Enterprise-Integrationspaket
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: jeconnoc
editor: ''
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/27/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 85bdaff5cb88bbadaed778458b66c0cce7bce9de
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298982"
---
# <a name="validate-and-transform-xml-encode-and-decode-flat-files-and-enrich-messages-features-in-logic-apps"></a>Validera och transformera XML, koda och avkoda flat-filer och utöka meddelanden funktioner i logic apps

Med logic apps kan ha du möjlighet att bearbeta XML-meddelanden som du skickar och tar emot. Den här funktionen ingår i Enterprise-Integrationspaket. Användare med bakgrund BizTalk Server ger Enterprise-Integrationspaket dig liknande förmåga att transformera och validera meddelanden kan arbeta med flat-filer och även använda XPath för att utöka eller extrahera specifika egenskaper från ett meddelande. 

För de användare som är nya i detta område, utöka dessa funktioner hur du bearbetar meddelanden i arbetsflödet. Till exempel om du är i ett scenario med business-to-business och arbeta med specifika XML-scheman kan du använda Enterprise-Integrationspaket för att förbättra hur företaget bearbetar dessa meddelanden. 

Enterprise-Integrationspaket innehåller: 

* [XML-verifiering](logic-apps-enterprise-integration-xml-validation.md "Lär dig mer om XML-meddelandevalidering") -validera en XML-meddelande för inkommande eller utgående mot ett visst schema.
* [XML-transformeringen](../logic-apps/logic-apps-enterprise-integration-transform.md "Lär dig mer om XML-meddelande omvandlingar och kartor") – konvertera eller anpassa en XML-meddelande utifrån dina behov eller kraven i en partner.
* [En platt Filkodning och flat fil avkodning](logic-apps-enterprise-integration-flatfile.md "Lär dig mer om kodning/avkodning flat fil") – koda eller avkoda en flat-fil. Till exempel SAP accepterar och skickar IDOC filer i platt format. Flera olika plattformar integration skapa XML-meddelanden, inklusive Logic Apps. Så kan du skapa en logikapp som använder flat fil-kodaren ”konvertera” XML-filer till flat-filer. 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) – utöka ett meddelande och extrahera specifika egenskaper från meddelandet. Du kan sedan använda de extraherade egenskaperna för att vidarebefordra meddelandet till en mål- eller en mellanliggande slutpunkt.

## <a name="try-it-out"></a>Prova
[Distribuera en fullt fungerande logikapp ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (GitHub-prov) med hjälp av XML-funktionerna i Logikappar i Azure.

## <a name="learn-more"></a>Läs mer
[Mer information om Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")
