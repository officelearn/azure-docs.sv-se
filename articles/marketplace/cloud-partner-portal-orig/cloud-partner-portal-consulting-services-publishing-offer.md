---
title: Konsulttjänster | Microsoft Docs
description: Guide för att definiera och publicera en konsult tjänsteerbjudanden.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: b3f6b9166afce9ca0cdeaa1c9dd6e10a5a976453
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811593"
---
<a name="defining-and-publishing-your-consulting-services-offer"></a>Definiera och publicera erbjudandet Consulting Services
======================================================

Den här guiden är utformad för att hjälpa dig att definiera och publicera dina konsult erbjuder i partnerportalen i molnet.

<a name="define-your-consulting-services-offer"></a>Definiera ditt Consulting Services-erbjudande
-------------------------------------

Definiera din paketerade konsult tjänst-erbjudande. Fokusera på fast omfattning, fast varaktighet, Uppskattat, fast pris (eller kostnadsfri) och främst certifikathierarkin sales-orienterade erbjudanden för en enda kund. Välj upprepningsbara paketerade engagemang som är populära och effektiva för att driva verksamheten åt dig.

<a name="publish-a-consulting-service-offer"></a>Publicera en konsult tjänsterbjudande
----------------------------------

I följande avsnitt beskrivs processen för att publicera erbjudandet konsulttjänster

1.  Skapa ett nytt erbjudande
2.  Definiera inställningar för erbjudandet
3.  Ange information i butiken och om du vill publicera på Azure Marketplace eller i AppSource.
4.  Publicera ditt erbjudande

### <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

Utför följande steg för att skapa ett nytt erbjudande:

1.  På huvudmenyn för Cloud Partner Portal, Välj **nytt erbjudande**.
2.  Nytt erbjudande-menyn väljer du **Consulting Service**.

    ![Skapa en ny erbjuder konsulttjänster](media/consulting-services-publishing-offer/cppselectnewconsultingoffer.png)

### <a name="define-offer-settings"></a>Definiera inställningar för erbjudandet

På skärmen för nytt erbjudande är det första steget att skapa erbjudandet identiteten.
Erbjudandet identiteten består av tre delar:

1.  ID för erbjudande
2.  Utgivar-ID
3.  Namn

De olika delarna beskrivs i följande avsnitt.

#### <a name="offer-id"></a>ID för erbjudande

Det här är ett unikt namn som du skapar när du skickar in erbjudandet. Den måste bestå enbart av gemena alfanumeriska tecken, bindestreck eller understreck. ID: T syns i URL-Adressen och påverkan motorn sökresultat. Till exempel *yourcompanyname\_exampleservice*

I exemplet visas erbjudande-ID kommer att läggas till i Publicerings-ID för att skapa en unik identifierare. Detta visas som en permanent länk som kan belasta och indexeras av de olika sökmotorer.

**När ett erbjudande är live kan den\'t uppdateras**

#### <a name="publisher-id"></a>Utgivar-ID

Detta är relaterat till ditt konto. När du har loggat in med ditt organisationskonto visas Publicerings-ID i den nedrullningsbara menyn.

#### <a name="name"></a>Namn

Det här är vad som visas som erbjudandenamn på AppSource eller Azure Marketplace.

**Viktigt:** bara ange namnet på den faktiska servicen här. Omfattar inte varaktighet och typ av tjänst.

I följande exempel av Edgewater Fullscope visas hur erbjudandenamn är klar. Erbjudandenamn ser ut så här:

![Skapa en ny erbjuder konsulttjänster](media/consulting-services-publishing-offer/cppsampleconsultingoffer.png)

Erbjudandenamn består av fyra delar:

-   **Varaktighet:** du definierar det på fliken Storefront information i redigeraren. Varaktighet kan uttryckas i timmar, dagar eller veckor.
-   **Typ av tjänst:** du definierar det på fliken Storefront information i redigeraren. Typer av tjänster finns utvärdering, genomgång, implementering, konceptbevis och Workshop.
-   **Preposition:** infogats av granskaren
-   **Namn:** det här är vad du har definierat i erbjuder inställningssidan.

Följande lista innehåller flera väl namngivna erbjudandet namn:

-   Essentials för professionella tjänster: genomgång för 1 tim
-   Molnplattform för migrering: 1 tim genomgång
-   PowerApps och Microsoft Flow: 1-dagars Workshop
-   Azure Machine Learning-tjänster: 3 veckor PoC
-   Bricka och klicka på exempel på lösning: genomgång för 1 tim
-   Hämta dina egna Data: 1 vecka Workshop
-   Molnet Analytics: 3 dagars Workshop
-   Utbildning för Power BI: 3 dagars Workshop
-   Försäljning hanteringslösning: 1 vecka implementering
-   CRM-Snabbstart: 1-dagars Workshop
-   Dynamics 365 for Sales: 2-dagars utvärdering

När du har slutfört den **erbjuder inställningar** fliken kan du spara ditt bidrag. Erbjudandenamn kommer nu att visas ovanför redigeraren och du hittar den i alla erbjuder.

### <a name="enter-storefront-details"></a>Ange Storefront information

Därefter måste du ange information för din butik. Storefront information består av följande avsnitt:

-   Erbjudandeinformation
-   Utgivarinformation
-   Lista information
-   Marknadsföring artefakter

#### <a name="offer-details"></a>Erbjudandeinformation

Avsnittet med erbjudandet innehåller följande fält:

-   Sammanfattning av erbjudandet
-   Beskrivning av erbjudande

##### <a name="offer-summary"></a>Sammanfattning av erbjudandet

Sammanfattning av erbjudandet är en kort beskrivning av ditt erbjudande som visas under erbjudandenamn. Du bör använda oformaterad text när du lägger till sammanfattning av erbjudandet och bör inte ha radbrytningar. Här följer några bra exempel på erbjudandet sammanfattningar tillsammans med motsvarande erbjudandet namn:

*Exempel 1*

-   **Erbjudandets namn:** molnbaserade analyser: 3 dagars Workshop
-   **Erbjuder Sammanfattning:** översikt över Microsoft Azure och Power BI, bedömning av aktuella miljön och mini POC.

*Exempel 2*

-   **Erbjudandets namn:** industriella Azure IoT: 30-dagars konceptbevis
-   **Erbjuder Sammanfattning:** skapa en industriella anslutna produkten pilotprojekt för att på ett säkert sätt ansluta utrustning i fältet till en Azure IoT Hub-lösning med instrumentpaneler, rapporter och meddelanden.

*Exempel 3*

-   **Erbjudandets namn:** professionella tjänster: genomgång för 1 tim
-   **Sammanfattning av erbjudandet:** översikt och demonstration av förkonfigurerade, utökad Dynamics 365 för åtgärder lösningen ger förbättrad hantering av projekt, fakturering och resurser för professionella tjänster.

*Exempel 4*

-   **Erbjudandets namn:** Power BI i din värld: 4 tim Workshop
-   **Erbjuder Sammanfattning:** komma igång med din första instrumentpanel och få metodtips. För upp till 12 studenter som genomförs på plats.

*Exempel 5*

-   **Erbjudandets namn:** Dynamics och projekt: 3-dagars utvärdering
-   **Erbjuder Sammanfattning:** krav samla in och utvärdering för ERP-lösning som utformats för professionella tjänster företag och projekt-drivna företag.

##### <a name="offer-description"></a>Beskrivning av erbjudande

Det här är en beskrivning av Consulting tjänsteerbjudanden. En bra erbjudandet beskrivning innehåller mer information om vad samarbetet kommer att se ut och vad blir slutet leverans till kunden. Det tydligt hjälper kunden förstå vad de får.

Ta inte med via e-post eller telefonnummer för att kontakta dig i ditt erbjudande beskrivning. Det blir en kontakta mig med ditt erbjudande som överför leder till målet för lead-hantering som du identifierar på ditt erbjudande.

Beskrivning av erbjudande anger du i Markdown-format. Om du inte är bekant med Markdown eller formatering för HTML, kan du granska resurser på den [Microsoft dokumentationswebbplats](https://docs.microsoft.com/contribute/how-to-write-use-markdown).

Dessa format säkerställer att ditt erbjudande har högsta läsbarhet för kunder.

Håll det kort beskrivning av ditt erbjudande och följa teckengränsen som användarna inte tycker om att läsa mycket text. Du har fortfarande möjlighet att ladda upp marknadsföring broschyrer, informationsbladet och andra dokument som beskriver ditt erbjudande i mer detalj.

I följande exempel visar en bra består beskrivning av erbjudande och dess relaterade namn och en sammanfattning:

**Erbjudandets namn:** molnbaserade analyser: 3 dagars Workshop

**Erbjuder Sammanfattning:** översikt över Microsoft Azure och Power BI, bedömning av aktuella miljön och mini POC.

**Erbjuder beskrivning:** det här 3-dagars workshop för tekniska och affärsmässiga ledare och lagras på plats hos klientens anläggning.

***Agenda***

Dag 1

-   Fokuserar på hur du skyddar, skala och organisera data i Microsoft-molnet med Azure Data Lake, HDInsight eller Azure SQL Data Warehouse.

Dag 2

-   Beskriver hur du konfigurerar och distribuerar lösningar för avancerade analyser med Microsoft R- och Azure Machine Learning.

Dag 3

-   Beskriver hur du rita användbara insikter och operationalisera analyser med Power BI, inklusive en samarbetsfunktioner session för att skapa en Power BI-instrumentpanel tillsammans.

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


#### <a name="publisher-information"></a>**Utgivarinformation**

**MPN-ID**

Ditt 9 siffror Microsoft Partner Network (MPN)-ID. Om du inte har en MPN-ID, går du till Microsoft Partner Center till något.

**Partnercenter-ID**

Ny Partner Center-ID, om du har en.

**MPN-ID**

Ange en hemlig nyckel om du vill förhandsgranska ditt erbjudande på AppSource innan de aktiveras.
Detta är inte ett lösenord.

#### <a name="listing-details"></a>Lista information

**Consulting tjänsttyp**

Microsoft är koncentrera exklusivt på fasta gränser, fast varaktighet, beräknad eller fast pris (eller kostnadsfri) och främst förproduktions-sales-orienterade consulting Tjänsterbjudanden för en enda kund och för utvärdering, implementering, konceptbevis, information, eller Workshop erbjudanden utförs antingen på plats eller virtuellt (implikationer måste vara ledda på plats).

För följande typer av erbjudanden ingår:

-   **Utvärderingen:** en utvärdering av en kunds miljö för att fastställa tillämpligheten hos en lösning och ange en uppskattning av kostnaden och val av tidpunkt.
-   **Genomgång:** en introduktion till en lösning eller en tjänst som konsult för att rita kunden närheten med hjälp av ramverk, demonstrationer och Kundexempel. Implikationer måste vara ledda på plats.
-   **Implementering:** en fullständig installation som resulterar i en fullt fungerande lösning. Microsoft rekommenderar att begränsa till lösningar som kan vara implementerade i en vecka eller mindre för detta pilotprogram.
-   **Konceptbevis:** implementering begränsat omfång för att avgöra om en lösning som passar kraven för en kund.
-   **Workshop:** en interaktiv engagement utförs i en kunds lokala som kan inkludera utbildningssessioner, implikationer, utvärderingar och demonstrationer som bygger på kundens data eller miljö.

**Tillgänglighet för land/region**

Välj land och region där den här konsult tjänsterbjudande ska vara tillgänglig. Ett erbjudande kan inte publiceras i flera länder eller regioner. Ett nytt erbjudande måste skapas för varje land eller region.

**Branscher**

Välj branscher som konsult tjänsten erbjuder mest gäller för.

**Varaktighet**

Välj ett tal (till exempel 3, 4, etc.) under varaktighet, och välj sedan timmen, dagen eller veckan.

**Primär produkter**

Om du vill publicera på Azure Marketplace, Välj **Azure** som huvudprodukten och välj sedan relevant Lösningsområden.

Om du vill publicera till AppSource, Välj **Dynamics 365**, **Power BI**, eller **PowerApps**som din primära produkt. Du kan också välja andra relevanta tillämpliga produkter, och din konsulttjänst erbjuda visas i listor som är kopplade till var och en av dessa produkter på AppSource.

**Relevanta kompetenser**

Välj kompetenser relevanta för det här erbjudandet att de visas tillsammans med informationen i erbjudandet.

#### <a name="marketing-artifacts"></a>Marknadsföring artefakter

**Företagets logotyp (PNG-format, 48 x 48 bildpunkter)**

Ladda upp en bild som visas på panelen för ditt erbjudande i visningssida för erbjudande-galleriet. Bilden måste vara en .png-avbildning med en upplösning på 48 x 48 bildpunkter.

**Företagets logotyp företagets logotyp (PNG-format, 216 x 216 bildpunkter)**

Ladda upp en bild som visas på informationssidan med i ditt erbjudande. Bilden måste vara en .png-avbildning med en upplösning på 216 x 216 bildpunkter.

**Videor (Max 4)**

Ladda upp upp till fyra kunden Fallstudie videor eller kund referens videor. Om du inte har någon kan du ladda upp en video som förklarar ditt företags expertis relaterade till erbjudandet. Ladda upp videon showcase här om du har en Power BI eller PowerApps solution showcase. Videolänkar måste vara för YouTube eller Vimeo.

**Dokument (Max 3)**

Ladda upp marknadsföring publikationer beskriver dina konsult tjänsteerbjudanden i detalj. Alternativt kan du också ladda upp företagets översikt, informationsbladet eller fallstudier.

**Skärmbilder (Max 5)**

Överföra upp till fem avbildningar som innehåller mer information om erbjudandet, erbjudande slutprodukter eller ditt företag. Till exempel ett kodfragment till broschyrens marknadsföring, en relevanta bild från en presentation eller en bild på företagets intresset eller expertis.

### <a name="publish-your-offer"></a>Publicera ditt erbjudande

När du har slutfört erbjuder inställningar, Storefront information och kontakter, Välj **publicera** och ange en e-postadress. När Microsoft är redo att publicera ditt erbjudande, får du ett e-postmeddelande om du vill förhandsgranska den innan det lanseras. Du kan gå tillbaka till portalen för att kontrollera status för erbjudandet när som helst i tid.

Erbjudanden kan visas i en ”publicera avbryts” eller ”publicera misslyckades” status under publiceringsprocessen. Det är en normal del av processen och att Microsoft kan göra ändringar i ditt erbjudande. Om du ser ditt erbjudande i ”Publicera har avbrutits”, lämna den i denna status.
