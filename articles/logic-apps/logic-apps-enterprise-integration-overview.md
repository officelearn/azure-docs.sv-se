---
title: Enterprise-Integration för B2B - Azure Logic Apps | Microsoft Docs
description: Skapa arbetsflöden för B2B och stöd för enterprise integrationsscenarier för logic apps med Enterprise-Integrationspaketet
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: jeconnoc
editor: cgronlun
ms.assetid: dd517c4d-1701-4247-b83c-183c4d8d8aae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: c4f83d2bf082b5f1358b4eee6468d470b5cb8088
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42059577"
---
# <a name="overview-b2b-scenarios-and-communication-with-the-enterprise-integration-pack"></a>Översikt: B2B-scenarier och kommunikation med Enterprise-Integrationspaketet

Du kan aktivera scenarion för enterprise-integration med Microsofts molnbaserade lösning, Enterprise-Integrationspaketet för business-to-business (B2B) arbetsflöden och sömlös kommunikation med Azure Logic Apps. Organisationer kan utbyta meddelanden elektroniskt, även om de använder olika protokoll och format. Paketet omvandlar olika format till ett format som organisationers system kan tolka och bearbeta. Organisationer kan utbyta meddelanden via vanliga protokoll, inklusive [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md), och [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). Du kan även skydda meddelanden med både kryptering och digitala signaturer.

Om du är bekant med BizTalk Server eller Microsoft Azure BizTalk Services, är Enterprise-Integration-funktioner enkla att använda eftersom de flesta principerna är liknande. En stor skillnad är att Företagsintegrering använder integrationskonton för att förenkla lagring och hantering av artefakter som används i B2B-kommunikation. 

Enterprise-Integrationspaketet är arkitektoniskt sett likadant utifrån ”integrationskonton”. Dessa konton är molnbaserad behållare som lagrar alla dina artefakter, t.ex. scheman, partner, certifikat, kartor och avtal. Du kan använda dessa artefakter att utforma, distribuera och underhålla dina B2B-appar och att skapa B2B arbetsflöden för logikappar. Men innan du kan använda dessa artefakter, måste du först koppla din integrationskontot till logikappen. Efter det logikappen kan komma åt din integration account artefakter.

## <a name="why-should-you-use-enterprise-integration"></a>Varför ska du använda enterprise-integration?

* Du kan lagra alla artefakter i ett och samma ställe--ditt integrationskonto med enterprise-integration.
* Du kan skapa B2B-arbetsflöden och integrera med tredje parts programvara som tjänst (SaaS)-appar, lokala appar och anpassade appar med hjälp av Azure Logic Apps-motorn och alla dess kopplingar.
* Du kan skapa anpassad kod för dina logikappar med Azure functions.

## <a name="how-to-get-started-with-enterprise-integration"></a>Hur du kommer igång med enterprise-integration?

Du kan skapa och hantera B2B-appar med Enterprise-Integrationspaketet via Logic App Designer i den **Azure-portalen**. Du kan också hantera dina logikappar med [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp "Logic apps PowerShell").

Här är de övergripande stegen som du måste vidta innan du kan skapa appar i Azure portal:

![Översiktsbild](media/logic-apps-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>Vilka är några vanliga scenarier?

Enterprise-Integration stöder dessa branschstandarder:

* EDI - Electronic Data Interchange
* EAI - integrering av företagsprogram

## <a name="heres-what-you-need-to-get-started"></a>Här är vad du behöver att komma igång

* En Azure-prenumeration med ett integrationskonto
* Visual Studio 2015 att skapa kartor och scheman
* [Microsoft Azure Logic Apps Enterprise-Integration Tools för Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it-now"></a>Prova nu

[Distribuera en fullt fungerande exempel AS2 skicka och ta emot logikapp](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) som använder B2B-funktioner för Azure Logic Apps.

## <a name="learn-more"></a>Läs mer
* [Avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md "Lär dig mer om enterprise integration-avtal")
* [Scenarier för Business-to-Business (B2B)](../logic-apps/logic-apps-enterprise-integration-b2b.md "Lär dig hur du skapar logikappar med B2B-funktioner ")  
* [Certifikat](logic-apps-enterprise-integration-certificates.md "Lär dig mer om integrering av företagscertifikat")
* [För platt fil kodning/avkodning](logic-apps-enterprise-integration-flatfile.md "Lär dig att koda och avkoda flat filinnehållet")  
* [Integrationskonton](../logic-apps/logic-apps-enterprise-integration-accounts.md "Lär dig mer om integrationskonton")
* [Mappar](../logic-apps/logic-apps-enterprise-integration-maps.md "Lär dig mer om maps för enterprise-integration")
* [Partner](logic-apps-enterprise-integration-partners.md "Lär dig mer om enterprise integration-partners")
* [Scheman](logic-apps-enterprise-integration-schemas.md "Lär dig mer om scheman för enterprise-integration")
* [XML-verifiering för meddelande](logic-apps-enterprise-integration-xml.md "Lär dig hur du verifierar XML-meddelanden med Logic apps")
* [XML-transformering](logic-apps-enterprise-integration-transform.md "Lär dig mer om maps för enterprise-integration")
* [Enterprise Integration-kopplingar](../connectors/apis-list.md "Lär dig mer om enterprise integration pack-kopplingar")
* [Integrering konto Metadata](../logic-apps/logic-apps-enterprise-integration-metadata.md "Lär dig mer om integrering konto metadata")
* [Övervaka B2B-meddelanden](logic-apps-monitor-b2b-message.md "Läs mer om hur du övervakar B2B-meddelanden")
* [Spåra B2B-meddelanden i OMS-portalen](logic-apps-track-b2b-messages-omsportal.md "Läs mer om att spåra B2B-meddelanden i OMS-portalen")

