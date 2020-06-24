---
title: Skapa en Dynamics 365 för drift erbjudande på den kommersiella Marketplace
description: Så här skapar du en ny Dynamics 365 för åtgärder för att visa eller sälja på Azure Marketplace, AppSource eller via program varan för Cloud Solution Provider (CSP) med hjälp av den kommersiella Marketplace på Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: badad6da0c0e244cbdb30e4b89955dd073782f04
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84752558"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Skapa ett erbjudande för Dynamics 365 for Operations

I det här avsnittet beskrivs hur du skapar en ny Dynamics 365 för Operations-erbjudandet. [Microsoft Dynamics 365 for ekonomi och Operations](https://dynamics.microsoft.com/finance-and-operations) är en tjänst för företags resurs planering (ERP) som stöder avancerad finans-, drift-, tillverknings-och leverans kedjas hantering. Alla erbjudanden för Dynamics 365 för åtgärder måste gå igenom vår certifierings process.

Innan du börjar [skapar du ett kommersiellt marknads plats konto i Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , om du inte har gjort det än. Se till att ditt konto är registrerat i programmet för kommersiella marknads platser.

>[!NOTE]
> När ett erbjudande har publicerats uppdateras bara ändringar i erbjudandet som gjorts i Partner Center i systemet och Store-frontend efter ompubliceringen. Se till att du skickar in erbjudandet för publicering när du har gjort ändringar.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. På menyn till vänster-navigerings väljer du **kommersiell Marketplace**-  >  **Översikt**.
3. På sidan Översikt väljer du **+ nytt erbjudande**  >  **Dynamics 365 för åtgärder**.

    ![Visar menyn till vänster-navigering.](./media/new-offer-dynamics-365-operations.png)

> [!NOTE]
> När ett erbjudande har publicerats visas bara ändringar som gjorts i Partner Center i butiker efter publiceringen av erbjudandet. Se till att du alltid publicerar igen när du har gjort ändringar.

## <a name="new-offer"></a>Nytt erbjudande

Ange ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto.

- Detta ID är synligt för kunder i webb adressen för Marketplace-erbjudandet och Azure Resource Manager mallar, om tillämpligt.
- Använd bara gemena bokstäver och siffror. Det kan innehålla bindestreck och under streck, men inte blank steg, och är begränsat till 50 tecken. Om du till exempel anger **test-erbjudandet-1** här, är webb adressen för erbjudandet `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Erbjudande-ID: t kan inte ändras när du har valt **skapa**.

Ange ett **erbjudande alias**. Detta är det namn som används för erbjudandet i Partner Center.

- Det här namnet används inte på Marketplace och skiljer sig från namnet på erbjudandet och andra värden som visas för kunderna.

Välj **skapa** för att generera erbjudandet och fortsätt.

## <a name="offer-setup"></a>Erbjudande konfiguration

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Hur vill du att potentiella kunder ska kunna interagera med det här registrerings erbjudandet?

Välj det alternativ som du vill använda för det här erbjudandet.

#### <a name="get-it-now-free"></a>Hämta nu (kostnads fritt)

Lista ditt erbjudande till kunder kostnads fritt genom att tillhandahålla en giltig URL (från och med *http* eller *https*) där de kan komma åt din app.  Till exempel, `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Kostnads fri utvärderings version (lista)

Lista ditt erbjudande till kunder med en länk till en kostnads fri utvärderings version genom att tillhandahålla en giltig URL (från `http` och med eller `https` ) där de kan få en utvärderings version. Exempelvis `https://contoso.com/trial/my-app`. Erbjudande om kostnads fria utvärderings versioner skapas, hanteras och konfigureras av din tjänst och har inga prenumerationer som hanteras av Microsoft.

> [!NOTE]
> De token som programmet tar emot via din utvärderings länk kan bara användas för att hämta användar information via Azure Active Directory (Azure AD) för att automatisera skapandet av konton i din app. Microsoft-konton stöds inte för autentisering med denna token.

#### <a name="contact-me"></a>Kontakta mig

Samla in kund kontakt information genom att ansluta ditt CRM-system (Customer Relations hip Management). Kunden uppmanas att ange behörighet för att dela sin information. Dessa kund uppgifter, tillsammans med erbjudande namnet, ID: t och Marketplace-källan där de hittade ditt erbjudande, skickas till det CRM-system som du har konfigurerat. Mer information om hur du konfigurerar din CRM finns i [kund leads](#customer-leads).

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

- [Metodtips för marknadsföring](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Rekommenderade tekniska metoder](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Översikt](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF, se till att blockering av popup-fönster är inaktiverat)

### <a name="customer-leads"></a>Kund ledare

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Mer information finns i [Översikt över hantering av leads](./commercial-marketplace-get-customer-leads.md).

Välj **Spara utkast** innan du fortsätter.

## <a name="properties"></a>Egenskaper

På den här sidan kan du definiera de kategorier och branscher som ska användas för att gruppera ditt erbjudande på Marketplace, din app-version och de juridiska avtal som stöder ditt erbjudande.

### <a name="category"></a>Kategori

Välj minst en och högst tre kategorier. Dessa kategorier kommer att användas för att placera ditt erbjudande i lämpliga sökområden för Marketplace. Var noga med att ta reda på hur ditt erbjudande stöder dessa kategorier i beskrivningen av erbjudandet.

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

### <a name="name"></a>Name

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

I det här avsnittet anger du namn, e-postadress och telefonnummer för en **support kontakt** och en **teknisk kontakt**. Den här informationen visas inte för kunder, men är tillgänglig för Microsoft och kan tillhandahållas till CSP-partner.

I avsnittet **support kontakt** anger du support- **URL: en** där CSP-partner kan hitta support för ditt erbjudande.

### <a name="supporting-documents"></a>Stöd dokument

Ange minst ett (och upp till tre) relaterade marknadsförings dokument här, till exempel fakta blad, broschyrer, check listor eller presentationer. Dessa dokument måste vara i PDF-format.

### <a name="marketplace-images"></a>Marketplace-avbildningar

I det här avsnittet kan du ange logo typer och avbildningar som ska användas när du visar ditt erbjudande för kunden. Alla bilder måste vara i PNG-format.

>[!Note]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar tjänsten som https://upload.xboxlive.com används av Partner Center.

#### <a name="store-logos"></a>Butiks logo typer

Ange ditt erbjudandes logo typ i två pixel storlekar:

- **Liten** (48 x 48)
- **Stor** (216 x 216)

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

På den här sidan får du alternativ om var och hur du gör ditt erbjudande tillgängligt.

### <a name="markets"></a>Marknaden

I det här avsnittet kan du ange vilka marknader som ditt erbjudande ska vara tillgängligt för. Det gör du genom att välja **Redigera marknader,** så visas popup-fönstret för **marknads val** .

Som standard är inga marknader markerade. Välj minst en marknad för att publicera ditt erbjudande. Klicka på **Välj alla** för att göra ditt erbjudande tillgängligt på alla möjliga marknader eller Välj de olika marknader som du vill lägga till. När du är klar väljer du **Spara**.

Dina val gäller endast för nya förvärv. om någon redan har din app på en viss marknad och du senare tar bort marknaden, kan de personer som redan har erbjudandet på marknaden fortsätta att använda den, men inga nya kunder på den marknaden kommer att kunna få ditt erbjudande.

> [!IMPORTANT]
> Det är ditt ansvar att uppfylla alla lokala juridiska krav, även om dessa krav inte finns med i listan här eller i Partner Center.

Tänk på att även om du väljer alla marknader kan lokala lagar och restriktioner eller andra faktorer förhindra att vissa erbjudanden visas i vissa länder och regioner.

### <a name="preview-audience"></a>Förhandsgranska mål grupp

Innan du publicerar erbjudandet Live på det bredare Marketplace-erbjudandet måste du först göra det tillgängligt för en begränsad **förhands gransknings publik**. Ange en **Dölj nyckel** (valfri sträng med endast gemener och/eller siffror) här. Medlemmar i din förhands visnings publik kan använda denna Dölj-nyckel som en token för att visa en förhands granskning av ditt erbjudande i Marketplace.

När du är redo att göra ditt erbjudande tillgängligt och ta bort begränsningen för för hands versionen måste du ta bort **Dölj-tangenten** och publicera igen.

Välj **Spara utkast** innan du fortsätter.

## <a name="technical-configuration"></a>Teknisk konfiguration

Den här sidan definierar de tekniska uppgifter som används för att ansluta till ditt erbjudande. Med den här anslutningen kan vi tillhandahålla ditt erbjudande för slutanvändaren om de väljer att förvärva det.

### <a name="solution-identifier"></a>Lösnings identifierare

Ange lösnings identifieraren (GUID) för din lösning.

Hitta lösnings-ID:
1. I Microsoft Dynamics Lifecycle Services (LCS) väljer du **lösnings hantering**.
2. Välj din lösning och leta efter **lösnings identifieraren** i **paket översikten**. Om identifieraren är tom väljer du **Redigera** och publicera om ditt paket och försöker igen.

### <a name="release-version"></a>Version

Välj den version av Dynamics 365 för ekonomi och åtgärder som den här lösningen fungerar med.

Välj **Spara utkast** innan du fortsätter.

## <a name="test-drive-technical-configuration"></a>Teknisk konfiguration för test enhet

På den här sidan kan du konfigurera en demonstration ("testenhet") som gör det möjligt för kunder att prova ditt erbjudande innan de köper det. Läs mer i artikeln [Vad är Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Om du vill aktivera en testen het markerar du kryss rutan **Aktivera en testenhet** på fliken [erbjudande konfiguration](#test-drive) . Avmarkera den här kryss rutan om du vill ta bort test enheten från erbjudandet.

Följande typer av test enheter är tillgängliga, var och en med sina egna tekniska konfigurations krav.

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

- **Testa enhet Azure Resource Manager mall** – Ladda upp zip-filen som innehåller din Azure Resource Manager-mall.  Lär dig mer om hur du skapar en Azure Resource Manager-mall i snabb starts artikeln [skapa och distribuera Azure Resource Manager mallar med hjälp av Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Test enhetens varaktighet** (krävs) – ange hur lång tid test enheten ska vara aktiv, i antal timmar. Test enheten avslutas automatiskt när den här tids perioden är slut. Denna varaktighet får bara anges med ett heltal (till exempel "2" timmar; "1,5" är inte giltig).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Teknisk konfiguration för Dynamics 365-testenhet

Microsoft kan ta bort komplexiteten för att konfigurera en testen het genom att vara värd för och underhålla tjänst etableringen och-distributionen med den här typen av test enhet. Konfigurationen av den här typen av värdbaserad test enhet är samma oavsett om test enheten är riktad mot en affärs Central, kund engagemang eller drifts grupp.

- **Maximalt antal samtidiga test enheter** (krävs) – Ange det maximala antalet kunder som kan använda test enheten samtidigt. Varje samtidig användare använder en Dynamics 365-licens medan test enheten är aktiv, så du måste se till att du har tillräckligt med licenser för att stödja Max gränsen. Rekommenderat värde 3-5.

- **Test enhetens varaktighet** (krävs) – ange hur lång tid test enheten ska vara aktiv genom att definiera antalet timmar. Efter så här många timmar avslutas sessionen och använder inte längre någon av dina licenser. Vi rekommenderar ett värde på 2-24 timmar beroende på hur komplex ditt erbjudande är. Denna varaktighet får bara anges med ett heltal (till exempel "2" timmar; "1,5" är inte giltig).  Användaren kan begära en ny session om de får slut på tid och vill komma åt test enheten igen.

- **Instans-URL** (obligatorisk) – URL: en där kunden kommer att påbörja sin test-enhet. Normalt är URL: en för din Dynamics 365-instans som kör appen med exempel data installerat (till exempel `https://testdrive.crm.dynamics.com` ).

- **URL för instans webb-API** (krävs) – Hämta URL: en för webb-API för din Dynamics 365-instans genom att logga in på ditt Microsoft 365-konto och navigera till **Inställningar**  >  **anpassning**av  >  **utvecklare resurser**  >  **instans webb-API (tjänstens rot-URL)**, kopiera webb adressen som finns här (till exempel `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- **Rollnamn** (obligatoriskt) – Ange namnet på den säkerhets roll som du har definierat i din anpassade Dynamics 365-testenhet. Detta kommer att tilldelas användaren under deras test enhet (till exempel Test-Drive-roll).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Teknisk konfiguration för Logic app-testenhet

Alla anpassade produkter bör använda den här typen av mall för distribution av test enheter, som omfattar flera olika komplexa lösnings arkitekturer. Mer information om hur du konfigurerar Logi Kap par test enheter finns i [åtgärder](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) och [kund engagemang](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) på GitHub.

- **Region** (obligatorisk listruta för enskild markering) – för närvarande finns det 26 Azure-regioner som stöds där din test enhet kan göras tillgänglig. Resurserna för din Logic-app distribueras i den region som du väljer. Om din Logi Kap par innehåller anpassade resurser som lagras i en viss region, se till att regionen är vald här. Det bästa sättet att se till att du har anpassade resurser för din region är att fullständigt distribuera din Logic app lokalt på din Azure-prenumeration i portalen och kontrol lera att den fungerar korrekt innan du gör det här valet.

- **Maximalt antal samtidiga test enheter** (krävs) – Ange det maximala antalet kunder som kan använda test enheten samtidigt. De här test enheterna har redan distribuerats, vilket gör det möjligt för kunder att komma åt dem direkt utan att vänta på en distribution.

- **Test enhetens varaktighet** (krävs) – ange hur lång tid test enheten ska vara aktiv, i antal timmar. Test enheten avslutas automatiskt när den här tids perioden är slut.

- **Namn på Azure-resurs grupp** (obligatoriskt) – Ange namnet på den [Azure-resurs grupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) där din Logic app-testenhet sparas.

- **Azure Logic app-namn** (obligatoriskt) – Ange namnet på den Logic-app som tilldelar användaren till användaren. Den här Logic-appen måste sparas i gruppen Azure-resurser ovan.

- **Avetablera Logic app-namn** (obligatoriskt) – Ange namnet på den Logic-app som avetablerar test enheten när kunden är färdig. Den här Logic-appen måste sparas i gruppen Azure-resurser ovan.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Teknisk konfiguration krävs inte för Power BI test enheter

Produkter som vill demonstrera ett interaktivt Power BI visuellt objekt kan använda en inbäddad länk för att dela en anpassad instrument panel som test enhet, ingen ytterligare teknisk konfiguration krävs. Läs mer om hur du konfigurerar[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) mallar.

### <a name="deployment-subscription-details"></a>Information om distributions prenumeration

För att distribuera test enheten åt dig skapar du och tillhandahåller en separat, unik Azure-prenumeration. (Krävs inte för Power BI test enheter).

- **ID för Azure-prenumeration** (krävs för Azure Resource Manager-och logi Kap par) – ange prenumerations-ID för att bevilja åtkomst till dina Azure-Kontotjänster för rapportering och fakturering av resursanvändning. Vi rekommenderar att du [skapar en separat Azure-prenumeration](https://docs.microsoft.com/azure/billing/billing-create-subscription) som ska användas för test enheter om du inte redan har en. Du hittar ditt prenumerations-ID för Azure genom att logga in på [Azure Portal](https://portal.azure.com/) och navigera till fliken **prenumerationer** på den vänstra menyn. Om du väljer fliken visas ditt prenumerations-ID (till exempel "a83645ac-1234-5AB6-6789-1h234g764ghty").

- **Azure AD-klient-ID** (krävs) – ange ditt Azure Active Directory (AD) [klient organisations-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Om du vill hitta detta ID loggar du in på [Azure Portal](https://portal.azure.com/), väljer fliken Active Directory på den vänstra menyn, väljer **Egenskaper**och letar efter **katalog-ID** -nummer som anges (till exempel 50c464d3-4930-494c-963c-1e951d15360e). Du kan också leta upp organisationens klient-ID med din domän namns adress på [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

- **Namn på Azure AD-klient** (krävs för dynamisk 365) – ange ditt Azure Active Directory (AD) namn. Du hittar det här namnet genom att logga in på [Azure Portal](https://portal.azure.com/)i det övre högra hörnet ditt klient namn visas under ditt konto namn.

- **ID för Azure AD-App** (krävs) – ange ditt Azure Active Directory (AD) [program-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Om du vill hitta detta ID loggar du in på [Azure Portal](https://portal.azure.com/), väljer fliken Active Directory på den vänstra menyn, väljer **Appregistreringar**och letar sedan efter det **program-ID-** nummer som anges (till exempel 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-appens klient hemlighet** (krävs) – ange [klient hemligheten](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)för Azure AD-programmet. Du hittar det här värdet genom att logga in på [Azure Portal](https://portal.azure.com/). Välj fliken **Azure Active Directory** på den vänstra menyn, Välj **Appregistreringar**och välj sedan appen Test Drive. Välj sedan **certifikat och hemligheter**, Välj **ny klient hemlighet**, ange en beskrivning, Välj **aldrig** under **upphör ande**och välj sedan **Lägg till**. Glöm inte att kopiera värdet. (Du kan inte navigera bort från sidan innan du noterar värdet eller så har du inte åtkomst till värdet.)

Välj **Spara utkast** innan du fortsätter.

### <a name="test-drive-marketplace-listings"></a>Marketplace-platser för enhets kontroll

Alternativet **Marketplace-lista** på fliken **testenhet** visar de språk som test enheten är tillgänglig på. För närvarande är **engelska (USA)** den enda platsen som är tillgänglig. Välj språk namnet för att ange information som beskriver test enhets upplevelsen.

- **Beskrivning** (krävs) – Beskriv din test enhet, vad som visas, mål för användaren att experimentera med, vilka funktioner som ska utforskas och all relevant information som hjälper användaren att avgöra om ditt erbjudande ska erhållas. Upp till 3 000 tecken text kan anges i det här fältet.

- **Åtkomst information** (krävs för Azure Resource Manager och logik test enheter) – förklara vad en kund behöver veta för att få åtkomst till och använda den här test enheten. Gå igenom ett scenario för att använda ditt erbjudande och exakt vad kunden bör känna till för att få åtkomst till funktioner i hela test enheten. Upp till 10 000 tecken text kan anges i det här fältet.

- **Användar handbok** (krävs) – en djupgående genom gång av test enhets upplevelsen. Användar handboken bör bestå av exakt vad du vill att kunden ska få från att använda test enheten och fungera som referens för alla frågor som de kan ha. Filen måste vara i PDF-format och ha namnet (högst 255 tecken) efter överföringen.

- **Videor** (valfritt) – videor kan laddas upp till YouTube eller Vimeo och refereras till med en länk och miniatyr bild (533 x 324 pixlar) så att en kund kan visa en genom gång av information för att hjälpa dem att bättre förstå test enheten, inklusive hur du kan använda funktionerna i ditt erbjudande och förstå scenarier som fokuserar på deras fördelar.
  - **Namn** (obligatoriskt)
  - **URL** (krävs; YouTube eller endast Vimeo)
  - **Miniatyr** bild (bilden måste vara i PNG-format och 533 x 324 bild punkter)

Välj **Spara utkast** innan du fortsätter.

## <a name="supplemental-content"></a>Kompletterande innehåll

På den här sidan kan du ange ytterligare information om ditt erbjudande för att hjälpa oss att validera ditt erbjudande. Den här informationen visas inte för kunder eller publiceras på Marketplace.

### <a name="validation-assets"></a>Validerings till gångar

Ladda upp en [anpassnings analys rapport (Car)](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report) i det här avsnittet. Den här rapporten genereras genom att analysera anpassnings-och tilläggs modeller baserat på en fördefinierad uppsättning regler för bästa praxis.

Den här filen måste vara i formatet. xls eller. xlsx. Om du har mer än en rapport kan du ladda upp en zip-fil som innehåller alla rapporter.

### <a name="does-solution-include-localizations"></a>Innehåller lösningen lokaliseringar?

Välj **Ja** om lösningen aktiverar användning av lokala standarder och principer (till exempel om den hanterar de olika löne regler som krävs av olika länder/regioner). Annars väljer du **Nej**.

### <a name="does-solution-enable-translations"></a>Aktiverar lösningen översättningar?

Svara **Ja** om texten i lösningen kan översättas till andra språk. Annars väljer du **Nej**.

Välj **Spara utkast** innan du fortsätter.

## <a name="publish"></a>Publicera

### <a name="submit-offer-to-preview"></a>Skicka erbjudande till för hands version

När du har slutfört alla obligatoriska avsnitt i erbjudandet väljer du **Granska och publicera** i det övre högra hörnet i portalen.

Om det är första gången du publicerar det här erbjudandet kan du:

- Se slut för ande status för varje avsnitt i erbjudandet.
    - **Inte startat** – avsnittet har inte vidrör ATS och bör slutföras.
    - **Ofullständig** – avsnittet innehåller fel som behöver åtgärdas eller som kräver mer information. Gå tillbaka till avsnitten och uppdatera det.
    - **Slutfört** – avsnittet är slutfört, alla data som krävs har angetts och det finns inga fel. Alla avsnitt i erbjudandet måste vara i ett komplett tillstånd innan du kan skicka in erbjudandet.
- I avsnittet **Obs!-certifiering** anger du test instruktioner till certifierings teamet för att kontrol lera att appen är korrekt testad, förutom eventuella kompletterande anteckningar som hjälper dig att förstå din app.
- Skicka in erbjudandet om publicering genom att välja **Skicka**. Vi skickar dig ett e-postmeddelande för att meddela dig när en för hands version av erbjudandet kan granskas och godkännas. Gå tillbaka till Partner Center och välj **Go-Live** för erbjudandet att publicera ditt erbjudande till allmänheten (eller om ett privat erbjudande till den privata mål gruppen).

## <a name="next-step"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Commercial Marketplace](./update-existing-offer.md)
