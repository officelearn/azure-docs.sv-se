---
title: Skapa en Dynamics 365 för kund engagemang & PowerApps-erbjudande på Microsofts kommersiella marknads plats
description: Så här skapar du en ny Dynamics 365 för kund engagemang & PowerApps-erbjudande för att visa eller sälja på Azure Marketplace, AppSource eller via program varan för Cloud Solution Provider (CSP) i Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: 96bdfc513023cafb2d94a68c06f1065cf20c3ba8
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130178"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Skapa ett erbjudande för Dynamics 365 for Customer Engagement & PowerApps

I det här avsnittet beskrivs hur du skapar en ny Dynamics 365 för kund engagemang & PowerApps-erbjudande. Alla appar för Dynamics 365 för kund engagemang (PowerApps, Sales, service, Project service och Field Service) måste gå igenom vår certifierings process och ge support för en utvärderings upplevelse. Certifierings processen kontrollerar din lösning för standard krav, kompatibilitet och korrekt praxis. Med utvärderings versionen kan användarna distribuera din lösning till en Live Dynamics 365-miljö.

Innan du börjar [skapar du ett kommersiellt marknads plats konto i Partner Center](create-account.md) , om du inte har gjort det än. Se till att ditt konto är registrerat i programmet för kommersiella marknads platser.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. På menyn till vänster-navigerings väljer du **kommersiell Marketplace** -  >  **Översikt** .
3. På sidan Översikt väljer du **+ nytt erbjudande**  >  **Dynamics 365 för kund engagemang & PowerApps** .

    ![Visar menyn till vänster-navigering.](./media/new-offer-dynamics-365-customer-engagement-powerapps.png)

> [!NOTE]
> När ett erbjudande har publicerats visas bara ändringar i Partner Center i onlinebutiker när du har publicerat om erbjudandet. Se till att du alltid publicerar igen när du har gjort ändringar.

## <a name="new-offer"></a>Nytt erbjudande

Ange ett **erbjudande-ID** . Detta är en unik identifierare för varje erbjudande i ditt konto.

- Detta ID är synligt för kunder i webb adressen för Marketplace-erbjudandet och Azure Resource Manager mallar, om tillämpligt.
- Erbjudande-ID: t kombinerat med utgivar-ID: t måste vara under 40 tecken långt.
- Använd bara gemena bokstäver och siffror. Det kan innehålla bindestreck och under streck, men inga blank steg. Om ditt utgivar-ID t. ex. är testpublisherid och du anger **test-erbjudande-1** , är webb adressen för erbjudandet `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Erbjudande-ID: t kan inte ändras när du har valt **skapa** .

Ange ett **erbjudande alias** . Detta är det namn som används för erbjudandet i Partner Center.

- Det här namnet används inte på Marketplace och skiljer sig från namnet på erbjudandet och andra värden som visas för kunderna.
- Detta kan inte ändras när du har valt **skapa** .

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

Välj **Spara utkast** innan du fortsätter.

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

### <a name="applicable-dynamics-365-products"></a>Tillämpliga Dynamics 365-produkter

Välj alla de Dynamics 365-produkter som det här erbjudandet gäller för.

### <a name="app-version"></a>Appversion

Ange versions numret för ditt erbjudande. Kunderna kommer att se den här versionen på erbjudandets detalj sida. Om du bara uppdaterar versions numret på grund av marknadsföring/beskrivande ändringar, markerar du rutan **endast marknadsförings** ändringar. Med det här alternativet kan erbjudandet kringgå certifierings-och etablerings stegen.

### <a name="terms-and-conditions"></a>Villkor

Ange dina egna juridiska villkor här. Du kan också ange adressen där du hittar dina villkor. Kunder måste acceptera dessa villkor innan de kan testa ditt erbjudande.

Välj **Spara utkast** innan du fortsätter.

## <a name="offer-listing"></a>Erbjudande lista

På den här sidan visas de språk som ditt erbjudande visas i. För närvarande är **engelska (USA)** det enda tillgängliga alternativet.

Definiera Marketplace-information för varje språk/marknad här, till exempel erbjudande namn, beskrivning och avbildningar. Välj språk/marknads namn för att ange den här informationen.

> [!NOTE]
> Erbjudande för List innehåll (till exempel beskrivning, dokument, skärm bilder och användnings villkor) behöver inte vara på engelska, förutsatt att beskrivningen börjar med frasen, "det här programmet är endast tillgängligt på [språk som inte är engelska]". Det är också acceptabelt att tillhandahålla en *användbar länk-URL* för att erbjuda innehåll på ett annat språk än det som används i innehålls förteckningen för erbjudandet.

Här är ett exempel på hur information om erbjudandet visas i Microsoft AppSource (de priser som visas i listan är endast avsedda att avspegla faktiska kostnader):

:::image type="content" source="media/example-azure-marketplace-d365-customer-engagement.png" alt-text="Visar hur det här erbjudandet visas i Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Logotyp
2. Produkter
3. Kategorier
4. Support adress (länk)
5. Användningsvillkor adress (länk)
6. Erbjudandets namn
7. Beskrivning
8. Skärm bilder/videor

### <a name="name"></a>Namn

Det namn som du anger här visas för kunder som rubrik på din erbjudande lista. Det här fältet fylls i automatiskt med texten du angav för **erbjud alias** när du skapade erbjudandet, men du kan ändra det här värdet. Det här namnet kan vara ett varumärke (och du kan inkludera varumärkes-eller Copyright-symboler). Namnet får innehålla högst 50 tecken och får inte innehålla några emojis.

### <a name="short-description"></a>Kort beskrivning

Ange en kort beskrivning av erbjudandet, upp till 100 tecken. Beskrivningen kan användas i Sök resultaten för Marketplace.

### <a name="description"></a>Beskrivning

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Sök efter nyckelord

Du kan också ange upp till tre Sök nyckelord för att hjälpa kunderna att hitta ditt erbjudande i Marketplace. För bästa resultat bör du också använda dessa nyckelord i beskrivningen.

### <a name="products-your-app-works-with"></a>Produkter som din app fungerar med

Om du vill låta kunderna veta att appen fungerar med vissa produkter anger du upp till tre produkt namn här.

### <a name="support-urls"></a>Support-URL: er

I det här avsnittet kan du ange länkar som hjälper kunderna att förstå mer om ditt erbjudande.

#### <a name="help-link"></a>Hjälp länk

Ange adressen där kunderna kan lära sig mer om ditt erbjudande.

#### <a name="privacy-policy-url"></a>URL för sekretess policy

Ange adressen till din organisations sekretess policy. Du ansvarar för att se till att appen uppfyller sekretess lagar och föreskrifter och att tillhandahålla en giltig sekretess policy.

### <a name="contacts"></a>Kontakter

Ange namn, e-postadress och telefonnummer för en **support kontakt** och en **teknisk kontakt** . Den här informationen visas inte för kunder, men är tillgänglig för Microsoft och kan tillhandahållas till CSP-partner.

I avsnittet **support kontakt** måste du också ange **Support-URL:** en där CSP-partner kan hitta support för ditt erbjudande.

### <a name="supporting-documents"></a>Stöd dokument

Ange minst ett (och upp till tre) relaterade marknadsförings dokument här, till exempel fakta blad, broschyrer, check listor eller presentationer i PDF-format.

### <a name="marketplace-images"></a>Marketplace-avbildningar

Ange logo typer och avbildningar för ditt erbjudande. Alla bilder måste vara i PNG-format.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
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

På den här sidan kan du definiera var och hur du vill göra ditt erbjudande tillgängligt.

### <a name="markets"></a>Marknaden

Ange de marknader där ditt erbjudande ska vara tillgängligt. Det gör du genom att välja **Redigera marknader** , så visas popup-fönstret för **marknads val** .

Som standard väljs inga marknader, men du måste välja minst en marknad för att kunna publicera erbjudandet. Välj  **Välj alla** om du vill göra ditt erbjudande tillgängligt på alla möjliga marknader eller Välj de olika marknader som du vill lägga till.

Dina val gäller endast för nya förvärv. om någon redan har din app på en viss marknad och du senare tar bort den marknaden, kan de personer som redan har erbjudandet på marknaden fortsätta att använda den, men inga nya kunder på den marknaden kommer att kunna få ditt erbjudande.

> [!IMPORTANT]
> Det är ditt ansvar att uppfylla alla lokala juridiska krav, även om dessa krav inte finns med i listan här eller i Partner Center. Även om du väljer alla marknader kan lokala lagar och restriktioner eller andra faktorer förhindra att vissa erbjudanden visas i vissa länder och regioner.

### <a name="preview-audience"></a>Förhandsgranska mål grupp

Innan du publicerar erbjudandet Live på det bredare Marketplace-erbjudandet måste du först göra det tillgängligt för en begränsad **förhands gransknings publik** . Ange en **Dölj nyckel** (valfri sträng med endast gemener och/eller siffror) här. Medlemmar i din förhands visnings publik kan använda denna Dölj-nyckel som en token för att visa en förhands granskning av ditt erbjudande i Marketplace.

När du är redo att göra ditt erbjudande tillgängligt och ta bort begränsningen för för hands versionen måste du ta bort **Dölj-tangenten** och publicera igen.

Välj **Spara utkast** innan du fortsätter.

## <a name="technical-configuration"></a>Teknisk konfiguration

Den här sidan definierar de tekniska uppgifter som används för att ansluta till ditt erbjudande. Med den här anslutningen kan vi tillhandahålla ditt erbjudande för slutanvändaren om de väljer att förvärva det.

### <a name="base-license-model"></a>Bas licens modell

Den grundläggande licens modellen avgör hur kunderna tilldelas ditt program i administrations centret för CRM. Välj **resurs** för instansbaserade licenser eller **användare** om licenser tilldelas en per klient.

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>Kräver utgående S2S-och CRM-åtkomst för säker lagring

Markera den här kryss rutan om du vill aktivera konfiguration av åtkomst till den säkra databasen i CRM eller server-till-Server (S2S). Den här funktionen kräver specialiserad uppmärksamhet från Dynamics 365-teamet under certifierings fasen. Microsoft kommer att kontakta dig för att slutföra ytterligare steg för att stödja den här funktionen.

### <a name="application-configuration-url"></a>URL för program konfiguration

Ange webb adressen till webb sidan för konfigurationen som gör det möjligt för kunden att konfigurera din app.

### <a name="crm-package"></a>CRM-paket

I **URL: en för din paket plats** anger du URL: en för ett Azure Blob Storage-konto som innehåller den överförda CRM Package. zip-filen. Ta med en skrivskyddad SAS-nyckel i URL: en så att Microsoft kan hämta ditt paket för verifiering.

> [!IMPORTANT]
> För att undvika ett publicerings block ser du till att utgångs datumet i URL: en för blob-lagringen inte har gått ut. Du kan ändra datumet genom att komma åt din princip. Vi rekommenderar att **förfallo tiden infaller** minst en månad i framtiden.

Markera kryss rutan **det finns mer än ett CRM-paket i paket filen** , om tillämpligt. I så fall, se till att inkludera alla paket i. zip-filen.

Detaljerad information om hur du skapar paketet och uppdaterar dess struktur finns i [steg 3: skapa ett AppSource-paket för din app](/powerapps/developer/common-data-service/create-package-app-appsource).

### <a name="crm-package-availability"></a>Tillgänglighet för CRM-paket

I det här avsnittet väljer du **+ Lägg till region** för att ange de geografiska regioner där ditt CRM-paket ska vara tillgängligt för kunderna. Distribution till följande suveräna regioner kräver särskilda behörigheter och verifiering under certifierings processen: [Tyskland](../../germany/index.yml), [amerikanska myndigheter](../../azure-government/documentation-government-welcome.md)och tips.

Som standard används den **URL för program konfiguration** som du angav ovan för varje region. Om du vill kan du ange en separat program konfigurations-URL för en eller flera specifika regioner. 

Välj **Spara utkast** innan du fortsätter.

## <a name="test-drive-technical-configuration"></a>Teknisk konfiguration för provkörning

På den här sidan kan du konfigurera en demonstration ("testenhet") som gör det möjligt för kunder att prova ditt erbjudande innan de köper det. Läs mer i [Vad är Test Drive](../what-is-test-drive.md).

Om du vill aktivera en testen het markerar du kryss rutan **Aktivera en testenhet** på fliken [erbjudande konfiguration](#test-drive) . Avmarkera den här kryss rutan om du vill ta bort test enheten från erbjudandet.

När du har konfigurerat test enheten väljer du **Spara utkast** innan du fortsätter.

## <a name="supplemental-content"></a>Kompletterande innehåll

På den här sidan kan du ange ytterligare information om ditt erbjudande för att hjälpa oss att validera ditt erbjudande. Den här informationen visas inte för kunder eller publiceras på Marketplace.

### <a name="key-usage-scenario"></a>Scenario för nyckel användning

Ladda upp en PDF-fil som visar dina erbjudandens nyckel användnings scenarier. Alla scenarier kan verifieras av vårt validerings team innan vi godkänner ditt erbjudande för Marketplace.

Välj **Spara utkast** innan du fortsätter.

## <a name="publish"></a>Publicera

### <a name="submit-offer-to-preview"></a>Skicka erbjudande till för hands version

När du har slutfört alla obligatoriska avsnitt i erbjudandet väljer du **Granska och publicera** i det övre högra hörnet i portalen.

Om det är första gången du publicerar det här erbjudandet kan du:

- Se slut för ande status för varje avsnitt i erbjudandet.
    - **Inte startad** – avsnittet har inte vidrör ATS och bör slutföras.
    - **Ofullständig** – avsnittet innehåller fel som behöver åtgärdas eller som kräver mer information. Gå tillbaka till avsnitten och uppdatera det.
    - **Slutfört** -avsnittet är slutfört, alla data som krävs har angetts och det finns inga fel. Alla avsnitt i erbjudandet måste vara i ett komplett tillstånd innan du kan skicka in erbjudandet.
- I avsnittet **Obs!-certifiering** anger du test instruktioner till certifierings teamet för att kontrol lera att appen är korrekt testad, förutom eventuella kompletterande anteckningar som hjälper dig att förstå din app.
- Skicka in erbjudandet om publicering genom att välja **Skicka** . Vi skickar dig ett e-postmeddelande för att meddela dig när en för hands version av erbjudandet kan granskas och godkännas. Gå tillbaka till Partner Center och välj **Go-Live** för erbjudandet att publicera till allmänheten.

## <a name="next-step"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Commercial Marketplace](./update-existing-offer.md)