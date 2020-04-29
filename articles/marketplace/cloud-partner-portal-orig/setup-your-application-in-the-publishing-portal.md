---
title: Konfigurera ditt program i publicerings portalen
description: Instruktioner i så här konfigurerar du ditt program i moln publicerings portalen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 2676dbf72309eeb51be1f08e7bae2c1502cc671e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280209"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Konfigurera ditt program i publicerings portalen

Du är nu redo att konfigurera ditt program i publicerings portalen.

## <a name="login-and-create-a-new-offer"></a>Logga in och skapa ett nytt erbjudande

1. Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Klicka på "+ nytt erbjudande" i det vänstra navigerings fältet och välj "Dynamics 365 för kund engagemang".

![Välja ett nytt erbjudande](./media/CRMScreenShot14.png)

1. Nu öppnas en ny "redaktör"-vy för dig och vi är redo att börja redigera.

![Fönstret ny erbjudande](./media/CRMScreenShot15.png)

1. De "Forms" som måste fyllas i visas till vänster i vyn "redaktör". Varje "form" består av en uppsättning fält som ska fyllas i. Obligatoriska fält markeras med en röd asterisk (\*).

Det finns fyra huvud formulär för att redigera ett Dynamics 365 för kund engagemang-erbjudande

* Erbjudande inställningar
* Teknisk information
* Butik-information
* Contacts

## <a name="fill-out-the-offer-settings-form"></a>Fyll i formuläret för erbjudande inställningar

Formuläret erbjudande inställningar är ett grundläggande formulär där du kan ange erbjudande inställningarna. De olika fälten beskrivs nedan.

### <a name="offer-id"></a>Erbjudande-ID

Detta är en unik identifierare för erbjudandet i en utgivar profil. Detta ID visas i produkt-URL: er. Det får bara innehålla gemena alfanumeriska tecken och bindestreck (-). ID: t får inte sluta med ett bindestreck och får innehålla högst 50 tecken. Det här fältet är låst när ett erbjudande går live.

till exempel, om en utgivares **"contoso"** -utgivare skapar ett erbjudande med erbjudande-ID **"Sample-webapp"**, visas det i AppSource som "https\/:/appsource.Microsoft.com/Marketplace/Apps/contoso.Sample-webapp?Tab=Overview"

### <a name="publisher-id"></a>Utgivar-ID

I den här List rutan kan du välja den utgivar profil som du vill publicera det här erbjudandet under. Det här fältet är låst när ett erbjudande går live.

### <a name="name"></a>Name

Detta är visnings namnet för ditt erbjudande. Detta är det namn som visas i [AppSource](https://appsource.microsoft.com/). Det får innehålla högst 50 tecken.

Klicka på Spara för att spara förloppet. Nästa steg är att lägga till teknisk information för ditt erbjudande.

## <a name="fill-out-the-technical-info-form"></a>Fyll i formuläret teknisk information


I formuläret teknisk information kan du fylla i information som är specifik för din Dynamics 365 för kund engagemang-lösning. Om du hovrar över visas mer information. Se exemplet nedan.

![Teknisk informations skärm](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Programinfo

De flesta utgivare lämnar dessa fält med standardvärdena, användare, nej, nej och en tom program konfigurations webb adress enligt skärm bilden ovan.

### <a name="crm-package"></a>CRM-paket

![Information om CRM-paket](./media/CRMScreenShot17.png)

Här är en förklaring till dessa fält:

* Paketets fil namn: det fil namn som du skapade i ovanstående steg när du skapar zip-filen som är ditt CRM AppSource-paket. I exemplet ovan är det "Microsoft\_SamplePackage. zip".
* URL till din paket plats: det här är URL: en till det Azure Storage konto som innehåller det paket fil namn som anges ovan. Det är den URL som skapades i steg 9 i avsnittet ovan.
* Finns det fler än ett CRM-paket i paket filen: Välj Ja **endast** om du har stöd för flera versioner av CRM med olika paket. För de flesta partner är detta "nej". Om du väljer Ja måste du skapa AppSource-paket för varje version av din lösning. _OBS! detta frågar inte om du har flera **zip** -filer. Om du har flera lösning. zip-filer men bara en version bör du fortfarande välja "nej". Med paketerings verktyget samlas dessa automatiskt in i automatiskt._

### <a name="crm-package-availability"></a>Tillgänglighet för CRM-paket

I det här avsnittet väljer du vilka regioner i CRM som paketet ska göras tillgängligt för. Information om vilka CRM-regioner som tjänar vilka länder/regioner finns i länken:[https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Obs! distribution till Tyskland "suverän och US Gov Cloud" suverän kräver särskild behörighet och verifiering under CERTIFIERINGen

## <a name="storefront-details"></a>Butik-information

### <a name="offer-summary"></a>Erbjudande Sammanfattning

Detta är en sammanfattning av ditt erbjudandes värde förslag. Den kommer att visas på Sök sidan för ditt erbjudande. Det får innehålla högst 100 tecken.

### <a name="offer-description"></a>Beskrivning av erbjudande

Detta är den beskrivning som visas på din app-informations sida. Maximalt antal tillåtna är 1300 tecken

### <a name="industries"></a>Branscher

Välj den bransch som appen är bäst justerad till. Om din app har en relation till flera branscher kan du lämna detta tomt.

### <a name="categories"></a>Kategorier

Välj de kategorier som är relevanta för din app. Välj högst 3.

### <a name="app-type"></a>Typ av app

Välj den typ av utvärdering som din app ska aktivera på AppSource. Free innebär att din app är kostnads fri. "Utvärdering" innebär att kunder kan testa din app under en kort period på AppSource. "Begäran om utvärderings version stöds inte för Dynamics 365 för Customer Engagement-appar. Välj inte det här alternativet.

### <a name="help-link-for-your-app"></a>Hjälp länk för din app

Ange URL till en sida som har hjälp relaterad information för din app.

### <a name="supported-countriesregions"></a>Länder/regioner som stöds

Det här fältet avgör de länder/regioner där ditt erbjudande ska vara tillgängligt för utvärderings versionen.

### <a name="supported-languages"></a>Språk som stöds

Välj de språk som appen stöder. Om din app har stöd för ytterligare språk som inte finns med i listan fortsätter du att publicera ditt erbjudande och skicka e [appsource@microsoft.com](mailto:appsource@microsoft.com) -post till oss på: för att berätta för oss.

### <a name="app-version"></a>Appversion

Ange versions numret för din app

### <a name="app-release-date"></a>Lanserings datum för app

Ange lanserings datum för din app

### <a name="products-your-app-works-with-max-3"></a>Produkter som din app fungerar med (max 3)

Visa en lista med de produkter som appen fungerar med. Du kan visa högst tre produkter. Om du vill visa en produkt klickar du på plus tecknet (bredvid nytt) så skapas ett nytt fält med öppen text där du kan ange namnet på en produkt som din app arbetar med.

### <a name="search-keywords-max-3"></a>Sök Nyckelord (max 3)

AppSource gör det möjligt för kunden att söka baserat på nyckelord. Du kan ange den uppsättning nyckelord som programmet ska visas för kunderna.

Om programmet till exempel är "min e-posttjänst" e-post, mailing, e-posttjänsten kan vara vissa nyckelord. Välj ord som användarna troligt vis kommer att använda för att söka efter din app i sökrutan AppSource.

### <a name="hide-key"></a>Dölj nyckel

Det här är en nyckel som kombineras med URL: en för för hands versionen för att dölja den från den offentliga vyn. Det är inte ett lösen ord. Du kan ange valfri sträng här.

### <a name="offer-logo-png-format-48x48"></a>Erbjudande logo typ (PNG-format, 48x48)

Detta visas på appens Sök sida. **Endast png-format är tillåtet.** Ladda upp en PNG-bild med upplösningen 48PX\*48PX

### <a name="offer-logo-png-format-216x216"></a>Erbjudande logo typ (PNG-format, 216x216)

Detta visas på appens informations sida. **Endast png-format är tillåtet.** Ladda upp en PNG-bild med upplösningen 216PX\*216PX

### <a name="videos"></a>Videoklipp

Du kan ladda upp högst fyra videor. För varje video som du vill ladda upp måste du fylla i videons namn, URL (YouTube eller Vimeo) och miniatyren för att koppla till videon. Miniatyren måste vara i PNG-format och måste\*vara 1280PX 720PX. Klicka på plus tecknet för att lägga till nya videor. Video miniatyrer visas på appens informations sida.

### <a name="documents"></a>Dokument

Du kan ladda upp högst tre dokument i PDF-format. För varje dokument som du vill ladda upp måste du fylla i dokument namnet och överföra dokumentet. Dokumentet måste vara i PDF-format.

Klicka på plus tecknet för att lägga till nya dokument (n)

### <a name="screenshots"></a>Skärmbilder

Detta är skärm bilder som visas på AppSource detalj sida för din app.

### <a name="privacy-policy"></a>Sekretesspolicy

Ange URL: en till appens sekretess policy

### <a name="terms-of-use"></a>Villkor för användning

Ange användnings villkoren för din app. AppSource-kunder måste acceptera dessa villkor innan de kan testa din app

### <a name="support-url"></a>Support-URL

Ange Support-URL: en för din app.

### <a name="lead-destination"></a>Lead-mål

Välj ett CRM-system där du kommer att lagra lead. Välj "Azure Table" här om du har något av följande CRM-system: Salesforce, Marketo, Microsoft Dynamics CRM. Det CRM-system du väljer här är där vi skriver information om slutanvändare som testar din app på AppSource (leads). Beroende på vilket CRM-system du väljer klickar du på motsvarande URL nedan för information om hur du slutför nästa uppsättning fält

* [Azure-tabell](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Butik-information

Kontakt uppgifterna används för intern kommunikation mellan partnern och Microsoft. Obs! det är viktigt att använda en e-postadress som övervakas i dessa fält. Vi kommer att använda det här e-postmeddelandet för att kommunicera med dig när du arbetar med publicering till AppSource. Endast Support-URL: en är synlig för kunderna.
