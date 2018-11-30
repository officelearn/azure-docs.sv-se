---
title: SaaS-program Technical publicera Guide | Microsoft Docs
description: Beskriver hur du publicera erbjudanden för SaaS-program på lämplig marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 7e648d6d3bd8d8ac14fb3f9723f4667c1cc33ac6
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620191"
---
<a name="saas-application-technical-publishing-guide"></a>SaaS-program Technical publicera Guide
===========================================

Välkommen till SaaS-program Technical som publicerar guiden! Den här guiden är utformad för att kandidat och befintliga utgivare för att publicera sina program och tjänster i AppSource eller Azure Marketplace med SaaS-program som erbjuder.

En översikt över alla andra Marketplace-erbjudanden, finns det [Marketplace Publiceringsguiden](https://aka.ms/sellerguide).


<a name="what-are-pre-requisites-for-publishing-a-saas-app"></a>Vilka är kraven för att publicera en SaaS-App?
-------------------------------------------------

Den [Cloud Partner Portal](https://cloudpartner.azure.com) ger rollbaserad åtkomst till portalen så att flera personer att samarbeta för att publicera ett erbjudande. Mer information finns i [hantera användare](./cloud-partner-portal-manage-users.md). 

Innan ett erbjudande kan publiceras åt en utgivare konto, ett för individer med *ägare* rollen behöva samtycker till att följa den [användningsvillkor](https://azure.microsoft.com/support/legal/website-terms-of-use/), [Microsoft PrivacyStatement](https://www.microsoft.com/privacystatement/default.aspx), och [Microsoft Azure Certified ProgramAgreement](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


<a name="creating-an-offer"></a>Skapa ett erbjudande
-----------------

Det här avsnittet beskriver processen för hur du skapar ett nytt erbjudande för SaaS-App.

![SaaS erbjuder översikt](media/cpp-creating-saas-offers/saas-offer-overview.png)

SaaS-App-erbjudandet består av fem delar, som beskrivs i följande tabell:

| **Erbjudandet avsnittet**  | **Beskrivning**                                                                                                     |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------|
| Erbjudandeinställningar     | Hjälper till att definiera ett unikt namn och ID för SaaS-appen                                                                         |
| Teknisk information     | Hjälper dig att konfigurera typ av SaaS-lösning och ange anslutningsinformationen för ditt program                            |
| Test Drive         | Valfria avsnittet som hjälper dig att definiera en tjänst som gör att kunder kan testa ditt erbjudande innan de köpa den. |
| Storefront information | Innehåller marknadsföring, juridisk information, lead-hantering och lista avsnitt:   <br/> -Avsnittet marknadsföring kan du ange den beskrivning och logotyper som krävs för erbjudandet att visas i marketplace-användarportalen.  <br/> -Lead-hantering kan du definiera en plats var att omdirigera användaren leads som skapades i användarportalen för Azure Marketplace slutet.  <br/> -Juridiska avsnittet kan du ange sekretesspolicy och användarvillkoren juridisk dokumentation.  |
| Kontakt            | Kan du ange erbjudandets kontaktinformation för support.                                                                  |
|  |  |


### <a name="creating-a-new-offer"></a>Skapa ett nytt erbjudande

När du loggar in på den [Cloud Partner Portal](https://cloudpartner.azure.com)väljer den **nytt erbjudande** objekt i den vänstra menyraden, som visar en meny med tillgängliga erbjudanden. Följande bild visar ett exempel på sådana erbjudanden:

![Nya SaaS-erbjudande](media/cpp-creating-saas-offers/saas-new-offer.png)

Välj det erbjudande som du har godkänts för i listan och ett nytt erbjudande formulär öppnas.

![Formuläret för nya SaaS-erbjudande](media/cpp-creating-saas-offers/saas-new-offer-2.png)

I följande tabell beskrivs fälten erbjudande:

| **Erbjudandet fält** | **Beskrivning**                                                                                            |
|------------------|----------------------------------------------------------------------------------------------------------- |
| ID för erbjudande         | En unik identifierare för erbjudandet i en utgivarprofil för. Detta ID syns i URL: er i produkten och fakturering rapporter. Det får bara innehålla gemena alfanumeriska tecken och bindestreck (-). ID: T får inte sluta med ett bindestreck och kan ha högst 50 tecken. Observera att det här fältet är låst när ett erbjudande lanseras. Till exempel om en utgivare, Contoso, publicerar ett erbjudande med erbjudande-ID exempel-vm, visas den i Azure marketplace som: [https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview](https://azuremarketplace.microsoft.com/) |
| Utgivar-ID     | Publicerings-ID är den unika identifieraren i Marketplace. Alla dina erbjudanden ska vara anslutna publisher-ID. Publicerings-ID kan inte ändras när erbjudandet har sparats.                                                                                       |
| Namn             | Det här är visningsnamnet för ditt erbjudande. Det här är det namn som visas i Azure Marketplace och i Azure Portal. Det får innehålla högst 50 tecken. Här vägledningen är att inkludera ett beskrivande namn för varumärke för din produkt. Omfattar inte ditt företagsnamn, såvida inte som är hur den släpps. Om du marknadsföring erbjudandet på din egen webbplats, se till att namnet exakt hur den visas på din webbplats.             |
|  |  |

Klicka på **spara** spara förloppet. I nästa avsnitt beskrivs planer på att lägga till att ditt erbjudande.


### <a name="technical-information"></a>Teknisk Information

Avsnittet teknisk information kan du ange följande information:

![SaaS erbjuder teknisk information](media/cpp-creating-saas-offers/saas-new-offer-technical-info.png)

Viktigaste beslutet är om du ska ha en SaaS-lista eller ha commerce aktiverat. Om du är en SaaS-lista, måste du välja mellan:

-   Kostnadsfritt - ange en URL för SaaS-program där kunderna kan få din app.
-   Kostnadsfri utvärderingsversion – ange en URL för SaaS-program där kunderna kan köra utvärderingsversionen innan de köper erbjudanden.
-   Kontakta mig – endast relevanta om du har ett lead anslutna system, det här alternativet kan så att kunder kan be att kontaktas och ett lead har delats med dig.

Om du är en SaaS-app som finns på Azure Marketplace och vill aktivera handel via Microsoft-transaktioner, väljer **sälj via Azure**.  
Mer information om hur du ansluter din SaaS-app finns i [SaaS - sälj via Azure](./cloud-partner-portal-saas-offer-subscriptions.md).


### <a name="test-drive"></a>Test Drive

Det är en bra idé att kontrollera att de kan köpa tryggt för att skapa en utvärderingsmiljö för dina kunder. För utvärderingsversionsalternativ som är tillgängliga är Test Drive de mest effektiva på att skapa högkvalitativa leads och ökad konvertering av dessa leads.

Tjänsten ger kunderna med en praktisk Migreringsprocess provversion av din produkt viktiga funktioner och fördelar, visas i ett scenario med verkliga implementering.

![SaaS-erbjudande Test Drive](media/cpp-creating-saas-offers/saas-new-offer-test-drive.png)


#### <a name="how-a-test-drive-works"></a>Så här fungerar en Test Drive

En potentiell kund söker och identifierar ditt program på Marketplace. Kunden loggar in och samtycker till att användningsvillkoren. Kunden får då din miljö som är förkonfigurerade för att prova i ett fast antal timmar, medan du får ett högt kvalificerade lead att följa upp med.

![SaaS-erbjudande test Drive 2](media/cpp-creating-saas-offers/saas-new-offer-test-drive-2.png)

Oavsett hur även komplexa ditt program är Microsoft Test Drive hjälper dig att förverkliga din produkt för kunden. Det finns tre olika typer av Provkörningar, var och en baserat på vilken typ av produkt, scenario och du är på marketplace.

-   **Azure Resource Manager** – en Azure ARM-Test Drive är en Distributionsmall som innehåller alla Azure-resurser som utgör en lösning som skapas av utgivaren. Produkter som passar den här typen av Test Drive är sådana som använder endast Azure-resurser.
-   **Logic App** – en Logic App Test Drive är en Distributionsmall som är avsedd att omfatta alla komplexa lösningsarkitekturer. Alla Dynamics-program eller anpassade produkter bör använda den här typen av Test Drive.
-   **Power BI** -provkörning A Power BI består av en inbäddad länk till en anpassade instrumentpanel. En produkt som du vill visa en interaktiva Power BI-objekt bör använda den här typen av Test Drive. Allt du behöver ladda upp är din inbäddade Power BI-URL.

De viktigaste publishing stegen för att lägga till ett test Drive är:

1.  Definiera ditt Test Drive-scenario
2.  Skapa och/eller ändra dina Resource Manager-mall
3.  Skapa din provkörning stegvisa manuell
4.  Publicera ditt erbjudande

Mer information finns i [Test Drive](./what-is-test-drive.md).


### <a name="storefront-details"></a>Storefront information

SaaS-appar måste de första två avsnitten i en sammanfattning och beskrivning anges om din app.

![SaaS erbjudandeinformation storefront](media/cpp-creating-saas-offers/saas-new-offer-storefront-details.png)

I följande tabell beskrivs erbjudandet\'s Storefront information:

| **Erbjudandet fält**        | **Beskrivning**                                                                                          |
|-------------------------| ---------------------------------------------------------------------------------------------------------|
| Sammanfattning av erbjudandet           | Sammanfattning av ditt erbjudande förslagsvärde. Den visas på söksidan för ditt erbjudande. Det bör vara högst 100 tecken.   |
| Beskrivning av erbjudande       | Beskrivningen som ska visas på detaljsidan för ditt program. Högsta tillåtna antalet är 1300 tecken *Obs* det här fältet accepterar HTML-innehåll med taggar som & ltp\>, & lth1\>, & lth2\>, & ltli\>, osv., som hjälper dig att gör det mycket mer lättillgängligt. Publicering av portal-teamet arbetar med att lägga till en funktion för att aktivera en för att visa en förhandsgranskning av deras storefront information att upprepade gånger göra innehållet mer lättillgängligt – under tiden kan du använda valfri du kan använda valfri online i realtid HTML-verktyg som http://htmledit.squarefree.com att se hur din beskrivning skulle se ut. |
| Branscher              | Välj branscher som ditt erbjudande är bäst anpassad efter. Om din app är kopplad till flera olika branscher, kan du välja högst två. |
| Föreslagna kategorier    | Välj kategorier som ditt erbjudande är bäst anpassad efter. Du kan välja högst tre kategorier.     |
| Programversion     | Ange det lägre versionsnumret för ditt program                                                                |
| Sökord (Max 3) | Ange upp till tre sökord som kunder kan använda för att hitta dina program på Marketplace storefront-webbplatsen. |
|  |  |


### <a name="marketing-artifacts"></a>Marknadsföring artefakter

Avsnittet marknadsföring artefakter kan du definiera Azure Marketplace marknadsföring tillgångar som logotyper, videor, skärmbilder och dokument:

![SaaS erbjuder marknadsföring artefakter](media/cpp-creating-saas-offers/saas-new-offer-marketing-artifacts.png)

I följande tabell beskrivs de marknadsföring fält:

| **Erbjudandet fält** | **Beskrivning**                                                                                                          |
|------------------| ------------------------------------------------------------------------------------------------------------------------ |
| Logotyper            | Om du är en **sälj via Azure** SaaS-appen, bör du ge alla bilder med logotyper. Om du bara en lista, krävs endast 2 logotyper. Alla logotyper laddades upp i den [Cloud Partner Portal](https://cloudpartner.azure.com) bör använda följande riktlinjer:     <br/> – Håll nere antalet primära och sekundära färger på din logotyp. Azure-designen har en enkel färgpalett.     <br/> -Undvik att använda svart eller vitt som bakgrundsfärgen för din logotyp. Temafärger i Azure-portalen är svart och vit. Använd istället en färg som gör din logotyp framstående i Azure Portal. Vi rekommenderar enkla primärfärger. Om du använder en genomskinlig bakgrund, se till att logotypen och texten inte är svart, vit eller blå.     <br/> – Använd inte en toning bakgrund på logotypen.     <br/> -Undvik att placera text, även företaget eller varumärke namn på logotypen. Utseendet och känslan av din logotyp måste vara ”fast” och Undvik toningar.    <br/> -Med logobilden bör inte vara har sträckts ut.                   |
| Videoklipp           | Kan du lägga till länkar för videor i ditt erbjudande. Du kan använda länkar till YouTube eller Vimeo videor som visas tillsammans med ditt erbjudande till kunder. Du måste också ange en miniatyrbild av videon med en png-bild av 1 280 x 720 bildpunkter. Du kan ha högst fyra videor per erbjudandet. |
| Dokument        | Kan du lägga till marknadsföring dokument till ditt erbjudande. Alla dokument måste vara i PDF-format och du kan ha högst tre dokument per erbjudandet.                                                                                                                                                      |
| Skärmbilder      | Kan du lägga till skärmdumpar av ditt erbjudande. Det finns högst fem skärmbilder som kan läggas till per erbjudandet. Maximal avbildningens storlek är 1 280 x 720 bildpunkter.                                                                                                                                             |
| Användbara länkar     | Kan du lägga till externa URL: er för ditt erbjudande för att peka på Arkitekturdiagram eller andra webbplatser som en kund vill se.                                                                                                                                                              |
|  |  |

Följande bild visar hur informationen visas i sökresultatet Marketplace:

![SaaS erbjuder marknadsföring publisher-info](media/cpp-creating-saas-offers/saas-new-offer-marketing-publisher-info.png)

Följande bild visar hur erbjudandet visas i Marketplace efter en kund klickar på panelen mindre erbjudande:

![SaaS erbjuder marknadsföring publisher info2](media/cpp-creating-saas-offers/saas-new-offer-marketing-publisher-info-2.png)


### <a name="lead-management-and-legal-information"></a>Leda hanterings- och juridisk Information

Juridiska avsnittet kan du ange erbjudandets juridisk dokumentation.
Det finns två juridiska dokument som krävs för varje erbjudande för SaaS-program: sekretesspolicy och användningsvillkor med. Mer information finns i  
[Konfigurera kundleads](./cloud-partner-portal-get-customer-leads.md).

![SaaS erbjuder marknadsföring juridisk information](media/cpp-creating-saas-offers/saas-new-offer-legal-info.png)

I följande tabell beskrivs avsnittsegenskaperna juridiska:

| **Erbjudandet fält**   | **Beskrivning**                                                                                 |
|--------------------| ----------------------------------------------------------------------------------------------- |
| Sekretesswebbadress för principen | URL till företagets sekretesspolicy.                                                       |
| Användningsvillkor       | Användningsvillkoren för ditt erbjudande. Skriv eller kopiera och klistra in användningsvillkoren här. Grundläggande HTML-användning tillåts, till exempel som det här fältet tar html innehåll med taggar som & ltp\>, & lth1\>, & lth2\>, & ltli\>osv. *Viktiga* vi rekommenderar starkt att du granskar och förhandsgranska HTML som du skapade i en webbläsare innan du skickar erbjudandet. |
|  |  |


### <a name="contact-information"></a>Kontaktinformation

I det här avsnittet anger du supportkontakter från ditt företag som ansvarar för att stödja kunder som använder det här erbjudandet.
Det finns tre huvudområden: Engineering Contact, Supportkontakt och stöd för URL: er:

![SaaS erbjuder marknadsföring kontaktinformation](media/cpp-creating-saas-offers/saas-new-offer-contact-info.png)


| **Kontakta**         | **Beskrivning**                                                                                                                          |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| Engineering Contact | Ange namn, e-post och telefonnumret till en tekniska kontakt som Microsoft kan kontakta för support och företag problem. |
| Supportkontakt     | Ange namn, e-post, telefonnummer och stöd för URL: en som dina kunder kan kontakta när de har supportärenden.                  |
|  |  |


När ditt erbjudande är klar och du trycker på Publish går erbjudandet igenom certifiering. Vi testa din SaaS-app via en manuell verifiering runt Testa URL: en om du har en lista eller slutpunkter om du har säljer via Azure valts. Under den här manuellt godkännande kan bestämma vi också lämpliga storefront din app ska visas på (AppSource, Azure Marketplace eller båda).

<a name="updating-the-offer"></a>Uppdaterar erbjudandet
------------------

Det finns olika typer av uppdateringar som du kan göra att ditt erbjudande när den har publicerats och är aktiv. Alla ändringar du gör i den nya versionen av ditt erbjudande ska sparas och publiceras igen att den återspeglar i Marketplace.

<a name="deleting-an-existing-offer"></a>Tar bort ett befintligt erbjudande
--------------------------

Du kan välja att ta bort erbjudandet från Marketplace. Tar bort erbjudandet garanteras att nya kunder inte längre kan köpa eller distribuera ditt erbjudande, men det inte påverkar befintliga kunder. Uppsägning av erbjudandet handlar om att säga upp tjänsten och/eller licensavtalet mellan dig och dina befintliga kunder.

Vägledning och principer som relaterar till borttagning av erbjudande och uppsägning regleras av Publiceringsavtalet för Microsoft Marketplace (se avsnitt 7) och Deltagandepolicyer (se avsnittet 6.2). Det här avsnittet innehåller information om olika stöds delete-scenarier och steg du kan vidta för dessa.

### <a name="delete-the-live-offer"></a>Ta bort Live erbjudandet

Det finns olika aspekter som måste vara tar hand om om en begäran om att ta bort ett erbjudande med live. Följ stegen nedan för att få vägledning från support-teamet att ta bort ett erbjudande med live från Azure Marketplace:

1.  Generera ett supportärende med den här länken
2.  Välj i listan problemtyp **hantera erbjudanden**, och välj i listan kategori **ändra ett erbjudande/SKU eller redan i produktion**.
3.  Skicka förfrågan

Supportteamet vägleder dig genom borttagningsprocessen erbjudandet.

> [!NOTE] 
> Tar bort ett erbjudande påverkar inte aktuella inköp av erbjudandet. Dessa kundinköp fortsätter att fungera som tidigare.
Men erbjudandet inte tillgängligt för alla nya inköp när borttagningen är klar.
