---
title: Azure och Dynamics 365 konsulttjänst erbjudandeinformationen - ange Storefront | Microsoft Docs
description: Guide för att definiera storefront information i en Azure- eller Dynamics 365-konsult tjänsteerbjudanden i partnerportalen i molnet.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: f587ca92c83680526a277a571eea98e73b82d902
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53346555"
---
# <a name="storefront-details-tab"></a>Fliken storefront information

Nu ska måste du ange information för din butik. Den **Storefront information** består av följande avsnitt:

-   Erbjudandeinformation
-   Utgivarinformation
-   Lista information
-   Marknadsföring artefakter

![Skapa en ny konsult tjänsterbjudande - fliken Storefront information](media/consultingoffer-storefront-details.png)

## <a name="offer-details"></a>Erbjudandeinformation

Avsnittet med erbjudandet innehåller följande fält:

-   Sammanfattning av erbjudandet
-   Beskrivning av erbjudande

### <a name="offer-summary"></a>Sammanfattning av erbjudandet

Sammanfattning av erbjudandet är en kort beskrivning av ditt erbjudande som visas under erbjudandenamn. Använd oformaterad text när du lägger till sammanfattning av erbjudandet och ska inte innehålla radbrytningar. Här följer några bra exempel på erbjudandet sammanfattningar tillsammans med motsvarande erbjudandet namn:

*Exempel 1*

-   **Erbjudandets namn:** Molnet Analytics: 3 dagars Workshop
-   **Sammanfattning av erbjudandet:** Översikt över Microsoft Azure och Power BI, bedömning av aktuella miljön och mini POC.

*Exempel 2*

-   **Erbjudandets namn:** Industriella Azure IoT: 30-dagars konceptbevis
-   **Sammanfattning av erbjudandet:** Skapa en industriella anslutna produkten pilotprojekt för att på ett säkert sätt ansluta utrustning i fältet till en Azure IoT Hub-lösning med instrumentpaneler, rapporter och meddelanden.

*Exempel 3*

-   **Erbjudandets namn:** Professionella tjänster: Genomgång för 1 tim
-   **Sammanfattning av erbjudandet:** Översikt och demonstration av förkonfigurerade, utökad Dynamics 365 för Operations-lösning som ger förbättrad hantering av projekt, fakturering och resurser för professionella tjänster.

*Exempel 4*

-   **Erbjudandets namn:** Powerbi i din värld: 4 tim Workshop
-   **Sammanfattning av erbjudandet:** Kom igång med din första instrumentpanel och Läs om bästa praxis. För upp till 12 studenter som genomförs på plats.

*Exempel 5*

-   **Erbjudandets namn:** Dynamics och projekt: 3-dagars utvärdering
-   **Sammanfattning av erbjudandet:** Samla in kraven och utvärdering för ERP-lösning som utformats för professionella tjänster företag och projekt-drivna företag.

### <a name="offer-description"></a>Beskrivning av erbjudande

Ange beskrivning av här konsult tjänsteerbjudanden. En bra erbjudandet beskrivning innehåller mer information om vad samarbetet kommer att se ut och vad blir slutet leverans till kunden. Det tydligt hjälper kunden förstå vad de får. Beskrivning av ditt erbjudande måste innehålla hur ditt erbjudande relaterar till Microsoft-produkt som du erbjuder konsulttjänster.

Ta inte med via e-post eller telefonnummer för att kontakta dig i ditt erbjudande beskrivning. Det finns en **kontakta mig** knapp med ditt erbjudande som överför leder till målet för lead-hantering som du identifierar på ditt erbjudande.

Beskrivning av erbjudande anger du i Markdown-format. Om du inte är bekant med Markdown eller formatering för HTML, kan du granska resurser på den [hur man använder Markdown för att skriva Docs](https://docs.microsoft.com/contribute/how-to-write-use-markdown).

Dessa format säkerställer att ditt erbjudande har högsta läsbarhet för kunder.

Håll det kort beskrivning av ditt erbjudande och följa teckengränsen som användarna inte tycker om att läsa lång text. Du har fortfarande möjlighet att ladda upp marknadsföring broschyrer, informationsbladet och andra dokument som beskriver ditt erbjudande i mer detalj.

I följande exempel visar en bra består beskrivning av erbjudande och dess relaterade namn och en sammanfattning:

**Erbjudandets namn:** Molnet Analytics: 3 dagars Workshop

**Sammanfattning av erbjudandet:** Översikt över Microsoft Azure och Power BI, bedömning av aktuella miljön och mini POC.

**Beskrivning av erbjudande:** Den här 3-dagars workshop är för tekniska och affärsmässiga ledare och lagras på plats hos klientens anläggning.

***Agenda***

Dag 1

-   Fokuserar på hur du skyddar, skala och organisera data i Microsoft-molnet med Azure Data Lake, HDInsight eller Azure SQL Data Warehouse.

Dag 2

-   Beskriver hur du konfigurerar och distribuerar lösningar för avancerade analyser med Microsoft R- och Azure Machine Learning.

Dag 3

-   Beskriver hur du rita användbara insikter och operationalisera analyser med Power BI, inklusive en samarbetsfunktioner session för att cobuild en Power BI-instrumentpanel.

I slutet av workshopen kommer klienten att kunna definiera en övergripande plan och en implementering översikt över data och Analyslösningar i Microsoft-molnet.

*Exempel Markdown-fil för ett erbjudande med hjälp av det här formatet:*

    This 3-day workshop is for technical and business leaders and is held on-site at the client’s facility.

      ### Agenda

      **Day 1**

      * Focuses on how to secure, scale, and organize data within the Microsoft cloud using Azure Data Lake, HDInsight, or Azure SQL Data Warehouse

      **Day 2**

      * Covers how to configure and deploy advanced analytics solutions with Microsoft R and Azure Machine Learning

      **Day 3**

      * Covers how to draw actionable insights and operationalize analytics with Power BI, including a collaborative session to co-build a Power BI dashboard.


      ### Deliverables
      By the end of the workshop, the client will be able to define a high-level plan and an implementation roadmap for data and analytics solutions in the Microsoft cloud.


## <a name="publisher-information"></a>Utgivarinformation

**MPN-ID**

Ditt 9 siffror Microsoft Partner Network (MPN)-ID. Om du inte har en MPN-ID, går du till Microsoft Partner Center till något.

**Partnercenter-ID**

Ny Partner Center-ID, om du har en.

**MPN-ID**

Ange en hemlig nyckel om du vill förhandsgranska ditt erbjudande på AppSource innan de aktiveras.
Den här identifieraren är inte ett lösenord.

## <a name="listing-details"></a>Lista information

**Consulting tjänsttyp**

Microsoft är koncentrera exklusivt på fasta gränser, fast varaktighet, beräknad eller fast pris (eller kostnadsfri) och främst förproduktions-sales-orienterade consulting Tjänsterbjudanden för en enda kund och för utvärdering, implementering, konceptbevis, information, eller Workshop erbjudanden utförs antingen på plats eller virtuellt. AppSource konsult services marketplace inte har stöd för hanterade eller prenumerationstjänster.

>[!Note]
>AppSource-konsulttjänster är inte lämplig marketplace för prenumeration eller kurser på begäran.

För följande typer av erbjudanden ingår:

-   **Utvärdering av:** En utvärdering av en kunds miljö för att fastställa tillämpligheten hos en lösning och ange en uppskattning av kostnaden och val av tidpunkt.
-   **Genomgång:** En introduktion till en lösning eller en tjänst som konsult för att rita kunden närheten med hjälp av ramverk, demonstrationer och Kundexempel. Implikationer måste vara ledda på plats.
-   **Implementering:** En fullständig installation som resulterar i en fullt fungerande lösning. Microsoft rekommenderar att begränsa till lösningar som kan vara implementerade i en vecka eller mindre för detta pilotprogram.
-   **Konceptbevis:** Implementering av begränsat omfång att avgöra om en lösning som passar kraven för en kund.
-   **Workshop:** En interaktiv engagement utförs i en kunds lokala som kan inkludera utbildningssessioner, implikationer, utvärderingar och demonstrationer som bygger på kundens data eller miljö.

**Tillgänglighet för land/region**

Välj land och region där den här konsult tjänsterbjudande ska vara tillgänglig. Ett erbjudande kan inte publiceras i flera länder eller regioner. Ett nytt erbjudande måste skapas för varje land eller region.

>[!Note]
>AppSource-konsulttjänster är för närvarande live i USA, USA och Kanada. Kan du skicka in ett erbjudande för ett land som ännu inte är aktiv och den kommer granskas och förberedd för livesändning. Ett minsta antal erbjudanden som är redo att behövs för att öppna ett nytt land så uppmuntras erbjudanden för som inte är live.

**Branscher**

Välj branscher som konsult tjänsten erbjuder mest gäller för.

**Varaktighet**

Välj ett tal (till exempel 3, 4, etc.) under varaktighet, och välj sedan timmen, dagen eller veckan.

**Primär produkter**

Om du vill publicera på Azure Marketplace, Välj **Azure** som huvudprodukten och välj sedan relevant Lösningsområden.

Om du vill publicera till AppSource, Välj **Dynamics 365**, **Power BI**, eller **PowerApps** som din primära produkt. Du kan också välja andra relevanta tillämpliga produkter, och din konsulttjänst erbjuda visas i listor som är kopplade till var och en av dessa produkter på AppSource.

**Relevanta kompetenser**

Välj kompetenser relevanta för det här erbjudandet att de visas tillsammans med informationen i erbjudandet.

## <a name="marketing-artifacts"></a>Marknadsföring artefakter

**Företagets logotyp (PNG-format, 48 x 48 bildpunkter)**

Ladda upp en bild som visas på panelen för ditt erbjudande i visningssida för erbjudande-galleriet. Bilden måste vara en .png-avbildning med en upplösning på 48 x 48 bildpunkter.

**Företagets logotyp företagets logotyp (PNG-format, 216 x 216 bildpunkter)**

Ladda upp en bild som visas på informationssidan med i ditt erbjudande. Bilden måste vara en .png-avbildning med en upplösning på 216 x 216 bildpunkter.

**Videor (Max 4)**

Ladda upp upp till fyra kunden Fallstudie videor eller kund referens videor. Om du inte har någon kan du ladda upp en video som förklarar ditt företags expertis relaterade till erbjudandet. Ladda upp videon showcase här om du har en Power BI eller PowerApps solution showcase. Videolänkar måste vara för YouTube eller Vimeo.

**Dokument (Max 3)**

Ladda upp marknadsföring publikationer beskriver dina konsult tjänsteerbjudanden i detalj. Alternativt kan du också ladda upp företagets översikt, informationsbladet eller fallstudier. Tänk på dina dokument använder aktuella namnen på produkter och inte funktionen Microsoft konkurrerande produkter.

**Skärmbilder (Max 5)**

Överföra upp till fem avbildningar som innehåller mer information om erbjudandet, erbjudande slutprodukter eller ditt företag. Till exempel ett kodfragment till broschyrens marknadsföring, en relevanta bild från en presentation eller en bild på företagets intresset eller expertis.

## <a name="next-steps"></a>Nästa steg

Nu är du redo att [publicera din konsulttjänster](./cpp-consulting-service-publish-offer.md) erbjuder.