---
title: "Enterprise-integrering för B2B - Azure Logikappar | Microsoft Docs"
description: "Skapa B2B-arbetsflöden och stöder enterprise integrationsscenarier för logic apps med Enterprise-Integrationspaket"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: dd517c4d-1701-4247-b83c-183c4d8d8aae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 9462707db03ecfcc3d5186ce7ded8655ad3bdcc9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="overview-b2b-scenarios-and-communication-with-the-enterprise-integration-pack"></a>Översikt: B2B-scenarier och kommunikation med Enterprise-Integrationspaket

Du kan aktivera företagsscenarier integration med Microsofts molnbaserad lösning, Enterprise-Integrationspaket för business-to-business (B2B) arbetsflöden och sömlös kommunikation med Azure Logikappar. Organisationer kan utbyta meddelanden elektroniskt, även om de använder olika protokoll och format. Packet transformerar olika format till ett format som organisationers system kan tolka och bearbeta. Organisationer kan utbyta meddelanden via standardiserade protokoll, inklusive [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md), och [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). Du kan även skydda meddelanden med både kryptering och digitala signaturer.

Om du är bekant med BizTalk Server eller BizTalk-tjänst för Microsoft Azure är Enterprise Integration-funktioner enkla att använda eftersom de flesta principerna är liknande. En största skillnaden är att Enterprise Integration använder integrationskonton för att förenkla lagring och hantering av artefakter som används i B2B-kommunikation. 

Enterprise-Integrationspaket är arkitektoniskt sett likadant baserat på ”integrationskonton”. Dessa konton är molnbaserad behållare som lagrar alla dina artefakter som scheman, partner, certifikat, kartor och avtal. Du kan använda dessa artefakter att utforma, distribuera och underhålla dina B2B-appar och även skapa B2B arbetsflöden för logic apps. Men innan du kan använda dessa artefakter, måste du först länka ditt integration konto till din logikapp. Efter det logikappen kan komma åt ditt konto integration artefakter.

## <a name="why-should-you-use-enterprise-integration"></a>Varför ska du använda enterprise integration?

* Du kan lagra alla artefakter på ett ställe – kontot för integrering med enterprise-integration.
* Du kan skapa B2B-arbetsflöden och integrera med tredje parts programvara som tjänst (SaaS)-appar, lokala appar och anpassade program med Azure Logikappar-motorn och alla kopplingar.
* Du kan skapa anpassade kod för dina logic apps med Azure functions.

## <a name="how-to-get-started-with-enterprise-integration"></a>Hur du kommer igång med enterprise integration?

Du kan skapa och hantera B2B-appar med Enterprise-Integrationspaket via logik App Designer i den **Azure-portalen**. Du kan också hantera dina logic apps med [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "Logic apps PowerShell-artiklar").

Här följer de övergripande stegen som du måste vidta innan du kan skapa appar i Azure-portalen:

![Översikt över bild](media/logic-apps-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>Vilka är några vanliga scenarier?

Enterprise Integration stöder dessa branschstandarder:

* EDI - elektroniskt datautbyte
* EAI - Integration av företagsprogram

## <a name="heres-what-you-need-to-get-started"></a>Här är vad du behöver att komma igång

* En Azure-prenumeration med ett konto för integrering
* Visual Studio 2015 att skapa mappningar och scheman
* [Microsoft Azure Logic Apps Enterprise Integration Tools för Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it-now"></a>Prova nu

[Distribuera en fullt fungerande exempel AS2 skicka och ta emot logikapp](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) som använder B2B-funktioner för Azure Logic Apps.

## <a name="learn-more"></a>Läs mer
* [Avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md "Lär dig mer om enterprise integration-avtal")
* [Scenarier för Business to Business (B2B)](../logic-apps/logic-apps-enterprise-integration-b2b.md "Lär dig att skapa logikappar med B2B-funktioner")  
* [Certifikat](logic-apps-enterprise-integration-certificates.md "Lär dig mer om integrering av företagscertifikat")
* [Flat fil kodning/avkodning](logic-apps-enterprise-integration-flatfile.md "Lär dig hur du kodar och avkodar flat filinnehållet")  
* [Integrationskonton](../logic-apps/logic-apps-enterprise-integration-accounts.md "Lär dig mer om integrationskonton")
* [Mappar](../logic-apps/logic-apps-enterprise-integration-maps.md "Lär dig mer om enterprise integration maps")
* [Partners](logic-apps-enterprise-integration-partners.md "Lär dig mer om enterprise integration-partner")
* [Scheman](logic-apps-enterprise-integration-schemas.md "Lär dig mer om enterprise integration-scheman")
* [XML-meddelandevalidering](logic-apps-enterprise-integration-xml.md "Lär dig hur du verifierar XML-meddelanden med Logic apps")
* [XML-transformeringen](logic-apps-enterprise-integration-transform.md "Lär dig mer om enterprise integration maps")
* [Enterprise Integration-kopplingar](../connectors/apis-list.md "Lär dig mer om enterprise integration pack-kopplingar")
* [Integration konto Metadata](../logic-apps/logic-apps-enterprise-integration-metadata.md "Lär dig mer om integrering konto metadata")
* [Övervaka B2B-meddelanden](logic-apps-monitor-b2b-message.md "Lär dig mer om hur du övervakar B2B-meddelanden")
* [Spåra B2B-meddelanden i OMS-portalen](logic-apps-track-b2b-messages-omsportal.md "Lär dig mer om hur du spårar B2B-meddelanden i OMS-portalen")

