---
title: Skapa en Dynamics 365 för kund engagemang & PowerApps-erbjudande på Microsofts kommersiella marknads plats
description: Så här skapar du en ny Dynamics 365 för kund engagemang & PowerApps-erbjudande för att visa eller sälja på Azure Marketplace, AppSource eller via program varan för Cloud Solution Provider (CSP) i Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: f975abcd05b92dd57eb736f076b63c88b4bf6769
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747262"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Skapa ett erbjudande för Dynamics 365 for Customer Engagement & PowerApps

I det här avsnittet beskrivs hur du skapar en ny Dynamics 365 för kund engagemang & PowerApps-erbjudande. Alla appar för Dynamics 365 för kund engagemang (PowerApps, Sales, service, Project service och Field Service) måste gå igenom vår certifierings process och ge support för en utvärderings upplevelse. Certifierings processen kontrollerar din lösning för standard krav, kompatibilitet och korrekt praxis. Med utvärderings versionen kan användarna distribuera din lösning till en Live Dynamics 365-miljö.

Innan du börjar [skapar du ett kommersiellt marknads plats konto i Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , om du inte har gjort det än. Se till att ditt konto är registrerat i programmet för kommersiella marknads platser.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. På menyn till vänster-navigerings väljer du **kommersiell Marketplace**-  >  **Översikt**.
3. På sidan Översikt väljer du **+ nytt erbjudande**  >  **Dynamics 365 för kund engagemang & PowerApps**.

    ![Visar menyn till vänster-navigering.](./media/new-offer-dynamics-365-cepa.png)

> [!NOTE]
> När ett erbjudande har publicerats visas bara ändringar som gjorts i Partner Center i butiker efter publiceringen av erbjudandet. Se till att du alltid publicerar igen när du har gjort ändringar.

## <a name="new-offer"></a>Nytt erbjudande

Ange ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto.

- Detta ID är synligt för kunder i webb adressen för Marketplace-erbjudandet och Azure Resource Manager mallar, om tillämpligt.
- Använd bara gemena bokstäver och siffror. Det kan innehålla bindestreck och under streck, men inte blank steg, och är begränsat till 50 tecken. Om du till exempel anger **test-erbjudande-1**, är webb adressen för erbjudandet `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Erbjudande-ID: t kan inte ändras när du har valt **skapa**.

Ange ett **erbjudande alias**. Detta är det namn som används för erbjudandet i Partner Center.

- Det här namnet används inte på Marketplace och skiljer sig från namnet på erbjudandet och andra värden som visas för kunderna.
- Detta kan inte ändras när du har valt **skapa**.

Välj **skapa** för att generera erbjudandet och fortsätt.

## <a name="offer-setup"></a>Erbjudande konfiguration

Följ dessa steg för att konfigurera erbjudandet.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Hur vill du att potentiella kunder ska kunna interagera med det här registrerings erbjudandet?

Välj det alternativ som du vill använda för det här erbjudandet.

#### <a name="get-it-now-free"></a>Hämta nu (kostnads fritt)

Lista ditt erbjudande till kunder kostnads fritt genom att tillhandahålla en giltig URL (från och med *http* eller *https*) där de kan komma åt din app.  Till exempel `https://contoso.com/my-app`.

#### <a name="free-trial-listing"></a>Kostnads fri utvärderings version (lista)

Lista ditt erbjudande till kunder med en länk till en kostnads fri utvärderings version genom att tillhandahålla en giltig URL (från och med *http* eller *https*) där de kan få en utvärderings version.  Till exempel `https://contoso.com/trial/my-app`. Erbjudande om kostnads fria utvärderings versioner skapas, hanteras och konfigureras av din tjänst och har inga prenumerationer som hanteras av Microsoft.

> [!NOTE]
> De token som programmet tar emot via din utvärderings länk kan bara användas för att hämta användar information via Azure Active Directory (Azure AD) för att automatisera skapandet av konton i din app. Microsoft-konton stöds inte för autentisering med denna token.

#### <a name="contact-me"></a>Kontakta mig

Samla in kund kontakt information genom att ansluta ditt CRM-system (Customer Relations hip Management). Kunden uppmanas att ange behörighet för att dela sin information. Dessa kund uppgifter, tillsammans med erbjudande namnet, ID: t och Marketplace-källan där de hittade ditt erbjudande, skickas till det CRM-system som du har konfigurerat. Mer information om hur du konfigurerar din CRM finns i [Anslut lead management](#connect-lead-management). 

Välj **Spara utkast** innan du fortsätter.

### <a name="test-drive"></a>Test enhet

En testen het är ett bra sätt att presentera ditt erbjudande för potentiella kunder genom att ge dem möjlighet att "prova innan du köper", vilket resulterar i ökad konvertering och skapandet av mycket kvalificerade leads. [Läs mer om test enheter](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Om du vill aktivera en testenhet under en fast tids period markerar du kryss rutan **Aktivera en testenhet** . Avmarkera den här kryss rutan om du vill ta bort test enheten från erbjudandet. Konfigurera testen hets miljö i avsnittet [test enhet teknisk konfiguration](#test-drive-technical-configuration) senare i det här avsnittet.

Mer information finns i [Testa ditt erbjudande på den kommersiella marknads platsen](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

#### <a name="type-of-test-drive"></a>Typ av test enhet

Välj bland följande alternativ:

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** – en distributions mall som innehåller alla Azure-resurser som utgör din lösning. Produkter som passar det här scenariot använder bara Azure-resurser.
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** – Microsoft är värd för och underhåller Test Drive-tjänsten (inklusive etablering och distribution) för ett företags huvud företags resurs planerings system (finans, Operations, service Chain, CRM osv.).  
- **[Dynamics 365 för kund engagemang](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** – Microsoft är värd för och underhåller tjänsten Test Drive (inklusive etablering och distribution) för ett kund engagemang system (Sales, service, Project service, Field service osv.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** – Microsoft är värd för och underhåller Test Drive-tjänsten (inklusive etablering och distribution) för ekonomi-och drift företags resurs planerings system (finans, drift, tillverkning, leverans kedja osv.). 
- **[Logic app](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** – en distributions mall som omfattar alla komplexa lösnings arkitekturer. Alla anpassade produkter bör använda den här typen av test enhet.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** – en inbäddad länk till en anpassad instrument panel. Produkter som vill demonstrera ett interaktivt Power BI visuellt objekt bör använda den här typen av test enhet. Allt du behöver ladda upp här är din inbäddade Power BI-URL.

#### <a name="additional-test-drive-resources"></a>Ytterligare resurser för enhets test

- [Rekommenderade tekniska metoder](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Metod tips för marknadsföring](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Översikt över test enheter](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (se till att blockering av popup-fönster är inaktiverat)

## <a name="connect-lead-management"></a>Anslut lead-hantering

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Mer information finns i [Översikt över hantering av leads](./commercial-marketplace-get-customer-leads.md).

Välj **Spara utkast** innan du fortsätter.

## <a name="properties"></a>Egenskaper

På den här sidan kan du definiera de kategorier och branscher som ska användas för att gruppera ditt erbjudande på Marketplace, din app-version och de juridiska avtal som stöder ditt erbjudande.

### <a name="category"></a>Kategori

Välj minst en och upp till tre kategorier. Dessa används för att placera ditt erbjudande i lämpliga sökområden för Marketplace. Se till att du beskriver hur ditt erbjudande stöder dessa kategorier i beskrivningen av erbjudandet.

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

### <a name="name"></a>Name

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

Ange namn, e-postadress och telefonnummer för en **support kontakt** och en **teknisk kontakt**. Den här informationen visas inte för kunder, men är tillgänglig för Microsoft och kan tillhandahållas till CSP-partner.

I avsnittet **support kontakt** måste du också ange **Support-URL:** en där CSP-partner kan hitta support för ditt erbjudande.

### <a name="supporting-documents"></a>Stöd dokument

Ange minst ett (och upp till tre) relaterade marknadsförings dokument här, till exempel fakta blad, broschyrer, check listor eller presentationer i PDF-format.

### <a name="marketplace-images"></a>Marketplace-avbildningar

Ange logo typer och avbildningar för ditt erbjudande. Alla bilder måste vara i PNG-format.

>[!NOTE]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar tjänsten som https://upload.xboxlive.com används av Partner Center.

#### <a name="store-logos"></a>Butiks logo typer

Ange ditt erbjudandes logo typ i tre pixel storlekar:
- **Liten** (krävs; 48 x 48)
- **Stor** (krävs; 216 x 216)
- **Bred** (valfritt; 255 x 115)

#### <a name="hero"></a>Hjälte

Hjälte avbildningen är valfri. Om du anger ett värde måste du mäta 815 x 290 pixlar.

#### <a name="screenshots"></a>Skärmbilder

Lägg till skärm bilder som visar hur ditt erbjudande fungerar. Minst en skärm bild krävs, och du kan lägga till upp till fem. Alla skärm dum par måste vara 1280 x 720 bild punkter.

#### <a name="videos"></a>Videoklipp

Du kan också lägga till upp till fyra videor som demonstrerar ditt erbjudande. Dessa videor bör finnas på YouTube och/eller Vimeo. För var och en, anger du videons namn, URL och en miniatyr bild av videon (1280 x 720 bild punkter)

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare platser för Marketplace-lista

[Metod tips för Marketplace-erbjudanden](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

Välj **Spara utkast** innan du fortsätter.

## <a name="availability"></a>Tillgänglighet

På den här sidan kan du definiera var och hur du vill göra ditt erbjudande tillgängligt.

### <a name="markets"></a>Marknaden

Ange de marknader där ditt erbjudande ska vara tillgängligt. Det gör du genom att välja **Redigera marknader**, så visas popup-fönstret för **marknads val** .

Som standard väljs inga marknader, men du måste välja minst en marknad för att kunna publicera erbjudandet. Välj **Välj alla** om du vill göra ditt erbjudande tillgängligt på alla möjliga marknader eller Välj de olika marknader som du vill lägga till.

Dina val gäller endast för nya förvärv. om någon redan har din app på en viss marknad och du senare tar bort den marknaden, kan de personer som redan har erbjudandet på marknaden fortsätta att använda den, men inga nya kunder på den marknaden kommer att kunna få ditt erbjudande.

> [!IMPORTANT]
> Det är ditt ansvar att uppfylla alla lokala juridiska krav, även om dessa krav inte finns med i listan här eller i Partner Center. Även om du väljer alla marknader kan lokala lagar och restriktioner eller andra faktorer förhindra att vissa erbjudanden visas i vissa länder och regioner.

### <a name="preview-audience"></a>Förhandsgranska mål grupp

Innan du publicerar erbjudandet Live på det bredare Marketplace-erbjudandet måste du först göra det tillgängligt för en begränsad **förhands gransknings publik**. Ange en **Dölj nyckel** (valfri sträng med endast gemener och/eller siffror) här. Medlemmar i din förhands visnings publik kan använda denna Dölj-nyckel som en token för att visa en förhands granskning av ditt erbjudande i Marketplace.

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

Markera kryss rutan **det finns mer än ett CRM-paket i paket filen**, om tillämpligt. I så fall, se till att inkludera alla paket i. zip-filen.

Detaljerad information om hur du skapar paketet och uppdaterar dess struktur finns i [steg 3: skapa ett AppSource-paket för din app](https://docs.microsoft.com/powerapps/developer/common-data-service/create-package-app-appsource).

### <a name="crm-package-availability"></a>Tillgänglighet för CRM-paket

I det här avsnittet väljer du **+ Lägg till region** för att ange de geografiska regioner där ditt CRM-paket ska vara tillgängligt för kunderna. Distribution till följande suveräna regioner kräver särskilda behörigheter och verifiering under certifierings processen: [Tyskland](https://docs.microsoft.com/azure/germany/), [amerikanska myndigheter](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)och tips.

Som standard används den **URL för program konfiguration** som du angav ovan för varje region. Om du vill kan du ange en separat program konfigurations-URL för en eller flera specifika regioner. 

Välj **Spara utkast** innan du fortsätter.

## <a name="test-drive-technical-configuration"></a>Teknisk konfiguration för test enhet

På den här sidan kan du konfigurera en demonstration ("testenhet") som gör det möjligt för kunder att prova ditt erbjudande innan de köper det. Läs mer i artikeln [Vad är Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Om du vill aktivera en testen het markerar du kryss rutan Aktivera en testenhet på fliken [erbjudande konfiguration](#test-drive) . Avmarkera den här kryss rutan om du vill ta bort test enheten från erbjudandet.

Läs mer i artikeln [Vad är Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Följande typer av test enheter är tillgängliga, var och en med sina egna tekniska konfigurations krav:

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logic app](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (teknisk konfiguration krävs inte)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Teknisk konfiguration för Azure Resource Manager testen het

En distributionsmall som innehåller alla Azure-resurser som utgör din lösning. Produkter som passar det här scenariot använder bara Azure-resurser. Läs mer om hur du konfigurerar en [Azure Resource Manager test-enhet](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regioner** (obligatoriskt) – för närvarande finns det 26 Azure-regioner som stöds där din test enhet kan göras tillgänglig. Vanligt vis vill du göra din test enhet tillgänglig i de regioner där du räknar med det största antalet kunder, så att de kan välja den region som är närmast för bästa prestanda. Du måste kontrol lera att din prenumeration har tillåtelse att distribuera alla resurser som behövs i varje region som du väljer.
- **Instanser** – Välj typ (frekvent eller kall) och antal tillgängliga instanser, vilket kommer att multipliceras med antalet regioner där ditt erbjudande är tillgängligt.

    Frekvent **– den** här typen av instans distribueras och väntar på åtkomst per vald region. Kunder kan komma åt en test enhets *aktiva* instanser direkt i stället för att vänta på en distribution. Kompromissen är att dessa instanser alltid körs på din Azure-prenumeration, så de kommer att ådra sig en högre drift tid. Vi rekommenderar starkt att du har minst en *aktiv* instans, eftersom de flesta kunder inte vill vänta på fullständiga distributioner, vilket resulterar i en kombination av kund användning om ingen *aktiv* instans är tillgänglig.

    **Kall** – den här typen av instans representerar det totala antalet instanser som eventuellt kan distribueras per region. Kalla instanser kräver att hela test enhetens Resource Manager-mall distribueras när en kund begär test enheten, så att *kalla* instanser är mycket långsammare att läsa in än *varma* instanser. Kompromissen är att du bara behöver betala under test enhetens varaktighet, den körs *inte* alltid på din Azure-prenumeration som en *aktiv* instans.

- **Testa enhet Azure Resource Manager mall** – Ladda upp zip-filen som innehåller din Azure Resource Manager-mall. Lär dig mer om hur du skapar en Azure Resource Manager-mall i snabb starts artikeln [skapa och distribuera Azure Resource Manager mallar med hjälp av Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Test enhetens varaktighet** (krävs) – ange antalet timmar som test enheten ska vara aktiv. TestdDrive avslutas automatiskt när den här tids perioden är slut. Denna varaktighet får bara anges i hela timmar (till exempel "2" timmar är giltig; "1,5" är inte).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Teknisk konfiguration för Dynamics 365-testenhet

Microsoft kan ta bort komplexiteten för att konfigurera en testen het genom att vara värd för och underhålla tjänst etableringen och-distributionen med den här typen av test enhet. Konfigurationen av den här typen av värdbaserad test enhet är samma oavsett om test enheten är riktad mot en affärs Central, kund engagemang eller drifts grupp.

- **Maximalt antal samtidiga test enheter** (krävs) – Ange det maximala antalet kunder som kan använda test enheten samtidigt. Varje samtidig användare kommer att använda en Dynamics 365-licens medan test enheten är aktiv, så du måste se till att du har tillräckligt med licenser för att stödja den maximala gränsen. Det rekommenderade värdet är 3-5.

- **Test enhetens varaktighet** (krävs) – ange antalet timmar som test enheten ska vara aktiv. Efter den här tiden upphör sessionen och använder inte längre någon av dina licenser. Vi rekommenderar ett värde på 2-24 timmar beroende på hur komplex ditt erbjudande är. Denna varaktighet får bara anges i hela timmar (till exempel "2" timmar är giltig; "1,5" är inte). Användaren kan begära en ny session om de får slut på tid och vill komma åt test enheten igen.

- **Instans-URL** (obligatorisk) – URL: en där kunden kommer att påbörja sin test-enhet. Normalt är URL: en för din Dynamics 365-instans som kör appen med exempel data installerat (till exempel `https://testdrive.crm.dynamics.com` ).

- **URL för instans webb-API** (krävs) – Hämta URL: en för webb-API för din Dynamics 365-instans genom att logga in på ditt Microsoft 365-konto och navigera till **Inställningar** \& gt; **Anpassning** \& gt **Resurser** \& för utvecklare gt **Instans webb-API (tjänstens rot-URL)**, kopiera URL-adressen som finns här (till exempel `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- **Rollnamn** (obligatoriskt) – Ange namnet på den säkerhets roll som du har definierat i din anpassade Dynamics 365-testenhet. Namnet på säkerhets rollen tilldelas användaren under deras test enhet (till exempel Test-Drive-roll).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Teknisk konfiguration för Logic app-testenhet

Alla anpassade produkter bör använda den här typen av mall för distribution av test enheter, som omfattar flera olika komplexa lösnings arkitekturer. Mer information om hur du konfigurerar Logi Kap par test enheter finns i [åtgärder](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) och [kund engagemang](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) på GitHub.

- **Region** (obligatorisk listruta för enskild markering) – för närvarande finns det 26 Azure-regioner som stöds där din test enhet kan göras tillgänglig. Resurserna för din Logic-app distribueras i den region som du väljer. Om din Logi Kap par innehåller anpassade resurser som lagras i en viss region, se till att regionen är vald här. Det bästa sättet att göra det är att helt distribuera din Logic app lokalt på din Azure-prenumeration i portalen och kontrol lera att den fungerar korrekt innan du gör det här valet.

- **Maximalt antal samtidiga test enheter** (krävs) – Ange det maximala antalet kunder som kan använda test enheten samtidigt. De här test enheterna har redan distribuerats, vilket gör det möjligt för kunder att komma åt dem direkt utan att vänta på en distribution.

- **Test enhetens varaktighet** (krävs) – ange hur lång tid test enheten ska vara aktiv, i antal timmar. Test enheten avslutas automatiskt när den här tids perioden är slut.

- **Namn på Azure-resurs grupp** (obligatoriskt) – Ange namnet på den [Azure-resurs grupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) där din Logic app-testenhet sparas.

- **Azure Logic app-namn** (obligatoriskt) – Ange namnet på den Logic-app som tilldelar användaren till användaren. Den här Logic-appen måste sparas i gruppen Azure-resurser ovan.

- **Avetablera Logic app-namn** (obligatoriskt) – Ange namnet på den Logic-app som avetablerar test enheten när kunden är färdig. Den här Logic-appen måste sparas i gruppen Azure-resurser ovan.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Teknisk konfiguration krävs inte för Power BI test enheter

Produkter som vill demonstrera ett interaktivt Power BI visuellt objekt kan använda en inbäddad länk för att dela en anpassad instrument panel som test enhet, ingen ytterligare teknisk konfiguration krävs. Läs mer om hur du konfigurerar[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) mallar.

### <a name="deployment-subscription-details"></a>Information om distributions prenumeration

Om du vill att Microsoft ska kunna distribuera test enheten åt dig skapar du och tillhandahåller en separat, unik Azure-prenumeration (krävs inte för Power BI test enheter).

- **ID för Azure-prenumeration** (krävs för Azure Resource Manager-och logi Kap par) – ange prenumerations-ID för att bevilja åtkomst till dina Azure-Kontotjänster för rapportering och fakturering av resursanvändning. Vi rekommenderar att du [skapar en separat Azure-prenumeration](https://docs.microsoft.com/azure/billing/billing-create-subscription) som ska användas för test enheter om du inte redan har en. Du hittar ditt prenumerations-ID för Azure genom att logga in på [Azure Portal](https://portal.azure.com/) och navigera till fliken **prenumerationer** på den vänstra menyn. Om du väljer fliken visas ditt prenumerations-ID (till exempel "a83645ac-1234-5AB6-6789-1h234g764ghty").

- **Azure AD-klient-ID** (krävs) – ange ditt Azure Active Directory (AD) [klient organisations-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Om du vill hitta detta ID loggar du in på [Azure Portal](https://portal.azure.com/), väljer fliken Active Directory på den vänstra menyn, väljer **Egenskaper**och letar efter **katalog-ID** -nummer som anges (till exempel 50c464d3-4930-494c-963c-1e951d15360e). Du kan också leta upp din organisations klient-ID med hjälp av din domän namn-URL på – [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

- **Namn på Azure AD-klient** (krävs för dynamisk 365) – ange ditt Azure Active Directory (AD) namn. Du hittar det här namnet genom att logga in på [Azure Portal](https://portal.azure.com/)i det övre högra hörnet ditt klient namn visas under ditt konto namn.

- **ID för Azure AD-App** (krävs) – ange ditt Azure Active Directory (AD) [program-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Om du vill hitta detta ID loggar du in på [Azure Portal](https://portal.azure.com/), väljer fliken Active Directory på den vänstra menyn, väljer **Appregistreringar**och letar sedan efter det **program-ID-** nummer som anges (till exempel 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-appens klient hemlighet** (krävs) – ange [klient hemligheten](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)för Azure AD-programmet. Du hittar det här värdet genom att logga in på [Azure Portal](https://portal.azure.com/). Välj fliken **Azure Active Directory** på den vänstra menyn, Välj **Appregistreringar**och välj sedan appen Test Drive. Välj sedan **certifikat och hemligheter**, Välj **ny klient hemlighet**, ange en beskrivning, Välj **aldrig** under **upphör ande**och välj sedan **Lägg till**. Glöm inte att kopiera värdet. Du behöver inte gå från sidan innan du kopierar värdet eller så har du inte åtkomst till värdet.)

### <a name="test-drive-marketplace-listings"></a>Marketplace-platser för enhets kontroll

Alternativet **Marketplace-lista** på fliken **testenhet** visar de språk som test enheten är tillgänglig på. För närvarande är **engelska (USA)** den enda platsen som är tillgänglig. Välj språk namnet för att ange information som beskriver test enhets upplevelsen.

- **Beskrivning** (krävs) – Beskriv din test enhet, vad som visas, mål för användaren att experimentera med, vilka funktioner som ska utforskas och all relevant information som hjälper användaren att avgöra om ditt erbjudande ska erhållas. Upp till 3 000 tecken text kan anges i det här fältet. 

- **Åtkomst information** (krävs för Azure Resource Manager och logik test enheter) – förklara vad en kund behöver veta för att få åtkomst till och använda den här test enheten. Gå igenom ett scenario för att använda ditt erbjudande och exakt vad kunden bör känna till för att få åtkomst till funktioner i hela test enheten. Upp till 10 000 tecken text kan anges i det här fältet.

- **Användar handbok** (krävs) – en djupgående genom gång av test enhets upplevelsen. Användar handboken bör bestå av exakt vad du vill att kunden ska få från att använda test enheten och fungera som referens för alla frågor som de kan ha. Filen måste vara i PDF-format och ha namnet (högst 255 tecken) efter överföringen.

- **Videor** (valfritt) – Ladda upp videor till en extern värd plats referens till länken och miniatyr bilden (533 x 324 pixlar) här. Detta gör att kunden kan visa en genom gång av information för att hjälpa dem att bättre förstå test enheten, inklusive hur du kan använda funktionerna i ditt erbjudande och förstå scenarier som fokuserar på deras fördelar.
  - **Namn** (obligatoriskt)
  - **Adress** (obligatoriskt, YouTube eller endast Vimeo)
  - **Miniatyr** bild (bild filen måste vara i PNG-format och 533 x 324 bild punkter)

Välj **Spara utkast** innan du fortsätter.

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
- Skicka in erbjudandet om publicering genom att välja **Skicka**. Vi skickar dig ett e-postmeddelande för att meddela dig när en för hands version av erbjudandet kan granskas och godkännas. Gå tillbaka till Partner Center och välj **Go-Live** för erbjudandet att publicera till allmänheten (eller om ett privat erbjudande till den privata mål gruppen).

## <a name="next-step"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Commercial Marketplace](./update-existing-offer.md)
