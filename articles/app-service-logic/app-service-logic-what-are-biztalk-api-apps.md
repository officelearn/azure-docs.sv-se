<properties 
    pageTitle="Vad är kopplingar och BizTalk-API Apps" 
    description="Läs om API Apps, kopplingar och BizTalk API Apps" 
    services="logic-apps" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="07/28/2016" 
    ms.author="mandia"/>

# Vad är kopplingar och BizTalk-API Apps

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


En *koppling* är en typ av API-app som fokuserar på anslutningsbarhet. Precis som alla andra API Apps används kopplingar från webbappar, Mobile Apps och Logic Apps. Kopplingar gör det enkelt att ansluta till befintliga tjänster och bidrar till att hantera autentisering, tillhandahålla övervakning, analyser och mycket annat.

Alla utvecklare kan skapa sina egna API Apps och distribuera dem privat. I framtiden kommer utvecklare att kunna dela och tjäna pengar på sina egna anpassade API Apps på marknadsplatsen. 

![Marknadsplats för API Apps](./media/app-service-logic-what-are-biztalk-api-apps/Marketplace.png)

För att utvecklare snabbare ska kunna skapa lösningar har Azure-teamet lagt till ett antal anslutningsappar på marknadsplatsen som tillgodoser många vanliga scenarier. Om du vill utöka räckvidden till komplicerade och avancerade integrationsscenarier finns också ett antal Premium- och BizTalk-funktioner tillgängliga.

Det finns olika tillgängliga servicenivåer. Alla nivåer omfattar alla kopplingar och API Apps, inklusive samtliga funktioner.  

[Priser för Apptjänst](https://azure.microsoft.com/pricing/details/app-service/) beskriver de här servicenivåerna och visar också vad som ingår i nivåerna. I följande avsnitt beskrivs de olika kategorierna av BizTalk-API Apps och kopplingar.


## Hybridkopplingar 
Hybridanslutningarna utökar räckvidden i företaget och ger anslutningsbarhet till [SAP](app-service-logic-connector-sap.md), [Oracle](app-service-logic-connector-oracle.md), [DB2](app-service-logic-connector-db2.md), [Informix](app-service-logic-connector-informix.md) och WebSphere MQ. 

## EAI- och EDI-tjänster
Att skapa affärskritiska appar kräver mer än bara anslutningsbarhet. BizTalk API Appsna bygger på grunden i Microsofts branschledande integrationsplattform, BizTalk Server, och ger avancerade integrationsmöjligheter som lätt kan överföras till dina webb-, mobil- och Logic Apps. Några av integrationsmöjligheterna är [validering](app-service-logic-xml-validator.md), [extrahering](app-service-logic-xpath-extract.md), [transformering](app-service-logic-transform-xml-documents.md), [kodare](app-service-logic-connector-jsonencoder.md), [hantering av handelspartners](app-service-logic-connector-tpm.md) och stöd för EDI-format såsom [X12](app-service-logic-connector-x12.md), [EDIFACT](app-service-logic-connector-edifact.md) och [AS2](app-service-logic-connector-as2.md).

Ytterligare resurser: [B2B-kopplingar (business-to-business) och API Apps](app-service-logic-b2b-connectors.md)  
[Skapa en B2B-process](app-service-logic-create-a-b2b-process.md)  
[Skapa ett handelspartneravtal](app-service-logic-create-a-trading-partner-agreement.md)  
[Spåra dina B2B-meddelanden](app-service-logic-track-b2b-messages.md)  


## Regler
Affärsregler är de principer och beslut som styr affärsprocesser. Normalt sett är regler dynamiska och ändras med tiden av olika skäl, till exempel affärsplaner, lagändringar och många andra orsaker. Med [BizTalk-reglerna](app-service-logic-use-biztalk-rules.md) kan du frikoppla de här principerna från din programkod och göra ändringprocessen enklare och snabbare.

## Lista över kopplingar och API Apps
Se [listan över kopplingar och API Apps](app-service-logic-connectors-list.md) för en fullständig lista över kopplingar och API Apps som ingår i respektive kategori, till exempel standard-, BizTalk EAI- och premiumkopplingarna och så vidare.
 



<!--HONumber=sep16_HO1-->


