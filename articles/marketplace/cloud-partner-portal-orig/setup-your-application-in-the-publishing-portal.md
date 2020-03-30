---
title: Konfigurera programmet i publiceringsportalen
description: Instruktioner för hur du konfigurerar ditt program i Cloud Publishing Portal.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 2676dbf72309eeb51be1f08e7bae2c1502cc671e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280209"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Konfigurera programmet i publiceringsportalen

Du är nu redo att konfigurera programmet i publiceringsportalen.

## <a name="login-and-create-a-new-offer"></a>Logga in och skapa ett nytt erbjudande

1. Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).
2. I det vänstra navigeringsfältet klickar du på "+ Nytt erbjudande" och väljer "Dynamics 365 for Customer Engagement".

![Välja ett nytt erbjudande](./media/CRMScreenShot14.png)

1. Ett nytt erbjudande "Editor" visa öppnas nu för dig, och vi är redo att börja författa.

![Skärmen Nytt erbjudande](./media/CRMScreenShot15.png)

1. De "formulär" som måste fyllas i visas till vänster i vyn "Redigerare". Varje "formulär" består av en uppsättning fält som ska fyllas i. Obligatoriska fält är markerade med en\*röd asterisk ( ).

Det finns fyra huvudformulär för att skapa ett Dynamics 365 for Customer Engagement-erbjudande

* Inställningar för erbjudande
* Teknisk information
* Information om skyltfönster
* Contacts

## <a name="fill-out-the-offer-settings-form"></a>Fyll i formuläret Erbjudandeinställningar

Formuläret för erbjudandeinställningar är ett grundläggande formulär för att ange erbjudandeinställningarna. De olika fälten beskrivs nedan.

### <a name="offer-id"></a>Erbjudande-ID

Detta är en unik identifierare för erbjudandet i en utgivarprofil. Detta ID visas i produktadresser. Det får bara innehålla gemena alfanumeriska tecken och bindestreck (-). ID:t kan inte sluta i ett streck och kan ha högst 50 tecken. Det här fältet är låst när ett erbjudande visas.

Om till exempel en utgivare **"contoso"-utgivare** skapar ett erbjudande med erbjudande-ID **"sample-WebApp"** visas\/det i AppSource som "https: /appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview"

### <a name="publisher-id"></a>Publisher-ID

Med den här listrutan kan du välja den utgivarprofil som du vill publicera erbjudandet under. Det här fältet är låst när ett erbjudande visas.

### <a name="name"></a>Namn

Det här är visningsnamnet för ditt erbjudande. Det här är namnet som visas i [AppSource](https://appsource.microsoft.com/). Det får innehålla högst 50 tecken.

Klicka på "Spara" för att spara dina framsteg. Nästa steg skulle vara att lägga till teknisk information för ditt erbjudande.

## <a name="fill-out-the-technical-info-form"></a>Fyll i formuläret Teknisk information


Det tekniska informationsformuläret är där du fyller i information som är specifik för din Dynamics 365 for Customer Engagement-lösning. Svävande över kommer att presentera dig med mer information. Se exemplet nedan.

![Skärmen Teknisk information](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Information om ansökan

De flesta utgivare lämnar dessa fält med standardvärdena, Användare, Nej, Nej och en tom URL för programkonfiguration enligt skärmbilden ovan.

### <a name="crm-package"></a>CRM-paket

![Information om CRM-paket](./media/CRMScreenShot17.png)

Här är en förklaring till dessa områden:

* Paketets filnamn: Filnamnet som du skapade i ovanstående steg när du skapar zip-filen som är crm-appsource-paketet. I exemplet ovan är det\_"Microsoft SamplePackage.zip".
* Url till din paketplats: Det här är URL:en till Azure Storage-kontot som innehåller det paketfilsnamn som anges ovan. Det är webbadressen som skapas i steg 9 i avsnittet ovan.
* Finns det mer än ett crm-paket i din paketfil: Välj Ja **ENDAST** om du stöder flera versioner av CRM med olika paket. För de flesta partners kommer detta att vara "Nej". Om du väljer Ja måste du skapa AppSource-paket för varje version av din lösning. _Obs: Detta är inte frågar om du har flera **zip-filer.** Om du har flera solution.zip-filer men bara en version bör du ändå välja Nej. Förpackningsverktyget kommer att föra samman dessa automatiskt._

### <a name="crm-package-availability"></a>Crm-paket tillgänglighet

I det här avsnittet väljer du vilka regioner i CRM ditt paket ska göras tillgängliga för. Mer information om vilka CRM-regioner som betjänar vilka länder/regioner finns i länken:[https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Obs: Distribuera till Tyskland "Sovereign och US Gov Cloud" Sovereign kräver särskild behörighet och validering under certifiering

## <a name="storefront-details"></a>Information om skyltfönster

### <a name="offer-summary"></a>Sammanfattning av erbjudandet

Detta är en sammanfattning av ditt erbjudande värde erbjudande. Den visas på erbjudandets söksida. Det bör vara högst 100 tecken.

### <a name="offer-description"></a>Beskrivning av erbjudandet

Det här är beskrivningen som visas på sidan med appinformation. Högsta tillåtna är 1300 tecken

### <a name="industries"></a>Branscher

Välj den bransch som appen är bäst justerad till. Om din app har att göra med flera branscher kan du lämna det tomt.

### <a name="categories"></a>Kategorier

Välj de kategorier som är relevanta för din app. Välj högst 3.

### <a name="app-type"></a>Apptyp

Välj den typ av utvärderingsversion som appen ska aktivera på AppSource. "Gratis" betyder att din app är gratis. Utvärdering innebär att kunderna kan prova din app under en kort period på AppSource. "Begäran om utvärderingsversion" stöds inte för Dynamics 365 for Customer Engagement-appar. Välj inte det här alternativet.

### <a name="help-link-for-your-app"></a>Hjälplänk för din app

Ange URL till en sida som har hjälp med relaterad information för din app.

### <a name="supported-countriesregions"></a>Länder/regioner som stöds

Det här fältet avgör i vilka länder/regioner ditt erbjudande ska vara tillgängligt för utvärderingsversion.

### <a name="supported-languages"></a>Språk som stöds

Välj de språk som appen stöder. Om din app stöder ytterligare språk som inte finns med i den [appsource@microsoft.com](mailto:appsource@microsoft.com) här listan fortsätter du att publicera ditt erbjudande och skicka e-post till oss på: för att meddela oss.

### <a name="app-version"></a>Appversion

Ange versionsnumret för appen

### <a name="app-release-date"></a>Datum för utgivning av appar

Ange utgivningsdatumet för appen

### <a name="products-your-app-works-with-max-3"></a>Produkter som appen fungerar med (Max 3)

Listspecifika produkter som appen fungerar med. Du kan lista högst tre produkter. Om du vill visa en lista över en produkt klickar du på plustecknet (bredvid nytt) så skapas ett nytt öppet textfält så att du kan ange namnet på en produkt som appen fungerar med.

### <a name="search-keywords-max-3"></a>Sökord (Max 3)

Med AppSource kan kunden söka baserat på nyckelord. Du kan ange den uppsättning nyckelord som programmet ska visas för kunderna för.

Till exempel om programmet är "Min e-posttjänst" E-post, Utskick, Mail service kan vara några sökord. Välj ord som användarna troligen kommer att använda för att söka efter din app i sökrutan AppSource.

### <a name="hide-key"></a>Dölj tangent

Detta är en nyckel som kommer att kombineras med erbjudandet förhandsgranska URL för att dölja den från offentlig visning. Det är inte ett lösenord. Du kan ange vilken sträng som helst här.

### <a name="offer-logo-png-format-48x48"></a>Erbjudande logotyp (png-format, 48x48)

Detta visas på appens söksida. **Endast png-format är tillåtet.** Ladda upp en png-bild med\*upplösningen 48PX 48PX

### <a name="offer-logo-png-format-216x216"></a>Erbjudande logotyp (png-format, 216x216)

Detta visas på appens detaljsida. **Endast png-format är tillåtet.** Ladda upp en png-bild med\*upplösningen 216PX 216PX

### <a name="videos"></a>Videoklipp

Du kan ladda upp högst fyra videor. För varje video som du vill ladda upp måste du fylla i videonamnet, webbadressen (endast YouTube eller Vimeo) och Miniatyr för att associera med videon. Miniatyrbild måste vara i png-format och\*måste vara 1280PX 720PX. Om du vill lägga till nya videoar klickar du på plustecknet. Videor visas på appens detaljsida.

### <a name="documents"></a>Dokument

Du kan ladda upp högst tre dokument i PDF-format. För varje dokument som du vill överföra måste du fylla i dokumentnamnet och ladda upp dokumentet. Dokumentet måste vara i pdf-format.

Om du vill lägga till nya dokument klickar du på plustecknet

### <a name="screenshots"></a>Skärmbilder

Det här är skärmbilder som visas på sidan AppSource-information för din app.

### <a name="privacy-policy"></a>Sekretesspolicy

Ange URL till appens sekretesspolicy

### <a name="terms-of-use"></a>Användningsvillkor

Ange användningsvillkoren för din app. AppSource-kunder måste acceptera dessa villkor innan de kan prova din app

### <a name="support-url"></a>Url:en för support

Ange support-URL:en för din app.

### <a name="lead-destination"></a>Huvudmål

Välj ett CRM-system där du leder kommer att lagras. Välj "Azure Table" här om du har något av följande CRM-system: Salesforce, Marketo, Microsoft Dynamics CRM. CRM-systemet du väljer här är där vi kommer att skriva information om slutanvändare som provar din app på AppSource (leads). Beroende på vilket CRM-system du väljer klickar du på motsvarande webbadress nedan för information om hur du fyller i nästa uppsättning fält

* [Azure-tabell](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Information om skyltfönster

Kontaktuppgifterna används endast för intern kommunikation mellan partnern och Microsoft. Det är viktigt att du använder en e-postadress som övervakas i dessa fält. Vi kommer att använda det här e-postmeddelandet för att kommunicera med dig om dina framsteg i publiceringen till AppSource. Endast support-URL:en är synlig för kunderna.
