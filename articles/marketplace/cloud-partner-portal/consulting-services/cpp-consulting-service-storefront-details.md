---
title: Ange skyltfönster detaljer för konsulterbjudande | Azure Marketplace
description: Definiera information i ett microsoft-företagsinformation i ett Azure- eller Dynamics 365-erbjudande om konsulttjänster i Cloud Partner Portal.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 7e1989ee5fcc428bc7d29c17432ded8dab24ecee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281705"
---
# <a name="storefront-details-tab"></a>Fliken Butiksinformation

I den här artikeln beskrivs hur du anger information för skyltfönstret. Fliken **Information om storefront** består av följande avsnitt:

-   **Information om erbjudandet**
-   **Information om utgivare**
-   **Lista detaljer**
-   **Marknadsföring Artefakter**

![Skapa ett nytt konsultserviceerbjudande - fliken Skyltfönsterdetaljer](media/consultingoffer-storefront-details.png)


## <a name="offer-details"></a>Information om erbjudandet

Avsnittet **Erbjudandeinformation** innehåller följande rutor:

-   **Sammanfattning av erbjudandet\***
-   **Beskrivning av erbjudandet\***


### <a name="offer-summary"></a>Sammanfattning av erbjudandet\*

Erbjudandet sammanfattning är en kort beskrivning av ditt erbjudande som visas precis under erbjudandets namn. Använd oformaterad text för att ange erbjudandesammanfattningen, utan några radbrytningar. Följande exempel är bra erbjudandesammanfattningar tillsammans med motsvarande erbjudandenamn.

*Exempel 1*

-   **Erbjudande namn:** Cloud Analytics: 3-dagars Workshop
-   **Sammanfattning**av erbjudandet : Översikt över Microsoft Azure och Power BI, bedömning av aktuell miljö och mini-POC.

*Exempel 2*

-   **Erbjudandenamn**: Industrial Azure IoT: 30-Day Proof of Concept
-   **Erbjudandesammanfattning**: Skapa en industriellt ansluten produktpilot för att säkert ansluta utrustning i fältet till en Azure IoT Hub-lösning med instrumentpaneler, rapporter och meddelanden.

*Exempel 3*

-   **Erbjudande namn:** Professionella tjänster: 1-Hr Briefing
-   **Sammanfattning av erbjudandet**: Översikt och demo av förkonfigurerad, utökad Dynamics 365 for Operations-lösning som ger förbättrad hantering av projekt, fakturering och resurser för professionella tjänster.

*Exempel 4*

-   **Erbjudande namn:** Power BI i din värld: 4-timmars Workshop
-   **Sammanfattning av erbjudandet**: Kom igång med den första instrumentpanelen och lär dig metodtips. För upp till 12 studenter, genomförs på plats.

*Exempel 5*

-   **Erbjudandenamn**: Dynamics och projekt: 3-dagars utvärdering
-   **Erbjudandesammanfattning**: Kravinsamling och bedömning av en affärssystemlösning avsedd för professionella tjänsteföretag och projektdrivna företag.


### <a name="offer-description"></a>Beskrivning av erbjudandet\*

Ange beskrivningen av konsulttjänsterbjudandet i **rutan Erbjudandebeskrivning.** En bra erbjudandebeskrivning täcker exakta detaljer om hur engagemanget ser ut och slutprodukten för kunden. Det bör helt klart hjälpa kunden att förstå vad de får. Inkludera hur ditt erbjudande relaterar till den Microsoft-produkt som du erbjuder konsulttjänster för.

Ta inte med din e-postadress eller ditt telefonnummer i din erbjudandebeskrivning. En **Kontakta mig-knapp** ingår i ditt erbjudande om att ladda upp leads till det leadhanteringsmål som du identifierar för ditt erbjudande.

Ange erbjudandebeskrivningen i Markdown-format. Om du inte är bekant med Markdown eller formatering för HTML läser du [Använda Markdown för att skriva dokument](/contribute/markdown-reference).

Använd dessa format för att se till att ditt erbjudande är enkelt för dina kunder att läsa.

Håll erbjudandebeskrivningen kort och följ teckengränsen eftersom användarna inte gillar att läsa lång text. Du kan också ladda upp marknadsföringsbroschyrer, faktablad och andra dokument som beskriver ditt erbjudande mer detaljerat.

Följande exempel visar en välkomponerad erbjudandebeskrivning och dess relaterade namn och sammanfattning:

**Erbjudande namn:** Cloud Analytics: 3-dagars Workshop

**Sammanfattning**av erbjudandet : Översikt över Microsoft Azure och Power BI, bedömning av aktuell miljö och mini-POC.

**Erbjudandebeskrivning**: Denna 3-dagars workshop är för tekniska och företagsledare och hålls på plats på kundens anläggning.

Dagordningen

Dag 1

-   Fokuserar på hur du skyddar, skalar och organiserar data i Microsoft-molnet med hjälp av Azure Data Lake, Azure HDInsight eller Azure SQL Data Warehouse.

Dag 2

-   Beskriver hur du konfigurerar och distribuerar avancerade analyslösningar med Microsoft R och Azure Machine Learning.

Dag 3

-   Beskriver hur du ritar användbara insikter och operationaliserar analyser med Power BI och innehåller en samarbetssession för att sambygga en Power BI-instrumentpanel.

Leverabler

I slutet av workshopen kan klienten definiera en plan på hög nivå och en implementeringsfärdplan för data- och analyslösningar i Microsoft-molnet.

Följande exempelmarkdown-fil är för det tidigare erbjudandet:

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

Ange ditt niosiffriga MPN-ID (Microsoft Partner Network). Om du inte har ett MPN-ID kan du skaffa ett i Microsoft Partner Center.

**Id för partnercenter**

Ange ditt nya partnercenter-ID om du har ett.

**MPN-ID**

Ange en hemlig nyckel för att förhandsgranska erbjudandet på AppSource innan det går live.
Den här identifieraren är inget lösenord.


## <a name="listing-details"></a>Lista detaljer

**Typ av konsulttjänster**

Microsoft fokuserar uteslutande på fast omfattning, fast löptid, uppskattat eller fast pris (eller gratis) och främst förförsäljningsorienterade konsulttjänster för en enskild kund. Typer av tjänster är bedömning, briefing, genomförande, proof of concept och workshop erbjudanden utförs antingen på plats eller praktiskt taget. AppSource-marknaden för konsulttjänster stöder inte listor för hanterade tjänster eller prenumerationstjänster.

>[!Note]
>AppSource-konsulttjänster är inte rätt marknadsplats för prenumerations- eller on-demand-utbildningar.

Följande fem typer av erbjudanden ingår:

-   **Bedömning**: En utvärdering av en kunds miljö för att avgöra tillämpligheten av en lösning och ge en uppskattning av kostnad och timing.
-   **Briefing**: En introduktion till en lösning eller en konsulttjänst för att dra kundernas intresse genom att använda ramverk, demonstrationer och kundexempel. Genomgångar måste utföras på plats.
-   **Implementering**: En komplett installation som resulterar i en fullt fungerande lösning. För den här piloten rekommenderar Microsoft att begränsa till lösningar som kan implementeras på en vecka eller mindre.
-   **Proof of concept**: En implementering med begränsad omfattning för att avgöra om en lösning uppfyller kundens krav.
-   **Workshop**: Ett interaktivt engagemang som utförs i en kunds lokaler och som kan omfatta utbildningstillfällen, genomgångar, utvärderingar eller demonstrationer som bygger på kundens data eller miljö.

**Tillgänglighet för land/region**

Välj det land och den region där det här konsulttjänsterbjudandet är tillgängligt. Ett enda erbjudande kan inte publiceras i flera länder eller regioner. Ett nytt erbjudande måste skapas för varje land eller region.

>[!Note]
>AppSource konsulttjänster är för närvarande live i USA, Storbritannien och Kanada. Du kan skicka ett erbjudande för ett land/en region som ännu inte är live, och det kommer att granskas och förberedas för att sända live. Ett minsta antal erbjudanden som är redo att gå live behövs för att öppna ett nytt land/en ny region, så erbjudanden för länder/regioner som inte bor uppmuntras.

**Branscher**

Välj de branscher som ditt konsulttjänsterbjudande är mest tillämpligt på.

**Varaktighet**

Välj ett tal (till exempel 3 eller 4) under **Varaktighet**och välj sedan **Timme,** **Dag**eller **Vecka**.

**Primära produkter**

Om du vill publicera på Azure Marketplace väljer du **Azure** som primärprodukt. Välj sedan relevanta **lösningsområden**.

Om du vill publicera på AppSource väljer du **Dynamics 365**, **Power BI**eller **PowerApps** som primärprodukt. Du kan också välja andra relevanta **tillämpliga produkter**. Sedan visar ditt erbjudande om konsulttjänster i listor som är associerade med var och en av dessa produkter på AppSource.

**Relevanta kompetenser**

Välj kompetenser som är relevanta för det här erbjudandet för att få dem visas tillsammans med erbjudandeinformationen.

## <a name="marketing-artifacts"></a>Marknadsföring artefakter

**Företagets logotyp (.png-format, 48 x 48 pixlar)**

Ladda upp en bild som visas på panelen för erbjudandet på visningssidan för erbjudandegalleriet. Bilden måste vara en PNG-bild med en upplösning på 48 x 48 pixlar.

**Företagets logotyp (.png-format, 216 x 216 pixlar)**

Ladda upp en bild som visas på informationssidan i erbjudandet. Bilden måste vara en PNG-bild med en upplösning på 216 x 216 pixlar.

**Videor (begränsat till fyra)**

Ladda upp upp till fyra videor fallstudie eller kundreferensvideor. Om du inte har några laddar du upp en video som förklarar företagets expertis som är relaterad till erbjudandet. Om du har en Power BI- eller PowerApps-lösningsmonter kan du ladda upp presentationsvideon här. Videolänkar måste finnas på YouTube eller Vimeo.

**Dokument (begränsade till tre)**

Ladda upp marknadsföringsbroschyren som beskriver ditt konsultserviceerbjudande i detalj. Du kan också ladda upp en företagsöversikt, faktablad eller fallstudier. Se till att dina dokument använder de aktuella namnen på utvalda produkter och inte har konkurrerande produkter från Microsoft.

**Skärmdumpar (begränsad till fem)**

Ladda upp upp till fem bilder som ger mer information om ditt erbjudande, dess slutprodukter eller ditt företag. Ett utdrag av marknadsföringsbroschyren, en relevant bild från en presentation eller en bild som visar företagets fart eller expertis är några exempel.


## <a name="next-steps"></a>Nästa steg

Du är nu redo att [publicera ditt erbjudande om konsulttjänster.](./cpp-consulting-service-publish-offer.md)
