---
title: Skapa Dynamics 365 för Operations erbjuder via Cloud Partner portal | Microsoft Docs
description: Skapa Dynamics 365 för Operations erbjuder via Cloud Partner portal
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
ms.openlocfilehash: 41f3fd55be02364b4028642db9db1d3f47043afa
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245094"
---
# <a name="how-to-create-dynamics-365-for-operations-offer-via-cloud-partner-portal"></a>Skapa Dynamics 365 för Operations erbjuder via Cloud Partner portal

Publisher-portalen ger rollbaserad åtkomst till portalen så att flera personer ska kunna samarbeta för att publicera ett erbjudande. Se [Portal hantera Molnanvändare](./cloud-partner-portal-manage-users.md) för mer information.

Innan ett erbjudande kan publiceras åt en utgivare konto, ett för individer med \"ägare\" rollen behöva samtycker till att följa den [användningsvillkor](https://azure.microsoft.com/support/legal/website-terms-of-use/), [sekretesspolicy för Microsoft](https://www.microsoft.com/privacystatement/default.aspx), och [Microsoft Azure Certified-programavtalet](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="how-to-create-a-new-dynamics-365-for-operations-offer"></a>Så här skapar du en ny Dynamics 365 för Operations erbjuder

När alla krav har uppfyllts, är du redo att börja redigera din Dynamics 365 för Operations-erbjudande.

1. Logga in på den [Cloud Partner Portal](http://cloudpartner.azure.com/).
2. Från det vänstra navigeringsfältet, klickar du på \"+ nytt erbjudande\" och välj \"Dynamics 365 for Operations\".
3. Ett nytt erbjudande \"redigeraren\" Visa nu öppnas för dig och vi är redo att börja redigera.
4. Den \"formulär\" som behöver anges visas till vänster i den \"redigeraren\" vy. Varje \"formuläret\" består av en uppsättning fält som ska fyllas i. Obligatoriskt fält är markerade med en röd asterisk (\*).

Det finns fyra huvudsakliga formulär för att skapa en Dynamics 365 för åtgärder erbjudande:

- Erbjudandeinställningar
- Teknisk information
- Storefront information
- Contacts

## <a name="how-to-fill-out-the-offer-settings-form"></a>Hur du fyller i formuläret erbjuder inställningar

Erbjudandet inställningar formuläret är en grundläggande och ange inställningar för erbjudandet. Olika fält beskrivs nedan.

### <a name="offer-id"></a>ID för erbjudande

Det här är en unik identifierare för erbjudandet i en utgivarprofil för. Detta ID syns i URL: er i produkten. Det får bara innehålla gemena alfanumeriska tecken och bindestreck (-). ID: T får inte sluta med ett bindestreck och kan ha högst 50 tecken. Det här fältet är låst när ett erbjudande lanseras.

till exempel om en utgivare contoso skapas ett erbjudande med ID för erbjudande *exempel-dynamics365 för åtgärder*, den visas i AppSource som \"https://appsource.microsoft.com/marketplace/apps/**contoso**.\*exempel – dynamics 365 för åtgärder\*? fliken = Översikt\"

### <a name="publisher-id"></a>Utgivar-ID

Den här listrutan kan du välja publisher-profil som du vill publicera det här erbjudandet under. Det här fältet är låst när ett erbjudande lanseras.

Namn

Det här är visningsnamnet för ditt erbjudande. Detta är det namn som visas i [AppSource](https://appsource.microsoft.com). Det får innehålla högst 50 tecken.

Klicka på \"spara\" spara förloppet. Nästa steg är att fylla i teknisk information för ditt erbjudande.

## <a name="how-to-fill-out-the-technical-info-form"></a>Hur du fyller i formuläret teknisk information

Formuläret teknisk information innehåller information som visas i ditt erbjudande-sidan. Anvisningar för olika områden beskrivs nedan.

![Ny erbjudandet skärm](./media/publish_d365_new_offer/Technical_info.png)

### <a name="solution-identifier"></a>Identifierare för lösningen

Först är din lösningsidentifierare.

1. Gå till tjänster och välj lösningshantering för att hitta den här identifieraren.
2. När du har valt rätt lösning, visas identifierare för lösningen i Översikt för paketet. \*\*Om identifieraren är tom, Välj Redigera och publicera ditt paket för identifieraren av lösningen ska visas.

### <a name="validation-assets"></a>Verifiering tillgångar

Ladda upp din bil (anpassning analysrapporter) här.

### <a name="does-solution-enable-translations"></a>Aktiverar lösningen translation(s)?

Välj \'Ja\' eller \'nr\'.

### <a name="does-solution-include-localizations"></a>Inkluderar lösning Localization(s)?

Välj \'Ja\' eller \'nr\'.

### <a name="product-version"></a>Produktversion

Välj ny AX. Klicka slutligen på Spara.

## <a name="how-to-fill-out-storefront-details-form"></a>Så här att fylla i formuläret om butiken.

Först är din erbjudandeinformation.

1. **Sammanfattning av erbjudandet**

    \- Ange en kort sammanfattning av din lösning (maximalt 100 tecken).

2. **Beskrivning av erbjudande**

    \- Ange en kort beskrivning av din lösning. Beskrivningen bör ha funktionella utrymmet för din lösning och direkt ska justeras med biblioteket BPM. Exempel: Om du anta att du har funktioner x, y, z i ditt marknadsföringsinnehåll under den slutliga granskningen vi garanterar att de dokumenteras i BPM-biblioteket i LCS.

![storefront informationsskärmen](./media/publish_d365_new_offer/offer_details.png)

### <a name="listing-details"></a>Lista information

![storefront informationsskärmen](./media/publish_d365_new_offer/storefront_details.png)

1. **Branscher** -Kontrollera högst två branscher från de angivna alternativen.
2. **Kategorier** -Kontrollera högst tre kategorier från de angivna alternativen.
3. **Typ av App** – Välj bland de angivna alternativen.
4. **Hjälplänk för din App** -ange hjälplänken för din lösning.
5. **Länder/regioner som stöds** -Kontrollera från de angivna alternativen.
6. **Språk som stöds** -Kontrollera från de angivna alternativen.
7. **Appversion** -ange version av din lösning som har släppts. (till exempel 1.0.0.0)
8. **Datum för versionen av appen** -ange versionen datumet för din solution(mm/dd/yyyy).
9. **Produkter som din app fungerar med** -specifika produkter som din app fungerar med. Du kan visa högst tre produkter. Om du vill visa en produkt, klicka på plustecknet (bredvid nytt) och ett nytt öppna textfält kommer att skapas för dig att ange namnet på en produkt som din app fungerar med.
10. **Sök efter nyckelord** -ange vanliga termer som kan användas för att hitta din lösning under en sökning. \*\*Dessa nyckelord visas inte i marketplace.
11. **Dölj nyckeln** – detta är vilken nyckel som skulle användas tillsammans med förhandsgransknings-URL för erbjudandet att dölja den från den gemensamma vyn. Det är inte ett lösenord. Du kan ange valfri sträng här.

### <a name="marketing-artifacts"></a>Marknadsföring artefakter

1. Sedan överför dina **logotyper**, **skärmbilder**. \*\*Observera storlekar för varje överföring och alla avbildningar ska vara i PNG-format.
2. **Demonstrera videor** -klickar du på \"+ ny\". Ladda upp en video med i lösningen (endast YouTube eller Vimeo-länkar). \*\*. Observera att bör du ladda upp en miniatyrbild för angivna storleken för att göra videon visas i Förproduktion.
3. **Dokument** – ladda upp eventuella dokument som rör din lösning och Kom ihåg att ange ett namn för dokumentet.

### <a name="legal"></a>Juridisk information

Den här informationen länkar till din sekretesspolicy och användningsvillkor. Ange lösningen sekretess princip-URL och dina användningsvillkor. \*\*Kunden kommer att kunna se dessa principer på portalen.

### <a name="customer-support"></a>Kundsupport

Stöd för URL: en endast visas i portalen för dina användare.

### <a name="leads-management"></a>Hantering av leads

Välj ett CRM-system där du leda kommer att lagras. Välj \"Azure Table\" här om du har någon av följande CRM-system: Salesforce, Marketo, Microsoft Dynamics CRM. CRM-system som du väljer här är där skriver du information för slutanvändare som provar din app på AppSource (leads). Beroende på i CRM-systemet som du väljer, klickar du på motsvarande URL nedan för information om hur du slutför nästa uppsättning fält.

![Leda hanteringsinformation](./media/publish_d365_new_offer/leads.png)

1. Azure Table finns [här](https://aka.ms/leadsettingforazuretable)
2. Dynamics CRM online, finns [här](https://aka.ms/leadsettingfordynamicscrm)
3. För Marketo [här](https://aka.ms/leadsettingformarketo)
4. Salesforce finns [här](https://aka.ms/leadsettingforsalesforce)

## <a name="how-to-fill-out-the-contacts-form"></a>Så här att fylla i formuläret kontakter.

Den här informationen används för Microsoft och Customer support. Ange Engineering Contact och kundsupport för ditt företag och Support-URL: en för din lösning. Den här informationen ska användas som en enda kontaktpunkt om Microsoft har en fråga om din lösning och även för kundsupport.

![Erbjudandet kontakter skärmen](./media/publish_d365_new_offer/Contacts.png)
