---
title: Så här hanterar du ett kommersiella Marketplace-konto i Partnercenter
description: Lär dig hur du hanterar en kommersiell Marketplace konto Partner Center.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: how-to
ms.date: 05/30/2019
ms.openlocfilehash: 5cb4caa6f0f8098e68d693be6cc2f33b5ccbeb32
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66752859"
---
# <a name="how-to-manage-your-commercial-marketplace-account-in-partner-center"></a>Så här hanterar du ditt kommersiella Marketplace-konto i Partnercenter 

När du har [skapade ett Partnercenter-konto](./create-account.md), du kan hantera ditt konto och erbjudanden med hjälp av den [kommersiella Marketplace instrumentpanelen](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

I den här artikeln har vi kommer fördjupa dig i hur du hanterar din Partner Center-konto, inklusive hur du: 

- [Få åtkomst till dina kontoinställningar för Partner Center](#access-your-account-settings)
- [Hitta din Publicerings-ID, ID för försäljning, användar-ID och Azure AD-klienter](#account-details)
- [Uppdatera kontaktinformation](#contact-info)
- [Hantera ekonomiska data (betalnings-konto, skatt profil betalnings hold-status)](#financial-details)
- [Konfigurera spårning av GUID för övervakning av kundens användning](#tracking-guids)
- [Hanteraranvändare](#manage-users)
- [Manager-grupper](#manage-groups)
- [Manager Azure AD-program](#manage-azure-ad-applications)
- [Definiera roller och behörigheter](#define-user-roles-and-permissions)
- [Hantera Azure AD-klienter (arbetskonton)](#manage-tenants)
- [Manager Partner Center-avtal](#agreements)


## <a name="access-your-account-settings"></a>Få åtkomst till dina kontoinställningar

Om du inte redan har gjort det du (eller din organisations administratör) ska komma åt den [kontoinställningar](https://partner.microsoft.com/dashboard/account/management) för Partnercenter-konto för att:
- Kontrollera status för ditt företags verifiering
- Bekräfta din försäljning ID, MPN-ID, Publicerings-ID och kontakta information, inklusive företagets godkännare och försäljning kontakt
- Konfigurera ditt företags finansiell information, inklusive skatt undantag om det är lämpligt
- skapa användarkonton för alla som använder ditt företag-konto i Partnercenter

### <a name="open-developer-settings"></a>Öppna inställningar för utvecklare

Kontoinställningar finns i det övre högra hörnet av din [kommersiella Marketplace instrumentpanelen](https://partner.microsoft.com/dashboard/commercial-marketplace) Partner Center. Välj kugghjulsikonen (nära det övre högra hörnet på instrumentpanelen) och välj sedan **utvecklarinställningar**. 

![Konto-inställningsmenyn i Partnercenter](./media/dashboard-developer-settings.png)

Inuti **kontoinställningar**, du kommer att kunna visa din:
- **Kontot information**: Kontotyp och kontostatus
- **Publisher-ID: N**: ID för försäljning, användar-ID, Publicerings-ID, Azure AD-klienter, osv.
- **Kontaktinfo**: Visningsnamn för utgivaren, kontaktperson för försäljning, e-post, telefon och adress
- **Ekonomiska data**: Betalnings-konto, skatt profil och betalnings hold-status
- **enheter**: Alla testning enheter som är associerade med ditt konto
- **Spåra GUID**: Alla spårning GUID associerat med ditt konto

### <a name="account-details"></a>Kontoinformation

I informationsavsnittet konto kan du se grundläggande information som ditt **kontotyp** (företag eller enskilda) och **verifieringsstatus** för ditt konto. Under ditt konto-verifieringen visas inställningarna varje steg som krävs, inklusive e-Postverifiering, anställning verifiering och verifiering för företag. Du kan också uppdatera din e-postadress här och skicka om verifieringen om det behövs. 

### <a name="publisher-ids"></a>Publisher-ID: N

I avsnittet Publisher-ID: N kan du se din **försäljning ID**, **MPN-ID**, och **Publicerings-ID**. Värdena tilldelas av Microsoft för att identifiera ditt developer-konto och kan inte redigeras.

### <a name="contact-info"></a>Kontaktuppgifter

I avsnittet kontakta info ser du din **Publisher visningsnamn**, **försäljning kontaktinformation** (den namn, e-post, telefonnummer och adress för företagets försäljning), och **företag godkännare** (namn, e-post och telefonnummer på personen med behörighet att godkänna beslut för företaget). 

### <a name="financial-details"></a>Ekonomiska data

Du kan ange eller uppdatera din finansiell information om du publicerar betalappar, tillägg eller tjänster i avsnittet finansiell information. 

Om du endast planerar att lista kostnadsfria erbjudanden, behöver du inte konfigurera ett betalnings-konto eller fylla i formulären skatt. Om du ändrar dig senare och bestämmer du vill sälja via Microsoft bör du konfigurera ditt betalnings-konto och fylla i skatt formulär vid den tidpunkten. 

#### <a name="payout-account"></a>Betalnings-konto

Ett betalnings-konto är det konto som fortsätter skickas från din försäljning. Det här konto måste vara i samma land där du registrerade ditt Partner Center-konto.

Om du vill konfigurera ditt betalnings-konto, måste du **associera din Account**:
1. I **kontoinställningar**under den **ekonomiska data** väljer **associera din Account**. 
2. När du uppmanas logga in med ditt Microsoft konto (MSA). Det här kontot kan inte redan vara associerad med en annan Partner Center-konto. 
3. Om du vill slutföra konfigurationen av kontot betalnings log helt utanför Partner Center sedan logga in igen med ditt Microsoft-Account (i stället för ditt arbetskonto). 

Nu när ditt Account är associerad, för att lägga till betalnings-konto behöver du:
- **Välj en betalningsmetod**: PayPal eller en
- **Lägg till betalningsinformation**: Detta kan omfatta att välja en kontotyp (kontrollerar eller besparingar), ange kontonamnet för innehavaren, kontonummer och routning tal, faktureringsadress, telefonnummer eller PayPal e-postadress. \* Mer information om hur du använder PayPal som din betalningsmetod för kontot och för att ta reda på om det finns stöd för din region på marknaden, finns i [PayPal info](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> Ändra ditt betalnings-konto kan fördröja betalningarna av upp till en betalningsmetod cykel. Den här fördröjningen beror på att vi behöver verifiera ändringen av tjänstkontot, precis som vi gör när du först konfigurerar betalnings-konto. Du kommer fortfarande få betalt för för hela beloppet när ditt konto har verifierats; alla betalningar på grund av för den aktuella betalningen cykel kommer att läggas till nästa.  

#### <a name="tax-profile"></a>Skatt profil

Granska din aktuella skatte-profilstatus, bekräftar rätt **entitetstypen** och **certifikat skatteinformation** visas. Välj **redigera** krävs att uppdatera eller slutföra formulär.

Du måste ange landet du bosatt och medborgarskap och slutföra lämpliga skatt formulären för land/region för att upprätta skattestatus.

Oavsett ditt land vistelseort eller medborgarskap, måste du fylla i USA skatt formulär att sälja några erbjudanden via Microsoft. Partner som uppfyller vissa krav på dataplacering USA måste fylla i ett formulär för IRS W-9. Andra partners utanför USA måste fylla i ett formulär för IRS skatteformulär w-8. Du kan fylla i dessa formulär online när du slutför din skatte-profil.

Förenta staternas enskilda skatte-ID (eller Individual) krävs inte att ta emot betalningar från Microsoft eller anspråk skatt fördraget fördelar.

Du kan slutföra och skicka dina skatt formulär elektroniskt i Partnercenter; i de flesta fall behöver du inte skriva ut och e-formulär.

Olika länder och regioner som har annan skattekrav. Exakt det belopp som du måste betala i skatter beror på länder och regioner där du sälja dina erbjudanden. Microsoft återförvisar försäljning och Använd skatt för din räkning i vissa länder. Dessa länder identifieras håller på att visa en lista över ditt erbjudande. I andra länder/regioner, beroende på var du är registrerad, kan du behöva betala in skatter för försäljning och Använd skatt för din försäljning direkt till den lokala skattemyndighet. Dessutom kanske försäljningspriset felmeddelandet momspliktiga som intäkt. Vi att starkt du kontaktar du relevant behörighet för land eller region som bäst att fastställa rätt skatte-information för din Microsoft-försäljning transaktioner.

##### <a name="withholding-rates"></a>Priserna för källskatt
Den information som du skickar i formulären skatt anger rätt hastighet för källskatt. Källskatt avgiften gäller endast försäljning som du gör i USA. försäljningen i icke-amerikanska platser omfattas inte av källskatt. Källskatt priserna kan variera, men för de flesta utvecklare som registrerar utanför USA Standardintervallet är 30 procent. Du har möjlighet att minska priset om ditt land har godkänt ett inkomstskatt avtal med USA.

##### <a name="tax-treaty-benefits"></a>Skatt fördraget fördelar
Om du är utanför USA kan du dra nytta av skatt fördraget fördelar. Dessa fördelar varierar från land till land och kan du minska mängden skatter som Microsoft tar ut. Du kan göra anspråk på skatt fördraget fördelar genom att fylla i en del II formulärets W 8BEN. Vi rekommenderar att du kommunicera med lämpliga resurser i ditt land eller region för att avgöra om dessa förmåner gäller dig.

[Läs mer om skatteuppgifter för utvecklare av Windows app/spel och Azure Marketplace-utgivare](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Payout hold-status

Som standard skickar Microsoft betalningar per månad. Du kan dock alternativet spärra din payouts, vilket gör att skicka betalningar till ditt konto. Om du väljer att spärra din payouts, vi kommer att fortsätta att registrera eventuella intäkter som du betalar, desto och ange information i din **Payout sammanfattning**. Vi kommer inte dock skicka betalningar till ditt konto förrän du tar bort spärren. 

Om du vill placera din betalningar behålls, gå till **kontoinställningar**. Under **ekonomiska data**i den **Payout lagra status** avsnittet, växla skjutreglaget till **på**. Du kan ändra din betalnings hold status när som helst, men tänk på att ditt beslut kommer att påverka nästa månatliga payout. Till exempel om du vill ska ha April's payout, se till att ange din payout hold-status **på** innan slutet av mars.

När du har konfigurerat din betalnings lagra status till **på**, alla payouts kommer att vänta tills du växlar skjutreglaget till **av**. När du gör det, kommer du att inkluderade under nästa månatliga betalnings cykel (förutsatt att alla tillämpliga betalning tröskelvärden har uppfyllts). Till exempel om du har haft din payouts Stoppad, men som ska vara en payout som genereras i juni och se till att växla betalnings håller status till **av** innan slutet av maj.

> [!NOTE]
> Din **Payout lagra status** alternativet gäller för **alla** intäkter källor som betalas via Microsoft Partner Center, inklusive Azure Marketplace, AppSource, Microsoft Store, reklam osv.). Du kan inte välja olika skäl status för varje intäkter-källa.

### <a name="devices"></a>Enheter

Inställningar för enhetshantering gäller endast för UWP-publicering. [Läs mer](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>Spåra GUID

Globalt är unika identifierare (GUID) unikt referensnummer (med 32 hexadecimala siffror), som kan användas för att spåra din Azure-användning. 

För att skapa GUID för spårning, bör du använda en GUID-generator. Azure Storage-teamet har skapat en [GUID generator formuläret](https://aka.ms/StoragePartners) att skicka en GUID för korrekt format och kan återanvändas i olika spårnings-system.

Vi rekommenderar att du skapar ett unikt GUID för varje erbjudande och distribution kanal för varje produkt. Du kan välja för att använda en enda GUID för produktens flera distributionskanaler om du inte vill att rapportering ska delas.

Om du distribuerar en produkt med hjälp av en mall och den är tillgänglig på både Azure Marketplace och på GitHub, kan du skapa och registrera 2 olika GUID:

*   Produkten A i Azure Marketplace
*   Produkten A på GitHub

Rapportering sker genom att värdet för partner (Microsoft Partner-ID) och GUID. Du kan också spåra GUID på en mer detaljerad nivå justerar mot varje plan inom ditt erbjudande.

Mer information finns i den [spåra Azure KUNDANVÄNDNING med vanliga frågor och svar GUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq).



## <a name="multi-user-account-management"></a>Hantering av flera användare

Partner Center utnyttjar [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) för flera användare åtkomst och hantering. Din organisations Azure AD associeras automatiskt med din Partner Center-konto som en del av registreringsprocessen. 

## <a name="manage-users"></a>Hantera användare

Den **användare** delen av Partner Center (under **kontoinställningar**) du använder vi Azure AD för att hantera användare, grupper och Azure AD-program som har åtkomst till Partnercenter-konto. Observera att för att hantera användare, du måste logga in med ditt [arbetskonto](./company-work-accounts.md) (den associerade Azure AD-klient). Hantera användare inom ett annat arbetskonto / -klient, behöver du logga ut och logga sedan in igen som en användare med **Manager** behörigheter på som arbetskonto / -klient. 

När du har loggat in med ditt arbetskonto (Azure AD-klient), kan du:
- [Lägg till eller ta bort användare](#add-or-remove-users)
- [Ändra en användarlösenord](#change-a-user-password)
- [Lägga till eller ta bort grupper](#add-or-remove-users)
- [Lägg till eller ta bort Azure AD-program](#add-new-azure-ad-applications)
- [Hantera nycklar för ett Azure AD-program](#manage-keys-for-an-azure-ad-application)
- [Definiera roller och behörigheter](#define-user-roles-and-permissions)


Tänk på att alla Partner Center-användare (inklusive grupper och Azure AD-program) måste ha en aktiv arbetskonto i en [Azure AD-klient](#manage-tenants) som är kopplad till ditt Partner Center-konto. 

### <a name="add-or-remove-users"></a>Lägg till eller ta bort användare

Kontot måste ha [ **hanterarnivån** ](#define-user-roles-and-permissions) behörigheter för den [arbetskonto (Azure AD-klient)](./company-work-accounts.md) som du vill lägga till eller redigera användare.

#### <a name="add-existing-users"></a>Lägga till befintliga användare

Lägga till användare till ditt Partner Center-konto som redan finns i ditt företags [arbetskonto (Azure AD-klient)](./company-work-accounts.md):

1. Gå till **användare** (under **kontoinställningar**) och välj **lägga till användare**.
2. Välj en eller flera användare i listan som visas. Du kan använda sökrutan för att söka efter specifika användare.
\* Om du väljer fler än en användare att lägga till Partner Center-konto, måste du tilldela dem samma roll eller uppsättning anpassade behörigheter. Upprepa dessa steg för varje roll eller en uppsättning av anpassade behörigheter för att lägga till flera användare med olika rollbehörigheter.
3.  När du har valt användare klickar du på **Lägg till vald**.
5.  I den **roller** anger roller eller anpassade behörigheter för de markerade användarna.
6.  Välj **Spara**.

#### <a name="create-new-users"></a>Skapa nya användare

För att skapa helt nya användarkonton, måste du ha ett konto med [ **Global administratör** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) behörigheter. 

1. Gå till **användare** (under **kontoinställningar**), Välj **lägga till användare**, välj sedan **skapa nya användare**.
1. Ange ett förnamn, efternamn och användarnamn för varje ny användare. 
1. Om du vill att den nya användaren att ha ett konto som global administratör i din organisations katalog, markerar du kryssrutan **ge användaren en Global administratör i din Azure AD, med fullständig kontroll över alla katalogresurser**. Detta ger användaren fullständig åtkomst till alla administrativa funktioner i ditt företag är Azure AD. De kommer att kunna lägga till och hantera användare i din organisation arbetskonto (Azure AD-klient), men inte i Partner Center, såvida inte du tilldela kontot rollen/behörighet. 
1. När du har markerat kryssrutan **och ge användaren en Global administratör**, måste du ange en **e för återställning av lösenord** för användaren att återställa sitt lösenord om det behövs.
1. I den **gruppmedlemskap** väljer du alla grupper som du vill att den nya användaren ska tillhöra.
1. I den **roller** anger roller eller anpassade behörigheter för användaren.
1. Välj **Spara**.

Skapa en ny användare i Partnercenter skapar också ett konto för användaren i arbetskonto (Azure AD-klient) som du har loggat in. Gör ändringar i en användares namn i Partnercenter gör samma ändringar i din organisations arbetskonto (Azure AD-klient).

#### <a name="invite-new-users-by-email"></a>Bjuda in nya användare via e-post

Om du vill bjuda in användare som inte är för närvarande en del av ditt företag arbetskonto (Azure AD-klient) via e-post, måste du ha ett konto med [ **Global administratör** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) behörigheter. 

1. Gå till **användare** (under **kontoinställningar**), Välj **lägga till användare**, välj sedan **bjuda in användare via e-post**.
2. Ange en eller flera e-postadress adresser (upp till tio), avgränsade med kommatecken eller semikolon.
3. I den **roller** anger roller eller anpassade behörigheter för användaren.
4. Välj **Spara**.

De användare som du inbjuden får en e-postinbjudan för att ansluta till ditt Partner Center-konto. Ett nytt gäst-konto skapas i ditt arbetskonto (Azure AD-klient). Varje användare måste godkänna sin inbjudan innan de kan komma åt ditt konto.

Om du vill skicka en inbjudan, gå till den **användare** hitta inbjudan i listan över användare, markera sin e-postadress (eller där det står *inbjudan väntande*). Längst ned på sidan Välj **skicka om inbjudan**.
 

> [!NOTE]
> Om din organisation använder [katalogintegrering](https://go.microsoft.com/fwlink/p/?LinkID=724033) för att synkronisera den lokala katalogtjänsten med din Azure AD, du kan skapa nya användare, grupper eller Azure AD-program i Partnercenter. Du (eller en annan administratör i din lokala katalog) behöver du skapa dem direkt i den lokala katalogen innan du kommer att kunna se och lägga till dem i Partnercenter.

#### <a name="remove-a-user"></a>Ta bort en användare

Om du vill ta bort en användare från ditt arbetskonto (Azure AD-klient), går du till **användare** (under **kontoinställningar**), Välj den användare som du vill ta bort med hjälp av kryssrutan i kolumnen längst till höger och välj sedan  **Ta bort** från tillgängliga åtgärder. Ett popup-fönster visas för dig att bekräfta att du vill ta bort de markerade användarna.

#### <a name="change-a-user-password"></a>Ändra en användarlösenord

Om en av dina användare måste ändra sina lösenord, kan de göra det själv om du har angett en **e för återställning av lösenord** när du skapar användarkontot. Du kan också uppdatera en användares lösenord genom att följa stegen nedan. Om du vill ändra en användares lösenord i ditt företag arbetskonto (Azure AD-klient), måste du vara inloggad på ett konto med [ **Global administratör** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) behörigheter. Observera att detta ändrar användarens lösenord i Azure AD-klienten, tillsammans med lösenord de använder för att få åtkomst till Partner Center.

1.  Från den **användare** sida (under **kontoinställningar**), Välj namnet på det användarkonto som du vill redigera.
2.  Välj den **Återställ lösenord** längst ned på sidan.
3.  En bekräftelsesida visas som visar inloggningsinformation för användare, inklusive ett tillfälligt lösenord. Glöm inte att skriva ut eller kopiera den här informationen och ge användaren, som du kan inte komma åt det tillfälliga lösenordet när du lämnar den här sidan.


## <a name="manage-groups"></a>Hantera grupper

Grupper kan du styra flera roller och behörigheter som helhet.

#### <a name="add-an-existing-group"></a>Lägg till en befintlig grupp

Lägga till en grupp som redan finns i din organisations arbetskonto (Azure AD-klient) till ditt Partner Center-konto: 

1.  Från den **användare** sida (under **kontoinställningar**), Välj **lägga till grupper**.
2.  Välj en eller flera grupper i listan som visas. Du kan använda sökrutan för att söka efter specifika grupper.
Om du väljer mer än en grupp att lägga till Partner Center-konto, måste du tilldela dem samma roll eller uppsättning anpassade behörigheter. Upprepa dessa steg för varje roll eller en uppsättning av anpassade behörigheter för att lägga till flera grupper med olika rollbehörigheter.
3.  När du har valt grupper klickar du på **Lägg till vald**.
4.  I den **roller** anger roller eller anpassade behörigheter för de valda grupperna. Alla medlemmar i gruppen kommer att kunna komma åt din Partner Center-konto med behörigheter som du använder på gruppen, oavsett roller och behörigheter som är associerade med sina individuella konto.
5.  Välj **Spara**.

När du lägger till en befintlig grupp kommer varje användare som är medlem i gruppen att kunna komma åt ditt Partner Center-konto med behörigheter som är associerade med gruppens tilldelade rollen.

#### <a name="add-a-new-group"></a>Lägg till en ny grupp

Lägg till en helt ny grupp i Partnercenter-konto: 

1.  Från den **användare** sida (under **kontoinställningar**), Välj **lägga till grupper**.
2.  På nästa sida väljer **ny grupp**.
3.  Ange visningsnamnet för den nya gruppen.
4.  Ange de roller eller anpassade behörigheter för gruppen. Alla medlemmar i gruppen kommer att kunna komma åt din Partner Center-konto med behörigheter som du använder här, oavsett den roller eller de behörigheter som är associerade med sina individuella konto.
5.  Välj användare för den nya gruppen i listan som visas. Du kan använda sökrutan för att söka efter specifika användare.
6.  När du har valt användare klickar du på **Lägg till vald** att lägga till dem till den nya gruppen.
7.  Välj **Spara**.

Observera att den nya gruppen kommer att skapas i din organisation arbetskonto (Azure AD-klient), inte bara i Partnercenter-konto.

#### <a name="remove-a-group"></a>Ta bort en grupp

Om du vill ta bort en grupp från ditt arbetskonto (Azure AD-klient), går du till **användare** (under **kontoinställningar**), Välj den grupp som du vill ta bort med hjälp av kryssrutan i kolumnen längst till höger och välj sedan  **Ta bort** från tillgängliga åtgärder. Ett popup-fönster visas för dig att bekräfta att du vill ta bort de valda grupperna.

## <a name="manage-azure-ad-applications"></a>Hantera Azure AD-program

Du kan tillåta program eller tjänster som är en del av ditt företags Azure AD för att komma åt ditt konto Partner Center. 

#### <a name="add-existing-azure-ad-applications"></a>Lägg till befintliga Azure AD-program 

Om du vill lägga till finns program som redan i ditt företags Azure Active Directory: 

1.  Från den **användare** sida (under **kontoinställningar**), Välj **lägga till Azure AD-program**.
2.  Välj en eller flera Azure AD-program i listan som visas. Du kan använda sökrutan för att söka efter specifika Azure AD-program. Om du väljer fler än en Azure AD-program att lägga till Partner Center-konto, måste du tilldela dem samma roll eller uppsättning anpassade behörigheter. Upprepa dessa steg för varje roll eller en uppsättning av anpassade behörigheter för att lägga till flera Azure AD-program med olika rollbehörigheter.
3.  När du har valt Azure AD-program, klickar du på **Lägg till vald**.
5.  I den **roller** anger roller eller anpassade behörigheter för den valda Azure AD-program.
6.  Välj **Spara**.

#### <a name="add-new-azure-ad-applications"></a>Lägg till nya Azure AD-program 

Om du vill bevilja åtkomst för Partner Center till en helt ny Azure AD-program-konto, kan du skapa en i den **användare** avsnittet. Observera att detta skapar ett nytt konto i ditt företag arbetskonto (Azure AD-klient), inte bara i Partnercenter-konto. Om du främst använder den här Azure AD-program för autentisering av Partner Center och behöver inte användare att komma åt den direkt, kan du ange en giltig adress för den **svars-URL** och **Appidentitets-URI**, upp till som dessa värden används inte av några andra Azure AD-program i din katalog.

1.  Från den **användare** sida (under **kontoinställningar**), Välj **lägga till Azure AD-program**.
2.  På nästa sida väljer **ny Azure AD-programmet**.
3.  Ange den **svars-URL** för den nya Azure AD-program. Det här är URL: en där användare kan logga in och använda Azure AD-program (ibland även känt som App-URL eller inloggnings-URL: en). Den **svars-URL** får inte vara längre än 256 tecken och måste vara unikt i din katalog.
4.  Ange den **Appidentitets-URI** för den nya Azure AD-program. Det här är en logisk identifierare för Azure AD-programmet som visas när en enkel inloggning på-begäran skickas till Azure AD. Observera att den **Appidentitets-URI** måste vara unikt för varje Azure AD-program i din katalog. Detta ID får inte vara längre än 256 tecken. Mer information om App-ID-URI finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts).
5.  I den **roller** anger roller eller anpassade behörigheter för Azure AD-programmet.
6.  Välj **Spara**.

När du lägger till eller skapa ett Azure AD-program, du kan gå tillbaka till den **användare** och väljer namnet på programmet för att granska inställningar för program, inklusive klient-ID, klient-ID, svars-URL och App-ID-URI.

#### <a name="remove-an-application"></a>Ta bort ett program

Om du vill ta bort ett program från ditt arbetskonto (Azure AD-klient), går du till **användare** (under **kontoinställningar**), Välj det program som du vill ta bort med hjälp av kryssrutan i kolumnen längst till höger Välj sedan **ta bort** från tillgängliga åtgärder. Ett popup-fönster visas för dig att bekräfta att du vill ta bort de valda programmen.

#### <a name="manage-keys-for-an-azure-ad-application"></a>Hantera nycklar för ett Azure AD-program

Om din Azure AD-program läser och skriver data i Microsoft Azure AD, måste en nyckel. Du kan skapa nycklar för ett Azure AD-program genom att redigera information på Partner Center. Du kan också ta bort nycklar som inte längre behövs.

1.  Från den **användare** sida (under **kontoinställningar**), Välj namnet på Azure AD-programmet. Du ser alla aktiva nycklar för Azure AD-program, inklusive datumet då nyckeln skapades och när den upphör. 
2. Om du vill ta bort en nyckel som inte längre behövs, Välj **ta bort**.
3.  Om du vill lägga till en ny nyckel, Välj **Lägg till ny nyckel**.
4.  Du ser ett skärmen som visar den **klient-ID** och **viktiga värden**. Glöm inte att skriva ut eller kopiera den här informationen som du kan inte komma åt den igen när du lämnar den här sidan.
4.  Om du vill skapa fler nycklar väljer **Lägg till en annan nyckel**.


### <a name="define-user-roles-and-permissions"></a>Definiera roller och behörigheter

Ditt företags användare kan tilldelas följande roller och behörigheter i kommersiella Marketplace-program på Partner Center. 

Obs Azure Active Directory (AAD)-klient roller vara global administratör, Användaradministration och CSP-roller. Icke-AAD-roller är de roller som inte hanterar klienten och de innehåller MPN admin, administratör för företag-profil, hänvisning admin, stimulansordningen admin och stimulansordningen användare.


|**Roll**|**Behörigheter**|
|----------------------------------|:---------------------------------|
|Global administratör|• Har åtkomst till alla Microsoft-konto/tjänster med fullständig behörighet
|      |• Skapa supportärenden för Partner Center
||• Visa avtal, prislistor och erbjudanden
||• Visa, skapa och hantera partneranvändare|
|Manager|• Kan komma åt alla Microsoft-kontofunktioner utom skatte- och betalnings-inställningar
|      |• Kan hantera användare, roller, och arbetsrelaterade konton (klienter)|
|Developer|• Kan ladda upp paket, skicka in appar och tillägg och visa användningsrapporten mer telemetri
|      |• Kan inte komma åt finansiell information eller dina inställningar|
|Företag deltagare|• Kan komma åt finansiell information och ange prisinformation
|      |• Kan inte skapa eller skicka in nya appar och tillägg|
|Finansiella deltagare|• Kan visa betalnings-rapporter
|      |• Kan inte göra ändringar i appar eller inställningar|
|Marknadsförare|• Kan svara på Kundrecensioner och icke-finansiella rapporter
|      |• Kan inte göra ändringar i appar eller inställningar|

Mer information om hur du hanterar roller och behörigheter i andra delar av Partner Center, till exempel Azure Active Directory (AD), Cloud Solution Provider (CSP), kontroll panelen leverantör (CPV), gästanvändare eller Microsoft Partner Network (MPN), se [tilldela användarroller och behörigheter i Partnercenter](https://docs.microsoft.com/partner-center/permissions-overview).


## <a name="manage-tenants"></a>Hantera klienter

En Azure Active Directory (AD)-klient, kallas även din ”arbetskonto” i den här dokumentationen är en representation av din organisation komma igång på Azure-portalen och hjälper dig att hantera en specifik instans av Microsoft-molntjänster som intern och externa användare. Om din organisation prenumererar på en Microsoft-molntjänst som Azure, Microsoft Intune eller Office 365, har Azure AD-klient upprättats för dig. 

Du kan konfigurera flera klienter för användning med Partner Center. Alla användare med den **Manager** roll i Partnercenter-konto har möjlighet att lägga till och ta bort Azure AD-klienter från kontot.  

### <a name="add-an-existing-tenant"></a>Lägg till en befintlig klient

Koppla en annan Azure AD-klient till Partnercenter-konto:

1.  Från den **klienter** sida (under **kontoinställningar**), Välj **associera en annan Azure AD-klient**.
2. Ange dina autentiseringsuppgifter för Azure AD för den klient som du vill associera.
3. Granska organisation och domän namn för din Azure AD-klient. För att slutföra associationen, Välj **Bekräfta**.

Om kopplingen lyckas du sedan redo att lägga till och hantera användare med konton i den **användare** avsnittet Partner Center.

### <a name="create-a-new-tenant"></a>Skapa en ny klient

Skapa en varumärke ny Azure AD-klient med din Partner Center-konto:

1.  Från den **klienter** sida (under **kontoinställningar**), Välj **skapa en ny Azure AD-klient**.
2. Ange kataloginformationen för din nya Azure AD:
    - **Domännamn**: Det unika namn som vi använder för din Azure AD-domän, tillsammans med ”. onmicrosoft.com”. Till exempel om du har angett ”exempel” blir din Azure AD-domänen ”example.onmicrosoft.com”.
    - **E-postadress**: En e-postadress där vi kan kontakta dig om ditt konto om det behövs.
    - **Information om global administratör användarkonto**: Det första namnet, senaste namn, användarnamn och lösenord som du vill använda för det nya globala administratörskontot.
3. Välj **skapa** att bekräfta den nya domän och kontonamnet informationen.
4. Logga in med en ny Azure AD global administratörsanvändarnamn och lösenord för att börja [lägga till och hantera användare](#manage-users).

Mer information om hur du skapar nya klienter i din Azure-portalen, i stället för via Partner Center-portalen finns i artikeln [skapar en ny klient i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

### <a name="remove-a-tenant"></a>Ta bort en klient

Om du vill ta bort en klient från Partnercenter-konto, hitta namnet på den **klienter** sidan (i **kontoinställningar**) och välj sedan **ta bort**. Du uppmanas att bekräfta att du vill ta bort innehavaren. När du har gjort några användare i den klienten kommer att kunna logga in på Partner Center-kontot och alla behörigheter som du har konfigurerat för dessa användare kommer att tas bort.

När du tar bort en klient kommer alla användare som har lagts till Partnercenter-konto från den klienten inte längre att kunna logga in på kontot.

> [!TIP]
> Du kan inte ta bort en klient om du har loggat in Partner Center med ett konto i samma klientorganisation. Om du vill ta bort en klient, som du måste logga in på Partner Center som en **Manager** för en annan klient som är associerat med kontot. Om det finns bara en klient som är associerade med kontot, kan den klienten endast tas bort när du har loggat in med det Microsoft-konto som öppnats kontot.


## <a name="agreements"></a>Avtal

Den **avtal** delen av Partner Center (under **kontoinställningar**) vi du visa en lista över de publicera avtal som du har behörighet. Dessa avtal visas enligt namn och versionsnummer, inklusive den dag som det togs emot och namnet på den användare som godkänt avtalet. 

**Åtgärder som behövs för** kan visas överst i den här sidan om det finns uppdateringar av avtalet som kräver din uppmärksamhet. För att godkänna en uppdaterade avtalet först läsa den länkade avtal-versionen och välj sedan **godkänna avtalet för**. 

Information om Cloud Solution Provider (CSP) avtal i Partnercenter på [Microsoft Cloud-avtal per region och språk](https://docs.microsoft.com/partner-center/agreements).

## <a name="next-steps"></a>Nästa steg

- [Skapa ett nytt SaaS-erbjudande](./create-new-saas-offer.md)
