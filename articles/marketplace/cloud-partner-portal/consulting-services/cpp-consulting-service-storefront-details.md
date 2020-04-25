---
title: Ange butik information för konsult erbjudandet | Azure Marketplace
description: Definiera butik information i ett Azure-eller Dynamics 365 konsult tjänst erbjudande i Cloud Partner Portal.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 9fd1330e0ca0a49556e79044570173adaf458e13
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148150"
---
# <a name="storefront-details-tab"></a>Fliken Butiksinformation

>[!Important]
>Från den 13 april 2020 börjar vi flytta hanteringen av dina konsult tjänst erbjudanden till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ anvisningarna i [Översikt över hur du skapar konsult tjänster](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-consulting-service-offer) för att hantera dina migrerade erbjudanden.

Den här artikeln förklarar hur du anger information för din butik. Fliken **butik information** består av följande avsnitt:

-   **Erbjudande information**
-   **Utgivar information**
-   **List information**
-   **Marknadsförings artefakter**

![Skapa en ny konsult tjänst erbjudande – butik information-fliken](media/consultingoffer-storefront-details.png)


## <a name="offer-details"></a>Erbjudande information

Avsnittet **erbjudande information** innehåller följande rutor:

-   **Erbjudande Sammanfattning\***
-   **Beskrivning av erbjudande\***


### <a name="offer-summary"></a>Erbjudande Sammanfattning\*

Erbjudande sammanfattningen är en kort beskrivning av ditt erbjudande som visas strax under namnet på erbjudandet. Använd oformaterad text för att ange erbjudande sammanfattningen utan rad brytningar. Följande exempel är en sammanfattning av ett erbjudande med motsvarande erbjudande namn.

*Exempel 1*

-   **Erbjudandets namn**: Cloud Analytics: 3 dagars workshop
-   **Erbjudande Sammanfattning**: översikt över Microsoft Azure och Power BI, utvärdering av aktuell miljö och mini POC.

*Exempel 2*

-   **Erbjudandets namn**: industriella Azure IoT: 30 dagars koncept bevis
-   **Erbjudande Sammanfattning**: skapa en industri ansluten produkt pilot för att på ett säkert sätt ansluta utrustning i fältet till en Azure IoT Hub-lösning med instrument paneler, rapporter och aviseringar.

*Exempel 3*

-   **Erbjudandets namn**: professionella tjänster: 1 – HR-kort
-   **Erbjudande Sammanfattning**: översikt och demonstration av förkonfigurerade, utökade Dynamics 365 för drift lösningar som ger förbättrad hantering av projekt, fakturering och resurser för professionella tjänster.

*Exempel 4*

-   **Namn på erbjudande**: Power BI i din värld: 4 – h workshop
-   **Erbjudande Sammanfattning**: kom igång med din första instrument panel och lär dig metod tips. För upp till 12 studenter, utfört på plats.

*Exempel 5*

-   **Namn på erbjudande**: Dynamics och projekt: 3 dagars utvärdering
-   **Erbjudande Sammanfattning**: krav på insamling och utvärdering av en ERP-lösning som är utformad för professionella tjänste företag och projekt drivna företag.


### <a name="offer-description"></a>Beskrivning av erbjudande\*

Ange beskrivningen av konsult tjänst erbjudandet i rutan **erbjudande Beskrivning** . En beskrivning av en bra Erbjudandet täcker exakta uppgifter om vad som händer och slut produkten till kunden. Det bör tydligt hjälpa kunden förstå vad de får. Inkludera hur ditt erbjudande relaterar till den Microsoft-produkt som du erbjuder konsult tjänster för.

Ta inte med din e-postadress eller ditt telefonnummer i din erbjudande beskrivning. Knappen **kontakta mig** ingår i ditt erbjudande för att ladda upp leads till det mål för hantering av lead som du identifierar för ditt erbjudande.

Ange en beskrivning av erbjudandet i markdown-format. Om du inte är bekant med markdown eller formatering för HTML, se [Använd markdown för att skriva dokument](/contribute/markdown-reference).

Använd de här formaten för att se till att ditt erbjudande är enkelt för dina kunder att läsa.

Se till att din erbjudande beskrivning är kortfattad och följer tecken gränsen eftersom användarna inte vill läsa lång text. Du kan också överföra marknadsförings broschyrer, fakta blad och andra dokument som beskriver ditt erbjudande i djupare detalj.

Följande exempel visar en väldefinierad erbjudande beskrivning och dess relaterade namn och Sammanfattning:

**Erbjudandets namn**: Cloud Analytics: 3 dagars workshop

**Erbjudande Sammanfattning**: översikt över Microsoft Azure och Power BI, utvärdering av aktuell miljö och mini POC.

**Erbjudande Beskrivning**: den här tre dagars workshopen är för teknik-och affärs ledare och hålls på plats på klientens anläggning.

Ordningen

Dag 1

-   Fokuserar på hur du skyddar, skalar och ordnar data i Microsoft-molnet med hjälp av Azure Data Lake, Azure HDInsight eller Azure SQL Data Warehouse.

Dag 2

-   Beskriver hur du konfigurerar och distribuerar avancerade analys lösningar med Microsoft R och Azure Machine Learning.

Dag 3

-   Här får du information om hur du ritar åtgärds bara insikter och operationalisera-analys med Power BI och innehåller en samarbets session för att skapa en Power BI-instrumentpanel.

Leverabler

I slutet av workshopen kan klienten definiera en plan på hög nivå och en implementerings översikt för data-och analys lösningar i Microsoft-molnet.

Följande exempel på MARKDOWN-fil är för föregående erbjudande:

    This 3-day workshop is for technical and business leaders and is held on-site at the client's facility.

      ### Agenda

      **Day 1**

      * Focuses on how to secure, scale, and organize data within the Microsoft cloud by using Azure Data Lake, Azure HDInsight, or Azure SQL Data Warehouse.

      **Day 2**

      * Covers how to configure and deploy advanced analytics solutions with Microsoft R and Azure Machine Learning.

      **Day 3**

      * Covers how to draw actionable insights and operationalize analytics with Power BI and includes a collaborative session to cobuild a Power BI dashboard.


      ### Deliverables
      By the end of the workshop, the client will be able to define a high-level plan and an implementation roadmap for data and analytics solutions in the Microsoft cloud.


## <a name="publisher-information"></a>Utgivarinformation

**MPN-ID**

Ange ditt MPN-ID (9-siffrigt Microsoft Partner Network). Om du inte har något MPN-ID kan du skaffa ett på Microsoft Partner Center.

**ID för partner Center**

Ange ditt nya ID för partner Center, om du har ett.

**MPN-ID**

Ange en hemlig nyckel för att förhandsgranska ditt erbjudande på AppSource innan det går live.
Den här identifieraren är inte ett lösen ord.


## <a name="listing-details"></a>List information

**Konsult tjänst typ**

Microsoft fokuserar enbart på fast omfattning, fast varaktighet, uppskattat eller fast pris (eller kostnads fritt) och i huvudsak konsult tjänst erbjudanden för en enskild kund. Typer av tjänster är bedömning, kortning, implementering, bevis på koncept och workshop-erbjudanden som antingen finns på plats eller i stort sett. AppSource Consulting Services-marknadsplatsen har inte stöd för listor för hanterade eller prenumerations tjänster.

>[!Note]
>AppSource Consulting Services är inte lämplig marknads plats för prenumerationer eller utbildning på begäran.

Följande fem typer av erbjudanden ingår:

-   **Utvärdering**: en utvärdering av en kunds miljö för att fastställa tillämpligheten för en lösning och ge en uppskattning av kostnader och tids inställningar.
-   **Kort**: en introduktion till en lösning eller en konsult tjänst för att dra nytta av kundernas intresse genom att använda ramverk, demonstrationer och kund exempel. Kort måste utföras på plats.
-   **Implementering**: en fullständig installation som resulterar i en helt fungerande lösning. För den här piloten rekommenderar Microsoft att du begränsar lösningar som kan implementeras i en vecka eller mindre.
-   **Koncept bevis**: en begränsad omfattning för att avgöra om en lösning uppfyller kundens krav.
-   **Workshop**: ett interaktivt engagemang på en kunds anläggning som kan omfatta utbildning, utvärderingar, utvärderingar eller demonstrationer som bygger på kundens data eller miljö.

**Tillgänglighet för land/region**

Välj land och region där det här konsult tjänst erbjudandet är tillgängligt. Ett enda erbjudande kan inte publiceras i flera länder eller regioner. Ett nytt erbjudande måste skapas för varje land eller region.

>[!Note]
>AppSource Consulting Services är för närvarande Live i USA, Storbritannien och Kanada. Du kan skicka ett erbjudande för ett land/en region som ännu inte är Live, och det kommer att granskas och förberedas för Live. Ett minsta antal erbjudanden som är klara att användas i real tid krävs för att öppna ett nytt land/region, så erbjudanden för länder/regioner som inte är Live uppmuntras.

**Branscher**

Välj de branscher som ditt konsult tjänst erbjudande är mest tillämpligt för.

**Varaktighet**

Välj ett tal (till exempel 3 eller 4) under **varaktighet**och välj sedan **timme**, **dag**eller **vecka**.

**Primära produkter**

Om du vill publicera på Azure Marketplace väljer du **Azure** som primär produkt. Välj sedan de relevanta **lösnings områdena**.

Om du vill publicera till AppSource väljer du **Dynamics 365**, **Power BI**eller **PowerApps** som primär produkt. Du kan också välja andra relevanta **tillämpliga produkter**. Sedan visar ditt konsult tjänst erbjudande i listor som är associerade med var och en av dessa produkter på AppSource.

**Relevanta kompetenser**

Välj kompetenser som är relevanta för det här erbjudandet om du vill att de ska visas tillsammans med erbjudande informationen.

## <a name="marketing-artifacts"></a>Marknadsförings artefakter

**Företags logo typ (. png-format, 48 x 48 bild punkter)**

Ladda upp en bild som visas på panelen för ditt erbjudande på sidan för att visa ett erbjudande Galleri. Bilden måste vara en. png-bild med en upplösning på 48 x 48 bild punkter.

**Företags logo typ (. png-format, 216 x 216 bild punkter)**

Ladda upp en bild som visas på sidan information i ditt erbjudande. Bilden måste vara en. png-bild med en upplösning på 216 x 216 bild punkter.

**Videor (begränsat till fyra)**

Överför upp till fyra kund fallstudier eller kund referens videoklipp. Om du inte har någon kan du ladda upp en video som förklarar ditt företags expertis som rör erbjudandet. Om du har en demonstration av Power BI eller PowerApps-lösning kan du ladda upp demonstrations videon här. Video länkar måste vara för YouTube eller Vimeo.

**Dokument (begränsat till tre)**

Överför marknadsförings broschyren som beskriver ditt konsult tjänst erbjudande i detalj. Du kan också överföra en företags översikt, fakta blad eller fallstudier. Se till att dokumenten använder de aktuella produkternas aktuella namn och att de inte har Microsofts konkurrerande produkter.

**Skärm bilder (begränsat till fem)**

Ladda upp upp till fem avbildningar som innehåller mer information om ditt erbjudande, dess slut produkter eller ditt företag. En del av din marknadsförings broschyr, en relevant bild från en presentation, eller en bild som visar företagets form eller expertis är några exempel.


## <a name="next-steps"></a>Nästa steg

Nu är du redo att [publicera erbjudandet om konsult tjänster](./cpp-consulting-service-publish-offer.md) .
