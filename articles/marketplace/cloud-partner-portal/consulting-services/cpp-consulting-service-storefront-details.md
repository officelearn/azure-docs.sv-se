---
title: Azure och Dynamics 365-konsult tjänsterbjudande - ange storefront information | Microsoft Docs
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
ms.openlocfilehash: ac383c85938f5cb7dfe4f711714f5bacd0c96b89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60865377"
---
# <a name="storefront-details-tab"></a>Fliken Butiksinformation

Den här artikeln beskriver hur du kan ange information för din butik. Den **Storefront information** fliken består av följande avsnitt:

-   **Information om erbjudandet**
-   **Utgivarinformation**
-   **Lista information**
-   **Marknadsföring artefakter**

![Skapa en ny konsult tjänsterbjudande - fliken Storefront information](media/consultingoffer-storefront-details.png)

## <a name="offer-details"></a>Erbjudandeinformation

Den **Erbjudandeinformationen** avsnittet innehåller följande rutor:

-   **Sammanfattning av erbjudandet**
-   **Beskrivning av erbjudande**

### <a name="offer-summary"></a>Sammanfattning av erbjudandet

Sammanfattning av erbjudandet är en kort beskrivning av ditt erbjudande som visas under erbjudandenamn. Använd oformaterad text för att ange sammanfattningen av erbjudandet, utan några radbrytningar. I följande exempel är bra erbjudandet sammanfattningar tillsammans med motsvarande erbjudandet namn.

*Exempel 1*

-   **Namn på erbjudande**: Cloud Analytics: 3 dagars Workshop
-   **Erbjuder sammanfattning**: Översikt över Microsoft Azure och Power BI, bedömning av aktuella miljön och mini POC.

*Exempel 2*

-   **Namn på erbjudande**: Industrial Azure IoT: 30-dagars konceptbevis
-   **Erbjuder sammanfattning**: Skapa en industri-anslutna produkten pilotprojekt för att på ett säkert sätt ansluta utrustning i fältet till en Azure IoT Hub-lösning med instrumentpaneler, rapporter och meddelanden.

*Exempel 3*

-   **Namn på erbjudande**: Professionella tjänster: Genomgång för 1 tim
-   **Erbjuder sammanfattning**: Översikt och demonstration av förkonfigurerade, utökad Dynamics 365 för Operations-lösning som ger förbättrad hantering av projekt, fakturering och resurser för professionella tjänster.

*Exempel 4*

-   **Namn på erbjudande**: Powerbi i din värld: 4 tim Workshop
-   **Erbjuder sammanfattning**: Kom igång med din första instrumentpanel och Läs om bästa praxis. För upp till 12 studenter som genomförs på plats.

*Exempel 5*

-   **Namn på erbjudande**: Dynamics och projekt: 3-dagars utvärdering
-   **Erbjuder sammanfattning**: Samla in kraven och utvärdering för en ERP-lösning som utformats för professionella tjänster företag och projekt-drivna företag.

### <a name="offer-description"></a>Beskrivning av erbjudande

Ange beskrivningen av konsult tjänsteerbjudanden i den **erbjuder beskrivning** box. En bra erbjudandet beskrivning innehåller mer information om hur samarbetet ser ut och slutet leverans till kunden. Det tydligt hjälper kunden förstå vad de får. Inkludera hur ditt erbjudande relaterar till Microsoft-produkt som du erbjuder konsulttjänster.

Inkludera inte numret till din e-postadress eller telefonnummer i beskrivning av ditt erbjudande. En **kontakta mig** knappen som ingår i erbjudandet att ladda upp leads till målet för lead-hantering som du identifierar för ditt erbjudande.

Ange beskrivning av erbjudande i Markdown-format. Om du inte är bekant med Markdown eller formatering för HTML [Använd Markdown för att skriva docs](https://docs.microsoft.com/contribute/how-to-write-use-markdown).

Använd dessa format för att se till att ditt erbjudande är lättare för kunderna att läsa.

Håll det kort beskrivning av ditt erbjudande och följa teckengränsen eftersom användarna inte gillar att läsa lång text. Du kan också överföra marknadsföring broschyrer, informationsbladet och andra dokument som beskriver ditt erbjudande i mer detalj.

I följande exempel visas en beskrivning av väl består erbjudande och dess relaterade namn och en sammanfattning:

**Namn på erbjudande**: Cloud Analytics: 3 dagars Workshop

**Erbjuder sammanfattning**: Översikt över Microsoft Azure och Power BI, bedömning av aktuella miljön och mini POC.

**Erbjuder beskrivning**: Den här 3-dagars workshop är för tekniska och affärsmässiga ledare och lagras på plats hos klientens anläggning.

Agenda

Dag 1

-   Fokuserar på hur du skyddar, skala och organisera data i Microsoft-molnet med hjälp av Azure Data Lake, Azure HDInsight eller Azure SQL Data Warehouse.

Dag 2

-   Beskriver hur du konfigurerar och distribuerar lösningar för avancerade analyser med Microsoft R- och Azure Machine Learning.

Dag 3

-   Beskriver hur du rita användbara insikter och operationalisera analytics med Power BI och innehåller en gemensam session att cobuild Power BI-instrumentpanel.

Slutprodukter

I slutet av workshopen kan klienten definiera en övergripande plan och en implementering översikt över data och Analyslösningar i Microsoft-molnet.

Följande exempel Markdown-filen är för det tidigare erbjudandet:

    This 3-day workshop is for technical and business leaders and is held on-site at the client’s facility.

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

Ange ditt nio siffror Microsoft Partner Network (MPN)-ID. Om du inte har en MPN-ID, får du en i Microsoft Partner Center.

**Partnercenter-ID**

Ange ditt nya Partner Center-ID, om du har en.

**MPN-ID**

Ange en hemlig nyckel om du vill förhandsgranska erbjudandet på AppSource innan det lanseras.
Den här identifieraren är inte ett lösenord.

## <a name="listing-details"></a>Lista information

**Consulting tjänsttyp**

Microsoft fokuserar enbart på fast omfattning, fast varaktighet, beräknad eller fast pris (eller kostnadsfri) och främst före försäljning inriktad consulting Tjänsterbjudanden för en enda kund. Typer av tjänster är utvärdering, genomgång, implementering, bevis på koncept och workshop erbjudanden från på plats eller virtuellt. AppSource konsult services marketplace har inte stöd för hanterade eller prenumerationstjänster.

>[!Note]
>AppSource-konsulttjänster är inte lämplig marketplace för prenumeration eller kurser på begäran.

För följande typer av erbjudanden ingår:

-   **Utvärdering av**: En utvärdering av en kunds miljö för att fastställa tillämpligheten hos en lösning och ange en uppskattning av kostnaden och val av tidpunkt.
-   **Information till**: En introduktion till en lösning eller en tjänst som konsult för att rita kundernas intresse med hjälp av ramverk, demonstrationer och Kundexempel. Implikationer måste utföras på plats.
-   **Implementering**: En fullständig installation som resulterar i en fullt fungerande lösning. Microsoft rekommenderar att begränsa till lösningar som kan vara implementerade i en vecka eller mindre för detta pilotprogram.
-   **Konceptbevis**: Implementering av begränsad omfattning att avgöra om en lösning uppfyller kraven för en kund.
-   **Workshop**: En interaktiv engagement utförs i en kunds lokala som kan inkludera utbildningssessioner, implikationer, utvärderingar och demonstrationer som bygger på kundens data eller miljö.

**Tillgänglighet för land/region**

Välj land och region där den här konsult tjänsterbjudande är tillgänglig. Ett erbjudande kan inte publiceras i flera länder eller regioner. Ett nytt erbjudande måste skapas för varje land eller region.

>[!Note]
>AppSource-konsulttjänster är för närvarande live i USA, USA och Kanada. Kan du skicka in ett erbjudande för ett land som ännu inte live och den granskas och förberedd för livesändning. Ett minsta antal erbjudanden som är redo att behövs för att öppna ett nytt land så uppmuntras erbjudanden för länder/regioner som inte är live.

**Branscher**

Välj branscher som konsult tjänsten erbjuder mest gäller för.

**Varaktighet**

Välj ett tal (till exempel 3 eller 4) under **varaktighet**, och välj sedan **timme**, **dag**, eller **vecka**.

**Primär produkter**

Om du vill publicera på Azure Marketplace, Välj **Azure** som huvudprodukten. Välj sedan den relevanta **Lösningsområden**.

Om du vill publicera till AppSource, Välj **Dynamics 365**, **Power BI**, eller **PowerApps** som din primära produkt. Du kan också välja andra relevanta **tillämpliga produkter**. Sedan visar dina konsult tjänsteerbjudanden i listor som är kopplade till var och en av dessa produkter på AppSource.

**Relevanta kompetenser**

Välj kompetenser relevanta för det här erbjudandet att de visas tillsammans med informationen i erbjudandet.

## <a name="marketing-artifacts"></a>Marknadsföring artefakter

**Företagets logotyp (PNG-format, 48 x 48 bildpunkter)**

Ladda upp en bild som visas på panelen för ditt erbjudande i visningssida för erbjudande-galleriet. Bilden måste vara en .png-avbildning med en upplösning på 48 x 48 bildpunkter.

**Företagets logotyp (PNG-format, 216 x 216 bildpunkter)**

Ladda upp en bild som visas på sidan för ditt erbjudande. Bilden måste vara en .png-avbildning med en upplösning på 216 x 216 bildpunkter.

**Videor (begränsat till fyra)**

Ladda upp upp till fyra kunden Fallstudie videor eller kund referens videor. Om du inte har någon kan du ladda upp en video som förklarar ditt företags expertis relaterade till erbjudandet. Ladda upp videon showcase här om du har en Power BI eller PowerApps solution showcase. Videolänkar måste vara för YouTube eller Vimeo.

**Dokument (begränsat till tre)**

Ladda upp marknadsföring publikationer beskriver dina konsult tjänsteerbjudanden i detalj. Du kan också överföra en företags-översikt, informationsbladet eller fallstudier. Se till att dina dokument använder du aktuella namnen på produkter och inte funktionen Microsoft konkurrerande produkter.

**Skärmbilder (begränsat till fem)**

Överföra upp till fem avbildningar som innehåller mer information om ditt erbjudande, dess slutprodukter eller ditt företag. Kodfragmentet för broschyrens marknadsföring, en relevanta bild från en presentation eller en bild som visar företagets intresset eller expertis följer några exempel.

## <a name="next-steps"></a>Nästa steg

Nu är du redo att [publicera din konsulttjänster](./cpp-consulting-service-publish-offer.md) erbjuder.
