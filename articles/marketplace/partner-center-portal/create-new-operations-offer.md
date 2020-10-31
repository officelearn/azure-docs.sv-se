---
title: Skapa en Dynamics 365 för drift erbjudande på den kommersiella Marketplace
description: Så här skapar du en ny Dynamics 365 för åtgärder för att visa eller sälja på Azure Marketplace, AppSource eller via program varan för Cloud Solution Provider (CSP) med hjälp av den kommersiella Marketplace på Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: 1c28b4edab1e1033bf9ac7df6b1249b549bb4fe1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131521"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Skapa ett erbjudande för Dynamics 365 for Operations

I det här avsnittet beskrivs hur du skapar en ny Dynamics 365 för Operations-erbjudandet. [Microsoft Dynamics 365 for ekonomi och Operations](https://dynamics.microsoft.com/finance-and-operations) är en tjänst för företags resurs planering (ERP) som stöder avancerad finans-, drift-, tillverknings-och leverans kedjas hantering. Alla erbjudanden för Dynamics 365 för åtgärder måste gå igenom vår certifierings process.

Innan du börjar [skapar du ett kommersiellt marknads plats konto i Partner Center](create-account.md) , om du inte har gjort det än. Se till att ditt konto är registrerat i programmet för kommersiella marknads platser.

>[!NOTE]
> När ett erbjudande har publicerats uppdateras bara ändringar i erbjudandet i Partner Center och onlinebutiken när du har skickat in erbjudandet för publicering igen.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. På menyn till vänster-navigerings väljer du **kommersiell Marketplace** -  >  **Översikt** .
3. På sidan Översikt väljer du **+ nytt erbjudande**  >  **Dynamics 365 för åtgärder** .

    ![Visar menyn till vänster-navigering.](./media/new-offer-dynamics-365-operations.png)

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

Välj **skapa** för att generera erbjudandet och fortsätt.

## <a name="offer-setup"></a>Erbjudande konfiguration

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

En testen het är ett bra sätt att presentera ditt erbjudande för potentiella kunder genom att ge dem möjlighet att "prova innan du köper", vilket resulterar i ökad konvertering och skapandet av mycket kvalificerade leads. Om du vill veta mer kan du börja med [Vad är Test Drive](../what-is-test-drive.md).

Om du vill aktivera en testenhet under en fast tids period markerar du kryss rutan **Aktivera en testenhet** . Avmarkera den här kryss rutan om du vill ta bort test enheten från erbjudandet.

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

På den här sidan visas de språk som ditt erbjudande visas i. För närvarande är **engelska (USA)** det enda tillgängliga alternativet.

Du måste definiera Marketplace-information (erbjudande namn, beskrivning, bilder osv.) för varje språk/marknad. Välj språk/marknads namn för att ange den här informationen.

> [!NOTE]
> Erbjudande för List innehåll (till exempel beskrivning, dokument, skärm bilder, användnings villkor osv.) behöver inte vara på engelska, förutsatt att beskrivningen börjar med frasen, "det här programmet är endast tillgängligt på [språk som inte är engelska]". Det är också acceptabelt att tillhandahålla en *användbar länk-URL* för att erbjuda innehåll på ett annat språk än det som används i innehålls förteckningen för erbjudandet.

Här är ett exempel på hur information om erbjudandet visas i Microsoft AppSource:

:::image type="content" source="media/example-azure-marketplace-d365-operations.png" alt-text="Visar hur det här erbjudandet visas i Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Logotyp
2. Produkter
3. Kategorier
4. Branscher
5. Support adress (länk)
6. Villkor för användning
7. Sekretesspolicy
8. Erbjudandets namn
9. Skärm bilder/videor
10. Beskrivning

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

### <a name="support-urls"></a>Support-URL: er

I det här avsnittet kan du ange länkar som hjälper kunderna att förstå mer om ditt erbjudande.

#### <a name="help-link"></a>Hjälp länk

Ange URL: en där kunderna kan lära sig mer om ditt erbjudande.

#### <a name="privacy-policy-url"></a>URL för sekretess policy

Ange URL: en till din organisations sekretess policy. Du ansvarar för att se till att appen uppfyller sekretess lagar och föreskrifter och att tillhandahålla en giltig sekretess policy.

### <a name="contacts"></a>Kontakter

I det här avsnittet anger du namn, e-postadress och telefonnummer för en **support kontakt** och en **teknisk kontakt** . Den här informationen visas inte för kunder, men är tillgänglig för Microsoft och kan tillhandahållas till CSP-partner.

I avsnittet **support kontakt** anger du support- **URL: en** där CSP-partner kan hitta support för ditt erbjudande.

### <a name="supporting-documents"></a>Stöd dokument

Ange minst ett (och upp till tre) relaterade marknadsförings dokument här, till exempel fakta blad, broschyrer, check listor eller presentationer. Dessa dokument måste vara i PDF-format.

### <a name="marketplace-images"></a>Marketplace-avbildningar

I det här avsnittet kan du ange logo typer och avbildningar som ska användas när du visar ditt erbjudande för kunden. Alla bilder måste vara i PNG-format.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar tjänsten som https://upload.xboxlive.com används av Partner Center.

#### <a name="store-logos"></a>Butiks logo typer

Ange en PNG-fil för logo typen med **stor** storlek. Partner Center använder detta för att skapa en **liten** logo typ. Du kan också ersätta detta med en annan avbildning senare.

- **Stor** (från 216 x 216 till 350 x 350 BPT, krävs)
- **Liten** (48 x 48 BPT, valfritt)

Dessa Logo typer används på olika platser i listan:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Skärmbilder

Lägg till skärm bilder som visar hur ditt erbjudande fungerar. Minst en skärm bild krävs, och du kan lägga till upp till fem. Alla skärm dum par måste vara 1280 x 720 bild punkter.

#### <a name="videos"></a>Videoklipp

Du kan också lägga till upp till fyra videor som demonstrerar ditt erbjudande. Dessa videor bör finnas på YouTube och/eller Vimeo. För var och en, anger du videons namn, URL och en miniatyr bild av videon (1280 x 720 bild punkter)

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare platser för Marketplace-lista

[Metod tips för Marketplace-erbjudanden](../gtm-offer-listing-best-practices.md)

Välj **Spara utkast** innan du fortsätter.

## <a name="availability"></a>Tillgänglighet

På den här sidan får du alternativ om var och hur du gör ditt erbjudande tillgängligt.

### <a name="markets"></a>Marknaden

I det här avsnittet kan du ange vilka marknader som ditt erbjudande ska vara tillgängligt för. Det gör du genom att välja **Redigera marknader,** så visas popup-fönstret för **marknads val** .

Som standard är inga marknader markerade. Välj minst en marknad för att publicera ditt erbjudande. Klicka på  **Välj alla** för att göra ditt erbjudande tillgängligt på alla möjliga marknader eller Välj de olika marknader som du vill lägga till. När du är klar väljer du **Spara** .

Dina val gäller endast för nya förvärv. om någon redan har din app på en viss marknad och du senare tar bort marknaden, kan de personer som redan har erbjudandet på marknaden fortsätta att använda den, men inga nya kunder på den marknaden kommer att kunna få ditt erbjudande.

> [!IMPORTANT]
> Det är ditt ansvar att uppfylla alla lokala juridiska krav, även om dessa krav inte finns med i listan här eller i Partner Center.

Tänk på att även om du väljer alla marknader kan lokala lagar och restriktioner eller andra faktorer förhindra att vissa erbjudanden visas i vissa länder och regioner.

### <a name="preview-audience"></a>Förhandsgranska mål grupp

Innan du publicerar erbjudandet Live på det bredare Marketplace-erbjudandet måste du först göra det tillgängligt för en begränsad **förhands gransknings publik** . Ange en **Dölj nyckel** (valfri sträng med endast gemener och/eller siffror) här. Medlemmar i din förhands visnings publik kan använda denna Dölj-nyckel som en token för att visa en förhands granskning av ditt erbjudande i Marketplace.

När du är redo att göra ditt erbjudande tillgängligt och ta bort begränsningen för för hands versionen måste du ta bort **Dölj-tangenten** och publicera igen.

Välj **Spara utkast** innan du fortsätter.

## <a name="technical-configuration"></a>Teknisk konfiguration

Den här sidan definierar de tekniska uppgifter som används för att ansluta till ditt erbjudande. Med den här anslutningen kan vi tillhandahålla ditt erbjudande för slutanvändaren om de väljer att förvärva det.

### <a name="solution-identifier"></a>Lösnings identifierare

Ange lösnings identifieraren (GUID) för din lösning.

Hitta lösnings-ID:

1. I Microsoft Dynamics Lifecycle Services (LCS) väljer du **lösnings hantering** .
2. Välj din lösning och leta efter **lösnings identifieraren** i **paket översikten** . Om identifieraren är tom väljer du **Redigera** och publicera om ditt paket och försöker igen.

### <a name="release-version"></a>Version

Välj den version av Dynamics 365 för ekonomi och åtgärder som den här lösningen fungerar med.

Välj **Spara utkast** innan du fortsätter.

## <a name="test-drive-technical-configuration"></a>Teknisk konfiguration för provkörning

På den här sidan kan du konfigurera en demonstration ("testenhet") som gör det möjligt för kunder att prova ditt erbjudande innan de köper det. Läs mer i [Vad är Test Drive](../what-is-test-drive.md).

Om du vill aktivera en testen het markerar du kryss rutan **Aktivera en testenhet** på fliken [erbjudande konfiguration](#test-drive) . Avmarkera den här kryss rutan om du vill ta bort test enheten från erbjudandet.

När du har konfigurerat test enheten väljer du **Spara utkast** innan du fortsätter.

## <a name="supplemental-content"></a>Kompletterande innehåll

På den här sidan kan du ange ytterligare information om ditt erbjudande för att hjälpa oss att validera ditt erbjudande. Den här informationen visas inte för kunder eller publiceras på Marketplace.

### <a name="validation-assets"></a>Validerings till gångar

Ladda upp en [anpassnings analys rapport (Car)](/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report) i det här avsnittet. Den här rapporten genereras genom att analysera anpassnings-och tilläggs modeller baserat på en fördefinierad uppsättning regler för bästa praxis.

Den här filen måste vara i formatet. xls eller. xlsx. Om du har mer än en rapport kan du ladda upp en zip-fil som innehåller alla rapporter.

### <a name="does-solution-include-localizations"></a>Innehåller lösningen lokaliseringar?

Välj **Ja** om lösningen aktiverar användning av lokala standarder och principer (till exempel om den hanterar de olika löne regler som krävs av olika länder/regioner). Annars väljer du **Nej** .

### <a name="does-solution-enable-translations"></a>Aktiverar lösningen översättningar?

Svara **Ja** om texten i lösningen kan översättas till andra språk. Annars väljer du **Nej** .

Välj **Spara utkast** innan du fortsätter.

## <a name="publish"></a>Publicera

### <a name="submit-offer-to-preview"></a>Skicka erbjudande till för hands version

När du har slutfört alla obligatoriska avsnitt i erbjudandet väljer du **Granska och publicera** i det övre högra hörnet i portalen.

Om det är första gången du publicerar det här erbjudandet kan du:

- Se slut för ande status för varje avsnitt i erbjudandet.
    - **Inte startat** – avsnittet har inte vidrör ATS och bör slutföras.
    - **Ofullständig** – avsnittet innehåller fel som behöver åtgärdas eller som kräver mer information. Gå tillbaka till avsnitten och uppdatera det.
    - **Slutfört** – avsnittet är slutfört, alla data som krävs har angetts och det finns inga fel. Alla avsnitt i erbjudandet måste vara i ett komplett tillstånd innan du kan skicka in erbjudandet.
- I avsnittet **Obs!-certifiering** anger du test instruktioner till certifierings teamet för att kontrol lera att appen är korrekt testad, förutom eventuella kompletterande anteckningar som hjälper dig att förstå din app. Mer information om testnings instruktioner och hur du slutför en första publicering finns i [AppSource Dynamics 365 finans och Operations funktionell validering](../dynamics-365-finance-operations-functional-validation.md).
- Skicka in erbjudandet om publicering genom att välja **Skicka** . Vi skickar dig ett e-postmeddelande för att meddela dig när en för hands version av erbjudandet kan granskas och godkännas. Gå tillbaka till Partner Center och välj **Go-Live** för erbjudandet om att publicera ditt erbjudande till allmänheten.

## <a name="next-step"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Commercial Marketplace](./update-existing-offer.md)