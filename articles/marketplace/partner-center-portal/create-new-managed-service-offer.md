---
title: Skapa ett nytt hanterat tjänst erbjudande på den kommersiella Marketplace
description: Så här skapar du ett nytt hanterat tjänst erbjudande för List på Azure Marketplace med hjälp av den kommersiella Marketplace-portalen i Partner Center.
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c82c3e0b6e6d11ccc33bf8556b06aeb04a6d37b5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147912"
---
# <a name="create-a-new-managed-service-offer"></a>Skapa ett nytt hanterat tjänst erbjudande

> [!IMPORTANT]
> Vi håller på att flytta hanteringen av dina hanterade tjänst erbjudanden från Cloud Partner Portal till Partner Center. Innan du har migrerat dina erbjudanden följer du anvisningarna i [publicera ett hanterat tjänst erbjudande på Azure Marketplace](../../lighthouse/how-to/publish-managed-services-offers.md) för att hantera dina erbjudanden i Cloud Partner Portal.

I den hanterade tjänsten får du hjälp att aktivera [Azure Lighthouse](../../lighthouse/overview.md) -scenarier. När en kund accepterar ett hanterat tjänst erbjudande kan de publicera resurser för [Azure-delegerad resurs hantering](../../lighthouse/concepts/azure-delegated-resource-management.md).

För att börja skapa hanterade tjänst erbjudanden måste du först [skapa ett partner Center-konto](./create-account.md) och öppna [instrument panelen för instrument panelen för instrument panelen](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)med **översikts** sidan vald. Du måste ha en [silver eller Gold-plattform för moln plattform](https://partner.microsoft.com/membership/cloud-platform-competency) eller vara en [Azure expert-MSP](https://partner.microsoft.com/membership/azure-expert-msp) för att publicera ett hanterat tjänst erbjudande.

![Instrument panel för extern Marketplace på Partner Center](./media/new-offer-overview.png)

>[!Note]
> När ett erbjudande har publicerats uppdateras bara ändringar i erbjudandet som gjorts i Partner Center i systemet och Store-frontend efter ompubliceringen. Kontrol lera att du har skickat in erbjudandet för publicering när du har gjort ändringar.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

Välj knappen **+ ny erbjudande** och välj sedan meny alternativet **hanterad tjänst** . Dialog rutan **nytt erbjudande** visas.

### <a name="offer-id-and-alias"></a>Erbjudande-ID och alias

- **Erbjudande-ID**: unik identifierare för varje erbjudande i ditt konto. Detta ID visas för kunder i URL-adressen för Marketplace-erbjudandet. Detta ID får bara innehålla gemena alfanumeriska tecken (inklusive bindestreck och under streck, men inget blank steg), begränsat till 50 tecken och kan inte ändras när du har valt **skapa**.  Om du till exempel anger *test-erbjudandet-1* här, är erbjudande-URL: en `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

- **Erbjudande Ali Aset**: namnet som används för att referera till erbjudandet i Partner Center. Det här namnet används inte på Marketplace och skiljer sig från namnet på erbjudandet och andra värden som ska visas för kunderna. Det går inte att ändra det här värdet när du har valt **skapa**.

När du har angett ditt **erbjudande-ID** och **erbjud alias**väljer du **skapa**. Du kommer sedan att kunna arbeta med alla olika delar av ditt erbjudande.

## <a name="offer-setup"></a>Erbjudande konfiguration

På sidan **erbjudande konfiguration** ber vi om följande information. Se till att välja **Spara** när du är klar med fälten.

## <a name="connect-lead-management"></a>Anslut lead-hantering

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Observera att det krävs ett **lead-mål** enligt [certifierings principerna för hanterade tjänster](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services). Då skapas en post i ditt CRM-system varje gång en kund distribuerar ditt erbjudande.

Mer information finns i [Översikt över hantering av leads](./commercial-marketplace-get-customer-leads.md).

Kom ihåg att **Spara** sidan innan du går vidare till nästa avsnitt.

## <a name="properties"></a>Egenskaper

På sidan **Egenskaper** kan du definiera de kategorier som används för att gruppera ditt erbjudande på Marketplace och de juridiska avtal som stöder ditt erbjudande. Välj **Spara** när du är klar med den här sidan.

### <a name="category"></a>Kategori

Välj minst en och högst fem kategorier som ska användas för att placera ditt erbjudande i lämpliga Sök områden för Marketplace. Var noga med att ta reda på hur ditt erbjudande stöder dessa kategorier i beskrivningen av erbjudandet.

### <a name="terms-and-conditions"></a>Villkor

Ange dina egna juridiska villkor i fältet **allmänna** villkor. Du kan också ange URL: en där du hittar dina villkor. Kunder måste acceptera dessa villkor innan de kan testa ditt erbjudande.

## <a name="offer-listing"></a>Erbjudande lista

På sidan **erbjudande lista** kan du definiera Marketplace-information (erbjudande namn, beskrivning, bilder osv.) för ditt erbjudande.

> [!NOTE]
> Erbjudande för List innehåll (till exempel beskrivning, dokument, skärm bilder, användnings villkor osv.) behöver inte vara på engelska, förutsatt att beskrivningen börjar med frasen, "det här programmet är endast tillgängligt på [språk som inte är engelska]". Det är också acceptabelt att tillhandahålla en *användbar länk-URL* för att erbjuda innehåll på ett annat språk än det som används i innehålls förteckningen för erbjudandet.

### <a name="name"></a>Name

Det namn som du anger här visas för kunder som rubrik på din erbjudande lista. Det här fältet fylls i automatiskt med texten du angav för **erbjud alias** när du skapade erbjudandet, men du kan ändra det här värdet. Det här namnet kan vara ett varumärke (och du kan inkludera varumärkes-eller Copyright-symboler). Namnet får innehålla högst 50 tecken och får inte innehålla några emojis.

### <a name="search-results-summary"></a>Sammanfattning av Sök Resultat

Ange en kort beskrivning av ditt erbjudande (upp till 100 tecken) som kan användas i Sök resultaten för Marketplace.

### <a name="long-summary"></a>Lång Sammanfattning

Ange en längre Beskrivning av ditt erbjudande (upp till 256 tecken). Den här långa sammanfattningen kan också användas i Sök resultaten för Marketplace.

### <a name="description"></a>Beskrivning

Ange en längre Beskrivning av ditt erbjudande (upp till 3 000 tecken). Den här beskrivningen visas för kunder i Översikt över Marketplace-registrering. Ta med ditt erbjudandes värde, viktiga fördelar, kategori-och/eller bransch associationer, inköps möjligheter för appar och eventuella uppgifter som krävs.

Några tips för att skriva din beskrivning:  

- Beskriv tydligt ditt erbjudande värde i de första meningarna i beskrivningen. Ta med följande objekt i ditt värde förslag:
  - Beskrivning av erbjudandet
  - Typ av användare som fördelar från erbjudandet
  - Kundernas behov eller smärta på erbjudandet
- Tänk på att de första meningarna kan visas i Sök motorns resultat.  
- Förlita dig inte på funktioner och funktioner för att sälja produkten. Fokusera i stället på det värde som du levererar.  
- Använd branschspecifika vokabulär eller kunskapsbaserade formuleringar så mycket som möjligt.

Om du vill göra din erbjudande Beskrivning mer engagerande använder du RTF-redigeraren för att formatera beskrivningen.

![Använda Rich Text Editor](./media/text-editor2.png)

Använd följande instruktioner för att använda Rich Text Editor:

- Om du vill ändra formatet på ditt innehåll markerar du den text som du vill formatera och väljer ett text format enligt nedan:

     ![Använda RTF-redigeraren för att ändra text format](./media/text-editor3.png)

- Om du vill lägga till en punkt lista eller numrerad lista i texten använder du alternativen nedan:

     ![Använda RTF-redigeraren för att lägga till listor](./media/text-editor4.png)

- Om du vill lägga till eller ta bort indrag i texten använder du alternativen nedan:

     ![Använda RTF-redigeraren för att dra in text](./media/text-editor5.png)

### <a name="privacy-policy-link"></a>Länk till sekretess policy

Ange URL: en till din organisations sekretess policy (som finns på din webbplats). Du ansvarar för att se till att appen uppfyller sekretess lagar och föreskrifter och att tillhandahålla en giltig sekretess policy.

### <a name="useful-links"></a>Användbara länkar

Ange valfria kompletterande online-dokument om din lösning. Lägg till ytterligare användbara länkar genom att klicka på **+ Lägg till en länk**.

### <a name="contact-information"></a>Kontaktinformation

I det här avsnittet måste du ange namn, e-postadress och telefonnummer för en **support kontakt** och en **teknisk kontakt**. Den här informationen visas inte för kunder, men är tillgänglig för Microsoft och kan tillhandahållas till CSP-partner.

### <a name="support-urls"></a>Support-URL: er

Om du har support webbplatser för **globala Azure-kunder** och/eller **Azure Government kunder**kan du ange dessa URL: er här.

### <a name="marketplace-images"></a>Marketplace-avbildningar

I det här avsnittet kan du ange logo typer och avbildningar som ska användas när du visar ditt erbjudande för kunden. Alla bilder måste vara i PNG-format.

>[!Note]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar https://upload.xboxlive.com tjänsten som används av Partner Center.

#### <a name="marketplace-logos"></a>Marketplace-logotyper

Fyra logo typer krävs: **liten (40x40)**, **medium (90x90)**, **Large (115x115)** och **wide (255x115)**. Följ dessa rikt linjer för dina logo typer:

- Azure-designen har en enkel färgpalett. Begränsa antalet primära och sekundära färger i logotypen.
- Portalens temafärger är svart och vitt. Använd inte de färgerna som bakgrundsfärg för logotypen. Använd en färg som gör att logotyp framträder tydligt i portalen. Vi rekommenderar enkla primärfärger.
- Om du använder en genomskinlig bakgrund måste du se till att logotypen och texten inte är vit, svart eller blå.
- Logotypens utseende och känsla bör vara platt och inte innehålla toningar. Använd inte en tonad bakgrund för logotypen.
- Placera inte text på logotypen – inte ens företagets eller varumärkets namn.
- Se till att logotypen inte har sträckts ut.

#### <a name="screenshots"></a>Skärmbilder

Lägg till upp till fem skärm bilder som visar hur ditt erbjudande fungerar. Alla skärm dum par måste vara 1280 x 720 bild punkter.

#### <a name="videos"></a>Videoklipp

Du kan också lägga till upp till fem videor som demonstrerar ditt erbjudande. Dessa videor bör finnas på YouTube och/eller Vimeo. För var och en anger du videons namn, URL och en miniatyr bild av videon (1280 x 720 pixlar).

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare platser för Marketplace-lista

- [Metod tips för Marketplace-erbjudanden](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Förhandsversion

Innan du publicerar erbjudandet Live på det bredare Marketplace-erbjudandet måste du först göra det tillgängligt för en begränsad förhands gransknings publik. På så sätt kan du kontrol lera hur du erbjuder den på Azure Marketplace innan du gör den tillgänglig för kunderna. Microsofts support-och teknik team kommer också att kunna se ditt erbjudande under för hands perioden.

Du kan definiera förhands gransknings mål genom att ange Azures prenumerations-ID i avsnittet för **hands version** . Du kan ange upp till 10 prenumerations-ID: n manuellt eller ladda upp en CSV-fil med upp till 100 prenumerations-ID.

Alla kunder som är kopplade till dessa prenumerationer kan se erbjudandet på Azure Marketplace innan det blir Live. Se till att ta med dina egna prenumerationer här så att du kan förhandsgranska ditt erbjudande.

## <a name="plan-overview"></a>Plan översikt

Varje erbjudande måste ha en eller flera planer (kallas ibland SKU: er). Du kan lägga till flera planer för att stödja olika funktions uppsättningar i olika priser eller för att anpassa en viss plan för en begränsad mål grupp för vissa kunder. Kunder kan se de planer som är tillgängliga för dem under det överordnade erbjudandet.

På sidan **plan översikt** väljer du **+ Skapa ny plan**. Ange sedan ett **plan-ID** och ett **plan namn**. Båda värdena får bara innehålla gemena alfanumeriska tecken, bindestreck och under streck, med högst 50 tecken. Dessa värden kan vara synliga för kunder och de kan inte ändras när du har publicerat erbjudandet.

Välj **skapa** när du har angett dessa värden för att fortsätta arbeta med planen. Det finns tre avsnitt att slutföra: **Planera lista**, **prissättning och tillgänglighet**samt **teknisk konfiguration**.

### <a name="plan-listing"></a>Plan lista

Börja med att ange en **Sammanfattning av Sök resultatet** för planen. Det här är en kort beskrivning av din plan (upp till 100 tecken) som kan användas i Sök resultaten för Marketplace.

Ange sedan en **Beskrivning** som ger en mer detaljerad förklaring av planen.

### <a name="pricing-and-availability"></a>Priser och tillgänglighet

För närvarande finns det bara en pris modell som kan användas för Managed Service-erbjudande: **Bring Your Own License (BYOL)**. Det innebär att du debiteras kunderna direkt för kostnader som rör erbjudandet, och Microsoft debiterar inte några avgifter till dig.

I avsnittet **plan synlighet** kan du ange om den här planen ska vara [privat](../../marketplace/private-offers.md). Om du lämnar kryss rutan **privat plan** omarkerad är din prenumeration inte begränsad till specifika kunder (eller till ett visst antal kunder).

Välj **Ja**om du vill att den här planen bara är tillgänglig för vissa kunder. När du gör det måste du identifiera kunderna genom att ange deras prenumerations-ID. De kan anges en i taget (för upp till 10 prenumerationer) eller genom att ladda upp en. csv-fil (högst 10 000 prenumerationer i alla planer). Se till att ta med dina egna prenumerationer här så att du kan testa och validera erbjudandet.

> [!IMPORTANT]
> När en plan har publicerats som offentlig kan du inte ändra den till privat. Använd en privat plan för att kontrol lera vilka kunder som kan acceptera ditt erbjudande och delegera resurser. Med en offentlig plan kan du inte begränsa tillgänglighet till vissa kunder eller till och med ett visst antal kunder (även om du kan sluta sälja planen helt om du väljer att göra det). Du kan [ta bort åtkomsten till en delegering efter att](../../lighthouse/how-to/remove-delegation.md) en kund har accepterat ett erbjudande endast om du har inkluderat en **auktorisering** med **roll definitionen** för [tilldelning av hanterade tjänster för registrering av hanterade tjänster](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) när du publicerade erbjudandet. Du kan också kontakta kunden och be dem att [ta bort åtkomsten](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

### <a name="technical-configuration"></a>Teknisk konfiguration

I det här avsnittet av planen skapas ett manifest med auktoriseringsinformation för hantering av kund resurser. Den här informationen krävs för att aktivera [Azure-delegerad resurs hantering](../../lighthouse/concepts/azure-delegated-resource-management.md).

Se till att granska [klienter, roller och användare i Azure Lighthouse-scenarier](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) för att förstå vilka roller som stöds och de bästa metoderna för att definiera dina auktoriseringar.

> [!NOTE]
> Som anges ovan gäller användare och roller i dina **Authorization** -poster för alla kunder som köper planen. Om du vill begränsa åtkomsten till en specifik kund måste du publicera en privat plan för deras exklusiva användning.

#### <a name="manifest"></a>Manifest

Ange först en **version** för manifestet. Använd formatet *n. n. n* (till exempel 1.2.5).

Ange sedan **klient-ID: t**. Detta är ett GUID som är kopplat till organisationens ID för Azure Active Directory (Azure AD). det vill säga den hanterings klient som du kommer att få åtkomst till dina kunders resurser. Om du inte har det här är praktiskt kan du hitta det genom att hovra över ditt konto namn längst upp till höger i Azure Portal eller genom att välja **Växla katalog**.

Om du publicerar en ny version av erbjudandet och behöver skapa ett uppdaterat manifest väljer du **+ nytt manifest**. Se till att öka versions numret från den tidigare manifest versionen.

#### <a name="authorization"></a>Auktorisering

Auktoriseringarna definierar de entiteter i din hanterings klient som har åtkomst till resurser och prenumerationer för kunder som köper planen. Var och en av dessa entiteter tilldelas en inbyggd roll som ger vissa åtkomst nivåer.

Du kan skapa upp till tjugo auktoriseringar för varje plan.

> [!TIP]
> I de flesta fall vill du tilldela roller till en Azure AD-användargrupp eller tjänstens huvud namn i stället för till en serie med enskilda användar konton. På så sätt kan du lägga till eller ta bort åtkomst för enskilda användare utan att behöva uppdatera och publicera om planen när dina åtkomst krav ändras. När du tilldelar roller till Azure AD-grupper måste du [Se till att **grupp typen** är **säkerhet** och inte **Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Ytterligare rekommendationer finns i [klienter, roller och användare i Azure Lighthouse-scenarier](../../lighthouse/concepts/tenants-users-roles.md).

För varje **auktorisering**måste du ange följande. Du kan sedan välja **+ Lägg till auktorisering** så många gånger som behövs för att lägga till fler användare och roll definitioner.

- **Azure AD-objekt-ID**: Azure AD-identifieraren för en användare, användar grupp eller ett program som kommer att beviljas vissa behörigheter (enligt definitionen i roll definitionen) till dina kunders resurser.
- **Visnings namn för Azure AD-objekt**: ett eget namn som hjälper kunden att förstå syftet med den här auktoriseringen. Kunden ser det här namnet när resurser delegeras.
- **Roll definition**: Välj en av de tillgängliga inbyggda Azure AD-rollerna i listan. Den här rollen avgör de behörigheter som användaren i fältet **Azure AD-objekt-ID** kommer att ha på dina kunders resurser. Beskrivningar av dessa roller finns [inbyggda roller](../../role-based-access-control/built-in-roles.md) och [roll stöd för Azure-delegerad resurs hantering](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management).
  > [!NOTE]
  > När så är tillämpligt nya inbyggda roller läggs till i Azure blir de tillgängliga här, även om det kan uppstå en fördröjning innan de visas.
- **Tilldelnings bara roller**: det här alternativet visas bara om du har valt administratör för användar åtkomst i **roll definitionen** för den här auktoriseringen. I så fall måste du lägga till en eller flera tilldelnings bara roller här. Användaren i fältet **Azure AD-objekt-ID** kommer att kunna tilldela dessa roller till [hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md), vilket krävs för att [distribuera principer som kan åtgärdas](../../lighthouse/how-to/deploy-policy-remediation.md). Observera att inga andra behörigheter som normalt är kopplade till rollen administratör för användar åtkomst gäller för den här användaren.

> [!TIP]
> För att se till att du kan [ta bort åtkomsten till en delegering](../../lighthouse/how-to/remove-delegation.md) , måste du ta med en **auktorisering** med **roll definitionen** inställd på [ta bort roll tilldelning för hanterade tjänster](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Om den här rollen inte är tilldelad kan delegerade resurser bara tas bort av en användare i kundens klient organisation.

När du har slutfört alla avsnitt för din plan kan du välja **+ skapa en ny plan** så många gånger som du behöver för att skapa ytterligare planer. När du är klar väljer du **Spara**.

## <a name="publish"></a>Publicera

### <a name="submit-offer-to-preview"></a>Skicka erbjudande till för hands version

När du har slutfört alla obligatoriska avsnitt i erbjudandet väljer du **publicera** i det övre högra hörnet i portalen. Du kommer att omdirigeras till sidan **Granska och publicera** .

Om det är första gången du publicerar det här erbjudandet kan du:

- Se slut för ande status för varje avsnitt i erbjudandet.
  - *Inte startad* – innebär att avsnittet inte har vidrör ATS och måste slutföras.
  - *Uncompleted* – innebär att avsnittet innehåller fel som behöver åtgärdas eller som kräver mer information. Gå tillbaka till avsnitten och uppdatera det.
  - *Complete* – innebär att avsnittet har slutförts, att alla nödvändiga data har angetts och att det inte finns några fel. Alla avsnitt i erbjudandet måste vara i ett komplett tillstånd innan du kan skicka in erbjudandet.
- I avsnittet **Obs!-certifiering** anger du test instruktioner till certifierings teamet för att kontrol lera att appen är korrekt testad, förutom eventuella kompletterande anteckningar som hjälper dig att förstå din app.
- Skicka in erbjudandet om publicering genom att välja **Skicka**. Vi kommer att skicka ett e-postmeddelande till dig när en för hands version av erbjudandet är tillgänglig för att granska och godkänna. Gå tillbaka till Partner Center och välj **Go-Live** för erbjudandet att publicera ditt erbjudande till allmänheten (eller om ett privat erbjudande till den privata mål gruppen).

### <a name="customer-experience-and-offer-management"></a>Kund upplevelse och erbjudande hantering

När en kund distribuerar ditt erbjudande kan de delegera prenumerationer eller resurs grupper för [Azure-delegerad resurs hantering](../../lighthouse/concepts/azure-delegated-resource-management.md). Mer information om den här processen finns i [kund onboarding-processen](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process).

Du kan när som helst [publicera en uppdaterad version av erbjudandet](update-existing-offer.md) . Du kanske till exempel vill lägga till en ny roll definition till ett tidigare publicerat erbjudande. När du gör det, kommer kunder som redan har lagt till erbjudandet att se en ikon på sidan [**tjänst leverantörer**](../../lighthouse/how-to/view-manage-service-providers.md) i Azure Portal som gör att de kan se att en uppdatering är tillgänglig. Varje kund kan granska ändringarna och bestämma om de vill uppdatera till den nya versionen.

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Commercial Marketplace](./update-existing-offer.md)
- [Lär dig mer om Azure Lighthouse](../../lighthouse/overview.md)