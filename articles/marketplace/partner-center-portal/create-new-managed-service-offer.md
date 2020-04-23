---
title: Skapa ett nytt erbjudande om managed service på den kommersiella marknadsplatsen
description: Så här skapar du ett nytt erbjudande om hanterade tjänster för listning på Azure Marketplace med hjälp av portalen Commercial Marketplace i Partner Center.
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c040907c6101d11ee645f9967278d3cf297bd74a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869804"
---
# <a name="create-a-new-managed-service-offer"></a>Skapa ett nytt erbjudande om hanterad tjänst

> [!IMPORTANT]
> Vi flyttar hanteringen av dina erbjudanden om hanterade tjänster från Cloud Partner Portal till Partner Center. Tills dina erbjudanden har migrerats följer du instruktionerna i [Publicera ett erbjudande om hanterad tjänst till Azure Marketplace](../../lighthouse/how-to/publish-managed-services-offers.md) för att hantera dina erbjudanden i Cloud Partner Portal.

Managed Service erbjuder hjälp för att aktivera [Azure Lighthouse-scenarier.](../../lighthouse/overview.md) När en kund accepterar ett erbjudande om hanterad tjänst kan de sedan använda inbyggda resurser för [Azure-delegerad resurshantering](../../lighthouse/concepts/azure-delegated-resource-management.md).

Om du vill börja skapa erbjudanden om hanterade tjänster kontrollerar du att du först [skapar ett Partner Center-konto](./create-account.md) och öppnar [instrumentpanelen för kommersiella marknadsplatser](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)med sidan **Översikt** markerad. Du måste ha en [kompetensnivå för Silver- eller Gold Cloud Platform](https://partner.microsoft.com/membership/cloud-platform-competency) eller vara en Azure Expert [MSP](https://partner.microsoft.com/membership/azure-expert-msp) för att kunna publicera ett erbjudande om hanterad tjänst.

![Instrumentpanelen för kommersiella marknadsplatser i Partner Center](./media/new-offer-overview.png)

>[!Note]
> När ett erbjudande har publicerats uppdateras redigeringarna av erbjudandet i Partner Center endast i systemet och lagrar fronter efter återpublicering. Se till att du skickar in erbjudandet för publicering när du har gjort ändringar.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

Välj knappen **+ Nytt erbjudande** och välj sedan **menyalternativet Hanterad tjänst.** Dialogrutan **Nytt erbjudande** visas.

### <a name="offer-id-and-alias"></a>Erbjudande-ID och alias

- **Erbjudande-ID:** Unik identifierare för varje erbjudande i ditt konto. Detta ID kommer att vara synligt för kunder i URL-adressen för marketplace-erbjudandet. Det här ID:t kan bara innehålla gemener alfanumeriska tecken (inklusive bindestreck och understreck, men inget blanktecken), begränsat till 50 tecken och kan inte ändras när du har valt **Skapa**.  Om du till exempel anger *test-erbjudande-1* här `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`blir erbjudandeadressen .

- **Offeralias**: Namnet som används för att referera till erbjudandet i partnercentret. Det här namnet används inte på marknadsplatsen och skiljer sig från erbjudandenamnet och andra värden som visas för kunderna. Det går inte att ändra det här värdet när du har valt **Skapa**.

När du har angett ditt **offer-ID** och **Offeralias**väljer du **Skapa**. Du kommer då att kunna arbeta med alla de olika delarna av ditt erbjudande.

## <a name="offer-setup"></a>Inställningar för erbjudande

På **inställningssidan** för Erbjudande frågar du efter följande information. Var noga med att välja **Spara** när du har slutfört dessa fält.

## <a name="connect-lead-management"></a>Anslut leadhantering

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Observera att enligt [certifieringsprinciperna för hanterade tjänster](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)krävs ett **leadmål.** Detta kommer att skapa en post i ditt CRM-system varje gång en kund distribuerar ditt erbjudande.

Mer information finns i [Översikt över leadhantering](./commercial-marketplace-get-customer-leads.md).

Kom ihåg att **spara** sidan innan du går vidare till nästa avsnitt.

## <a name="properties"></a>Egenskaper

På sidan **Egenskaper** kan du definiera de kategorier som används för att gruppera ditt erbjudande på marknadsplatsen och de juridiska kontrakt som stöder ditt erbjudande. Välj **Spara** när du har slutfört den här sidan.

### <a name="category"></a>Kategori

Välj minst en och högst fem kategorier som kommer att användas för att placera ditt erbjudande i lämpliga marknadsplats sökområden. Var noga med att ropa ut hur ditt erbjudande stöder dessa kategorier i erbjudandebeskrivningen.

### <a name="terms-and-conditions"></a>Villkor

Ange dina egna juridiska villkor i fältet **Regler och villkor.** Du kan också ange webbadressen där dina villkor finns. Kunderna måste acceptera dessa villkor innan de kan prova ditt erbjudande.

## <a name="offer-listing"></a>Lista över erbjudande

På sidan **Erbjudandelista** kan du definiera marknadsplatsinformation (erbjudandenamn, beskrivning, bilder, etc.) för ditt erbjudande.

> [!NOTE]
> Erbjudande notering innehåll (såsom beskrivning, dokument, skärmdumpar, användarvillkor, etc.) är inte skyldig att vara på engelska, så länge erbjudandet beskrivningen börjar med frasen"Denna ansökan är endast tillgänglig på [icke-engelska språket]." Det är också acceptabelt att ange en *användbar länk-URL* för att erbjuda innehåll på ett annat språk än det som används i innehåll för erbjudandelistor.

### <a name="name"></a>Namn

Namnet du anger här visas för kunderna som titeln på din erbjudandebeskrivning. Det här fältet är förifyllt med den text som du angav för **Offer alias** när du skapade erbjudandet, men du kan ändra det här värdet. Detta namn kan vara varumärkesskyddat (och du kan inkludera varumärkes- eller upphovsrättssymboler). Namnet får inte vara fler än 50 tecken och kan inte innehålla några emojis.

### <a name="search-results-summary"></a>Sammanfattning av sökresultat

Ge en kort beskrivning av ditt erbjudande (upp till 100 tecken), som kan användas i marknadsplats sökresultat.

### <a name="long-summary"></a>Lång sammanfattning

Ge en längre beskrivning av ditt erbjudande (upp till 256 tecken). Den här långa sammanfattningen kan också användas i marketplace-sökresultat.

### <a name="description"></a>Beskrivning

Ge en längre beskrivning av ditt erbjudande (upp till 3 000 tecken). Den här beskrivningen visas för kunder i översikten för marknadsplatslistning. Inkludera erbjudandets värdeerbjudande, viktiga fördelar, kategori- och/eller branschorganisationer, köpmöjligheter i appen och eventuella nödvändiga upplysningar.

Några tips för att skriva din beskrivning:  

- Beskriv tydligt erbjudandets värdeerbjudande i de första meningarna i din beskrivning. Inkludera följande objekt i ditt värdeerbjudande:
  - Beskrivning av erbjudandet
  - Den typ av användare som drar nytta av erbjudandet
  - Kundens behov eller smärta som erbjudandet adresserar
- Tänk på att de första meningarna kan visas i sökmotorresultat.  
- Lita inte på funktioner och funktioner för att sälja din produkt. Fokusera istället på det värde du levererar.  
- Använd branschspecifik vokabulär eller förmånsbaserad formulering så mycket som möjligt.

Om du vill göra erbjudandebeskrivningen mer engagerande använder du RTF-redigeraren för att formatera beskrivningen.

![Använda RTF-redigeraren](./media/text-editor2.png)

Använd följande instruktioner för att använda RTF-redigeraren:

- Om du vill ändra innehållets format markerar du den text som du vill formatera och markerar ett textformat, som visas nedan:

     ![Använda RTF-redigeraren för att ändra textformat](./media/text-editor3.png)

- Om du vill lägga till en punktlista eller numrerad lista i texten använder du alternativen nedan:

     ![Använda RTF-redigeraren för att lägga till listor](./media/text-editor4.png)

- Om du vill lägga till eller ta bort indrag i texten använder du alternativen nedan:

     ![Använda RTF-redigeraren för att dra in](./media/text-editor5.png)

### <a name="privacy-policy-link"></a>Länk till sekretesspolicy

Ange webbadressen till organisationens sekretesspolicy (finns på din webbplats). Du är ansvarig för att din app följer sekretesslagar och sekretessregler och för att tillhandahålla en giltig sekretesspolicy.

### <a name="useful-links"></a>Användbara länkar

Tillhandahålla extra kompletterande onlinedokument om din lösning. Lägg till ytterligare användbara länkar genom att klicka på **+ Lägg till en länk**.

### <a name="contact-information"></a>Kontaktinformation

I det här avsnittet måste du ange namn, e-post och telefonnummer för en **supportkontakt** och en **teknisk kontakt**. Den här informationen visas inte för kunder, men kommer att vara tillgänglig för Microsoft och kan tillhandahållas CSP-partner.

### <a name="support-urls"></a>Stöd webbadresser

Om du har supportwebbplatser för **Azure Global Customers** och/eller Azure **Government-kunder**anger du dessa webbadresser här.

### <a name="marketplace-images"></a>Marketplace-bilder

I det här avsnittet kan du ange logotyper och bilder som ska användas när du visar ditt erbjudande till kunden. Alla bilder måste vara i PNG-format.

>[!Note]
>Om du har problem med att ladda upp filer https://upload.xboxlive.com kontrollerar du att det lokala nätverket inte blockerar tjänsten som används av Partner Center.

#### <a name="marketplace-logos"></a>Marketplace-logotyper

Fyra logotypstorlekar krävs: **Små (40x40),** **Medium (90x90),** **Stor (115x115)** och **Bred (255x115)**. Följ dessa riktlinjer för dina logotyper:

- Azure-designen har en enkel färgpalett. Begränsa antalet primära och sekundära färger i logotypen.
- Portalens temafärger är svart och vitt. Använd inte de färgerna som bakgrundsfärg för logotypen. Använd en färg som gör att logotyp framträder tydligt i portalen. Vi rekommenderar enkla primärfärger.
- Om du använder en genomskinlig bakgrund måste du se till att logotypen och texten inte är vit, svart eller blå.
- Logotypens utseende och känsla bör vara platt och inte innehålla toningar. Använd inte en tonad bakgrund för logotypen.
- Placera inte text på logotypen – inte ens företagets eller varumärkets namn.
- Se till att logotypen inte har sträckts ut.

#### <a name="screenshots"></a>Skärmbilder

Lägg till upp till fem skärmbilder som visar hur erbjudandet fungerar. Alla skärmdumpar måste vara 1280 x 720 pixlar.

#### <a name="videos"></a>Videoklipp

Du kan också lägga till upp till fem videor som visar ditt erbjudande. Dessa videor bör finnas på YouTube och/eller Vimeo. För var och en anger du videons namn, webbadress och en miniatyrbild av videon (1 280 x 720 pixlar).

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare resurser för marknadsplatsnotering

- [Metodtips för marknadsplatserbjudanden](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Förhandsversion

Innan du publicerar ditt erbjudande live på det bredare marketplace-erbjudandet måste du först göra det tillgängligt för en begränsad förhandsgranskningspublik. På så sätt kan du bekräfta hur du erbjuder visas på Azure Marketplace innan du gör det tillgängligt för kunder. Microsofts support- och teknikteam kan också se ditt erbjudande under den här förhandsgranskningsperioden.

Du kan definiera förhandsversionen av målgruppen genom att ange Azure-prenumerations-ID:er i avsnittet **Förhandsgranska målgrupp.** Du kan ange upp till 10 prenumerations-ID manuellt eller ladda upp en CSV-fil med upp till 100 prenumerations-ID: n.

Alla kunder som är associerade med dessa prenumerationer kan visa erbjudandet på Azure Marketplace innan det publiceras. Var noga med att inkludera dina egna prenumerationer här så att du kan förhandsgranska ditt erbjudande.

## <a name="plan-overview"></a>Översikt över plan

Varje erbjudande måste ha en eller flera planer (kallas ibland SKU:er). Du kan lägga till flera abonnemang för att stödja olika funktionsuppsättningar till olika priser eller för att anpassa en viss plan för en begränsad målgrupp av specifika kunder. Kunder kan visa de planer som är tillgängliga för dem under det överordnade erbjudandet.

På sidan **Planera översikt** väljer du + Skapa **ny plan**. Ange sedan ett **plan-ID** och ett **plannamn**. Båda dessa värden kan bara innehålla gemener alfanumeriska tecken, streck och understreck, med högst 50 tecken. Dessa värden kan vara synliga för kunder och de kan inte ändras när du har publicerat erbjudandet.

Välj **Skapa** när du har angett dessa värden om du vill fortsätta arbeta med planen. Det finns tre avsnitt att slutföra: **Planera listning,** **Prissättning och tillgänglighet**och Teknisk **konfiguration**.

### <a name="plan-listing"></a>Planera listning

Ange först en **sammanfattning av sökresultaten** för planen. Detta är en kort beskrivning av din plan (upp till 100 tecken), som kan användas i marknadsplats sökresultat.

Ange sedan en **beskrivning** som ger en mer detaljerad förklaring av planen.

### <a name="pricing-and-availability"></a>Priser och tillgänglighet

För närvarande finns det bara en prismodell som kan användas för Managed Service-erbjudande: **Ta med din egen licens (BYOL).** Det innebär att du debiterar dina kunder direkt för kostnader i samband med detta erbjudande, och Microsoft tar inte ut några avgifter till dig.

I avsnittet **Planera synlighet** kan du ange om den här planen ska vara [privat](../../marketplace/private-offers.md). Om du lämnar rutan **Det här är en privat plan** omarkerad begränsas inte planen till specifika kunder (eller till ett visst antal kunder).

Om du bara vill göra den här planen tillgänglig för specifika kunder väljer du **Ja**. När du gör det måste du identifiera kunderna genom att ange deras prenumerations-ID. Dessa kan anges en efter en (för upp till 10 prenumerationer) eller genom att ladda upp en CSV-fil (för högst 10 000 prenumerationer i alla abonnemang). Var noga med att inkludera dina egna prenumerationer här så att du kan testa och validera erbjudandet.

> [!IMPORTANT]
> När en plan har publicerats som offentlig kan du inte ändra den till privat. Om du vill styra vilka kunder som kan acceptera erbjudandet och delegera resurser använder du en privat plan. Med en offentlig plan kan du inte begränsa tillgängligheten till vissa kunder eller ens till ett visst antal kunder (även om du kan sluta sälja planen helt om du väljer att göra det). Du kan [ta bort åtkomsten till en delegering](../../lighthouse/how-to/onboard-customer.md#remove-access-to-a-delegation) när en kund bara har accepterat ett erbjudande om du har inkluderat ett **auktorisering med** **rolldefinitionen** inställd på [Borttagning av tilldelning](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) av hanterade tjänster när du publicerade erbjudandet. Du kan också nå ut till kunden och be dem att [ta bort din åtkomst](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

### <a name="technical-configuration"></a>Teknisk konfiguration

I det här avsnittet i planen skapas ett manifest med auktoriseringsinformation för hantering av kundresurser. Den här informationen krävs för att aktivera [Azure-delegerad resurshantering](../../lighthouse/concepts/azure-delegated-resource-management.md).

Var noga med att granska [klienter, roller och användare i Azure Lighthouse-scenarier](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) för att förstå vilka roller som stöds och de bästa metoderna för att definiera dina auktoriseringar.

> [!NOTE]
> Som nämnts ovan gäller användare och roller i **dina auktoriseringstransaktioner** för varje kund som köper planen. Om du vill begränsa åtkomsten till en viss kund måste du publicera en privat plan för deras exklusiva användning.

#### <a name="manifest"></a>Manifest

Ange först en **version** för manifestet. Använd formatet *n.n.n* (till exempel 1.2.5).

Ange sedan ditt **klient-ID**. Detta är ett GUID som är associerat med Azure Active Directory (Azure AD) klient-ID för din organisation; det vill säga den hanterande klient som du kommer åt dina kunders resurser från. Om du inte har det här praktiskt kan du hitta det genom att hovra över ditt kontonamn på den övre högra sidan av Azure-portalen, eller genom att välja **Växla katalog**.

Om du publicerar en ny version av erbjudandet och behöver skapa ett uppdaterat manifest väljer du **+ Nytt manifest**. Var noga med att öka versionsnumret från den tidigare manifestversionen.

#### <a name="authorization"></a>Auktorisering

Auktoriseringar definierar entiteterna i din hantering av klienten som kan komma åt resurser och prenumerationer för kunder som köper planen. Var och en av dessa entiteter tilldelas en inbyggd roll som ger specifika åtkomstnivåer.

Du kan skapa upp till tjugo auktoriseringar för varje plan.

> [!TIP]
> I de flesta fall vill du tilldela roller till en Azure AD-användargrupp eller tjänsthuvudnamn, i stället för till en serie enskilda användarkonton. På så sätt kan du lägga till eller ta bort åtkomst för enskilda användare utan att behöva uppdatera och publicera om planen när åtkomstkraven ändras. När du tilldelar roller till Azure AD-grupper ska [du se till att **grupptypen** är **Säkerhet** och inte **Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Ytterligare rekommendationer finns i [Klienter, roller och användare i Azure Lighthouse-scenarier](../../lighthouse/concepts/tenants-users-roles.md).

För varje **auktorisering**måste du ange följande. Du kan sedan välja **+ Lägg till auktorisering** så många gånger som behövs för att lägga till fler användare och rolldefinitioner.

- **Azure AD-objekt-ID:** Azure AD-identifieraren för en användare, användargrupp eller ett program som kommer att beviljas vissa behörigheter (enligt definitionen i rolldefinitionen) till dina kunders resurser.
- **Visningsnamn för Azure AD-objekt:** Ett eget namn som hjälper kunden att förstå syftet med den här auktoriseringen. Kunden ser det här namnet när du delegerar resurser.
- **Rolldefinition**: Välj en av de tillgängliga Azure AD-inbyggda rollerna i listan. Den här rollen avgör vilka behörigheter som användaren i fältet **Azure AD Object ID** ska ha på dina kunders resurser. Beskrivningar av dessa roller finns i Inbyggda roller och [rollstöd för Azure-delegerad resurshantering](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management). [Built-in roles](../../role-based-access-control/built-in-roles.md)
  > [!NOTE]
  > När nya inbyggda roller läggs till i Azure blir de tillgängliga här, även om det kan finnas vissa fördröjningar innan de visas.
- **Tilldelar roller**: Det här alternativet visas bara om du har valt Användaråtkomstadministratör i **rolldefinitionen** för den här auktoriseringen. I så fall måste du lägga till en eller flera tilldelningsbara roller här. Användaren i fältet **Azure AD Object ID** kan tilldela dessa roller till [hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md), vilket krävs för att [distribuera principer som kan åtgärdas](../../lighthouse/how-to/deploy-policy-remediation.md). Observera att inga andra behörigheter som normalt associeras med rollen Administratör för användaråtkomst gäller för den här användaren.

> [!TIP]
> Om du vill vara säkra på att du kan [ta bort åtkomsten till en delegering](../../lighthouse/how-to/onboard-customer.md#remove-access-to-a-delegation) om det behövs tar du med en **auktorisering** med **rolldefinitionen** som angetts till [Rollen Borttagning av tilldelning](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)av hanterade tjänster . Om den här rollen inte tilldelas kan delegerade resurser endast tas bort av en användare i kundens klientorganisation.

När du har slutfört alla avsnitt för din plan kan du välja **+ Skapa ny plan** så många gånger som du behöver för att skapa ytterligare planer. När du är klar väljer du **Spara**.

## <a name="publish"></a>Publicera

### <a name="submit-offer-to-preview"></a>Skicka erbjudande till förhandsgranskning

När du har slutfört alla nödvändiga delar av erbjudandet väljer du **publicera** i det övre högra hörnet av portalen. Du omdirigeras till sidan **Granska och publicera.**

Om det är första gången du publicerar det här erbjudandet kan du:

- Se slutförandestatusen för varje avsnitt i erbjudandet.
  - *Inte startad* - innebär att avsnittet inte har berörts och måste slutföras.
  - *Ofullständig* - innebär att avsnittet har fel som måste åtgärdas eller kräver mer information som ska tillhandahållas. Gå tillbaka till avsnittet/avsnitten och uppdatera det.
  - *Komplett* - betyder att avsnittet är komplett, alla nödvändiga data har lämnats och det finns inga fel. Alla delar av erbjudandet måste vara i ett fullständigt tillstånd innan du kan skicka erbjudandet.
- I avsnittet **Anteckningar för certifiering** tillhandahåller du testinstruktioner till certifieringsteamet för att säkerställa att din app testas korrekt, utöver eventuella kompletterande anteckningar som kan vara till hjälp för att förstå din app.
- Skicka erbjudandet för publicering genom att välja **Skicka**. Vi skickar ett e-postmeddelande när en förhandsversion av erbjudandet är tillgänglig för dig att granska och godkänna. Gå tillbaka till Partner Center och välj **Go-live** för erbjudandet att publicera ditt erbjudande till allmänheten (eller om ett privat erbjudande, till den privata målgruppen).

### <a name="customer-experience-and-offer-management"></a>Kundupplevelse och erbjudandehantering

När en kund distribuerar ditt erbjudande kan de delegera prenumerationer eller resursgrupper för [Azure-delegerad resurshantering](../../lighthouse/concepts/azure-delegated-resource-management.md). Mer information om den här processen finns i [Den inbyggda kunden.](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process)

Du kan när som helst [publicera en uppdaterad version av erbjudandet.](update-existing-offer.md) Du kanske till exempel vill lägga till en ny rolldefinition i ett tidigare publicerat erbjudande. När du gör det ser kunder som redan har lagt till erbjudandet en ikon på sidan [**Tjänstleverantörer**](../../lighthouse/how-to/view-manage-service-providers.md) i Azure-portalen som låter dem veta att en uppdatering är tillgänglig. Varje kund kommer att kunna granska ändringarna och bestämma om de vill uppdatera till den nya versionen.

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Commercial Marketplace](./update-existing-offer.md)
- [Läs mer om Azure Lighthouse](../../lighthouse/overview.md)