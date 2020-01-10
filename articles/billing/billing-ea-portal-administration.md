---
title: Administration i Azure EA-portalen
description: I den här artikeln förklaras några vanliga uppgifter som administratörer utför i Azure EA-portalen.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/02/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 4db710dc93b0a1fc3c85d24e9d79fb2e2d552cd1
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644553"
---
# <a name="azure-ea-portal-administration"></a>Administration i Azure EA-portalen

I den här artikeln förklaras några vanliga uppgifter som administratörer utför i Azure EA-portalen (https://ea.azure.com). Azure EA-portalen är en onlinehanteringsportal där kunder kan hantera kostnaden för sina Azure EA-tjänster. Grundläggande information om Azure EA-portalen finns i artikeln [Kom igång med Azure EA-portalen](billing-ea-portal-get-started.md).

## <a name="add-a-new-enterprise-administrator"></a>Lägga till en ny företagsadministratör

Företagsadministratörer har fullständig behörighet i hanteringen av en Azure EA-registrering. Den första Azure EA-administratören skapades när EA-avtalet tecknades. Du kan dock när som helst lägga till och ta bort administratörer. Det är bara befintliga administratörer som kan lägga till nya administratörer. Du kan läsa mer om att lägga till fler företagsadministratörer i [Skapa en ny företagsadministratör](billing-ea-portal-get-started.md#create-another-enterprise-admin). Mer information om roller och uppgifter för faktureringsprofiler finns i [Roller och uppgifter för faktureringsprofiler](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-user-state-from-pending-to-active"></a>Uppdatera användarstatus från Väntar till Aktiv

När nya kontoinnehavare först läggs till i en Azure EA-registrering är deras status _väntande_. När en ny kontoinnehavare får sitt aktiveringsmail kan kontoinnehavaren logga in och aktivera sitt konto. När kontoinnehavaren aktiverar sitt konto uppdateras kontostatusen från _väntande_ till _aktiv_. Kontoinnehavaren måste läsa varningsmeddelandet och klicka på **Continue** (Fortsätt). Nya användare kan uppmanas att ange sitt för- och efternamn och skapa ett handelskonto. I så fall måste personen ange den nödvändiga informationen innan kontot aktiveras.

## <a name="add-a-department-admin"></a>Lägga till en avdelningsadministratör

När en Azure EA-administratör skapar en avdelning så kan Azure-företagsadministratören lägga till avdelningsadministratörer och koppla dem till en avdelning. En avdelningsadministratör kan skapa nya konton. Nya konton behövs till att skapa Azure EA-prenumerationer.

Du kan läsa mer om att lägga till en avdelningsadministratör under [Skapa en Azure EA-avdelningsadministratör](billing-ea-portal-get-started.md#add-a-department-admin).

## <a name="associate-an-account-to-a-department"></a>Associera ett konto med en avdelning

Företagsadministratörer kan associera befintliga konton till avdelningar under registreringen.

### <a name="to-associate-an-account-to-a-department"></a>Associera ett konto med en avdelning

1. Logga in i Azure EA-portalen som företagsadministratör.
1. Klicka på **Manage** (Hantera) i det vänstra navigeringsfältet.
1. Klicka på **Department** (Avdelning).
1. Hovra över raden med det önskade kontot och klicka på pennikonen till höger.
1. Välj önskad avdelning i den nedrullningsbara menyn.
1. Klicka på **Spara**.

## <a name="department-spending-quotas"></a>Avdelningens utgiftskvoter

EA-kunder kan ange eller ändra utgiftskvoter för varje avdelning under en registrering. Utgiftskvotens belopp anges för det aktuella åtagandeperioden. I slutet av den aktuella åtagandeperioden förlänger systemet den befintliga utgiftskvoten till nästa åtagandeperiod om inte värdena uppdateras.

Avdelningsadministratören kan visa utgiftskvoten utan bara företagsadministratören kan uppdatera kvotbeloppet. Företagsadministratören och avdelningsadministratören får meddelanden när kvoten har nått 50 %, 75 %, 90 % och 100%.

### <a name="enterprise-administrator-to-set-the-quota"></a>Företagsadministratör för att ange kvoten:

 1. Öppna Azure EA-portalen.
 1. Klicka på **Manage** (Hantera) i det vänstra navigeringsfältet.
 1. Klicka på fliken **Department** (Avdelning).
 1. Klicka på önskad avdelning.
 1. Klicka på pennikonen i avsnittet Avdelningsinformation eller klicka på knappen **+ Add Department** (+ Lägg till avdelning) för att lägga till en utgiftskvot tillsammans med en ny avdelning.
 1. Under användningsinformationen anger du ett utgiftskvotbelopp i registreringens valuta i rutan Spending Quota $ (Utgiftskvot $) (måste vara större än 0).
    - Avdelningsnamnet och kostnadsstället kan också redigeras nu.
 1. Tryck på **Save** (Spara).

Avdelningens utgiftskvot syns nu i vyn Department List (Avdelningslista) under fliken Department (Avdelning). I slutet av det aktuella åtagandet behåller Azure EA-portalen utgiftskvoterna till nästa åtagandeperiod.

Avdelningskvotens belopp är oberoende av det aktuella betalningsåtagandet, och kvotbeloppet och aviseringarna gäller bara för förstapartsanvändning. Avdelningens utgiftskvot är bara för information och tillämpar inte utgiftsgränser.

### <a name="department-administrator-to-view-the-quota"></a>Avdelningsadministratör för att visa kvoten:

1. Öppna Azure EA-portalen.
1. Klicka på **Manage** (Hantera) i det vänstra navigeringsfältet.
1. Klicka på fliken **Department** (Avdelning) och visa vyn Department List (Avdelningslista) med utgiftskvoter.

Om du är en indirekt kund måste din kanalpartner aktivera kostnadsfunktioner.

## <a name="enterprise-user-roles"></a>Roller för företagsanvändare

Du kan administrera kostnader och användning för Azure EA i Azure EA-portalen. Det finns tre huvudsakliga roller i Azure EA-portalen:

- EA-administratör
- Avdelningsadministratör
- Kontoinnehavare

De olika rollerna har olika åtkomstnivå och behörighet.

Du kan läsa mer om användarroller under [Enterprise user roles](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles) (Roller för företagsanvändare).

## <a name="add-an-azure-ea-account"></a>Lägga till ett Azure EA-konto

Azure EA-konton är en organisationsenhet i Azure EA-portalen som används till att administrera prenumerationer och skapa rapporter. Om du ska kunna komma åt och använda Azure-tjänster måste du eller någon annan skapa ett konto åt dig.

Du kan läsa mer om Azure-konton under Lägga till ett konto.

## <a name="enterprise-devtest-offer"></a>Enterprise Dev/Test-erbjudande

Som Azure Enterprise-administratör kan du nu aktivera kontoinnehavare i organisationen för att skapa prenumerationer baserade på EA Dev/Test-erbjudandet. Du kan göra det genom att markera Dev/Test-rutan för den kontoinnehavaren i Azure EA-portalen.

När du har markerat Dev/Test-rutan meddelar du kontoinnehavaren att han/hon kan ställa in de EA Dev/Test-prenumerationer som behövs för teamet med Dev/Test-prenumeranter.

Det här erbjudandet gör det möjligt för aktiva Visual Studio-prenumeranter att köra utvecklings- och testarbetsbelastningar på Azure till särskilda Dev/Test-priser, med åtkomst till hela galleriet med Dev/Test-avbildningar inklusive Windows 8.1 och Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Konfigurera Enterprise Dev/Test-erbjudandet:

1. Logga in som företagsadministratör.
1. Klicka på **Manage** (Hantera) i det vänstra navigeringsfältet.
1. Klicka på fliken **Account** (Konto).
1. Klicka på raden för det konto där du vill aktivera Dev/Test-åtkomst.
1. Klicka på pennikonen till höger om raden.
1. Markera kryssrutan Dev/Test.
1. Tryck på **Save** (Spara).

När en användare läggs till som kontoinnehavare via Azure EA-portalen konverteras alla Azure-prenumerationer associerade med kontoinnehavaren som baseras på antingen PAYG Dev/Test-erbjudandet eller erbjudanden med månatlig kredit för Visual Studio-prenumeranter till EA Dev/Test-erbjudandet. Prenumerationer baserade på andra erbjudandetyper, till exempel PAYG (betala per användning), associerade med kontoinnehavaren konverteras till Microsoft Azure Enterprise-erbjudanden.

Dev/Test-erbjudandet gäller för närvarande inte för Azure Gov-kunder.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Överföra ett företagskonto till en ny registrering

Vid en kontoöverföring flyttas en kontoägare från en registrering till en annan. Alla relaterade prenumerationer under kontoägaren flyttas till målregistreringen. Detta sker om det finns flera aktiva registreringar och du bara vill flytta specifika kontoägare.

Tänk på följande när du ska överföra ett företagskonto till en ny registrering:

- Det är bara de konton som anges i förfrågan som överförs. Om du väljer alla konton så överförs samtliga.
- Källregistreringen behåller statusen aktiv eller utvidgad. Du kan fortsätta att använda registreringen tills den upphör att gälla.

### <a name="effective-transfer-date"></a>Effektivt överföringsdatum

Du kan bakåtdatera en kontoöverföring så långt tillbaka som målregistreringens startdatum eller kontots startdatum, beroende på vilket som har ett senare startdatum. När kontot har överförts behålls all användningsinformation för kontot från tiden innan det effektiva överföringsdatumet i registreringen du överför från. Användningsinformation efter överföringsdatumet flyttas till målregistreringen.

### <a name="prerequisites"></a>Krav

När du begär en kontoöverföring ska du ange följande information:

- Numret på målregistreringen, kontonamnet och e-postadressen för kontoägaren för kontot som ska överföras
- För källregistreringen, registreringsnumret och kontot som ska överföras
- För kontoöverföringens giltighetsdatum kan det bakåtdateras så långt tillbaka som målregistreringens startdatum eller kontots startdatum, beroende på vilket som har ett senare startdatum

Här är några andra saker du bör tänka på innan en kontoöverföring:

- Du behöver godkännande från en EA-administratör för mål- och källregistreringen
- Om en kontoöverföring inte uppfyller dina behov kan du överväga en registreringsöverföring.
- Kontoöverföringen överför alla tjänster och prenumerationer som tillhör aktuella konton.
- När överföringen är klar visas det överförda kontot som inaktivt under källregistreringen och som aktivt under målregistreringen.
- Kontot visar ett slutdatum som motsvarar aktuellt överföringsdatum för källregistreringen och startdatum för målregistreringen.
- All användning som utförts på kontot innan det aktuella överföringsdatumet finns kvar under källregistreringen.


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Överföra en företagsregistrering till en ny registrering

En registreringsöverföring är lämpligt när:

- Åtagandeperioden för en aktuell registrering har gått ut.
- En registrering har status som förfallen/förlängd och ett nytt avtal förhandlas.
- Om du har flera registreringar och vill konsolidera alla konton och faktureringen under en enda registrering.

När du begär att en hel företagsregistrering ska överföras till en ny registrering utförs följande åtgärder:

- Alla tjänster, prenumerationer, konton, avdelningar och hela registreringsstrukturen, inklusive alla EA-avdelningsadministratörer, överförs till en ny målregistrering.
- Registreringsstatus ställs in som _Överförd_. Den överförda registreringen är bara tillgänglig för rapporter om historisk användning.
- Du kan inte lägga till roller eller prenumerationer i en överförd registrering. Statusen Överförd förhindrar ytterligare användning mot registreringen.
- Eventuella saldon för ekonomiska åtaganden i registreringen går förlorade, inklusive framtida avtal.
-   Om registreringen du överför från innehåller RI-köp blir inköpspriset för RI kvar källregistreringen, men alla RI-förmåner överförs för användning i den nya registreringen.
-   Engångsavgiften för köp på Marketplace och alla månatliga fasta avgifter som redan påförts på den gamla registreringen överförs inte till den nya registreringen. Förbrukningsbaserade Marketplace-debiteringar överförs.
-   Efter en retroaktiv registreringsöverföring blir alla engångsavgifter från köp kvar i källregistreringen.

### <a name="effective-transfer-date"></a>Effektivt överföringsdatum

Den effektiva överföringsdagen kan vara på eller efter startdatumet för målregistreringen.

Användning i källregistreringen debiteras mot det ekonomiska åtagandet eller som en överförbrukning. Användning efter det effektiva överföringsdatumet överförs till den nya registreringen och debiteras där.

En bakåtdaterad överföring stöds så långt tillbaka som målregistreringens startdatum. Att ange det valda överföringsdatumet påverkar inte användningen av en överförbrukningsfaktura som redan har utfärdats.

### <a name="prerequisites"></a>Krav

När du begär överföring av en registrering ska du ange följande information:

- För källregistreringen, registreringsnumret.
- För målregistreringen, registreringsnumret som överföringen ska göras till.
- Det effektiva datumet för överföringen av registreringen kan vara målregistreringens startdatum eller något senare datum. Det valda datumet påverkar inte användningen i eventuella fakturor för överförbrukning som redan har utfärdats.

Här är några andra saker du bör tänka på innan du överför en registrering:

- Det krävs godkännande från EA-administratörer för både mål- och källregistrering.
- Om överföring av en registrering inte uppfyller dina behov kan du överväga en kontoöverföring.
- Källregistreringens status uppdateras till Överförd och är bara tillgänglig för rapportering av tidigare användning.

### <a name="monetary-commitment"></a>Ekonomiskt åtagande

Ekonomiska åtaganden kan inte överföras mellan registreringar. Saldon för ekonomiska åtaganden är avtalsmässigt bundna till den registrering där åtagandet beställdes. Ekonomiska åtaganden överförs inte när konton eller registreringar överförs.

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>Inga tjänster påverkas av konto- och registreringsöverföringar

Det förekommer inga avbrott under en konto- eller registreringsöverföring. De kan genomföras samma dag som förfrågan inkommer om du anger all nödvändig information.

## <a name="change-account-owner"></a>Ändra kontoägare

Du kan överföra prenumerationer från en kontoinnehavare till en annan i Azure EA-portalen. Mer information finns i [Ändra kontoinnehavare](billing-ea-portal-get-started.md#change-account-owner).

## <a name="subscription-transfer-effects"></a>Åtgärder som utförs vid överföring av prenumerationer

När du överför en Azure-prenumeration till ett konto i samma klientorganisation i Azure Active Directory så behåller alla användare, grupper och huvudnamn för tjänster med [rollbaserad åtkomst (RBAC)](../role-based-access-control/overview.md) för hantering av resurser sin åtkomst.

Så här visar du användare med rollbaserad åtkomst för prenumerationen:

1. Öppna **Prenumerationer** i Azure-portalen.
2. Välj den prenumeration du vill visa och välj sedan **Åtkomstkontroll (IAM)** .
3. Välj **Rolltilldelningar**. På sidan med rolltilldelningar ser du alla användare som har rollbaserad åtkomst för prenumerationen.

Om prenumerationen överförs till ett konto i en annan klientorganisation i Azure AD så kommer alla användare, grupper och huvudnamn för tjänster med [rollbaserad åtkomst (RBAC)](../role-based-access-control/overview.md) för hantering av resurser att _förlora_ sin behörighet. Även utan rollbaserad åtkomst kan användare få åtkomst till prenumerationen via olika säkerhetsmekanismer. Här är några exempel:

- Hanteringscertifikat som ger användaren administratörsbehörighet till prenumerationsresurser. Mer information finns i [Skapa och ladda upp ett hanteringscertifikat för Azure](../cloud-services/cloud-services-certs-create.md).
- Åtkomstnycklar för tjänster som Storage. Mer information finns i [kontoöversikten för Azure Storage](../storage/common/storage-account-overview.md).
- Autentiseringsuppgifter för fjärråtkomst för tjänster som Azure Virtual Machines.

Om mottagaren behöver begränsa åtkomsten till sina Azure-resurser bör denne överväga att uppdatera eventuella hemligheter som är associerade med tjänsten. De flesta resurser kan uppdateras med hjälp av följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Alla resurser** i hubbmenyn.
3. Välj resursen.
4. Klicka på **Inställningar** på resurssidan för att visa och uppdatera befintliga hemligheter.

## <a name="delete-subscription"></a>Ta bort prenumeration

Ta bort en prenumeration där du är kontoinnehavare:

1. Logga in på Azure-portalen med autentiseringsuppgifterna som är kopplade till ditt konto.
1. Välj **Prenumerationer** på navmenyn.
1. På prenumerationsfliken i det övre vänstra hörnet på sidan väljer du den prenumeration du vill ha för att avbryta och klicka på **Avbryt prenumeration** för att starta fliken Avbryt.
1. Ange prenumerationens namn och välj en uppsägningsorsak och klicka på knappen **Avbryt prenumeration**.

Observera att bara kontoadministratörer kan avbryta prenumerationer.

## <a name="delete-an-account"></a>Ta bort ett konto

Borttagning av konto kan bara slutföras för aktiva konton utan aktiva prenumerationer.

1. I EA-portalen väljer du **Manage** (Hantera) i det vänstra navigeringsfältet.
1. Klicka på fliken **Account** (Konto).
1. I tabellen med konton väljer du de konton du vill ta bort.
1. Klicka på X-ikonen till höger om kontoraden.
1. När det inte finns några aktiva prenumerationer under kontot klickar du på knappen **Yes** (Ja) under kontoraden för att bekräfta borttagningen av kontot.

## <a name="update-notification-settings"></a>Uppdatera meddelandeinställningar

Företagsadministratörer registreras automatiskt för att få användningsmeddelanden kopplade till sin registrering. Varje företagsadministratör kan ändra hur ofta meddelanden ska skickas, eller så kan de stängas av helt.

Du kan se kontaktuppgifterna för meddelanden i avsnittet **Meddelandekontakt** i Azure EA-portalen. Genom att hantera dina meddelandekontakter ser du till att rätt personer i organisationen får meddelanden om Azure EA.

Så här visar du aktuella meddelandeinställningar:

1. Öppna Azure EA-portalen och navigera till **Hantera** > **Meddelandekontakt**.
2. E-postadress – e-postadressen som är kopplad till företagsadministratörens Microsoft-konto, arbetskonto eller skolkonto som tar emot meddelanden.
3. Frekvens för meddelanden om utestående saldo – hur ofta meddelanden skickas till respektive företagsadministratör.

Så här lägger du till en kontakt:

1. Klicka på **+ Lägg till kontakt**.
2. Ange e-postadressen och bekräfta den.
3. Klicka på **Spara**.

Den nya meddelandekontakten visas i avsnittet **Meddelandekontakt**. Om du vill ändra meddelandefrekvensen markerar du meddelandekontakten och klickar på pennsymbolen till höger om den markerade raden. Ställ in frekvensen som **varje dag**, **varje vecka**, **varje månad** eller **aldrig**.

Du kan välja att hoppa över livscykelmeddelandena _täckningsperiodens slutdatum närmar sig_ och _datumet för inaktivering och avetablering närmar sig_. Om du hoppar över livscykelmeddelandena skickas inga meddelanden om slutdatumet för täckningsperioden eller avtalet.

## <a name="manage-partner-administrators"></a>Hantera partneradministratörer

Varje partneradministratör i Azure EA-portalen kan lägga till eller ta bort andra partneradministratörer. Partneradministratörer är associerade med partnerorganisationerna för indirekta registreringar och är inte direkt associerade med registreringarna.

### <a name="add-a-partner-administrator"></a>Lägga till en partneradministratör

Om du vill visa en lista över alla registreringar som är associerade till samma partnerorganisation som den aktuella användaren klickar du på fliken **Enrollment** (Registrering) och väljer en önskad registreringsruta.

1. Logga in som partneradministratör.
1. Klicka på **Manage** (Hantera) i det vänstra navigeringsfältet.
1. Klicka på fliken **Partner**.
1. Klicka på **+ Add Administrator** (+ Lägg till administratör) och fyll i e-postadress, meddelandekontakt och meddelandeinformation.
1. Tryck på **Add** (Lägg till).

### <a name="remove-a-partner-administrator"></a>Ta bort en partneradministratör

Om du vill visa en lista över alla registreringar som är associerade till samma partnerorganisation som den aktuella användaren klickar du på fliken **Enrollment** (Registrering) och väljer en önskad registreringsruta.

1. Logga in som partneradministratör.
1. Klicka på **Manage** (Hantera) i det vänstra navigeringsfältet.
1. Klicka på fliken **Partner**.
1. Under avsnittet Administrator (Administratör) väljer du lämplig rad för den administratör du vill ta bort.
1. Tryck på X-ikonen till höger.
1. Bekräfta att du vill ta bort.

## <a name="manage-partner-notifications"></a>Hantera partnermeddelanden

Partneradministratörer kan hantera hur ofta de ska få användningsmeddelanden för sina registreringar. De får automatiskt ett meddelande om utestående saldo varje vecka. De kan ändra hur ofta enskilda meddelanden ska skickas till varje månad, varje vecka, varje dag eller aldrig.

Om ett meddelande till en användare uteblir gör du så här för att kontrollera att användarens meddelandeinställningar är korrekta.

1. Logga in på Azure EA-portalen som partneradministratör.
2. Klicka på **Hantera** och sedan på fliken **Partner**.
3. Granska listan med administratörer i avsnittet Administrator (Administratör).
4. Om du vill redigera meddelandeinställningarna hovrar du över administratören och klickar på pennikonen.
5. Uppdatera meddelandefrekvensen och livscykelmeddelandena efter behov.
6. Lägg till en kontakt om det behövs och klicka på **Lägg till**.
7. Klicka på **Spara**.

![Exempel som visar rutan Lägg till kontakt ](./media/billing-ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="view-enrollments-for-partner-administrators"></a>Visa registreringar för partneradministratörer

Partneradministratörer kan visa en lista över alla direkta och indirekta registreringar i Azure EA-portalen. Rutor som innehåller en översikt över alla registreringar visas med registreringsnummer, registreringens namn, saldo och överförbrukningsbelopp.

### <a name="view-a-list-of-enrollments"></a>Visa en lista över registreringar

1. Logga in som partneradministratör.
1. Klicka på **Manage** (Hantera) i navigeringsfältet till vänster på sidan.
1. Klicka på fliken **Registrering**.
1. Markera rutan för önskad registrering.

En vy över alla registreringar blir kvar högst upp på sidan, med rutor för varje registrering. Dessutom kan du navigera mellan registreringar genom att klicka på det aktuella registreringsnumret på navigeringsfältet till vänster på sidan. En utfällning visas där du kan söka efter registreringar eller välja en annan registrering genom att klicka på lämplig ruta.

## <a name="azure-sponsorship-offer"></a>Azure-sponsringserbjudande

Azure-sponsringserbjudandet är ett begränsat, sponsrat Microsoft Azure-konto. Inbjudan skickas via e-post till ett begränsat antal kunder som väljs ut av Microsoft. Om du är berättigad till Microsoft Azure-sponsringserbjudandet så har du fått en e-postinbjudan till ditt konto-id.

Om du vill ha mer information kan du skapa en [supportbegäran för sponsringsaktivering](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>Konvertering till autentisering med arbets- eller skolkonto

Azure Enterprise-användare kan konvertera autentiseringstypen från ett Microsoft-konto (MSA eller Live ID) till ett arbets- eller skolkonto (som använder Active Directory i Azure).

Så här börjar du:

1. Lägg till arbets- eller skolkontot i Azure EA-portalen i de rollers som krävs.
1. Om du får felmeddelanden kanske kontot inte är giltigt i den aktiva katalogen.  Azure använder användarhuvudnamn (UPN), som inte alltid är identiskt med e-postadressen.
1. Autentisera till Azure EA-portalen med arbets- eller skolkonto.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Så här konverterar du prenumerationer från Microsoft-konton för arbets- eller skolkonton:

1. Logga in på hanteringsportalen med det Microsoft-konto som äger prenumerationerna.
1. Använd överföring av kontoägarskap för att flytta till det nya kontot.
1. Nu bör Microsoft-kontot vara fritt från aktiva prenumerationer och kan tas bort.
1. Borttagna konton blir kvar i vyn i portalen med inaktiv status av historiska faktureringsskäl.  Du kan filtrera bort dem från vyn genom att markera kryssrutan för att bara visa aktiva konton.

## <a name="account-subscription-ownership-faq"></a>Vanliga frågor och svar om prenumerationsägarskap

I det här dokumentet besvaras frågor som rör ägarskapet för kontoprenumerationen.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Hur många Azure-kontoinnehavare kan du ha per prenumeration?

Bara en kontoinnehavare tillåts per prenumeration.  Ytterligare roller kan läggas till med rollbaserad åtkomst eller Åtkomstkontroll (IAM) på prenumerationsfliken i det övre vänstra hörnet på sidan på [portal.azure.com]](https://portal.azure.com).

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Kan en Azure-kontoinnehavare listas under mer än en avdelning?

En kontoinnehavare kan bara associeras med en enda avdelning.  Det är för att säkerställa korrekt övervakning och fördelning av kostnader/utgifter kopplade till avdelningen de är justerade mot under EA-registreringen i Azure EA-portalen

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Kan en Azure-kontoinnehavare listas som en säkerhetsgrupp?

Nej, en prenumerationsägare måste vara ett unikt Microsoft-konto (MSA) eller en Azure Active Directory-autentisering (AAD). För att ta hänsyn till följden i organisationen kan du skapa allmänna konton och använda AAD för att hantera prenumerationsåtkomst.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Kan en enskild användare äga flera prenumerationer?

En Azure-kontoinnehavare kan skapa och hantera ett obegränsat antal prenumerationer.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Hur kan jag få åtkoms till/visa alla min organisations prenumerationer?

Idag måste detta göras med en princip. Det betyder att du skulle kräva att ditt konto läggs till i en prenumerationsroll med rollbaserad åtkomst för varje prenumeration som skapas.

### <a name="where-do-i-go-to-create-a-subscription"></a>Var skapar jag en prenumeration?

Innan du kan skapa en Azure EA-prenumeration måste ditt konto läggas till i rollen som kontoinnehavare av din administratören för din EA-registrering i Azure EA-portalen. Du måste sedan logga in på Azure EA-portalen för att få din rättighet att skapa prenumerationer för EA-erbjudanden. Vi rekommenderar att din första EA-prenumeration skapas via länken ”+ Add Subscription” (+ Lägg till prenumeration) på prenumerationsfliken i EA-portalen.  Men när ditt konto är berättigat kan det vara enklare att skapa prenumerationer i portal.azure.com på prenumerationsfliken i det övre högra hörnet på sidan, där du både kan skapa och byta namn på prenumerationen i ett enda steg.

### <a name="who-can-create-a-subscription"></a>Vem kan skapa en prenumeration?

Om du vill skapa en prenumeration för ett Azure-erbjudande för företag måste du vara berättigad i rollen som kontoinnehavare i [EA-portalen](https://ea.azure.com).

## <a name="next-steps"></a>Nästa steg

- Läs om hur du kan spara pengar med [reservationer för virtuella datorer](billing-ea-portal-vm-reservations.md).
- Om du behöver hjälp med att felsöka problem med Azure EA-portalen kan du läsa [Felsöka åtkomst till Azure EA-portalen](billing-ea-portal-troubleshoot.md).
