---
title: Skapa ett nytt Dynamics 365 for Customer Engagement-& PowerApps-erbjudande på den kommersiella marknadsplatsen
description: Så här skapar du ett nytt Dynamics 365 for Customer Engagement-& PowerApps-erbjudande för att lista eller sälja i Azure Marketplace, AppSource eller csp-programmet (Cloud Solution Provider) med hjälp av Den kommersiella Marketplace-portalen på Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 0ec62095f14b38d0c4ffebfabc14c4a0a27a70fb
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869826"
---
# <a name="create-a-new-dynamics-365-for-customer-engagement--powerapps-offer"></a>Skapa ett nytt Dynamics 365 for Customer Engagement-& PowerApps-erbjudande

I det här avsnittet beskrivs hur du skapar ett nytt Dynamics 365 for Customer Engagement & PowerApps-erbjudande. Alla appar för Dynamics 365 for Customer Engagement (PowerApps, Sales, Service, Project Service och Field Service) måste gå igenom vår certifieringsprocess och stödja en utvärderingsupplevelse. Certifieringsprocessen kontrollerar din lösning efter standardkrav, kompatibilitet och korrekta metoder. Utvärderingsversionen gör det möjligt för användare att distribuera din lösning till en live Dynamics 365-miljö.

Om du vill börja skapa Dynamics 365 för Customer Engagement & PowerApps erbjuder, se till att du först [skapar ett Partner Center-konto](./create-account.md) och öppnar [instrumentpanelen för Kommersiella Marknadsplatser](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), med fliken **Översikt** vald.

![Instrumentpanelen för kommersiella marknadsplatser i Partner Center](./media/new-offer-overview.png)

>[!Note]
> När ett erbjudande har publicerats uppdateras redigeringarna av erbjudandet i Partner Center endast i systemet och lagrar fronter efter återpublicering. Se till att du skickar in erbjudandet för publicering när du har gjort ändringar.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

Välj knappen **+ Nytt erbjudande** och välj sedan menyalternativet Dynamics **365 for Customer Engagement & PowerApps.** Dialogrutan **Nytt erbjudande** visas.

### <a name="offer-id-and-alias"></a>Erbjudande-ID och alias

- **Erbjudande-ID:** Unik identifierare för varje erbjudande i ditt konto. Det här ID:t visas för kunder i URL-adressen för marketplace-erbjudandet och Azure Resource Manager-mallar (om tillämpligt). Erbjudande-ID måste vara gemener alfanumeriska tecken (inklusive bindestreck och understreck, men inget blanktecken). ID:t är begränsat till 50 tecken och kan inte ändras när du har valt **Skapa**. Om du till exempel anger *test-erbjudande-1* här `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`blir erbjudandeadressen .

- **Offeralias**: Namnet som används för att referera till erbjudandet i partnercentret. Det här namnet används inte på marknadsplatsen och skiljer sig från erbjudandenamnet och andra värden som visas för kunderna. Det går inte att ändra det här värdet när du har valt **Skapa**.

När du har angett ditt **offer-ID** och **Offeralias**väljer du **Skapa**. Du kommer då att kunna arbeta med alla de olika delarna av ditt erbjudande.

## <a name="offer-setup"></a>Inställningar för erbjudande

På **inställningssidan** för Erbjudande frågar du efter följande information. Var noga med att välja **Spara** när du har slutfört dessa fält.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Hur vill du att potentiella kunder ska interagera med det här listerbjudandet?

Välj det alternativ som du vill använda för det här erbjudandet.

#### <a name="get-it-now-free"></a>Få det nu (gratis)

Lista ditt erbjudande till kunder gratis genom att ange en giltig webbadress (som börjar med *http* eller *https)* där de kan komma åt din app.  Exempel: `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Gratis provperiod (lista)

Lista ditt erbjudande till kunder med en länk till en kostnadsfri utvärderingsversion genom att tillhandahålla en giltig webbadress (som börjar med *http* eller *https)* där de kan få en utvärderingsversion.  Till exempel: `https://contoso.com/trial/my-app`. Kostnadsfria utvärderingsversioner skapas, hanteras och konfigureras av tjänsten och har inte prenumerationer som hanteras av Microsoft.

> [!NOTE]
> De token som ditt program får via din utvärderingslänk kan endast användas för att hämta användarinformation via Azure Active Directory (Azure AD) för att automatisera kontoskapande i din app. Microsoft-konton stöds inte för autentisering med den här token.

#### <a name="contact-me"></a>Kontakta mig

Samla in kundkontaktinformation genom att ansluta ditt CRM-system (Customer Relationship Management). Kunden kommer att bli ombedd att dela sin information. Dessa kunduppgifter, tillsammans med erbjudandenamnet, ID och marknadsplatskällan där de hittade ditt erbjudande, skickas till CRM-systemet som du har konfigurerat. Mer information om hur du konfigurerar CRM finns i [Ansluta leadhantering](#connect-lead-management). 

### <a name="test-drive"></a>Provkörning

En provkörning är ett bra sätt att visa upp ditt erbjudande till potentiella kunder genom att ge dem möjlighet att "prova innan du köper", vilket resulterar i ökad konvertering och generering av högt kvalificerade leads. [Läs mer om provkörningar.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Om du vill aktivera en provkörning markerar du rutan **Aktivera en provkörning.** Du måste sedan konfigurera en demonstrationsmiljö i den tekniska konfigurationen för [testlusten](#test-drive-technical-configuration) som konfigureras så att kunderna kan prova erbjudandet under en viss tidsperiod. 

#### <a name="type-of-test-drive"></a>Typ av provkörning

Välj bland följande alternativ:

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)**: En distributionsmall som innehåller alla Azure-resurser som utgör din lösning. Produkter som passar det här scenariot använder endast Azure-resurser.
- **[Dynamics 365 för Business Central:](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** Microsoft är värd för och underhåller provkörningstjänsten (inklusive etablering och distribution) för ett Business Central-system för planering av företagsresurser (ekonomi, åtgärder, leveranskedja, CRM osv.).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)**: Microsoft är värd för och underhåller provkörningstjänsten (inklusive etablering och distribution) för ett Customer Engagement-system (försäljning, service, projektservice, fältservice osv.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)**: Microsoft är värd för och underhåller provkörningstjänsten (inklusive etablering och distribution) för ett ekonomi- och driftsresursplaneringssystem (ekonomi, drift, tillverkning, leveranskedja osv.). 
- **[Logikapp:](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** En distributionsmall som omfattar alla komplexa lösningsarkitekturer. Alla anpassade produkter bör använda den här typen av provkörning.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)**: En inbäddad länk till en specialbyggd instrumentpanel. Produkter som vill demonstrera ett interaktivt Power BI-visuellt objekt bör använda den här typen av testenhet. Allt du behöver ladda upp här är din inbäddade Power BI-URL.

#### <a name="additional-test-drive-resources"></a>Ytterligare provkörningsresurser

- [Metodtips för testkörning](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Metodtips för provkörningsmarknadsföring](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Översikt över provkörning en personsökare](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Anslut leadhantering

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Mer information finns i [Översikt över leadhantering](./commercial-marketplace-get-customer-leads.md).

Kom ihåg att **spara** innan du går vidare till nästa avsnitt.

## <a name="properties"></a>Egenskaper

På sidan **Egenskaper** kan du definiera de kategorier och branscher som används för att gruppera ditt erbjudande på marknadsplatsen, din appversion och de juridiska kontrakt som stöder ditt erbjudande. Välj **Spara** när du har slutfört den här sidan.

### <a name="category"></a>Kategori

Välj minst en och högst tre kategorier. **Kategorin** kommer att användas för att placera ditt erbjudande i lämpliga marknadsplats sökområden. Var noga med att ropa ut hur ditt erbjudande stöder dessa kategorier i erbjudandebeskrivningen.

### <a name="industry"></a>Bransch

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>Tillämpliga Dynamics 365-produkter

Välj alla Dynamics 365-produkter som det här erbjudandet gäller för.

### <a name="app-version"></a>Appversion

Ange versionsnumret för ditt erbjudande. Kunderna ser den här versionen på erbjudandeets informationssida. Om du bara uppdaterar versionsnumret på grund av ändringar i marknadsföring/beskrivande, markerar du rutan **Endast marknadsföringsändring.** Med det här alternativet kan erbjudandet kringgå certifierings- och etableringsfaserna.

### <a name="terms-and-conditions"></a>Villkor

Ange dina egna juridiska villkor i fältet **Regler och villkor.** Du kan också ange webbadressen där dina villkor finns. Kunderna måste acceptera dessa villkor innan de kan prova ditt erbjudande.

## <a name="offer-listing"></a>Lista över erbjudande

På sidan Erbjudandelista visas de språk på vilka ditt erbjudande visas. För närvarande är **engelska (USA)** det enda tillgängliga alternativet.

Du måste definiera marknadsplatsinformation (erbjudandenamn, beskrivning, bilder, etc.) för varje språk/marknad. Välj språk-/marknadsnamn för att ange den här informationen.

> [!NOTE]
> Erbjudande notering innehåll (såsom beskrivning, dokument, skärmdumpar, användarvillkor, etc.) är inte skyldig att vara på engelska, så länge erbjudandet beskrivningen börjar med frasen"Denna ansökan är endast tillgänglig på [icke-engelska språket]." Det är också acceptabelt att ange en *användbar länk-URL* för att erbjuda innehåll på ett annat språk än det som används i innehåll för erbjudandelistor.

### <a name="name"></a>Namn

Namnet du anger här visas för kunderna som titeln på din erbjudandebeskrivning. Det här fältet är förifyllt med den text som du angav för **Offer alias** när du skapade erbjudandet, men du kan ändra det här värdet. Detta namn kan vara varumärkesskyddat (och du kan inkludera varumärkes- eller upphovsrättssymboler). Namnet får inte vara fler än 50 tecken och kan inte innehålla några emojis.

### <a name="short-description"></a>Kort beskrivning

Ge en kort beskrivning av ditt erbjudande (upp till 100 tecken). Den här beskrivningen kan användas i marketplace-sökresultat.











### <a name="description"></a>Beskrivning

Ge en längre beskrivning av ditt erbjudande (upp till 3 000 tecken). Den här beskrivningen visas för kunder i översikten för marknadsplatslistning. Inkludera erbjudandets värdeerbjudande, viktiga fördelar, kategori- och/eller branschorganisationer, köpmöjligheter i appen och eventuella nödvändiga upplysningar.

Några tips för att skriva din beskrivning:  

- Beskriv tydligt erbjudandets värdeerbjudande i de första meningarna i din beskrivning. Inkludera följande information i ditt värdeerbjudande:
  - Beskrivning av produkten
  - Den typ av användare som drar nytta av produkten
  - Kundens behov eller smärta som produkten adresserar
- Tänk på att de första meningarna kan visas i sökmotorresultat.  
- Lita inte på funktioner och funktioner för att sälja din produkt. Fokusera istället på det värde du levererar.  
- Använd branschspecifik vokabulär eller förmånsbaserad formulering så mycket som möjligt.
- Överväg att använda HTML-taggar för att formatera beskrivningen och göra den mer engagerande.

Om du vill göra erbjudandebeskrivningen mer engagerande använder du RTF-redigeraren för att formatera beskrivningen.

![Använda RTF-redigeraren](./media/text-editor2.png)

Använd följande instruktioner för att använda RTF-redigeraren:

- Om du vill ändra innehållets format markerar du den text som du vill formatera och markerar ett textformat, som visas nedan:

     ![Använda RTF-redigeraren för att ändra textformat](./media/text-editor3.png)

- Om du vill lägga till en punktlista eller numrerad lista i texten använder du alternativen nedan:

     ![Använda RTF-redigeraren för att lägga till listor](./media/text-editor4.png)

- Om du vill lägga till eller ta bort indrag i texten använder du alternativen nedan:

     ![Använda RTF-redigeraren för att dra in](./media/text-editor5.png)







### <a name="search-keywords"></a>Sök nyckelord

Du kan också ange upp till tre sökord för att hjälpa kunderna att hitta ditt erbjudande på marknaden. För bästa resultat, försök att använda dessa sökord i din beskrivning också.

### <a name="products-your-app-works-with"></a>Produkter som appen fungerar med

Om du vill att kunderna ska veta att din app fungerar med specifika produkter anger du upp till tre produktnamn här.

### <a name="support-urls"></a>Stöd webbadresser

I det här avsnittet kan du tillhandahålla länkar som hjälper kunderna att förstå mer om ditt erbjudande.

#### <a name="help-link"></a>Länk till hjälp

Ange webbadressen där kunderna kan läsa mer om ditt erbjudande.

#### <a name="privacy-policy-url"></a>Url till sekretesspolicy

Ange webbadressen till organisationens sekretesspolicy. Du är ansvarig för att din app följer sekretesslagar och sekretessregler och för att tillhandahålla en giltig sekretesspolicy.

### <a name="contacts"></a>Contacts

I det här avsnittet måste du ange namn, e-post och telefonnummer för en **supportkontakt** och en **teknisk kontakt**. Den här informationen visas inte för kunder, men kommer att vara tillgänglig för Microsoft och kan tillhandahållas CSP-partner.

I avsnittet **Supportkontakt** måste du också ange **support-URL:en** där CSP-partner kan hitta support för ditt erbjudande.

### <a name="supporting-documents"></a>Verifikationer

Du måste tillhandahålla minst ett (och upp till tre) relaterade marknadsföringsdokument här, till exempel faktablad, broschyrer, checklistor eller presentationer. Dessa dokument måste vara i PDF-format.

### <a name="marketplace-images"></a>Marketplace-bilder

I det här avsnittet kan du ange logotyper och bilder som ska användas när du visar ditt erbjudande till kunden. Alla bilder måste vara i PNG-format.

>[!Note]
>Om du har problem med att ladda upp filer https://upload.xboxlive.com kontrollerar du att det lokala nätverket inte blockerar tjänsten som används av Partner Center.

#### <a name="store-logos"></a>Lagra logotyper

Du kan ange erbjudandets logotyp i tre storlekar: **Small (48 x 48)**, **Large (216 x 216)** och **Wide (255 x 115)**. De små och stora storlekarna krävs.

#### <a name="hero"></a>Hjälte

Hjältebilden är valfri. Om du anger en, måste den mäta 815 x 290 pixlar.

#### <a name="screenshots"></a>Skärmbilder

Lägg till skärmdumpar som visar hur erbjudandet fungerar. Minst en skärmdump krävs, och du kan lägga till upp till fem. Alla skärmdumpar måste vara 1280 x 720 pixlar.

#### <a name="videos"></a>Videoklipp

Du kan också lägga till upp till fyra videor som visar ditt erbjudande. Dessa videor bör finnas på YouTube och/eller Vimeo. För var och en anger du videons namn, dess webbadress och en miniatyrbild av videon (1 280 x 720 pixlar)

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare resurser för marknadsplatsnotering

- [Metodtips för marknadsplatserbjudanden](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>Tillgänglighet

På sidan **Tillgänglighet** får du alternativ om var och hur du gör erbjudandet tillgängligt.

### <a name="markets"></a>Marknader

I det här avsnittet kan du ange på vilka marknader ditt erbjudande ska vara tillgängligt. Om du vill göra det väljer du **Redigera marknader**, som visar **popup-fönstret Marknadsval.**

Som standard väljs inga marknader, men du måste välja minst en marknad för att kunna publicera ditt erbjudande. Klicka på **Markera alla** om du vill göra ditt erbjudande tillgängligt på alla möjliga marknader eller välja de specifika marknader som du vill lägga till. När du är klar väljer du **Spara**.

Dina val gäller här endast för nyförvärv; Om någon redan har din app på en viss marknad och du senare tar bort den marknaden, kan de människor som redan har erbjudandet på den marknaden fortsätta att använda den, men inga nya kunder på den marknaden kommer att kunna få ditt erbjudande.

> [!IMPORTANT]
> Det är ditt ansvar att uppfylla lokala juridiska krav, även om dessa krav inte anges här eller i Partner Center. Även om du väljer alla marknader kan lokala lagar och begränsningar eller andra faktorer förhindra att vissa erbjudanden visas i vissa länder och regioner.

### <a name="preview-audience"></a>Förhandsgranska målgrupp

Innan du publicerar ditt erbjudande live på det bredare marketplace-erbjudandet måste du först göra det tillgängligt för en begränsad **förhandsversion.** Ange en **Dölj-tangent** (valfri sträng med endast gemener och/eller siffror) här. Medlemmar i din förhandsgranskningsmålgrupp kan använda den här döljnyckeln som en token för att visa en förhandsgranskning av ditt erbjudande på marknadsplatsen.

När du sedan är redo att göra erbjudandet tillgängligt och ta bort förhandsgranskningsbegränsningen måste du ta bort **dölj-tangenten** och publicera igen.

## <a name="technical-configuration"></a>Teknisk konfiguration

Sidan **Teknisk konfiguration** definierar de tekniska detaljer som används för att ansluta till ditt erbjudande. Denna anslutning gör det möjligt för oss att etablera ditt erbjudande för slutkunden om de väljer att förvärva det.

### <a name="base-license-model"></a>Baslicensmodell

Baslicensmodellen avgör hur kunder tilldelas ditt program i CRM Admin Center. Välj **Resurs** för instansbaserad licensiering eller **Användare** om licenser tilldelas en per klient.

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>Kräver S2S-utgående och åtkomst till CRM Secure Store

Markera den här rutan om du vill aktivera konfigurationen av utgående åtkomst för CRM Secure Store eller Server-to-Server (S2S). Den här funktionen kräver specialiserad hänsyn från Dynamics 365-teamet under certifieringsfasen. Microsoft kontaktar dig för att utföra ytterligare steg för att stödja den här funktionen.

### <a name="application-configuration-url"></a>URL för programkonfiguration

Ange URL:en för konfigurationswebbsidan som gör det möjligt för kunden att konfigurera appen.

### <a name="crm-package"></a>CRM-paket

I **URL:en** för fältet paketplats anger du URL:en för ett Azure Storage-konto som innehåller den uppladdade ZIP-filen för CRM-paketet. Den här webbadressen bör innehålla en skrivskyddad SAS-nyckel så att Microsoft kan hämta paketet för verifiering.

Markera rutan med etiketten **Det finns mer än ett CRM-paket i min paketfil**, om tillämpligt. Om så är fallet, se till att inkludera alla paket i zip-filen.

### <a name="crm-package-availability"></a>Crm-paket tillgänglighet

I det här avsnittet väljer du **+ Lägg till region** om du vill ange de geografiska regioner där CRM-paketet ska vara tillgängligt för kunder. Distribution till följande suveräna regioner kräver särskild behörighet och validering under certifieringsprocessen: [Tyskland](https://docs.microsoft.com/azure/germany/), [US Government Cloud](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)och TIP.

Som standard används **den programkonfigurations-URL** som du angav ovan för varje region. Om du vill kan du ange en separat URL för programkonfiguration för en eller flera specifika regioner. 

## <a name="test-drive-technical-configuration"></a>Teknisk konfiguration för provkörning

Om du har valt **Aktivera en provkörning** på [inställningssidan för Erbjudande](#offer-setup) måste du ange information här för att kunderna ska kunna uppleva en provkörning av ditt erbjudande.

På **sidan Provkörning** kan du ställa in en demonstration (eller "provkörning") som gör det möjligt för kunder att prova ditt erbjudande innan de bestämmer sig för att köpa det. Läs mer i artikeln [Vad är Provkörning?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Om du inte längre vill tillhandahålla en provkörning för erbjudandet går du tillbaka till **[inställningssidan För Erbjudande](#offer-setup)** och **avmarkerar Aktivera provkörning**.

Följande typer av testenheter är tillgängliga, var och en med sina egna tekniska konfigurationskrav.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logikapp](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (Teknisk konfiguration krävs inte)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Teknisk konfiguration för Azure Resource Manager-testenhet

En distributionsmall som innehåller alla Azure-resurser som utgör din lösning. Produkter som passar det här scenariot använder endast Azure-resurser. Läs mer om hur du konfigurerar en [Azure Resource Manager-testenhet](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regioner** (obligatoriskt): För närvarande finns det 26 Azure-stödda regioner där din provkörning kan göras tillgänglig. Vanligtvis vill du göra din provkörning tillgänglig i de regioner där du förutser det största antalet kunder, så att de kan välja den närmaste regionen för bästa prestanda. Du måste se till att din prenumeration tillåts distribuera alla resurser som behövs i var och en av de regioner som du väljer.

- **Instanser**: Välj typ (varmt eller kallt) och antal tillgängliga instanser, som multipliceras med antalet regioner där erbjudandet är tillgängligt.

**Hot**: Den här typen av instans distribueras och väntar på åtkomst per vald region. Kunder kan omedelbart komma åt *Heta* instanser av en provkörning, i stället för att behöva vänta på en distribution. Avvägningen är att dessa instanser alltid körs på din Azure-prenumeration, så att de medför en större drifttidskostnad. Vi rekommenderar starkt att ha *Hot* minst en Hot-instans, eftersom de flesta kunder inte vill vänta på fullständiga distributioner, vilket resulterar i en drop-off i kundanvändningen om ingen *Hot-instans* är tillgänglig.

**Kall**: Den här typen av instans representerar det totala antalet instanser som eventuellt kan distribueras per region. Kalla instanser kräver att hela Test Drive Resource Manager-mallen distribueras när en kund begär provkörningen, så *kalla* instanser är mycket långsammare att läsa in än *heta* instanser. Avvägningen är att du bara behöver betala under hela provkörningen, det körs *inte* alltid på din Azure-prenumeration som med en *Hot-instans.*

- **Azure Resource Manager-mall för testenhet:** Ladda upp ZIP som innehåller din Azure Resource Manager-mall.  Läs mer om hur du skapar en Azure Resource Manager-mall i snabbstartsartikeln [Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Provkörningslängd** (obligatoriskt): Ange hur lång tid provkörningen ska vara aktiv, i antal timmar. Provkörningen avslutas automatiskt när den här tidsperioden är. Denna varaktighet får endast fastställas med ett helt antal timmar (till exempel "2" timmar; "1.5" är ogiltigt).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Teknisk konfiguration för Dynamics 365 provkörning

Microsoft kan ta bort komplexiteten i att konfigurera en provkörning genom att vara värd för och underhålla tillhandahållande och distribution av tjänsten med den här typen av provkörning. Konfigurationen för den här typen av värdbaserade provkörningar är densamma oavsett om testenheten riktar sig till en Business Central-, Customer Engagement- eller Operations-målgrupp.

- **Max samtidiga provkörningar** (krävs): Ange det maximala antalet kunder som kan använda provkörningen samtidigt. Varje samtidig användare förbrukar en Dynamics 365-licens medan provkörningen är aktiv, så du måste se till att du har tillräckligt med licenser tillgängliga för att stödja den högsta angivna gränsen. Rekommenderat värde på 3-5.

- **Provkörningslängd** (obligatoriskt): Ange hur lång tid provkörningen ska vara aktiv genom att definiera antalet timmar. Efter så här många timmar avslutas sessionen och förbrukar inte längre någon av dina licenser. Vi rekommenderar ett värde på 2-24 timmar beroende på komplexiteten i ditt erbjudande. Denna varaktighet får endast fastställas med ett helt antal timmar (till exempel "2" timmar; "1.5" är ogiltigt). Användaren kan begära en ny session om de får på tid och vill komma åt provkörningen igen.

- **Instans-URL** (obligatoriskt): Webbadressen där kunden börjar sin provkörning. Vanligtvis url:en för din Dynamics 365-instans som kör `https://testdrive.crm.dynamics.com`din app med exempeldata installerade (till exempel ).

- **Instans webb-API URL** (krävs): Hämta webb-API URL för din Dynamics 365-instans genom att logga in på ditt Microsoft 365-konto och navigera till **Inställningar** \&gt; **Anpassning** \&gt; **Utvecklarresurser** \&gt; **Instanswebb-API (Tjänstrot-URL)**, kopiera url:en som finns här (till exempel `https://testdrive.crm.dynamics.com/api/data/v9.0`).

- **Rollnamn** (obligatoriskt): Ange det säkerhetsrollnamn som du har definierat i din anpassade Dynamics 365-provkörning. Det här säkerhetsrollnamnet tilldelas användaren under provkörningen (till exempel provkörningsrollen).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Teknisk konfiguration för Logic-apptestenhet

Alla anpassade produkter bör använda den här typen av distributionsmall för testkörning, som omfattar en mängd komplexa lösningsarkitekturer. Mer information om hur du konfigurerar Logic App-testenheter finns i [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) and [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) på GitHub.

- **Region** (obligatorisk listruta med ett urval): För närvarande finns det 26 Azure-stödda regioner där din provkörning kan göras tillgänglig. Resurserna för logikappen distribueras i den region du väljer. Om logikappen har anpassade resurser som lagras i en viss region kontrollerar du att regionen är markerad här. Det bästa sättet att göra det är att distribuera din Logic App lokalt på din Azure-prenumeration i portalen och kontrollera att den fungerar korrekt innan du gör det här valet.

- **Max samtidiga provkörningar** (krävs): Ange det maximala antalet kunder som kan använda provkörningen samtidigt. Dessa testenheter har redan distribuerats, vilket gör att kunderna kan komma åt dem direkt utan att vänta på en distribution.

- **Provkörningslängd** (obligatoriskt): Ange hur lång tid provkörningen ska vara aktiv, i antal timmar. Provkörningen avslutas automatiskt när den här tidsperioden är.

- **Namn på Azure-resursgrupp** (obligatoriskt): Ange [azure-resursgruppsnamnet](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) där din Logic App-provkörning sparas.

- **Azure logic app namn** (krävs): Ange namnet på Logic-appen som tilldelar provkörningen till användaren. Den här logic-appen måste sparas i Azure-resursgruppen ovan.

- **Avetablerar logikappnamn** (obligatoriskt): Ange namnet på logic-appen som avetablerar provkörningen när kunden är klar. Den här logic-appen måste sparas i Azure-resursgruppen ovan.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Teknisk konfiguration krävs inte för Power BI-testenheter

Produkter som vill demonstrera ett interaktivt Power BI-visuellt objekt kan använda en inbäddad länk för att dela en specialbyggd instrumentpanel som sin provkörning, ingen ytterligare teknisk konfiguration krävs. Läs mer om hur du konfigurerar Power BI-mallappar.[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)

### <a name="deployment-subscription-details"></a>Information om distributionsprenumeration

Skapa och tillhandahålla en separat, unik Azure-prenumeration (krävs inte för Power BI-testenheter) för att distribuera testenheten för din räkning.

- **Azure-prenumerations-ID** (krävs för Azure Resource Manager- och Logic-appar): Ange prenumerations-ID för att bevilja åtkomst till dina Azure-kontotjänster för resursanvändningsrapportering och fakturering. Vi rekommenderar att du överväger att [skapa en separat Azure-prenumeration](https://docs.microsoft.com/azure/billing/billing-create-subscription) som ska användas för testenheter om du inte redan har en. Du hittar ditt Azure-prenumerations-ID genom att logga in på [Azure-portalen](https://portal.azure.com/) och navigera till fliken **Prenumerationer** på menyn till vänster. Om du väljer fliken visas ditt prenumerations-ID (till exempel "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure AD-klient-ID** (obligatoriskt): Ange ditt Azure Active Directory (AD) [klient-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Om du vill hitta det här ID:t loggar du in på [Azure-portalen,](https://portal.azure.com/)väljer fliken Active Directory på menyn, väljer **Egenskaper**och letar sedan efter **katalog-ID-numret** som anges (till exempel 50c464d3-4930-494c-963c-1e951d15360e). Du kan också slå upp organisationens klient-ID med [https://www.whatismytenantid.com](https://www.whatismytenantid.com)din domännamnsadress på: .

- **Azure AD-klientnamn** (krävs för Dynamic 365): Ange ditt AD-namn (Azure Active Directory). För att hitta det här namnet loggar du in på [Azure-portalen](https://portal.azure.com/), i det övre högra hörnet kommer ditt klientnamn att visas under ditt kontonamn.

- **Azure AD-app-ID** (obligatoriskt): Ange ditt AZURE Active Directory (AD) [program-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Om du vill hitta det här ID:t loggar du in på [Azure-portalen,](https://portal.azure.com/)väljer fliken Active Directory på menyn till vänster, väljer **Appregistreringar**och letar sedan efter **program-ID-numret** som anges (till exempel 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-appklienthemma** (obligatoriskt): Ange din [Azure AD-programklienthemlighet](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets). Logga in på [Azure-portalen](https://portal.azure.com/)om du vill hitta det här värdet . Välj fliken **Azure Active Directory** på den vänstra menyn, välj Appregistreringar och välj sedan din provkörningsapp. **App registrations** Välj sedan **Certifikat och hemligheter**, välj Ny **klienthemlighet**, ange en beskrivning, välj **Aldrig** under Upphör **att gälla**och välj sedan Lägg **till**. Se till att kopiera ner värdet. Navigera inte bort från sidan innan du kopierar värdet eller så har du inte tillgång till värdet.)

### <a name="test-drive-marketplace-listings"></a>Listor över marketplace-testkörningar

Alternativet **Marketplace-listning** som finns på fliken **Testenhet** visar de språk på vilka provkörningen är tillgänglig. För närvarande är **engelska (USA)** den enda tillgängliga platsen. Välj språknamn för att ange information som beskriver provkörningsupplevelsen.

- **Beskrivning** (obligatoriskt): Beskriv din provkörning, vad som visas, mål för användaren att experimentera med, funktioner att utforska och all relevant information som hjälper användaren att avgöra om du vill hämta ditt erbjudande. Upp till 3 000 tecken text kan anges i det här fältet. 

- **Åtkomstinformation** (krävs för Azure Resource Manager och Logic testenheter): Förklara vad en kund behöver veta för att komma åt och använda den här testenheten. Gå igenom ett scenario för att använda ditt erbjudande och exakt vad kunden bör veta för att komma åt funktioner under hela provkörningen. Upp till 10 000 tecken text kan anges i det här fältet.

- **Användarhandbok** (krävs): En djupgående genomgång av din provkörningsupplevelse. Användarhandboken bör omfatta exakt vad du vill att kunden ska vinna på att uppleva provkörningen och fungera som referens för eventuella frågor som de kan ha. Filen måste vara i PDF-format och namnges (max 255 tecken) efter uppladdning.

- **Videor: Lägg till videor** (valfritt): Videor kan laddas upp till YouTube eller Vimeo och refereras här med en länk och miniatyrbild (533 x 324 pixlar) så att en kund kan visa en genomgång av information för att hjälpa dem att bättre förstå provkörningen, inklusive hur du framgångsrikt använder funktionerna i ditt erbjudande och förstår scenarier som belyser deras fördelar.
  - **Namn** (obligatoriskt)
  - **URL (endast YouTube eller Vimeo)** (obligatoriskt)
  - **Miniatyr (533 x 324px):** Bildfilen måste vara i PNG-format.

## <a name="supplemental-content"></a>Supplementellt innehåll

På den här sidan kan du ge ytterligare information om ditt erbjudande för att hjälpa oss att validera ditt erbjudande. Den här informationen visas inte för kunder eller publiceras på marknadsplatsen.

### <a name="key-usage-scenario"></a>Scenario för nyckelanvändning

Det är nödvändigt att ladda upp en PDF-fil som listar erbjudandets scenarier för nyckelanvändning som anges i ett dokument (.pdf-format). Alla scenarier som anges här kan verifieras av vårt valideringsteam innan vi godkänner ditt erbjudande för marknadsplatsen.

## <a name="publish"></a>Publicera

### <a name="submit-offer-to-preview"></a>Skicka erbjudande till förhandsgranskning

När du har slutfört alla nödvändiga delar av erbjudandet väljer du **publicera** i det övre högra hörnet av portalen. Du omdirigeras till sidan **Granska och publicera.**

Om det är första gången du publicerar det här erbjudandet kan du:

- Se slutförandestatusen för varje avsnitt i erbjudandet.
    - *Inte startad* - innebär att avsnittet inte har berörts och bör slutföras.
    - *Ofullständig* - innebär att avsnittet har fel som måste åtgärdas eller kräver mer information som ska tillhandahållas. Gå tillbaka till avsnittet/avsnitten och uppdatera det.
    - *Komplett* - betyder att avsnittet är komplett, alla nödvändiga data har lämnats och det finns inga fel. Alla delar av erbjudandet måste vara i ett fullständigt tillstånd innan du kan skicka erbjudandet.
- I avsnittet **Anteckningar för certifiering** tillhandahåller du testinstruktioner till certifieringsteamet för att säkerställa att din app testas korrekt, utöver eventuella kompletterande anteckningar som kan vara till hjälp för att förstå din app.
- Skicka erbjudandet för publicering genom att välja **Skicka**. Vi skickar ett e-postmeddelande till dig när en förhandsversion av erbjudandet är tillgänglig för dig att granska och godkänna. Gå tillbaka till Partner center och välj **Go-live** för erbjudandet att publicera till allmänheten (eller om ett privat erbjudande, till den privata målgruppen).

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Commercial Marketplace](./update-existing-offer.md)
