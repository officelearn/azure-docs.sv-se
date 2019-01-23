---
title: Konfigurera programmet i Publiceringsportalen | Microsoft Docs
description: Anvisningarna i hur du ställer in ditt program i molnet Publiceringsportalen.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 7b5bb0bf8ff5fac10c47cf5bdf9564903f4d6b94
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448491"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Konfigurera programmet i Publiceringsportalen

Du är nu redo att konfigurera ditt program i publiceringsportalen.

## <a name="login-and-create-a-new-offer"></a>Logga in och skapa ett nytt erbjudande

1. Logga in på den [Cloud Partner Portal](http://cloudpartner.azure.com/).
2. I det vänstra navigeringsfältet, klicka på ”+ New erbjuder” och Välj ”Dynamics 365 för kundengagemang”.

![Att välja ett nytt erbjudande](./media/CRMScreenShot14.png)

3. Ett nytt erbjudande ”Editor” Visa nu öppnas för dig och vi är redo att börja redigera.

![Ny erbjudandet skärm](./media/CRMScreenShot15.png)

4. ”Formulären” som måste fyllas i visas till vänster i vyn ”Editor”. Varje ”form” består av en uppsättning fält som ska fyllas i. Obligatoriskt fält är markerade med en röd asterisk (\*).

Det finns fyra huvudsakliga formulär för att skapa en Dynamics 365 för kundengagemang erbjudande

* Erbjudandeinställningar
* Teknisk information
* Storefront information
* Contacts

## <a name="fill-out-the-offer-settings-form"></a>Fyll i formuläret erbjuder inställningar

Erbjudandet inställningar formuläret är en grundläggande och ange inställningar för erbjudandet. Olika fält beskrivs nedan.

### <a name="offer-id"></a>ID för erbjudande

Det här är en unik identifierare för erbjudandet i en utgivarprofil för. Detta ID syns i URL: er i produkten. Det får bara innehålla gemena alfanumeriska tecken och bindestreck (-). ID: T får inte sluta med ett bindestreck och kan ha högst 50 tecken. Det här fältet är låst när ett erbjudande lanseras.

Exempel: om en utgivare **”contoso”** utgivare skapar ett erbjudande med erbjudande-ID **”exempel WebApp”**, den visas i AppSource som ”https://appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview”

### <a name="publisher-id"></a>Utgivar-ID

Den här listrutan kan du välja publisher-profil som du vill publicera det här erbjudandet under. Det här fältet är låst när ett erbjudande lanseras.

### <a name="name"></a>Namn

Det här är visningsnamnet för ditt erbjudande. Detta är det namn som visas i [AppSource](https://appsource.microsoft.com/). Det får innehålla högst 50 tecken.

Klicka på ”Spara” för att spara ditt arbete. Nästa steg är att lägga till teknisk information för ditt erbjudande.

## <a name="fill-out-the-technical-info-form"></a>Fyll i formuläret teknisk information


Teknisk information om formuläret är där du ska fylla i information som är specifik för din Dynamics 365 för kundengagemang lösning. Hovra över som det innehåller mer information. Se exemplet nedan.

![Teknisk information-skärmen](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Programinformation

De flesta utgivare lämnar dessa fält med standardvärden, användare, Nej, Nej, och en tom programmets konfiguration URL enligt skärmbilden ovan.

### <a name="crm-package"></a>CRM-paketet

![Information om CRM](./media/CRMScreenShot17.png)

Här är en förklaring till dessa fält:

* Filnamn för ditt paket: Namnet på filen du skapade i steget ovan när du skapar zip-filen som är ditt CRM AppSource-paket. I exemplet ovan är det ”Microsoft\_SamplePackage.zip”.
* URL: en för din plats för paket: Det här är URL: en till Azure Storage-kontot som innehåller namnet på paketet som anges ovan. Det är den URL som skapades i steg 9 av avsnittet ovan.
* Finns det mer än en crm-paketet i din paketfil: Välj Ja **endast** om du stöder flera versioner av crm med olika paket. Det här är ”No” för de flesta partner. Om du väljer Ja måste du skapa AppSource-paket för varje version av din lösning. _Obs! Det inte handlar om du har flera **zip** filer. Om du har flera solution.zip filer men endast en version kan väljer du fortfarande ”Nej” Verktyget paketering ska du använda dessa automatiskt._

### <a name="crm-package-availability"></a>Tillgänglighet för CRM-paket

Välj vilka regioner av CRM ditt paket kommer att göras tillgänglig för det här avsnittet. Information som regioner för att hantera vilka länder finns i länken: [http://o365datacentermap.azurewebsites.net/](http://o365datacentermap.azurewebsites.net/)

Obs! Distribuera till Tyskland ”suveräna och molnet för amerikanska myndigheter” suveräna kräver särskild behörighet och verifiering under CERTIFIKATUTFÄRDARE

## <a name="storefront-details"></a>Storefront information

### <a name="offer-summary"></a>Sammanfattning av erbjudandet

Det här är en sammanfattning av ditt erbjudande förslagsvärde. Den visas på söksidan för ditt erbjudande. Det bör vara högst 100 tecken.

### <a name="offer-description"></a>Beskrivning av erbjudande

Det här är den beskrivning som visas på sidan med information om appen. Högsta tillåtna antalet är 1300 tecken

### <a name="industries"></a>Branscher

Välj bransch som din app är bäst anpassad efter. Om din app har kopplat till flera branscher, du kan lämna det tomt.

### <a name="categories"></a>Kategorier

Välj kategorier som är relevanta för din app. Välj högst 3.

### <a name="app-type"></a>Apptyp

Välj typ av utvärderingsversion som gör att din app på AppSource. ”Fria” innebär att din app är kostnadsfria. ”Utvärderingsversion” innebär att kunder kan testa din app under en kort period på AppSource. ”Begär för utvärderingsversion' stöds inte för Dynamics 365 för kundengagemang appar. Välj inte det här alternativet.

### <a name="help-link-for-your-app"></a>Hjälplänk för din app

Ange en Webbadress till en sida som har relaterade information för din app.

### <a name="supported-countriesregions"></a>Länder/regioner

Det här fältet anger de länder/regioner där ditt erbjudande blir tillgänglig för utvärdering.

### <a name="supported-languages"></a>Språk som stöds

Välj de språk som har stöd för din app. Om din app har stöd för fler språk som inte finns på den här listan, fortsätter att publicera ditt erbjudande och mejla oss på: [ appsource@microsoft.com ](mailto:appsource@microsoft.com) att berätta för oss.

### <a name="app-version"></a>Appversion

Ange versionsnumret för din app

### <a name="app-release-date"></a>App lanseringsdatum

Ange datumet för din app

### <a name="products-your-app-works-with-max-3"></a>Produkter som din app fungerar med (Max 3)

Listspecifika produkter som din app fungerar med. Du kan visa högst tre produkter. Om du vill visa en produkt, klicka på plustecknet (bredvid nytt) och ett nytt öppna textfält kommer att skapas för dig att ange namnet på en produkt som din app fungerar med.

### <a name="search-keywords-max-3"></a>Sökord (Max 3)

AppSource kan kunden söker efter nyckelord. Du kan ange uppsättningen nyckelord som programmet kommer att visas för kunderna.

Till exempel om programmet är ”min e-posta tjänst” e-postmeddelanden, skicka, e-posttjänst kanske vissa nyckelord. Välj typ av ord som användare använder sannolikt att söka för din app i rutan Sök AppSource.

### <a name="hide-key"></a>Dölj nyckel

Det här är en nyckel som kommer att kombineras med förhandsgransknings-URL för erbjudandet att dölja den från den gemensamma vyn. Det är inte ett lösenord. Du kan ange valfri sträng här.

### <a name="offer-logo-png-format-48x48"></a>Erbjudandet logotyp (png-format, 48 x 48)

Detta visas på söksidan för din app. **Png-format är tillåtet.** Ladda upp en png-bild med upplösning på 48PX\*48PX

### <a name="offer-logo-png-format-216x216"></a>Erbjudandet logotyp (png-format, 216 x 216)

Detta visas på detaljsidan för din app. **Png-format är tillåtet.** Ladda upp en png-bild med upplösning på 216PX\*216PX

### <a name="videos"></a>Videoklipp

Du kan överföra upp till fyra videor. För varje video som du vill ladda upp, måste du fylla i video namn, URL (YouTube eller Vimeo endast) och miniatyrbilden för att associera med en video. Miniatyr måste vara i png-format och måste vara 1280PX\*720PX. Klicka på plustecknet för att lägga till nya video(s). Videor thumbnail(s) ska visas på detaljsidan för din app.

### <a name="documents"></a>Dokument

Du kan överföra upp till tre dokument i PDF-format. För varje dokument som du vill ladda upp, måste du fylla i dokumentnamn på och överför dokumentet. Dokumentet måste finnas i pdf-format.

Klicka på plustecknet för att lägga till nya dokument

### <a name="screenshots"></a>Skärmbilder

Det här är skärmbilder som visas på sidan med AppSource för din app.

### <a name="privacy-policy"></a>Sekretesspolicy

Ange Webbadressen till din Apps sekretesspolicy

### <a name="terms-of-use"></a>Användningsvillkor

Ange villkor för användning av din app. AppSource-kunder som krävs för att acceptera villkoren innan de kan testa din app

### <a name="support-url"></a>Support-URL

Ange Webbadressen till support för din app.

### <a name="lead-destination"></a>Leda mål

Välj ett CRM-system där du leda kommer att lagras. Välj ”Azure Table” om du har någon av följande CRM-system: Salesforce, Marketo, Microsoft Dynamics CRM. CRM-system som du väljer här är där skriver du information för slutanvändare som provar din app på AppSource (leads). Beroende på i CRM-systemet som du väljer, klickar du på motsvarande URL nedan för information om hur du slutför nästa uppsättning fält

* [Azure Table](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Storefront information

Kontaktinformation som används för intern kommunikation mellan partner och Microsoft endast. Obs! Det är viktigt att du använder en e-postadress som övervakas i de här fälten. Vi använder den här e-post för att kommunicera med dig på förloppet på Publicera till AppSource. Endast stöd för URL: en ska vara synliga för kunderna.
