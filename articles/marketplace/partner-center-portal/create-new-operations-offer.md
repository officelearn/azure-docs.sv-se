---
title: Skapa en ny Dynamics 365 för drift erbjudande på den kommersiella Marketplace
description: Så här skapar du en ny Dynamics 365 för åtgärder för att visa eller sälja på Azure Marketplace, AppSource eller via program varan för Cloud Solution Provider (CSP) med hjälp av den kommersiella Marketplace på Microsoft Partner Center.
author: JnHs
manager: evansma
ms.author: jenhayes
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 84738d9de880e09177ebb5c060fbd7bbd4613006
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036818"
---
# <a name="create-a-new-dynamics-365-for-operations-offer"></a>Skapa en ny Dynamics 365 for Operations-erbjudande

I det här avsnittet beskrivs hur du skapar en ny Dynamics 365 för Operations-erbjudandet. [Microsoft Dynamics 365 for ekonomi och Operations](https://dynamics.microsoft.com/finance-and-operations) är en tjänst för företags resurs planering (ERP) som stöder avancerad finans-, drift-, tillverknings-och leverans kedjas hantering. Alla erbjudanden för Dynamics 365 för åtgärder måste gå igenom vår certifierings process.

För att börja skapa Dynamics 365 för drifts erbjudanden måste du först [skapa ett partner Center-konto](./create-account.md) och öppna [instrument panelen för instrument panelen för instrument panelen](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)med **översikts** sidan vald.

![Instrument panel för extern Marketplace på Partner Center](./media/new-offer-overview.png)

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

Välj knappen **+ ny erbjudande** och välj sedan meny alternativet **Dynamics 365 för åtgärder** . Dialog rutan **nytt erbjudande** visas.

### <a name="offer-id-and-alias"></a>Erbjudande-ID och alias

- **Erbjudande-ID**: Unik identifierare för varje erbjudande i ditt konto. Detta ID visas för kunder i URL-adressen för Marketplace-erbjudandet och Azure Resource Manager mallar (om tillämpligt). Erbjudande-ID: t måste bestå av gemena alfanumeriska tecken (inklusive bindestreck och under streck, men inget blank steg). Detta är begränsat till 50 tecken och kan inte ändras när du har valt **skapa**.  Om du till exempel anger *test-erbjudandet-1* här, är erbjudande-URL: en `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

- **Erbjud alias**: Namnet som används för att referera till erbjudandet i Partner Center. Det här namnet används inte på Marketplace och skiljer sig från namnet på erbjudandet och andra värden som ska visas för kunderna. Det går inte att ändra det här värdet när du har valt **skapa**.

När du har angett ditt **erbjudande-ID** och **erbjud alias**väljer du **skapa**. Du kommer sedan att kunna arbeta med alla olika delar av ditt erbjudande.

## <a name="offer-setup"></a>Erbjudande konfiguration

På sidan **erbjudande konfiguration** ber vi om följande information. Se till att välja **Spara** när du är klar med fälten.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Hur vill du att potentiella kunder ska kunna interagera med det här registrerings erbjudandet?

Välj det alternativ som du vill använda för det här erbjudandet.

#### <a name="get-it-now-free"></a>Hämta nu (kostnads fritt)

Lista ditt erbjudande till kunder kostnads fritt genom att tillhandahålla en giltig URL (från och med *http* eller *https*) där de kan komma åt din app.  Exempel: `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Kostnads fri utvärderings version (lista)

Lista ditt erbjudande till kunder med en länk till en kostnads fri utvärderings version genom att tillhandahålla en giltig URL (från och med *http* eller *https*) där de kan få en utvärderings version.  Till exempel: `https://contoso.com/trial/my-app`. Erbjudande om kostnads fria utvärderings versioner skapas, hanteras och konfigureras av din tjänst och har inga prenumerationer som hanteras av Microsoft.

> [!NOTE]
> De token som programmet tar emot via din utvärderings länk kan bara användas för att hämta användar information via Azure Active Directory (Azure AD) för att automatisera skapandet av konton i din app. Microsoft-konton stöds inte för autentisering med denna token.

#### <a name="contact-me"></a>Kontakta mig

Samla in kund kontakt information genom att ansluta ditt CRM-system (Customer Relations hip Management). Kunden uppmanas att ange behörighet för att dela sin information. Dessa kund uppgifter, tillsammans med erbjudande namnet, ID: t och Marketplace-källan där de hittade ditt erbjudande, skickas till det CRM-system som du har konfigurerat. Mer information om hur du konfigurerar din CRM finns i [Anslut lead management](#connect-lead-management). 

### <a name="test-drive"></a>Testkör

En testen het är ett bra sätt att presentera ditt erbjudande för potentiella kunder genom att ge dem möjlighet att "prova innan du köper", vilket resulterar i ökad konvertering och skapandet av mycket kvalificerade leads. [Läs mer om test enheter.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Markera kryss rutan **Aktivera en testenhet** om du vill aktivera en test enhet. Sedan måste du konfigurera en demonstrations miljö i den [tekniska konfigurationen för test enheten](#test-drive-technical-configuration) konfigurera så att kunderna kan testa ditt erbjudande under en bestämd tids period. 

#### <a name="type-of-test-drive"></a>Typ av test enhet

Välj bland följande alternativ:

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : En distributionsmall som innehåller alla Azure-resurser som utgör din lösning. Produkter som passar det här scenariot använder bara Azure-resurser.
- **[Dynamics 365 för företag, Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Microsoft är värd för och underhåller Test Drive-tjänsten (inklusive etablering och distribution) för ett företags organisations planerings system för företag (finans, drift, leverans kedja, CRM osv.).  
- **[Dynamics 365 för kund engagemang](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Microsoft är värd för och underhåller Test Drive-tjänsten (inklusive etablering och distribution) för ett kund engagemang system (Sales, service, Project service, Field service osv.).  
- **[Dynamics 365 för åtgärder](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Microsoft är värd för och underhåller Test Drive-tjänsten (inklusive etablering och distribution) för ett ekonomi-och drift företags resurs planerings system (finans, drift, tillverkning, leverans kedja osv.). 
- **[Logic app](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : En distributions mall som omfattar alla komplexa lösnings arkitekturer. Alla anpassade produkter bör använda den här typen av test enhet.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : En inbäddad länk till en anpassad instrument panel. Produkter som vill demonstrera ett interaktivt Power BI visuellt objekt bör använda den här typen av test enhet. Allt du behöver ladda upp här är din inbäddade Power BI-URL.

#### <a name="additional-test-drive-resources"></a>Ytterligare resurser för enhets test

- [Test Drive tekniska metod tips](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Metod tips för marknadsföring av test enheter](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Översikt över test enhet en pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Anslut lead-hantering

Anslut till kunder direkt genom att koppla samman ditt kund Relations hanterings system (CRM). När du gör det får du kund kontakt information när en kund uttrycker intresse eller distribuerar din produkt.

Om du vill ansluta ditt CRM-system väljer du **Anslut**.

### <a name="choose-a-lead-destination"></a>Välj ett mål för lead

När du har valt **Anslut**visas en nedrullningsbar meny där du kan välja ditt CRM-system och sedan ange anslutnings information.

Partner Center stöder följande CRM-system för hantering av leads. Välj länken för installations anvisningar.

- [Azure-tabell](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) – ange anslutnings strängen för ditt lagrings konto. 
- [Dynamics 365 för kund engagemang (tidigare Dynamics CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) – ange din Dynamics 365-instans-URL och autentiseringsläge (Office 365 eller Azure Active Directory).
- [Https-slutpunkt](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) – ange din HTTPS-slutpunkt-URL. 
- [Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) – ange ditt Server-ID, Munchkin-konto-ID och formulär-ID.
- [Salesforce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) – ange ditt organisations-ID.

#### <a name="additional-lead-management-resources"></a>Ytterligare resurser för ledar hantering

- [Vanliga frågor och svar om lead management](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Vanliga konfigurations fel för lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Översikt över ledar hantering en personsökare](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

## <a name="properties"></a>properties

På sidan **Egenskaper** kan du definiera de kategorier och branscher som ska användas för att gruppera ditt erbjudande på Marketplace, din app-version och de juridiska avtal som stöder ditt erbjudande. Välj **Spara** när du är klar med den här sidan.

### <a name="category"></a>Category

Välj minst en och högst tre kategorier. Dessa kommer att användas för att placera ditt erbjudande i lämpliga sökområden för Marketplace. Var noga med att ta reda på hur ditt erbjudande stöder dessa kategorier i beskrivningen av erbjudandet. 

### <a name="industry"></a>Bransch

Alternativt kan du välja upp till två branscher för att kategorisera ditt erbjudande i Marketplace. Lämna avsnittet tomt om erbjudandet inte är speciellt för en bransch. Var noga med att ta reda på hur ditt erbjudande stöder de valda branscher i beskrivningen av erbjudandet. 

### <a name="app-version"></a>Appversion

Ange versions numret för ditt erbjudande. Kunderna kommer att se den här versionen på erbjudandets detalj sida.

### <a name="standard-contract"></a>Standardavtal

För att förenkla inköps processen för kunder och minska den juridiska komplexiteten för program varu leverantörer erbjuder Microsoft en standard kontrakts mal len för att hjälpa till att under lätta en transaktion i Marketplace.

I stället för att använda anpassade allmänna villkor kan du välja att erbjuda program varan under standard kontraktet, som kunderna bara behöver Undersök och godkänna en gång.

Du hittar standard kontraktet här: https://go.microsoft.com/fwlink/?linkid=2041178

Om du vill använda standard kontraktet kontrollerar du rutan **Använd standard kontrakt?** .

#### <a name="terms-of-use"></a>Användningsvillkor

Om du inte markerar kryss rutan **Använd standard kontrakt?** måste du ange dina egna juridiska användnings villkor i fältet **användningsvillkor** . Ange upp till 10 000 tecken text, eller ange URL: en där du kan hitta ytterligare licens villkor om dina användnings villkor kräver en längre beskrivning. Kunder måste acceptera dessa villkor innan de kan testa din app.

## <a name="offer-listing"></a>Erbjudande lista

På sidan erbjudande lista visas de språk som ditt erbjudande visas i. Observera att för närvarande är **engelska (USA)** det enda tillgängliga alternativet.

Du måste definiera Marketplace-information (erbjudande namn, beskrivning, bilder osv.) för varje språk/marknad. Välj språk/marknads namn för att ange den här informationen.

> [!NOTE]
> Erbjudande för List innehåll (till exempel beskrivning, dokument, skärm bilder, användnings villkor osv.) behöver inte vara på engelska, förutsatt att beskrivningen börjar med frasen, "det här programmet är endast tillgängligt på [språk som inte är engelska]". Det är också acceptabelt att tillhandahålla en *användbar länk-URL* för att erbjuda innehåll på ett annat språk än det som används i innehålls förteckningen för erbjudandet.

### <a name="name"></a>Name

Det namn som du anger här visas för kunder som rubrik på din erbjudande lista. Det här fältet fylls i automatiskt med texten du angav för **erbjud alias** när du skapade erbjudandet, men du kan ändra det här värdet. Det här namnet kan vara ett varumärke (och du kan inkludera varumärkes-eller Copyright-symboler). Namnet får innehålla högst 50 tecken och får inte innehålla några emojis.

### <a name="short-description"></a>Kort beskrivning

Ange en kort beskrivning av ditt erbjudande (upp till 100 tecken). Detta kan användas i Sök resultaten för Marketplace.

### <a name="description"></a>Beskrivning

Ange en längre Beskrivning av ditt erbjudande (upp till 3 000 tecken). Den här beskrivningen visas för kunder i Översikt över Marketplace-registrering. Ta med ditt erbjudandes värde, viktiga fördelar, kategori-och/eller bransch associationer, inköps möjligheter för appar och eventuella uppgifter som krävs. 

Några tips för att skriva din beskrivning:  

- Beskriv tydligt ditt erbjudande värde i de första meningarna i beskrivningen. Ta med följande i ditt värde:
  - Beskrivning av produkten
  - Typ av användare som fördelar från produkten
  - Kunden behöver eller smärta om produkt adresser
- Tänk på att de första meningarna kan visas i Sök motorns resultat.  
- Förlita dig inte på funktioner och funktioner för att sälja produkten. Fokusera i stället på det värde som du levererar.  
- Använd branschspecifika vokabulär eller kunskapsbaserade formuleringar så mycket som möjligt. 
- Överväg att använda HTML-taggar för att formatera beskrivningen och göra den mer intressant.

### <a name="search-keywords"></a>Sök efter nyckelord

Du kan också ange upp till tre Sök nyckelord för att hjälpa kunderna att hitta ditt erbjudande i Marketplace. För bästa resultat bör du även försöka använda dessa nyckelord i beskrivningen.

### <a name="products-your-app-works-with"></a>Produkter som din app fungerar med

Om du vill låta kunderna veta att appen fungerar med vissa produkter anger du upp till tre produkt namn här.

### <a name="support-urls"></a>Support-URL: er

I det här avsnittet kan du ange länkar som hjälper kunderna att förstå mer om ditt erbjudande.

#### <a name="help-link"></a>Hjälplänk

Ange URL: en där kunderna kan lära sig mer om ditt erbjudande.

#### <a name="privacy-policy-url"></a>URL för sekretess policy

Ange URL: en till din organisations sekretess policy. Du ansvarar för att se till att appen uppfyller sekretess lagar och föreskrifter och att tillhandahålla en giltig sekretess policy.

### <a name="contacts"></a>Kontakter

I det här avsnittet måste du ange namn, e-postadress och telefonnummer för en **support kontakt** och en **teknisk kontakt**. Den här informationen visas inte för kunder, men är tillgänglig för Microsoft och kan tillhandahållas till CSP-partner.

I avsnittet **support kontakt** måste du också ange **Support-URL:** en där CSP-partner kan hitta support för ditt erbjudande.

### <a name="supporting-documents"></a>Stöd dokument

Du måste ange minst ett (och upp till tre) relaterade marknadsförings dokument här, till exempel fakta blad, broschyrer, check listor eller presentationer. Dessa dokument måste vara i PDF-format.

### <a name="marketplace-images"></a>Marketplace-avbildningar

I det här avsnittet kan du ange logo typer och avbildningar som ska användas när du visar ditt erbjudande för kunden. Alla bilder måste vara i PNG-format.

#### <a name="store-logos"></a>Butiks logo typer

Du måste ange ditt erbjudandes logo typ i två storlekar: **Liten (48 x 48)** och **stor (216 x 216)** .

#### <a name="hero"></a>Hjälte

Hjälte avbildningen är valfri. Om du anger ett värde måste du mäta 815 x 290 pixlar.

#### <a name="screenshots"></a>Skärmbilder

Lägg till skärm bilder som visar hur ditt erbjudande fungerar. Minst en skärm bild krävs, och du kan lägga till upp till fem. Alla skärm dum par måste vara 1280 x 720 bild punkter.

#### <a name="videos"></a>Videor

Du kan också lägga till upp till fyra videor som demonstrerar ditt erbjudande. Dessa videor bör finnas på YouTube och/eller Vimeo. För var och en, anger du videons namn, URL och en miniatyr bild av videon (1280 x 720 bild punkter)

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare platser för Marketplace-lista

- [Metod tips för Marketplace-erbjudanden](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>Tillgänglighet

På sidan **tillgänglighet** får du alternativ om var och hur du gör ditt erbjudande tillgängligt.

### <a name="markets"></a>Marknaden

I det här avsnittet kan du ange vilka marknader som ditt erbjudande ska vara tillgängligt för. Det gör du genom att välja **Redigera marknader.** Då visas popup-fönstret för **marknads val** .

Som standard väljs inga marknader, men du måste välja minst en marknad för att kunna publicera erbjudandet. Klicka på **Välj alla** för att göra ditt erbjudande tillgängligt på alla möjliga marknader eller Välj de olika marknader som du vill lägga till. När du är klar väljer du **Spara**.

Observera att dina val endast gäller för nya förvärv, om någon redan har din app på en viss marknad och du senare tar bort marknaden, kan de personer som redan har erbjudandet på marknaden fortsätta att använda den, men inga nya kunder på den marknaden kommer att kunna få ditt erbjudande.

> [!IMPORTANT]
> Det är ditt ansvar att uppfylla alla lokala juridiska krav, även om dessa krav inte finns med i listan här eller i Partner Center.

Tänk på att även om du väljer alla marknader kan lokala lagar och restriktioner eller andra faktorer förhindra att vissa erbjudanden visas i vissa länder och regioner.

### <a name="preview-audience"></a>Förhandsgranska mål grupp

Innan du publicerar erbjudandet Live på det bredare Marketplace-erbjudandet måste du först göra det tillgängligt för en begränsad förhands **gransknings publik**. Ange en **Dölj nyckel** (valfri sträng med endast gemener och/eller siffror) här. Medlemmar i din förhands visnings publik kan använda denna Dölj-nyckel som en token för att visa en förhands granskning av ditt erbjudande i Marketplace.

När du är redo att göra ditt erbjudande tillgängligt och ta bort begränsningen för för hands versionen måste du ta bort **Dölj-tangenten** och publicera igen.

## <a name="technical-configuration"></a>Teknisk konfiguration

Sidan **teknisk konfiguration** definierar de tekniska uppgifter som används för att ansluta till ditt erbjudande. Med den här anslutningen kan vi tillhandahålla ditt erbjudande för slutanvändaren om de väljer att förvärva det.

### <a name="solution-identifier"></a>Lösnings identifierare

Ange lösnings identifieraren (GUID) för din lösning.

Hitta lösnings-ID:
1. I Microsoft Dynamics Lifecycle Services (LCS) väljer du **lösnings hantering**.
2. Välj din lösning och leta efter lösnings **identifieraren** i **paket översikten**. Om identifieraren är tom väljer du **Redigera** och publicera om ditt paket och försöker igen.

### <a name="release-version"></a>Version

Välj den version av Dynamics 365 för ekonomi och åtgärder som den här lösningen fungerar med.

## <a name="test-drive-technical-configuration"></a>Teknisk konfiguration för test enhet

Om du har valt **Aktivera en testen het** på sidan [erbjudande konfiguration](#offer-setup) måste du ange information här för att kunderna ska kunna uppleva en test enhet för ditt erbjudande.

På sidan testenhet kan du konfigurera en demonstration (eller "testenhet") som gör det möjligt för kunder att prova ditt erbjudande innan de genomför köpet. Läs mer i artikeln [Vad är Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Om du inte längre vill ange en testen het för ditt erbjudande går du tillbaka till sidan **[erbjudande konfiguration](#offer-setup)** och avmarkerar **Aktivera test enhet**.

Följande typer av test enheter är tillgängliga, var och en med sina egna tekniska konfigurations krav.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logic app](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (Teknisk konfiguration krävs inte)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Teknisk konfiguration för Azure Resource Manager testen het

En distributionsmall som innehåller alla Azure-resurser som utgör din lösning. Produkter som passar det här scenariot använder bara Azure-resurser. Läs mer om hur du konfigurerar en [Azure Resource Manager test-enhet](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regioner** (krävs): För närvarande finns det 26 Azure-regioner som stöds där din test-enhet kan göras tillgänglig. Vanligt vis vill du göra din test enhet tillgänglig i de regioner där du räknar med det största antalet kunder, så att de kan välja den region som är närmast för bästa prestanda. Du måste kontrol lera att din prenumeration har tillåtelse att distribuera alla resurser som behövs i varje region som du väljer.

- **Instanser**: Välj typ (frekvent eller kall) och antal tillgängliga instanser, vilket kommer att multipliceras med antalet regioner där ditt erbjudande är tillgängligt.

**Het**: Den här typen av instans distribueras och väntar på åtkomst per vald region. Kunder kan komma åt en test enhets *aktiva* instanser direkt i stället för att vänta på en distribution. Nackdelen är att dessa instanser körs alltid på din Azure-prenumeration, så leder de en större upptid kostnad. Vi rekommenderar starkt att du har minst en *aktiv* instans, eftersom de flesta kunder inte vill vänta på fullständiga distributioner, vilket resulterar i en kombination av kund användning om ingen *aktiv* instans är tillgänglig.

**Kall**: Den här typen av instans representerar det totala antalet instanser som eventuellt kan distribueras per region. Kalla instanser kräver att hela test enhetens Resource Manager-mall distribueras när en kund begär test enheten, så att *kalla* instanser är mycket långsammare att läsa in än *varma* instanser. Kompromissen är att du bara behöver betala under test enhetens varaktighet, den körs *inte* alltid på din Azure-prenumeration som en *aktiv* instans.

- **Testa enhet Azure Resource Manager mall**: Ladda upp zip-filen som innehåller din Azure Resource Manager-mall.  Lär dig mer om hur du skapar en Azure Resource Manager-mall i snabb starts artikeln [skapa och distribuera Azure Resource Manager mallar med hjälp av Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Test enhetens varaktighet** (krävs): Ange hur lång tid test enheten ska vara aktiv, i antal timmar. Test Drive avslutas automatiskt efter denna tid har löpt ut. Den här varaktigheten kan bara anges med ett heltal på flera timmar (t. ex. "2" timmar, "1,5" är inte giltigt).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Teknisk konfiguration för Dynamics 365-testenhet

Microsoft kan ta bort komplexiteten för att konfigurera en testen het genom att vara värd för och underhålla tjänst etableringen och-distributionen med den här typen av test enhet. Konfigurationen av den här typen av värdbaserad test enhet är samma oavsett om test enheten är riktad mot en affärs Central, kund engagemang eller drifts grupp.

- **Maximalt antal samtidiga test enheter** (krävs): Ange det maximala antalet kunder som kan använda test enheten samtidigt. Varje samtidig användare använder en Dynamics 365-licens medan test enheten är aktiv, så du måste se till att du har tillräckligt med licenser för att stödja Max gränsen. Rekommenderat värde 3-5.

- **Test enhetens varaktighet** (krävs): Ange hur lång tid test enheten ska vara aktiv genom att definiera antalet timmar. Efter så här många timmar avslutas sessionen och använder inte längre någon av dina licenser. Vi rekommenderar ett värde på 2-24 timmar beroende på hur komplex ditt erbjudande är. Den här varaktigheten kan bara anges med ett heltal på flera timmar (t. ex. "2" timmar, "1,5" är inte giltigt).  Användaren kan begära en ny session om de får slut på tid och vill komma åt test enheten igen.

- **Instans-URL** (krävs): URL: en där kunden kommer att påbörja sin test-enhet. Normalt är URL: en för din Dynamics 365-instans som kör appen med exempel data installerat https://testdrive.crm.dynamics.com) (t. ex.

- **URL för instans webb-API** (krävs): Hämta URL: en för webb-API för din Dynamics 365-instans genom att logga in på ditt Microsoft 365-konto och navigera till **Inställningar** \&gt; **Anpassning** \&gt; **Resurser för utvecklare** \&gt; **Instans webb-API (tjänstens rot-URL)** , kopiera URL-adressen som https://testdrive.crm.dynamics.com/api/data/v9.0) finns här (t. ex.

- **Roll namn** (krävs): Ange namnet på den säkerhets roll som du har definierat i din anpassade Dynamics 365-testenhet. Detta kommer att tilldelas användaren under deras test enhet (t. ex. Test-Drive-roll).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Teknisk konfiguration för Logic app-testenhet

Alla anpassade produkter bör använda den här typen av distributions mal len test enhet som omfattar en mängd olika komplexa lösnings arkitekturer. Mer information om hur du konfigurerar Logi Kap par test enheter finns i [åtgärder](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) och [kund engagemang](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) på GitHub.

- **Region** (obligatorisk listruta för List rutor med enstaka val): För närvarande finns det 26 Azure-regioner som stöds där din test-enhet kan göras tillgänglig. Resurserna för din Logic-app distribueras i den region som du väljer. Om din Logi Kap par innehåller anpassade resurser som lagras i en viss region, se till att regionen är vald här. Det bästa sättet att göra detta är att fullständigt distribuera din Logic app lokalt på din Azure-prenumeration i portalen och kontrol lera att den fungerar korrekt innan du gör det här valet.

- **Maximalt antal samtidiga test enheter** (krävs): Ange det maximala antalet kunder som kan använda test enheten samtidigt. De här test enheterna har redan distribuerats, vilket gör det möjligt för kunder att komma åt dem direkt utan att vänta på en distribution.

- **Test enhetens varaktighet** (krävs): Ange hur lång tid test enheten ska vara aktiv, i antal timmar. Test enheten avslutas automatiskt när den här tids perioden är slut.

- **Namn på Azure-resurs grupp** (krävs): Ange namnet på den [Azure-resurs grupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) där din Logic app-testenhet sparas.

- **Namn på Azure Logic-appen** (krävs): Ange namnet på den Logic-app som tilldelar användaren till-test enheten. Den här Logic-appen måste sparas i gruppen Azure-resurser ovan.

- **Avetablera Logic app-namn** (krävs): Ange namnet på den Logic-app som avetablerar test enheten när kunden är färdig. Den här Logic-appen måste sparas i gruppen Azure-resurser ovan.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Teknisk konfiguration krävs inte för Power BI test enheter

Produkter som vill demonstrera ett interaktivt Power BI visuellt objekt kan använda en inbäddad länk för att dela en anpassad instrument panel som test enhet, ingen ytterligare teknisk konfiguration krävs. Läs mer om hur du konfigurerar[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) mallar.

### <a name="deployment-subscription-details"></a>Information om distributions prenumeration

För att kunna distribuera test enheten åt dig kan du skapa och tillhandahålla en separat, unik Azure-prenumeration. (Krävs inte för Power BI test enheter).

- **ID för Azure-prenumeration** (krävs för Azure Resource Manager-och Logic Apps): Ange prenumerations-ID för att bevilja åtkomst till dina Azure-Kontotjänster för rapportering och fakturering av resursanvändning. Vi rekommenderar att du [skapar en separat Azure-prenumeration](https://docs.microsoft.com/azure/billing/billing-create-subscription) som ska användas för test enheter om du inte redan har en. Du hittar ditt prenumerations-ID för Azure genom att logga in på [Azure Portal](https://portal.azure.com/) och navigera till fliken prenumerationer på den vänstra menyn. Om du väljer fliken visas ditt prenumerations-ID (t. ex. "a83645ac-1234-5AB6-6789-1h234g764ghty").

- **ID för Azure AD-klient** (krävs): Ange din Azure Active Directory (AD) [klient-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Om du vill hitta detta ID loggar du in på [Azure Portal](https://portal.azure.com/), väljer fliken Active Directory på den vänstra menyn, väljer **Egenskaper** och letar efter **katalog-ID** -nummer som anges (t. ex. 50c464d3-4930-494c-963c-1e951d15360e). Du kan också leta upp organisationens klient-ID med din domän namns-URL [https://www.whatismytenantid.com](https://www.whatismytenantid.com)på:.

- **Namn på Azure AD-klient** (krävs för dynamisk 365): Ange ditt Azure Active Directory (AD) namn. Du hittar det här namnet genom att logga in på [Azure Portal](https://portal.azure.com/)i det övre högra hörnet ditt klient namn visas under ditt konto namn.

- **ID för Azure AD-App** (krävs): Ange ditt Azure Active Directory (AD) [program-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Om du vill hitta detta ID loggar du in på [Azure Portal](https://portal.azure.com/), väljer fliken Active Directory på den vänstra menyn, väljer **Appregistreringar**och letar sedan efter det **program-ID** som anges (t. ex. 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-appens klient hemlighet** (krävs): Ange [klient hemligheten](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)för Azure AD-programmet. Du hittar det här värdet genom att logga in på [Azure Portal](https://portal.azure.com/). Välj fliken **Azure Active Directory** på den vänstra menyn, Välj **Appregistreringar**och välj sedan appen Test Drive. Välj sedan **certifikat och hemligheter**, Välj **ny klient hemlighet**, ange en beskrivning, Välj **aldrig** under **upphör ande**och välj sedan **Lägg till**. Glöm inte att kopiera värdet. (Du kan inte navigera bort från sidan innan du gör detta, eller så har du inte till gång till värdet.)

Kom ihåg att **Spara** innan du går vidare till nästa avsnitt!

### <a name="test-drive-marketplace-listings"></a>Marketplace-platser för enhets kontroll

Alternativet **Marketplace-lista** på fliken testenhet visar de språk som test enheten är tillgänglig på. Observera att för närvarande är **engelska (USA)** den enda tillgängliga platsen. Välj språk namnet för att ange information som beskriver test enhets upplevelsen.

- **Beskrivning** (krävs): Beskriv din test enhet, vad som visas, mål för användaren att experimentera med, vilka funktioner som ska utforskas och all relevant information som hjälper användaren att avgöra om ditt erbjudande ska erhållas. Upp till 3 000 tecken text kan anges i det här fältet. 

- **Åtkomst information** (krävs för Azure Resource Manager-och logik test enheter): Förklara vad en kund behöver veta för att få åtkomst till och använda den här test enheten. Gå igenom ett scenario för att använda ditt erbjudande och exakt vad kunden bör känna till för att få åtkomst till funktioner i hela test enheten. Upp till 10 000 tecken text kan anges i det här fältet.

- **Användar handbok** (krävs): En djupgående genom gång av test enhets upplevelsen. Användar handboken bör bestå av exakt vad du vill att kunden ska få från att använda test enheten och fungera som referens för alla frågor som de kan ha. Filen måste vara i PDF-format och ha namnet (högst 255 tecken) efter överföringen.

- **Videos Lägg till** videor (valfritt): Videor kan laddas upp till YouTube eller Vimeo och refereras till med en länk och miniatyr bild (533 x 324 pixlar) så att en kund kan visa en genom gång av information för att hjälpa dem att bättre förstå test enheten, inklusive hur du kan använda funktionerna i Erbjud och förstå scenarier som framhäver deras fördelar.
  - **Namn** kunna
  - **URL (endast YouTube eller Vimeo)** kunna
  - **Miniatyr bild (533 x 324px)** : Bild filen måste vara i PNG-format.

## <a name="supplemental-content"></a>Kompletterande innehåll

På den här sidan kan du ange ytterligare information som krävs om ditt erbjudande.

### <a name="validation-assets"></a>Validerings till gångar

Du måste överföra en anpassnings analys rapport (CAR) i det här avsnittet. Den här rapporten genereras genom att analysera anpassnings-och tilläggs modeller baserat på en fördefinierad uppsättning regler för bästa praxis.

Den här filen måste vara i formatet. xls eller. xlsx. Om du har mer än en rapport kan du ladda upp en zip-fil som innehåller alla rapporter.

### <a name="does-solution-include-localizations"></a>Innehåller lösningen lokaliseringar?

Välj **Ja** om lösningen aktiverar användning av lokala standarder och principer (till exempel om den hanterar de olika löne regler som krävs av olika länder/regioner). Annars väljer **nr**.

### <a name="does-solution-enable-translations"></a>Aktiverar lösningen översättningar?

Svara **Ja** om texten i lösningen kan översättas till andra språk. Annars väljer **nr**.

## <a name="publish"></a>Publicera

### <a name="submit-offer-to-preview"></a>Skicka erbjudande till för hands version

När du har slutfört alla obligatoriska avsnitt i erbjudandet väljer du **publicera** i det övre högra hörnet i portalen. Du omdirigeras sedan till sidan **Granska och publicera** . 

Om det här är första gången du publicerar det här erbjudandet kan du:

- Se slut för ande status för varje avsnitt i erbjudandet.
    - *Inte startad* – innebär att avsnittet inte har vidrör ATS och måste slutföras.
    - *Ofullständig* – innebär att avsnittet innehåller fel som behöver åtgärdas eller som kräver mer information. Gå tillbaka till avsnitten och uppdatera det.
    - *Complete* – innebär att avsnittet har slutförts, att alla nödvändiga data har angetts och att det inte finns några fel. Alla avsnitt i erbjudandet måste vara i ett komplett tillstånd innan du kan skicka in erbjudandet.
- I avsnittet **Obs!-certifiering** anger du test instruktioner till certifierings teamet för att kontrol lera att appen är korrekt testad, förutom eventuella kompletterande anteckningar som hjälper dig att förstå din app.
- Skicka in erbjudandet om publicering genom att välja **Skicka**. Vi skickar dig ett e-postmeddelande för att meddela dig när en för hands version av erbjudandet kan granskas och godkännas. Du måste gå tillbaka till Partner Center och välja **Go-Live** för erbjudandet att publicera ditt erbjudande till allmänheten (eller om ett privat erbjudande till den privata mål gruppen).

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i den kommersiella Marketplace](./update-existing-offer.md)
