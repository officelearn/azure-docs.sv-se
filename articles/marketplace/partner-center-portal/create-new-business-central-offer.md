---
title: Skapa ett Dynamics 365 Business Central-erbjudande – Microsoft Commercial Marketplace
description: Lär dig mer om steg och överväganden för att skapa ett nytt Dynamics 365 Business Central-erbjudande på Microsofts kommersiella marknads plats i Partner Center. Du kan visa eller sälja ditt erbjudande på Azure Marketplace eller via program varan för Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 08/07/2020
ms.openlocfilehash: 71cbc09f21e21c325665809953f209615d835124
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130161"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>Skapa ett erbjudande för Dynamics 365 for Business Central

Den här artikeln beskriver hur du skapar ett nytt Dynamics 365 Business Central-erbjudande. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) är ett ERP-system (Enterprise Resource Planning) som hanterar ett brett utbud av affärs processer, inklusive ekonomi, åtgärder, leverans kedja, CRM och projekt hantering och elektronisk handel. Premium-paket stöder även klassisk distributions modell och tillverkning. Alla erbjudanden för Dynamics 365 Business Central måste gå igenom vår certifierings process.

Innan du börjar [skapar du ett kommersiellt marknads plats konto i Partner Center](create-account.md) , om du inte har gjort det än. Se till att ditt konto är registrerat i programmet för kommersiella marknads platser.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. På menyn till vänster-navigerings väljer du **kommersiell Marketplace** -  >  **Översikt** .
3. På sidan Översikt väljer du **+ nytt erbjudande**  >  **Dynamics 365 Business Central** .

    ![Visar menyn till vänster-navigering.](./media/new-offer-dynamics-365-business-central.png)

> [!NOTE]
> När ett erbjudande har publicerats visas bara ändringar i Partner Center i onlinebutiker när du har publicerat om erbjudandet. Se till att du alltid publicerar igen när du har gjort ändringar.

## <a name="new-offer"></a>Nytt erbjudande

Ange ett **erbjudande-ID** . Detta är en unik identifierare för varje erbjudande i ditt konto.

- Detta ID är synligt för kunder i webb adressen för Marketplace-erbjudandet och Azure Resource Manager mallar, om tillämpligt.
- Erbjudande-ID: t kombinerat med utgivar-ID: t måste vara under 40 tecken långt.
- Använd bara gemena bokstäver och siffror. Det kan innehålla bindestreck och under streck, men inga blank steg. Om ditt utgivar-ID t. ex. är testpublisherid och du anger **test-erbjudandet-1** så är webb adressen för erbjudandet `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Erbjudande-ID: t kan inte ändras när du har valt **skapa** .

Ange ett **erbjudande alias** . Detta är det namn som används för erbjudandet i Partner Center.

- Det här namnet används inte på Marketplace och skiljer sig från namnet på erbjudandet och andra värden som visas för kunderna.
- Det går inte att ändra namnet på erbjudandet när du har valt **skapa** .

Välj **skapa** för att generera erbjudandet och fortsätt.

## <a name="offer-setup"></a>Erbjudande konfiguration

Följ dessa steg för att konfigurera erbjudandet.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Hur vill du att potentiella kunder ska kunna interagera med det här registrerings erbjudandet?

Välj det alternativ som du vill använda för det här erbjudandet.

#### <a name="get-it-now-free"></a>Hämta nu (kostnads fritt)

Lista ditt erbjudande till kunder kostnads fritt.

#### <a name="free-trial-listing"></a>Kostnads fri utvärderings version (lista)

Lista ditt erbjudande till kunder med en länk till en kostnads fri utvärderings version. Erbjudande om kostnads fria utvärderings versioner skapas, hanteras och konfigureras av din tjänst och har inga prenumerationer som hanteras av Microsoft.

> [!NOTE]
> De token som programmet tar emot via din utvärderings länk kan bara användas för att hämta användar information via Azure Active Directory (Azure AD) för att automatisera skapandet av konton i din app. Microsoft-konton stöds inte för autentisering med denna token.

#### <a name="contact-me"></a>Kontakta mig

Samla in kund kontakt information genom att ansluta ditt CRM-system (Customer Relations hip Management). Kunden uppmanas att ange behörighet för att dela sin information. Dessa kund uppgifter, tillsammans med erbjudande namnet, ID: t och Marketplace-källan där de hittade ditt erbjudande, skickas till det CRM-system som du har konfigurerat. Mer information om hur du konfigurerar din CRM finns i [kund leads](#customer-leads).

### <a name="test-drive"></a>Test enhet

I det här skedet stöder Dynamics 365 Business Central-erbjudanden inte Test Drive. Om du vill ta bort test enheten från erbjudandet avmarkerar du kryss rutan **Aktivera en testenhet** .

### <a name="customer-leads"></a>Kund ledare

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Mer information finns i [Översikt över hantering av leads](./commercial-marketplace-get-customer-leads.md).

Välj **Spara utkast** innan du fortsätter.

## <a name="properties"></a>Egenskaper

På den här sidan kan du definiera de kategorier och branscher som ska användas för att gruppera ditt erbjudande på Marketplace, din app-version och de juridiska avtal som stöder ditt erbjudande.

### <a name="category"></a>Kategori

Välj kategorier och under Kategorier för att placera ditt erbjudande i lämpliga sökområden för Marketplace. Se till att du beskriver hur ditt erbjudande stöder dessa kategorier i beskrivningen av erbjudandet. Välj:

- Minst en och upp till två kategorier, inklusive en primär och en sekundär kategori (valfritt).
- Upp till två under Kategorier för varje primär och/eller sekundär kategori. Om ingen under kategori gäller ditt erbjudande väljer du **ej tillämpligt** .

Se den fullständiga listan över kategorier och under Kategorier i [erbjudande lista med bästa praxis](../gtm-offer-listing-best-practices.md).

### <a name="industry"></a>Industri

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Appversion

Ange versions numret för ditt erbjudande. Kunderna kommer att se den här versionen på erbjudandets detalj sida.

### <a name="terms-and-conditions"></a>Villkor

Ange dina egna juridiska villkor i fältet **allmänna** villkor. Du kan också ange URL: en där du hittar dina villkor. Kunder måste acceptera dessa villkor innan de kan testa ditt erbjudande.

Välj **Spara utkast** innan du fortsätter.

## <a name="offer-listing"></a>Erbjudande lista

Det är här du definierar information om ditt erbjudande, till exempel namn, beskrivning och avbildningar.

> [!NOTE]
> Du kan endast ange information om erbjudandet på ett språk. Det är inte nödvändigt att vara på engelska, så länge erbjudande beskrivningen börjar med frasen, "det här programmet är endast tillgängligt på [språk som inte är engelska]". Det är också acceptabelt att tillhandahålla en *URL för hjälp länkar* för att erbjuda innehåll på ett annat språk än det som används i list innehållet för erbjudandet.

Här är ett exempel på hur information om erbjudandet visas i Microsoft AppSource (de priser som visas i listan är endast avsedda att avspegla faktiska kostnader):

:::image type="content" source="media/example-d365-business-central.png" alt-text="Visar hur det här erbjudandet visas i Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Logotyp
2. Produkter
3. Kategorier
4. Support adress (länk)
5. Villkor för användning
6. Sekretesspolicy
7. Erbjudandets namn
8. Sammanfattning
9. Beskrivning
10. Skärm bilder/videor

### <a name="name"></a>Namn

Det namn som du anger här visas för kunder som rubrik på din erbjudande lista. Det här fältet fylls i automatiskt med texten du angav för **erbjud alias** när du skapade erbjudandet, men du kan ändra det här värdet. Det här namnet kan vara ett varumärke (och du kan inkludera varumärkes-eller Copyright-symboler). Namnet får innehålla högst 50 tecken och får inte innehålla några emojis.

### <a name="short-description"></a>Kort beskrivning

Ange en kort beskrivning av erbjudandet, upp till 100 tecken. Beskrivningen kan användas i Sök resultaten för Marketplace.

### <a name="description"></a>Beskrivning

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Sök efter nyckelord

Du kan också ange upp till tre Sök nyckelord för att hjälpa kunderna att hitta ditt erbjudande i Marketplace. För bästa resultat bör du även försöka använda dessa nyckelord i beskrivningen.

### <a name="products-your-app-works-with"></a>Produkter som din app fungerar med

Om du vill låta kunderna veta att appen fungerar med vissa produkter anger du upp till tre produkt namn här.

### <a name="helpprivacy-urls"></a>Hjälp/sekretess-URL: er

I det här avsnittet kan du ange länkar som hjälper kunderna att förstå mer om ditt erbjudande.

#### <a name="help-link"></a>Hjälp länk

Ange URL: en där kunderna kan lära sig mer om ditt erbjudande. Din **Hjälp länk** kan inte vara samma som din **Support-URL** (förklaras nedan).

#### <a name="privacy-policy-link"></a>Länk till sekretess policy

Ange URL: en till din organisations sekretess policy. Du ansvarar för att se till att appen uppfyller sekretess lagar och föreskrifter och att tillhandahålla en giltig sekretess policy.

### <a name="contact-information"></a>Kontaktinformation

I det här avsnittet måste du ange namn, e-postadress och telefonnummer för en **support kontakt** och en **teknisk kontakt** . Den här informationen visas inte för kunder, men är tillgänglig för Microsoft och kan tillhandahållas till CSP-partner.

I avsnittet **support kontakt** måste du också ange **Support-URL:** en där CSP-partner kan hitta support för ditt erbjudande. Support-URL: en får inte vara samma som **Hjälp länken** .

### <a name="supporting-documents"></a>Stöd dokument

Ange minst ett (och upp till tre) relaterade marknadsförings dokument här, till exempel fakta blad, broschyrer, check listor eller presentationer. Dessa dokument måste vara i PDF-format.

### <a name="marketplace-images"></a>Marketplace-avbildningar

Ange en PNG-fil för logo typen med **stor** storlek. Partner Center använder detta för att skapa en **liten** logo typ. Du kan också ersätta detta med en annan avbildning senare.

- **Stor** (från 216 x 216 till 350 x 350 BPT, krävs)
- **Liten** (48 x 48 BPT, valfritt)

Dessa Logo typer används på olika platser i listan:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar tjänsten som `https://upload.xboxlive.com` används av Partner Center.

#### <a name="screenshots"></a>Skärmbilder

Lägg till skärm bilder som visar hur ditt erbjudande fungerar. Minst tre skärm bilder krävs och du kan lägga till upp till fem. Alla skärm dum par måste vara 1280 x 720 bild punkter.

#### <a name="videos"></a>Videoklipp

Du kan också lägga till upp till fem videor som demonstrerar ditt erbjudande. Dessa videor bör finnas på YouTube och/eller Vimeo. För var och en anger du videons namn, URL och en miniatyr bild av videon (1280 x 720 pixlar).

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare platser för Marketplace-lista

[Metod tips för Marketplace-erbjudanden](../gtm-offer-listing-best-practices.md)

Välj **Spara utkast** innan du fortsätter.

## <a name="availability"></a>Tillgänglighet

På den här sidan får du alternativ för var och hur du gör ditt erbjudande tillgängligt.

### <a name="markets"></a>Marknaden

I det här avsnittet kan du ange vilka marknader som ditt erbjudande ska vara tillgängligt för. Det gör du genom att välja **Redigera marknader** , så visas popup-fönstret för **marknads val** .

Välj minst en marknad för att publicera erbjudandet. Välj **Välj alla** om du vill göra ditt erbjudande tillgängligt på alla möjliga marknader eller Välj de olika marknader som du vill lägga till.

Dina val gäller endast för nya förvärv. om någon redan har din app på en viss marknad och du senare tar bort marknaden, kan de personer som redan har erbjudandet på marknaden fortsätta att använda den, men inga nya kunder på den marknaden kommer att kunna få ditt erbjudande.

> [!IMPORTANT]
> Det är ditt ansvar att uppfylla alla lokala juridiska krav, även om dessa krav inte finns med i listan här eller i Partner Center.

Tänk på att även om du väljer alla marknader, lokala lagar, begränsningar eller andra faktorer kan förhindra att vissa erbjudanden visas i vissa länder och regioner.

### <a name="preview-audience"></a>Förhandsgranska mål grupp

Innan du publicerar erbjudandet Live på det bredare Marketplace-erbjudandet måste du först göra det tillgängligt för en begränsad **förhands gransknings publik** . Ange en **Dölj nyckel** (valfri sträng med endast gemener och/eller siffror) här. Medlemmar i din förhands visnings publik kan använda denna Dölj-nyckel som en token för att visa en förhands granskning av ditt erbjudande i Marketplace.

När du är redo att göra ditt erbjudande tillgängligt och ta bort begränsningen för för hands versionen måste du ta bort **Dölj-tangenten** och publicera igen.

Välj **Spara utkast** innan du fortsätter.

## <a name="technical-configuration"></a>Teknisk konfiguration

Den här sidan definierar de tekniska uppgifter som används för att ansluta till ditt erbjudande. Med den här anslutningen kan vi tillhandahålla ditt erbjudande för slutanvändaren om de väljer att förvärva det.

### <a name="package-type"></a>Pakettyp

Välj det alternativ som gäller för ditt erbjudande:

* **Lägg till** – en app för tillägg utökar upplevelsen och de befintliga funktionerna i Dynamics 365 Business Central. Mer information finns i [lägga till appar](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
* **Anslut** – en Connect-app kan användas i scenariot där det måste upprättas en punkt-till-punkt-anslutning mellan Dynamics 365 Business Central och en lösning eller tjänst från tredje part. Mer information finns i [ansluta appar](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

### <a name="file-upload"></a>Fil uppladdning

Om du har valt **Lägg till** ovan, där du laddar upp paket filen för erbjudandet, tillsammans med paketfilerna för alla tillägg som det har beroenden för.

#### <a name="extensions-package-file"></a>Paket fil för tillägg

Ladda upp tilläggs paket filen (. app) för ditt erbjudande.

#### <a name="library-package-file"></a>Biblioteks paket fil

Krävs om ditt erbjudande måste installeras tillsammans med ett annat tillägg som inte kommer att publiceras på Marketplace. Om så är fallet laddar du ned appens app-fil här.

>[!NOTE]
>Beroende paket filen används inte längre. Ladda upp en biblioteks paket fil i stället.

### <a name="url-to-app-installation"></a>URL till app-installation

Om du har valt **Anslut** ovan anger du adressen till din app-installation här. För anslutna tjänster som inte kräver installation anger du adressen för din tjänsts landnings sida eller registrerings sida.

Välj **Spara utkast** innan du fortsätter.

## <a name="test-drive-technical-configuration"></a>Teknisk konfiguration för provkörning

På den här sidan kan du konfigurera en demonstration ("testenhet") som gör det möjligt för kunder att prova ditt erbjudande innan de köper det. Läs mer i [Vad är Test Drive](../what-is-test-drive.md).

Om du vill aktivera en testen het markerar du kryss rutan **Aktivera en testenhet** på fliken [erbjudande konfiguration](#test-drive) . Avmarkera den här kryss rutan om du vill ta bort test enheten från erbjudandet.

När du har konfigurerat test enheten väljer du **Spara utkast** innan du fortsätter.

## <a name="supplemental-content"></a>Kompletterande innehåll

På den här sidan kan du ange ytterligare information om ditt erbjudande för att hjälpa oss att validera ditt erbjudande. Den här informationen visas inte för kunder eller publiceras på Marketplace.

### <a name="target-release"></a>Målversion

Ange vilken version av Microsoft Dynamics Business Central din lösnings mål: **aktuell** , **nästa större** eller **Nästa mindre** . Med den här informationen kan vi testa din lösning på lämpligt sätt.

### <a name="supported-editions"></a>Versioner som stöds

Om ditt erbjudande kräver Premium-versionen av Microsoft Dynamics 365 Business Central väljer du endast **Premium** . Annars väljer du både **Essentials** och **Premium** .

### <a name="key-usage-scenario"></a>Scenario för nyckel användning

Du måste ladda upp en PDF-fil som visar dina erbjudandens scenarier för nyckel användning som visas i ett dokument (. pdf-format). Alla scenarier som listas här kan kontrol leras av vårt validerings team innan vi godkänner ditt erbjudande för Marketplace.

### <a name="app-tests-automation"></a>Automatisering av app-test

Om erbjudandet är en app för tilläggsprogram måste du ladda upp en Automation-fil (. app) för **app-test** . Den här filen kan inte användas för att ansluta appar.

### <a name="test-accounts"></a>Test konton

Om ett test konto krävs för att vårt certifierings team ska kunna granska ditt erbjudande, laddar du upp en PDF-, doc-eller DOCX-fil med din **test konto** information.

## <a name="publish"></a>Publicera

### <a name="submit-offer-to-preview"></a>Skicka erbjudande till för hands version

När du har slutfört alla obligatoriska avsnitt i erbjudandet väljer du **publicera** i det övre högra hörnet i portalen. Du kommer att omdirigeras till sidan **Granska och publicera** . 

Om det är första gången du publicerar det här erbjudandet kan du:

- Se slut för ande status för varje avsnitt i erbjudandet.
    - *Inte startad* – innebär att avsnittet inte har vidrör ATS och måste slutföras.
    - *Uncompleted* – innebär att avsnittet innehåller fel som behöver åtgärdas eller som kräver mer information. Gå tillbaka till avsnitten och uppdatera det.
    - *Complete* – innebär att avsnittet har slutförts, att alla nödvändiga data har angetts och att det inte finns några fel. Alla avsnitt i erbjudandet måste vara i ett komplett tillstånd innan du kan skicka in erbjudandet.
- I avsnittet **Obs!-certifiering** anger du test instruktioner till certifierings teamet för att kontrol lera att appen är korrekt testad, förutom eventuella kompletterande anteckningar som hjälper dig att förstå din app.
- Skicka in erbjudandet om publicering genom att välja **Skicka** . Vi kommer att skicka ett e-postmeddelande till dig när en för hands version av erbjudandet är tillgänglig för att granska och godkänna. Gå tillbaka till Partner Center och välj **Go-Live** för erbjudandet om att publicera ditt erbjudande till allmänheten.

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Commercial Marketplace](./update-existing-offer.md)