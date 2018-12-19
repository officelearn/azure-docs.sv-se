---
title: Cortana intelligence | Microsoft Docs
description: Publicera ett erbjudande med Cortana intelligence.
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
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 74cde720af012b3355b813cf6da2b2bdf10b9b8e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51257215"
---
# <a name="publish-a-cortana-intelligence-offer-using-the-cloud-partner-portal"></a>Publicera en Cortana Intelligence-erbjudande med partnerportalen i molnet

Den här artikeln beskriver hur du publicerar en Cortana Intelligence-erbjudande med partnerportalen i molnet.

## <a name="prerequisites"></a>Förutsättningar

Cloud Partner Portal stöder rollbaserad åtkomst till portalen, vilket gör det möjligt för deltagare att samarbeta på Publicera ett erbjudande. Mer information finns i [Portal hantera Molnanvändare](./cloud-partner-portal-manage-users.md).

Innan ett erbjudande kan publiceras åt en utgivare konto, ett för individer med \"ägare\" rollen behöva samtycker till att följa den [användningsvillkor](https://azure.microsoft.com/support/legal/website-terms-of-use/), [sekretesspolicy för Microsoft](https://www.microsoft.com/privacystatement/default.aspx), och [Microsoft Azure Certified-programavtalet](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="to-start-creating-a-cortana-inteligence-offer"></a>Börja skapa ett erbjudande med Cortana Inteligence

När alla krav har uppfyllts, är du redo att börja redigera Cortana Inteligence erbjudandet.

1.  Logga in på den [partnerportalen i molnet](http://cloudpartner.azure.com/).
2.  I det vänstra navigeringsfältet väljer **+ nytt erbjudande**.
3. Välj **Cortana Intelligence**.
4. Välj den **redigeraren** fliken i den **nytt erbjudande** vy för att konfigurera följande alternativ:
    -   Erbjudandeinställningar
    -   Teknisk information
    -   Storefront information
    -   Contacts

    Vart och ett av alternativen ovan visar ett formulär där du fyller.  Obligatoriskt fält för varje formulär är markerade med en röd asterisk (\*).

## <a name="to-configure-offer-settings"></a>Konfigurera inställningar för erbjudande

Erbjuder inställningarna innehåller grundläggande information om erbjudandet, till exempel en erbjudande-ID, Publicerings-ID och erbjudandenamn.

### <a name="offer-id"></a>ID för erbjudande

Det här är en unik identifierare för erbjudandet i en utgivarprofil för.
Detta ID syns i URL: er i produkten. Det får bara innehålla gemena alfanumeriska tecken och bindestreck (-). ID: T får inte sluta med ett bindestreck och kan ha högst 50 tecken. 
>[!Note]
>Det här fältet är låsta när ett erbjudande lanseras.

**Exempel:**

Om utgivaren **contoso** skapar ett erbjudande med erbjudande-ID *exempel Cortana Intelligence*, den visas i AppSource som ”https://appsource.microsoft.com/marketplace/apps/**contoso**.*exempel – Cortana Intelligence* ? fliken = Overview ”.

### <a name="publisher-id"></a>Utgivar-ID

En listruta med utgivare profiler. Använd för att välja den publisher-profil som du vill publicera det här erbjudandet under.

>[!Note]
>Det här fältet är låsta när ett erbjudande lanseras.

### <a name="name"></a>Namn

Visningsnamn för ditt erbjudande. Det här namnet visas i [AppSource](https://appsource.microsoft.com). Det får innehålla högst 50 tecken.

När du är klar tillhandahåller den information som behövs i erbjuder inställningar, Välj **spara** att gå vidare till teknisk information om en del av erbjudandet. alternativet.

## <a name="to-configure-technical-info"></a>Så här konfigurerar du teknisk information

Använd den här vyn för att tillhandahålla teknisk information som ska visas på sidan erbjudandet. Följande gäller för den här vyn.

### <a name="partner-mpn-id"></a>Partner MPN-Id

Om du är en registrerad Microsoft-partner kan du gå till den [partner webbplats](https://partners.microsoft.com/) och logga in och hämta din organisation Partner-Id. Ange det ID för den **Partner MPN-Id**.

### <a name="analytics-components-used"></a>Analytics-komponenter som används

Välj de komponenter som används av ditt erbjudande. Välj minst 1 komponent. Välj **Microsoft R** endast om du använder någon av FRU-licenser för SQL 2016 R services, HDInsight Premium eller FRU som körs på virtuella datorer.

### <a name="additional-components-used"></a>Ytterligare komponenter som används

Välj alla andra komponenter som använder din lösning.

### <a name="customer-name-using-solution"></a>Kundens namn med hjälp av lösningen

Ange kundnamnet som använder den här lösningen i produktion. 

>[!Note]
>Den här informationen publiceras inte på AppSource. Den används endast för utvärdering av lösningen.

### <a name="azure-consumption-requirement-met"></a>Krav för Azure-förbrukning uppfyllt?

Ange huruvida lösningen genererar minst $1 K per månad per kund av programsvitkomponenterna i Azure eller om lösningen använder Microsoft R.

>[!Note]
>Den här informationen publiceras inte på AppSource. Den används endast för utvärdering av lösningen.

### <a name="demo-video-url"></a>Demo Video-URL

Ange en URL för en video för lösning/app demo att teamet före försäljning kan visa kunderna. 

>[!Note]
>Den här informationen publiceras inte på AppSource. Den används endast för utvärdering av lösningen.

#### <a name="demo-video-guidelines"></a>Demo video riktlinjer

- Videolängd ska vara mindre än 15 minuter.
- Videon ska redigeras minimalt registrering av lösningens funktioner. 
- Videon visar de viktigaste aspekterna i funktionen användarinriktade och fokuserar på integreringen av avancerad analys. 
- Demonstrera videor **inte** vara tillgänglig offentligt till kunder men förväntas vara representativ för hur lösningen *skulle vara* visas att en potentiell kund. Därför ska de skriptbaserade och repeterbar.
- Använd valfri skärm inspelningsverktyget som exporterar ett standardformat video (till exempel MPEG) för att skapa din video. 

Följande instruktioner visar hur du skapar ett videoklipp med Skype för företag. 

1. [Starta ett möte](https://support.office.com/article/Start-a-Skype-for-Business-conference-call-8dc8ac52-91ac-4db9-8672-11551fdaf997)
2. [Dela ditt skrivbord](https://support.office.com/article/Share-your-screen-in-Skype-for-Business-2d436dc9-d092-4ef1-83f1-dd9f7a7cd3fc)
3. [Börja spela in](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)
4. När du stoppar inspelningen, [använda inspelning manager för att publicera din inspelning](https://support.office.com/article/Recording-Manager-save-and-publish-59a3beb7-c700-40cf-ab21-bc82a2b06351)

Ladda upp din inspelade video till en tjänst som gör att du kan generera en delad URL. Till exempel en [gäst länk i OneDrive eller Sharepoint](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232).

### <a name="supported-regions"></a>Regioner som stöds

Välj alla regioner som har stöd för din lösning. Välj minst 1 region.

### <a name="power-bi-desktop-file-pbix"></a>Power BI desktop-fil (.pbix)

Om så är tillämpligt, överföra en .pbix-fil. Se till att data har importerats till filen, inte till externt. Du skapar vi den inbäddade rapporten.

### <a name="solution-architecture"></a>Lösningsarkitektur

Överför ett dokument som beskriver din lösningsarkitektur. För att ladda upp en lösning arkitektur diagram riktlinje Se [Advanced Analytics arbetsflöde lösningsmallen](https://partnersprofilesint.blob.core.windows.net/partner-assets/documents/AppSource%20Solution%20Publishing%20Guide%20Docs/Advanced%20Analytics%20Solution%20Workflow%20Template%20V.2017.3.23.pptx).

>[!Note]
>Den här informationen publiceras inte på AppSource. Den används endast för utvärdering av lösningen.

### <a name="select-segments"></a>Välj segment

Välj alla relaterade branschsegment. Om din app är för alla olistade segment, anger du segmentnamn i den **andra** fält. Du är begränsad till tre orden för segment.

### <a name="select-business-processes"></a>Välj affärsprocesser

Välj affärsprocesser som bäst beskriver din lösning. Om din app är för alla processer som inte finns i listan, ange Processnamnet på i den **andra** fält. Du är begränsad till tre orden för processen.

### <a name="trial-info"></a>Utvärderingsversion Info

-   **URL för SaaS-utvärdering:** ange URL: en för utvärderingsversionen för din app.
-   **Test Drive utvärderingsversion URL:** ange URL: en för din app Test Drive-upplevelse.

Mer information om utvärderingsversioner finns i **apptyp** i nästa avsnitt av den här artikeln.

När du är klar tillhandahåller den information som behövs i teknisk information, Välj **spara** att gå vidare till butiken information om en del av erbjudandet. 

## <a name="to-configure-storefront-details"></a>Konfigurera Storefront-information

### <a name="offer-summary"></a>Sammanfattning av erbjudandet

Det här är en sammanfattning av ditt erbjudande förslagsvärde. Den visas på söksidan för ditt erbjudande. Den maximala längden på sammanfattningen är 100 tecken.

### <a name="offer-description"></a>Beskrivning av erbjudande

Det här är den beskrivning som visas på detaljsidan för din app.
Den maximala längden på beskrivningen är 1300 tecken.

Observera att det här fältet tar html innehåll med taggar som \<p\>, \<h1\>, \<h2\>, \<li\>, osv., där du kan göra innehållet mycket mer lättillgängligt. Publicering av portal-teamet arbetar med att lägga till en funktion för att aktivera en för att visa en förhandsgranskning av deras storefront information att upprepade gånger göra innehållet mer lättillgängligt – under tiden kan du använda valfri du kan använda valfri online i realtid html-verktyg som [ http://htmledit.squarefree.com ](http://htmledit.squarefree.com/) att se hur din beskrivning ser ut.

Föreslagna formatet för beskrivning är att du har delar upp text i underordnade avsnitt baserat på värdet propositions, var och en med markerad med en underrubrik. Besökare översiktlig vanligtvis över fältet ”erbjuda sammanfattning” och underrubriker att hämta gist av vilka app-adresserna och varför du bör överväga de appen i bara en snabb överblick. Därför är det viktigt att hämta användaren\'s uppmärksamhet ge dem en anledning att läsa på att hämta informationen.

#### <a name="partner-examples"></a>Partner-exempel

- [Neal Analytics inventering optimering](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)
- [Plexure Retail-anpassning](https://appsource.microsoft.com/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [AvePoint Medborgartjänster](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

### <a name="industries"></a>Branscher

Välj bransch som din app är bäst anpassad efter. Om din app är kopplad till flera olika branscher, lämnar du fältet tomt.

### <a name="categories"></a>Kategorier

Välj kategorier som är relevanta för din app. Välj högst två kategorier.

### <a name="app-type"></a>Apptyp

Välj typ av utvärderingsversion som stöd för din app på AppSource. Välj på följande försök:
- **Kostnadsfria** innebär att din app är kostnadsfria.
- **Utvärderingsversion** innebär att kunder kan testa din för en angiven tidsperiod.
- **Begäran för utvärderingsversion** innebär att kunder kan begära en utvärderingsversion av appen.

Partner kan ge två typer av utvärderingsversion upplevelser på AppSource.

- Den **utvärderingsversion** alternativet, som även kallas **kunden ledde utvärderingsversioner (CLT)** kan vara något av följande typer: 
    - SaaS-utvärdering
        - Kunden kan navigera till en Url som du eller din partner tillhandahåller. Kunden passerar AAD federerad SSO till har en tid boxed utvärderingsversionen.
        - Det här är en SaaS-app med flera innehavare som isolerar en användares/konfigurationsdata från andra användare. Eller den här guiden innehåller endast en delmängd som använder skrivskyddade åtgärder.

        **Exempel:**

        - [AFS POP detaljhandel körning](https://appsource.microsoft.com/product/web-apps/afs.fa9fc926-3bc3-43dd-becd-3ef41b52c10b?tab=Overview)
        - [AvePoint Medborgartjänster](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview). (Den här appen innehåller en skyddad erfarenhet med tydliga sökvägar för en vald uppsättning användare personer.)

     - Test Drive
        - Din (eller din partner) lösning eller en delmängd av det kan paketeras med hjälp av Azure Resource Manager-mallar som AppSource instansiera och. AppSource kan du hantera appen i ett partnerprenumeration med tiden boxing och underhålla varma/kalla antalet instanser osv.
        - Vi kan erbjuda mallar och exempelkod som hjälper dig att om du väljer det här alternativet.

        **Exempel:**

        - [Neal Analytics inventering optimering](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome)

- Den **Utvärderingsförfrågan** (**Partner ledde utvärderingar / PLT**) alternativet kräver kunder att fylla ett kontaktinformation formulär för partner uppföljning. Partnern in och ger en demonstration eller utvärderingsversion av appen. Mer information finns i [AppSource utvärderingsversionen genomgången](https://aka.ms/trialexperienceforwebapps) video för en översikt.

>[!Note]
>Data visar att **kunden ledde utvärderingsversioner** har en högre generering av leads potentiella än **Partner ledde utvärderingsversioner**.


### <a name="help-link-for-your-app"></a>Hjälplänk för din app

Ange en URL till en sida med hjälpinformation för din app.

### <a name="supported-countriesregions"></a>Länder/regioner

Det här fältet anger de länder/regioner där ditt erbjudande ska vara tillgänglig för utvärdering.

![Länder/regioner](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot3.png)

### <a name="supported-languages"></a>Språk som stöds

Välj de språk som har stöd för din app. Om din app har stöd för språk som inte finns på den här listan, publicera ditt erbjudande och mejla oss på: <appsource@microsoft.com> som upplyser oss om stöd för andra språk.

### <a name="app-version"></a>Appversion

Ange det lägre versionsnumret för din app.

### <a name="products-your-app-works-with"></a>Produkter som din app fungerar med

Vilka specifika produkter som din app fungerar med. Du kan visa upp till 3 produkter. Om du vill visa en produkt, Välj den **plustecknet** (bredvid nytt) och en öppen textfält skapas åt dig. Ange namnet på en produkt som din app fungerar med.

### <a name="hide-key"></a>Dölj nyckel

Det här är en nyckel som kombineras med förhandsgransknings-URL för erbjudandet att dölja erbjudande från offentligt. Det är inte ett lösenord. Du kan ange valfri alfanumerisk sträng.

### <a name="offer-logo-small"></a>Erbjudandet logotyp (liten)

Den här logotypen visas på söksidan för din app. Png-avbildningsformatet är det enda format som är tillåtet. Bildstorleken är 48 x 48 pixlar.

### <a name="offer-logo-large"></a>Erbjudandet logotyp (stor)

Den här logotypen visas på detaljsidan för din app. Png-avbildningsformatet är det enda format som är tillåtet. Bildstorleken är 48 bildpunkter x 48 bildpunkter.

### <a name="video"></a>Video

Du kan överföra upp till fyra videor. För varje video som du vill ladda upp:
- Ange namnet på videon
- Ange en URL (YouTube eller Vimeo endast)
- Ange en miniatyr för att associera med en video. Miniatyren måste använda png-avbildningsformatet och dess storlek måste vara minst 1 280 bildpunkter X 720 bildpunkter. 

Om du vill lägga till nya video(s), Välj **+ ny**, visas i nästa skärmdump.

![Lägg till ny video](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot4.png)

### <a name="document"></a>Dokument

Du kan överföra upp till tre dokument. Varje dokument måste använda filformatet PDF. Lägga till ett nytt dokument:

- Välj **+ ny**
- Ange ett dokumentnamn
- Välj **överför** att ladda upp ett dokument.

![Lägg till nytt dokument](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot5.png)

### <a name="privacy-policy"></a>Sekretesspolicy

Ange URL: en för din Apps sekretesspolicy

### <a name="terms-of-use"></a>Användningsvillkor

Ange villkor för användning av din app. AppSource-kunder måste acceptera villkoren innan de kan testa din app.

>[!Note]
>Det här fältet accepterar innehåll med hjälp av HTML-taggar, till exempel html-innehåll < p\>, < h1\>, och < li\>. Dessa taggar kan användas för att formatera ditt innehåll. 

### <a name="lead-destination"></a>Leda mål

Välj en CRM-systemet där dina leads ska lagras. 

Välj **Azure Table** om du använder någon av dessa CRM-system: Microsoft Dynamics CRM, Salesforce eller Marketo. 

Om du vill ha mer information om CRM-system som du vill använda, väljer du något av följande länkar för system som stöds.

-   [Azure-tabell](./cloud-partner-portal-lead-management-instructions-azure-table.md)
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
-   [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
-   [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)
