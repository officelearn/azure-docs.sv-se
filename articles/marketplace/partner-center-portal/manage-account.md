---
title: Hantera ett kommersiellt Marketplace-konto i Partner Center
description: Läs om hur du hanterar ett kommersiellt Marketplace-konto i Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 6d3952c38703d8102a45c4117e3c59e3fa464957
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275823"
---
# <a name="how-to-manage-your-commercial-marketplace-account-in-partner-center"></a>Så här hanterar du ditt Commercial Marketplace-konto i Partnercenter

När du har [skapat ett Partner Center-konto](./create-account.md)kan du hantera ditt konto och erbjudanden med [instrumentpanelen på Kommersiella Marknadsplatser](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

I den här artikeln ska vi gå in på hur du hanterar ditt Partner Center-konto, inklusive hur du:

- [Få tillgång till kontoinställningar för Partner Center](#access-your-account-settings)
- [Hitta dina Publisher-ID-, Symantec-ID-, säljar-ID-, användar-ID-, MPN-ID- och Azure AD-klienter](#account-details)
- [Uppdatera kontaktinformation](#contact-info)
- [Ställ in spårnings-GUID för övervakning av kundanvändning](#tracking-guids)
- [Hantera användare](#manage-users)
- [Hantera grupper](#manage-groups)
- [Hantera Azure AD-program](#manage-azure-ad-applications)
- [Definiera användarroller och behörigheter](#define-user-roles-and-permissions)
- [Hantera Azure AD-klienter (arbetskonton)](#manage-tenants)
- [Hantera partnercenteravtal](#agreements)

## <a name="access-your-account-settings"></a>Få tillgång till dina kontoinställningar

Om du inte redan har gjort det bör du (eller organisationens administratör) komma åt [kontoinställningarna](https://partner.microsoft.com/dashboard/account/management) för ditt Partner Center-konto för att:
- Kontrollera företagets kontoverifieringsstatus
- Bekräfta ditt Symantec-ID, säljar-ID, MPN-ID, Publisher-ID och kontaktinformation, inklusive företagets godkännare och säljarkontakt
- Skapa användarkonton för alla som använder ditt företagskonto i Partner center

### <a name="open-developer-settings"></a>Öppna utvecklarinställningar

Kontoinställningarna finns i det övre högra hörnet på [instrumentpanelen](https://partner.microsoft.com/dashboard/commercial-marketplace) för Kommersiella Marknadsplatser i Partnercenter. Välj kugghjulsikonen (nära det övre högra hörnet av instrumentpanelen) och välj sedan **Utvecklarinställningar**.

![Menyn Kontoinställningar i Partnercenter](./media/dashboard-developer-settings.png)

Insida **kontoinställningar**kan du visa din:
- **Kontouppgifter**: Kontotyp och kontostatus
- **Utgivarens ID:** Säljar-ID, användar-ID, Publisher ID, Azure AD-klienter osv.
- **Kontaktinformation**: Utgivarens visningsnamn, säljarens kontaktnamn, e-post, telefon och adress
- **Spåra GUID:er:** Alla spårnings-GUIDs som associerar med ditt konto

### <a name="account-details"></a>Kontouppgifter

I avsnittet Kontoinformation kan du se grundläggande information, till exempel din **kontotyp** (företag eller individ) och **verifieringsstatusen för** ditt konto. Under kontoverifieringsprocessen visar dessa inställningar varje steg som krävs, inklusive e-postverifiering, anställningsverifiering och företagsverifiering. Du kan också uppdatera din e-post här och skicka verifieringen igen om det behövs.

### <a name="publisher-ids"></a>Publisher-ID:er

I avsnittet Publisher-ID:er kan du se dina **Symantec-ID-,** **säljar-ID-,** **användar-ID-,** **MPN-ID-** och **Azure AD-klienter**. Dessa värden tilldelas av Microsoft för att unikt identifiera ditt utvecklarkonto och kan inte redigeras.

### <a name="contact-info"></a>Kontaktuppgifter

I avsnittet Kontaktinformation kan du se ditt **Publisher-visningsnamn**, **Säljarens kontaktinformation** (kontaktnamn, e-postadress, telefonnummer och adress för företagets säljare) och **företagsgodkännaren** (namn, e-post och telefonnummer för den person som har behörighet att godkänna beslut för företaget).

#### <a name="payout-account"></a>Utbetalningskonto

Ett utbetalningskonto är det bankkonto som intäkterna skickas till från din försäljning. Det här bankkontot måste finnas i samma land där du registrerade ditt Partner Center-konto.

Om du vill konfigurera ditt utbetalningskonto måste du **koppla ditt Microsoft-konto:**
1. Gå till [översiktssidan för Kommersiella marknadsplatser](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) i Partnercenter.
2. Under avsnittet Profil väljer du **Associera ditt Microsoft-konto**.
3. Logga in med ditt Microsoft-konto (MSA) när du uppmanas att göra det. Det här kontot kan inte redan associeras med ett annat Partner Center-konto.
4. För att slutföra konfigurationen av ditt utbetalningskonto loggar du ut helt från Partner Center och loggar sedan in igen med ditt Microsoft-konto (i stället för ditt arbetskonto).

Nu när ditt Microsoft-konto är kopplat måste du:
- **Välj betalningsmetod**: Bankkonto eller PayPal
- **Lägg till betalningsinformation:** Detta kan inkludera att välja en kontotyp (kontroll eller besparingar), ange kontoinnehavarens namn, kontonummer och operationsföljdsnummer, faktureringsadress, telefonnummer eller PayPal-e-postadress. *Mer information om hur du använder PayPal som betalningsmetod och ta reda på om det stöds i din marknadsregion finns i [PayPal info](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> Om du ändrar ditt utbetalningskonto kan du fördröja dina betalningar med upp till en betalningscykel. Den här förseningen beror på att vi måste verifiera kontoändringen, precis som vi gör när vi först konfigurerar utbetalningskontot. Du får fortfarande betalt för hela beloppet efter att ditt konto har verifierats. Eventuella betalningar som förfaller till betalning för den aktuella betalningscykeln kommer att läggas till nästa.  

#### <a name="tax-profile"></a>Skatteprofil

Granska din aktuella skatteprofilstatus, som bekräftar rätt **entitetstyp** och **skattecertifikatinformation** visas. Välj **Redigera** om du vill uppdatera eller fylla i nödvändiga formulär.

För att fastställa din skattestatus måste du ange ditt bosättnings- och medborgarskapsland och fylla i lämpliga skatteformulär som är kopplade till ditt land/din region.

Oavsett vilket land du har uppehållsrätt eller medborgarskap måste du fylla i amerikanska skatteformulär för att sälja erbjudanden via Microsoft. Partner som uppfyller vissa krav på uppehållstillstånd i USA måste fylla i ett IRS W-9-formulär. Andra partner utanför USA måste fylla i ett IRS W-8-formulär. Du kan fylla i dessa formulär online när du fyller i din skatteprofil.

Ett amerikanskt individuellt personnummer (eller ITIN) krävs inte för att ta emot betalningar från Microsoft eller göra anspråk på skatteavtalsförmåner.

Du kan fylla i och skicka in dina skatteformulär elektroniskt i Partner Center; I de flesta fall behöver du inte skriva ut och skicka några formulär.

Olika länder och regioner har olika skattekrav. Det exakta beloppet som du måste betala i skatt beror på de länder och regioner där du säljer dina erbjudanden. Microsoft skickar moms och använder moms för din räkning i vissa länder. Dessa länder kommer att identifieras i processen att lista ditt erbjudande. I andra länder, beroende på var du är registrerad, kan du behöva skicka ut moms och använda moms för din försäljning direkt till den lokala skattemyndigheten. Dessutom kan de försäljningsintäkter du får beskattas som inkomst. Vi rekommenderar starkt att du kontaktar relevant myndighet för ditt land eller din region som bäst kan hjälpa dig att fastställa rätt skatteinformation för dina Microsoft-försäljningstransaktioner.

##### <a name="withholding-rates"></a>Källskatt
Den information du skickar i dina skatteformulär avgör lämplig källskatt. Källskattegraden gäller endast för försäljning som du gör till USA. försäljning till platser utanför USA är inte föremål för undanhållande. Källskattefrekvensen varierar, men för de flesta utvecklare som registrerar sig utanför USA är standardfrekvensen 30%. Du har möjlighet att sänka denna skattesats om ditt land har gått med på ett inkomstskatteavtal med USA.

##### <a name="tax-treaty-benefits"></a>Fördelar med skatteavtalet
Om du är utanför USA, kanske du kan dra nytta av skatteavtal förmåner. Dessa förmåner varierar från land till land och kan göra det möjligt för dig att sänka momsbeloppet som Microsoft undanhåller. Du kan göra anspråk på skatteavtal förmåner genom att fylla i del II i W-8BEN form. Vi rekommenderar att du kommunicerar med lämpliga resurser i ditt land eller din region för att avgöra om dessa förmåner gäller för dig.

[Läs mer om skatteinformation för Windows-app-/spelutvecklare och Azure Marketplace-utgivare](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Status för utbetalningsspärr

Som standard skickar Microsoft betalningar månadsvis. Du har dock möjlighet att spärra dina utbetalningar, vilket förhindrar att du skickar betalningar till ditt konto. Om du väljer att spärra dina utbetalningar fortsätter vi att registrera eventuella intäkter som du tjänar och lämna information i din **utbetalningssammanfattning.** Vi skickar dock inga betalningar till ditt konto förrän du tar bort spärren. 

Om du vill spärra dina betalningar går du till **Kontoinställningar .** Växla skjutreglaget till **På**i avsnittet Status **för**utbetalningsspärr i avsnittet Status för **utbetalningsspärr.** Du kan ändra din utbetalningsspärr när som helst, men tänk på att ditt beslut kommer att påverka nästa månadsutbetalning. Om du till exempel vill hålla aprilutbetalningen måste du ställa in din utbetalningsspärr till **På** före slutet av mars.

När du har ställt in din utbetalningsspärr status **på På,** kommer alla utbetalningar att vara i is tills du växlar skjutreglaget tillbaka till **Off.** När du gör det inkluderas du under nästa månatliga utbetalningscykel (förutsatt att eventuella tillämpliga betalningströsklar har uppfyllts). Om du till exempel har haft dina utbetalningar på is, men vill ha en utbetalning som genereras i juni, se till att växla utbetalningsspärrens status till **Av** före slutet av maj.

> [!NOTE]
> Statusvalet **för utbetalningsspärr** gäller för **alla** intäktskällor som betalas via Microsoft Partner Center, inklusive Azure Marketplace, AppSource, Microsoft Store, annonsering osv.). Du kan inte välja olika spärrstatus för varje intäktskälla.

### <a name="devices"></a>Enheter

Enhetshanteringsinställningarna gäller endast för UWP-publicering. [Läs mer](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>Spåra GUID

Globalt unika identifierare (GUIDs) är unika referensnummer (med 32 hexadecimala siffror) som kan användas för att spåra din Azure-användning. 

Om du vill skapa GUID:er för spårning bör du använda en GUID-generator. Azure Storage-teamet har skapat ett [GUID-generatorformulär](https://aka.ms/StoragePartners) som skickar ett GUID-gränssnitt med rätt format och kan återanvändas över de olika spårningssystemen.

Vi rekommenderar att du skapar ett unikt GUID för varje erbjudande och distributionskanal för varje produkt. Du kan välja att använda ett enda GUID för produktens flera distributionskanaler om du inte vill att rapporteringen ska delas.

Om du distribuerar en produkt med hjälp av en mall och den är tillgänglig på både Azure Marketplace och på GitHub kan du skapa och registrera två olika GUIDS:

*   Produkt A på Azure Marketplace
*   Produkt A på GitHub

Rapporteringen görs av partnervärdet (Microsoft Partner ID) och GUID:erna. Du kan också spåra GUID:er på en mer detaljerad nivå som justerar till varje plan i erbjudandet.

Mer information finns i vanliga frågor och svar om spårning av [Azure-kunder med vanliga frågor och svar om GUIDs](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq).

## <a name="create-a-billing-profile"></a>Skapa en faktureringsprofil

Om du publicerar ett [Dynamics 365 for Customer Engagement-& Power Apps](./create-new-customer-engagement-offer.md) eller Dynamics [365 for Operations-erbjudande](./create-new-operations-offer.md) måste du fylla i **faktureringsprofilen**.

Faktureringsadressen är ifylld från din juridiska person och du kan uppdatera den här adressen senare. Fälten MOMS och MOMS-ID är valfria.  Landsnamnet och företagsnamnet kan inte redigeras.

## <a name="multi-user-account-management"></a>Hantering av flera användarkonton

Partner Center utnyttjar [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) för åtkomst och hantering av flera användarkonton. Organisationens Azure AD associeras automatiskt med ditt Partner Center-konto som en del av registreringsprocessen.

## <a name="manage-users"></a>Hantera användare

I avsnittet **Användare** i Partner center (under **Kontoinställningar)** kan du använda Azure AD för att hantera användare, grupper och Azure AD-program som har åtkomst till ditt Partner Center-konto. För att kunna hantera användare måste du vara inloggad med ditt [arbetskonto](./company-work-accounts.md) (den associerade Azure AD-klienten). För att hantera användare inom ett annat arbetskonto/klientorganisation måste du logga **Manager** ut och sedan logga in igen som användare med chefsbehörighet för det arbetskontot/klienten.

När du har loggat in med ditt arbetskonto (Azure AD-klienten) kan du:
- [Lägga till eller ta bort användare](#add-or-remove-users)
- [Ändra ett användarlösenord](#change-a-user-password)
- [Lägga till eller ta bort grupper](#add-or-remove-users)
- [Lägga till eller ta bort Azure AD-program](#add-new-azure-ad-applications)
- [Hantera nycklar för ett Azure AD-program](#manage-keys-for-an-azure-ad-application)
- [Definiera användarroller och behörigheter](#define-user-roles-and-permissions)

Tänk på att alla Partner Center-användare (inklusive grupper och Azure AD-program) måste ha ett aktivt arbetskonto i en [Azure AD-klient som](#manage-tenants) är associerad med ditt Partner Center-konto.

### <a name="add-or-remove-users"></a>Lägga till eller ta bort användare

Ditt konto måste ha behörigheter [**på hanterarensnivå**](#define-user-roles-and-permissions) för [arbetskontot (Azure AD-klienten)](./company-work-accounts.md) där du vill lägga till eller redigera användare.

#### <a name="add-existing-users"></a>Lägga till befintliga användare

Så här lägger du till användare i ditt Partner Center-konto som redan finns i företagets [arbetskonto (Azure AD-klient)](./company-work-accounts.md):

1. Gå till **Användare** (under **Kontoinställningar)** och välj **Lägg till användare**.
2. Välj en eller flera användare i listan som visas. Du kan använda sökrutan för att söka efter specifika användare.
*Om du väljer fler än en användare att lägga till i ditt Partner Center-konto måste du tilldela dem samma roll eller uppsättning anpassade behörigheter. Om du vill lägga till flera användare med olika roller/behörigheter upprepar du dessa steg för varje roll eller uppsättning anpassade behörigheter.
3. När du har valt användare klickar du på **Lägg till markerat**.
4. I avsnittet **Roller** anger du rollerna eller anpassade behörigheter för de valda användarna.
5. Välj **Spara**.

#### <a name="create-new-users"></a>Skapa nya användare

Om du vill skapa helt nya användarkonton måste du ha ett konto med global administratörsbehörighet. [**Global administrator**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

1. Gå till **Användare** (under **Kontoinställningar**), välj **Lägg till användare**och välj sedan Skapa nya **användare**.
1. Ange ett förnamn, efternamn och användarnamn för varje ny användare. 
1. Om du vill att den nya användaren ska ha ett globalt administratörskonto i organisationens katalog markerar du rutan Gör den **här användaren till global administratör i Din Azure AD, med full kontroll över alla katalogresurser**. Detta ger användaren fullständig åtkomst till alla administrativa funktioner i företagets Azure AD. De kan lägga till och hantera användare i organisationens arbetskonto (Azure AD-klient) men inte i Partner Center, såvida du inte ger kontot rätt roll/behörighet.
1. Om du har markerat rutan för att **göra den här användaren**till global administratör måste du ange ett **e-postmeddelande** om återställning av lösenord för att användaren ska kunna återställa sitt lösenord om det behövs.
1. I avsnittet **Gruppmedlemskap** väljer du alla grupper som du vill att den nya användaren ska tillhöra.
1. I avsnittet **Roller** anger du rollen eller de anpassade behörigheterna för användaren.
1. Välj **Spara**.

Att skapa en ny användare i Partner Center skapar också ett konto för den användaren i arbetskontot (Azure AD-klienten) som du är inloggad på. Om du gör ändringar i en användares namn i Partner Center ändras samma ändringar i organisationens arbetskonto (Azure AD-klientorganisation).

#### <a name="invite-new-users-by-email"></a>Bjud in nya användare via e-post

Om du vill bjuda in användare som för närvarande inte är en del av företagets arbetskonto (Azure AD-klient) via e-post måste du ha ett konto med globala administratörsbehörigheter. [**Global administrator**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

1. Gå till **Användare** (under **Kontoinställningar**), välj **Lägg till användare**och välj sedan Bjud in användare via **e-post**.
2. Ange en eller flera e-postadresser (upp till tio), avgränsade med kommatecken eller semikolon.
3. I avsnittet **Roller** anger du rollen eller de anpassade behörigheterna för användaren.
4. Välj **Spara**.

De användare du har bjudit in får en e-postbjudan för att gå med i ditt Partner Center-konto. Ett nytt gästanvändarkonto skapas i ditt arbetskonto (Azure AD-klientorganisation). Varje användare måste acceptera sin inbjudan innan de kan komma åt ditt konto.

Om du behöver skicka en inbjudan igen besöker du sidan **Användare,** hittar inbjudan i listan över användare, väljer deras e-postadress (eller texten som säger *Väntande inbjudan).* Välj sedan Skicka **inbjudan**igen längst ned på sidan .

> [!NOTE]
> Om din organisation använder [katalogintegration](https://go.microsoft.com/fwlink/p/?LinkID=724033) för att synkronisera den lokala katalogtjänsten med din Azure AD kan du inte skapa nya användare, grupper eller Azure AD-program i Partner Center. Du (eller en annan administratör i den lokala katalogen) måste skapa dem direkt i den lokala katalogen innan du kan se och lägga till dem i Partner center.

#### <a name="remove-a-user"></a>Ta bort en användare

Om du vill ta bort en användare från ditt arbetskonto (Azure AD-klient) går du till **Användare** (under **Kontoinställningar**) väljer du den användare som du vill ta bort med hjälp av kryssrutan längst till höger och väljer sedan **Ta bort** från de tillgängliga åtgärderna. Ett popup-fönster visas där du kan bekräfta att du vill ta bort de markerade användarna.

#### <a name="change-a-user-password"></a>Ändra ett användarlösenord

Om en av dina användare behöver ändra sitt lösenord kan de själva göra det om du har angett ett **e-postmeddelande** om återställning av lösenord när du skapar användarkontot. Du kan också uppdatera en användares lösenord genom att följa stegen nedan. Om du vill ändra en användares lösenord i företagets arbetskonto (Azure AD-klient) måste du vara inloggad på ett konto med globala administratörsbehörigheter. [**Global administrator**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) Observera att detta kommer att ändra användarens lösenord i din Azure AD-klient, tillsammans med det lösenord de använder för att komma åt Partner Center.

1. På sidan **Användare** (under **Kontoinställningar**) väljer du namnet på det användarkonto som du vill redigera.
2. Välj knappen **Återställ lösenord** längst ned på sidan.
3. En bekräftelsesida visas som visar inloggningsinformationen för användaren, inklusive ett tillfälligt lösenord. Var noga med att skriva ut eller kopiera den här informationen och ge den till användaren, eftersom du inte kan komma åt det tillfälliga lösenordet när du har lämnat den här sidan.

## <a name="manage-groups"></a>Hantera grupper

Med grupper kan du styra flera användarroller och behörigheter tillsammans.

#### <a name="add-an-existing-group"></a>Lägga till en befintlig grupp

Så här lägger du till en grupp som redan finns i organisationens arbetskonto (Azure AD-klient) i ditt Partner Center-konto:

1. På sidan **Användare** (under **Kontoinställningar**) väljer du **Lägg till grupper**.
2. Markera en eller flera grupper i listan som visas. Du kan använda sökrutan för att söka efter specifika grupper.
Om du väljer mer än en grupp som ska läggas till i ditt Partner Center-konto måste du tilldela dem samma roll eller uppsättning anpassade behörigheter. Om du vill lägga till flera grupper med olika roller/behörigheter upprepar du dessa steg för varje roll eller uppsättning anpassade behörigheter.
3. När du har valt grupper klickar du på **Lägg till markerat**.
4. I avsnittet **Roller** anger du rollerna eller anpassade behörigheter för de valda grupper som har valts. Alla medlemmar i gruppen kan komma åt ditt Partner Center-konto med de behörigheter du tillämpar på gruppen, oavsett vilka roller och behörigheter som är kopplade till deras enskilda konto.
5. Välj **Spara**.

När du lägger till en befintlig grupp kan varje användare som är medlem i den gruppen komma åt ditt Partner Center-konto med behörigheterna som är associerade med gruppens tilldelade roll.

#### <a name="add-a-new-group"></a>Lägga till en ny grupp

Så här lägger du till en helt ny grupp i ditt Partner Center-konto:

1. På sidan **Användare** (under **Kontoinställningar**) väljer du **Lägg till grupper**.
2. Välj **Ny grupp**på nästa sida .
3. Ange visningsnamnet för den nya gruppen.
4. Ange rollerna eller anpassade behörigheter för gruppen. Alla medlemmar i gruppen kan komma åt ditt Partner Center-konto med de behörigheter du använder här, oavsett vilka roller/behörigheter som är kopplade till deras enskilda konto.
5. Välj användare för den nya gruppen i listan som visas. Du kan använda sökrutan för att söka efter specifika användare.
6. När du är klar med att välja användare klickar du på **Lägg till markerad** om du vill lägga till dem i den nya gruppen.
7. Välj **Spara**.

Observera att den här nya gruppen kommer att skapas i organisationens arbetskonto (Azure AD-klient) också, inte bara i ditt Partner Center-konto.

#### <a name="remove-a-group"></a>Ta bort en grupp

Om du vill ta bort en grupp från ditt arbetskonto (Azure AD-klient) går du till **Användare** (under **Kontoinställningar**) markerar du den grupp som du vill ta bort med kryssrutan längst till höger och väljer sedan **Ta bort** från de tillgängliga åtgärderna. Ett popup-fönster visas där du kan bekräfta att du vill ta bort de markerade grupper.A pop-up window will appear to you to confirm that you want to remove the selected group(s.

## <a name="manage-azure-ad-applications"></a>Hantera Azure AD-program

Du kan tillåta att program eller tjänster som ingår i företagets Azure AD får åtkomst till ditt Partner Center-konto.

#### <a name="add-existing-azure-ad-applications"></a>Lägga till befintliga Azure AD-program

Så här lägger du till program som redan finns i företagets Azure Active Directory:

1. På sidan **Användare** (under **Kontoinställningar**) väljer du **Lägg till Azure AD-program**.
2. Välj ett eller flera Azure AD-program i listan som visas. Du kan använda sökrutan för att söka efter specifika Azure AD-program. Om du väljer mer än ett Azure AD-program som ska läggas till i ditt Partner Center-konto måste du tilldela dem samma roll eller uppsättning anpassade behörigheter. Om du vill lägga till flera Azure AD-program med olika roller/behörigheter upprepar du dessa steg för varje roll eller uppsättning anpassade behörigheter.
3. När du är klar med att välja Azure AD-program klickar du på **Lägg till markerade**.
4. I avsnittet **Roller** anger du rollerna eller anpassade behörigheter för de valda Azure AD-programen.
5. Välj **Spara**.

#### <a name="add-new-azure-ad-applications"></a>Lägg till nya Azure AD-program

Om du vill ge Partner Center åtkomst till ett helt nytt Azure AD-programkonto kan du skapa ett i avsnittet **Användare.** Observera att detta skapar ett nytt konto i ditt företagsarbetskonto (Azure AD-klient) inte bara i ditt Partner Center-konto. Om du främst använder det här Azure AD-programmet för Partner Center-autentisering och inte behöver användare för att komma åt det direkt, kan du ange en giltig adress för **svars-URL:en** och **App-ID-URI**, så länge dessa värden inte används av något annat Azure AD-program i katalogen.

1. På sidan **Användare** (under **Kontoinställningar**) väljer du **Lägg till Azure AD-program**.
2. På nästa sida väljer du **Nytt Azure AD-program**.
3. Ange **svars-URL:en** för det nya Azure AD-programmet. Det här är webbadressen där användare kan logga in och använda ditt Azure AD-program (kallas ibland även app-URL:en eller inloggnings-URLn). **Svars-URL:en** får inte vara längre än 256 tecken och måste vara unik i katalogen.
4. Ange **app-ID-URI** för det nya Azure AD-programmet. Detta är en logisk identifierare för Azure AD-programmet som presenteras när en enda inloggningsbegäran skickas till Azure AD. Observera att **App-ID URI** måste vara unikt för varje Azure AD-program i din katalog. Det här ID:t får inte vara längre än 256 tecken. Mer information om app-ID-URI finns i [Integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts).
5. I avsnittet **Roller** anger du rollerna eller anpassade behörigheter för Azure AD-programmet.
6. Välj **Spara**.

När du har lagt till eller skapat ett Azure AD-program kan du återgå till avsnittet **Användare** och välja programnamnet för att granska inställningarna för programmet, inklusive klient-ID, klient-ID, Svars-URL och App-ID URI.

#### <a name="remove-an-application"></a>Ta bort ett program

Om du vill ta bort ett program från ditt arbetskonto (Azure AD-klient) går du till **Användare** (under **Kontoinställningar**) markerar du det program som du vill ta bort med kryssrutan längst till höger och väljer sedan **Ta bort** från de tillgängliga åtgärderna. Ett popup-fönster visas där du kan bekräfta att du vill ta bort de markerade programen.

#### <a name="manage-keys-for-an-azure-ad-application"></a>Hantera nycklar för ett Azure AD-program

Om ditt Azure AD-program läser och skriver data i Microsoft Azure AD behöver det en nyckel. Du kan skapa nycklar för ett Azure AD-program genom att redigera informationen i Partner Center. Du kan också ta bort nycklar som inte längre behövs.

1. På sidan **Användare** (under **Kontoinställningar**) väljer du namnet på Azure AD-programmet. Du ser alla aktiva nycklar för Azure AD-programmet, inklusive det datum då nyckeln skapades och när den upphör att gälla. 
2. Om du vill ta bort en nyckel som inte längre behövs väljer du **Ta bort**.
3. Om du vill lägga till en ny nyckel väljer du **Lägg till ny nyckel**.
4. Du kommer att se en skärm som visar **klient-ID** och **Nyckelvärden**. Var noga med att skriva ut eller kopiera den här informationen, eftersom du inte kan komma åt den igen när du har lämnat den här sidan.
5. Om du vill skapa fler tangenter väljer du **Lägg till en annan nyckel**.

## <a name="define-user-roles-and-permissions"></a>Definiera användarroller och behörigheter

Företagets användare kan tilldelas följande roller och behörigheter för programmet Commercial Marketplace på Partner Center:

- **Ansvarig**
  - Kan komma åt alla Microsoft-kontofunktioner utom skatte- och utbetalningsinställningar
  - Kan hantera användare, roller och arbetskonton (klienter)
- **Developer**
  - Kan hantera och publicera erbjudanden
  - Kan visa vissa utgivarrapporter

> [!NOTE]
> För kommersiella marketplace-program används inte rollerna Global admin, Business Contributor, Financial Contributor och Marketer. Att tilldela dessa roller till användare har ingen effekt. Endast rollerna Chef och Utvecklare ger användarna behörighet.

Mer information om hur du hanterar roller och behörigheter i andra delar av Partner Center, till exempel Azure Active Directory (AD), CSP (Cloud Solution Provider), CPV (Kontrollpanelen Vendor), Gästanvändare eller Microsoft Partner Network (MPN), finns [i Tilldela användarroller och behörigheter i Partnercenter](https://docs.microsoft.com/partner-center/permissions-overview).

## <a name="manage-tenants"></a>Hantera klienter

En Azure Active Directory-klientorganisation (AD), även kallad ditt "arbetskonto" i den här dokumentationen, är en representation av din organisation som konfigurerats i Azure-portalen och hjälper dig att hantera en viss instans av Microsofts molntjänster för din interna och externa användare. Om din organisation prenumererade på en Microsoft-molntjänst, till exempel Azure, Microsoft Intune eller Office 365, har en Azure AD-klient upprättats åt dig.

Du kan ställa in flera klienter som ska användas med Partner Center. Alla användare med **rollen Manager** i Partner Center-kontot har möjlighet att lägga till och ta bort Azure AD-klienter från kontot.  

### <a name="add-an-existing-tenant"></a>Lägga till en befintlig klient

Så här associerar du en annan Azure AD-klient med ditt Partner Center-konto:

1. På sidan **Klienter** (under **Kontoinställningar**) väljer du **Associera en annan Azure AD-klientorganisation**.
2. Ange dina Azure AD-autentiseringsuppgifter för den klient som du vill associera.
3. Granska organisationen och domännamnet för din Azure AD-klientorganisation. Om du vill slutföra kopplingen väljer du **Bekräfta**.

Om associationen lyckas är du redo att lägga till och hantera kontoanvändare i avsnittet **Användare** i Partnercenter.

### <a name="create-a-new-tenant"></a>Skapa en ny klient

Så här skapar du en helt ny Azure AD-klient med ditt Partner Center-konto:

1. På sidan **Klienter** (under **Kontoinställningar**) väljer du **Skapa en ny Azure AD-klientorganisation**.
2. Ange kataloginformation för din nya Azure AD:
    - **Domännamn:** Det unika namn som vi använder för din Azure AD-domän, tillsammans med ".onmicrosoft.com". Om du till exempel angav "exempel" skulle din Azure AD-domän vara "example.onmicrosoft.com".
    - **Kontakta e-post**: En e-postadress där vi kan kontakta dig om ditt konto om det behövs.
    - **Global administratörsanvändarkontoinformation**: Förnamn, efternamn, användarnamn och lösenord som du vill använda för det nya globala administratörskontot.
3. Välj **Skapa** för att bekräfta den nya domänen och kontoinformationen.
4. Logga in med ditt nya azure AD-globala administratörsanvändarnamn och lösenord för att börja [lägga till och hantera användare](#manage-users).

Mer information om hur du skapar nya klienter i Azure-portalen i stället för via Partner Center-portalen finns i artikeln [Skapa en ny klient i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

### <a name="remove-a-tenant"></a>Ta bort en klient

Om du vill ta bort en klient från partnercenterkontot hittar du namnet på sidan **Klienter** (i **Kontoinställningar**) och väljer sedan **Ta bort**. Du uppmanas att bekräfta att du vill ta bort klienten. När du gör det kan inga användare i den klienten logga in på Partner Center-kontot och alla behörigheter som du har konfigurerat för dessa användare tas bort.

När du tar bort en klient kan alla användare som har lagts till i Partner Center-kontot från den klienten inte längre logga in på kontot.

> [!TIP]
> Du kan inte ta bort en klient om du för närvarande är inloggad i Partner Center med ett konto i samma klientorganisation. Om du vill ta bort en klient måste du logga in på Partner Center som **chef** för en annan klient som är kopplad till kontot. Om det bara finns en klient kopplad till kontot kan den klienten endast tas bort när den har loggat in med Microsoft-kontot som öppnade kontot.

## <a name="agreements"></a>Avtal

I avsnittet **Avtal** i Partnercenter (under **Kontoinställningar)** kan du visa en lista över de publiceringsavtal som du har auktoriserat. Dessa avtal är listade enligt namn och versionsnummer, inklusive det datum då det accepterades och namnet på den användare som accepterade avtalet.

**Åtgärder som behövs** kan visas högst upp på den här sidan om det finns avtalsuppdateringar som behöver din uppmärksamhet. Om du vill acceptera ett uppdaterat avtal läser du först den länkade avtalsversionen och väljer sedan **Acceptera avtal**.

## <a name="next-steps"></a>Nästa steg

- [Skapa ett nytt SaaS-erbjudande](./create-new-saas-offer.md)
