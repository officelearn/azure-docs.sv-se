---
title: Felsöka åtkomst till Azure EA-portalen
description: Den här artikeln beskriver några vanliga problem som kan uppstå med ett Azure Enterprise-avtal (EA) i Azure EA-portalen.
author: bandersmsft
ms.author: banders
ms.date: 05/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: fdcce868cb53c0fdd6a51f2b351491e1ee998d61
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691191"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>Felsöka åtkomst till Azure EA-portalen

Den här artikeln beskriver några vanliga problem som kan uppstå med ett Azure Enterprise-avtal (EA). Azure EA-portalen används för att hantera användare av och kostnader för Enterprise-avtal. Dessa problem kan uppstå när du konfigurerar eller uppdaterar åtkomst till Azure EA-portalen.

## <a name="issues-adding-an-admin-to-an-enrollment"></a>Problem med att lägga till en administratör till en registrering

Det finns olika typer av autentiseringsnivåer för företagsregistreringar. När autentiseringsnivåer tillämpas felaktigt kan det uppstå problem när du försöker logga in på Azure EA-portalen.

Du kan använda Azure EA-portalen för att ge åtkomst till användare med olika autentiseringsnivåer. En företagsadministratör kan uppdatera autentiseringsnivån för att uppfylla organisationens säkerhetskrav.

### <a name="authentication-level-types"></a>Typer av autentiseringsnivå

- Endast Microsoft-konto – för organisationer som vill använda, skapa och hantera användare via Microsoft-konton.
- Arbets- eller skolkonto – för organisationer som har konfigurerat Active Directory med federation till molnet och där alla konton finns i en enda klientorganisation.
- Arbets- eller skolkonto mellan klientorganisationer – för organisationer som har konfigurerat Active Directory med federation till molnet och kommer att ha konton i flera klientorganisationer.
- Blandat konto – gör att du kan lägga till användare med Microsoft-konto och/eller ett arbets- eller skolkonto.

Det första arbets- eller skolkonto som läggs till i registreringen avgör _standard-_ eller _huvuddomänen_. För att lägga till ett arbets- eller skolkonto med en annan klientorganisation måste du ändra autentiseringsnivån under registreringen till autentisering mellan klientorganisationer.

Så här uppdaterar du autentiseringsnivån:

1. Logga in på Azure EA-portalen som företagsadministratör.
2. Klicka på **Hantera** i det vänstra navigeringsfönstret.
3. Klicka på fliken **Registrering**.
4. Under **Registreringsinformation** väljer du **Autentiseringsnivå**.
5. Klicka på pennsymbolen.
6. Klicka på **Spara**.

![Exempel som visar autentiseringsnivåer ](./media/ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Microsoft-konton måste ha ett associerat ID som skapas på [https://signup.live.com](https://signup.live.com/).

Arbets- eller skolkonton är tillgängliga för organisationer som har konfigurerat Active Directory med federation och där alla konton finns i en enda klientorganisation. Användare kan läggas till med autentisering av extern användare i arbete eller skola om företagets interna Active Directory är federerad.

Om din organisation inte använder Active Directory-federation kan du inte använda din e-postadress för arbete eller skola. I stället registrerar eller skapar du en ny e-postadress och registrerar den som ett Microsoft-konto.

## <a name="unable-to-access-the-azure-ea-portal"></a>Det går inte att komma åt Azure EA-portalen

Om du får ett felmeddelande när du försöker logga in på Azure EA-portalen använder du följande felsökningssteg:

- Se till att du använder rätt URL för Azure EA-portalen, som är https://ea.azure.com.
- Ta reda på om din åtkomst till Azure EA-portalen lades till som ett arbets- eller skolkonto eller som ett Microsoft-konto.
  - Om du använder ditt arbetskonto anger du din e-postadress och ditt lösenord för arbete. Ditt arbetslösenord tillhandahålls av din organisation. Du kan höra med din IT-avdelning om hur du återställer lösenordet om det är problem med det.
  - Om du använder ett Microsoft-konto anger du e-postadressen och lösenordet för ditt Microsoft-konto. Om du har glömt lösenordet till Microsoft-kontot kan du återställa det på [https://account.live.com/password/reset](https://account.live.com/password/reset).
- Använd en privat eller inkognito webbläsarsession för att logga in så att inga cookies eller cachelagrad information från tidigare eller befintliga sessioner sparas. Rensa webbläsarens cache och använd ett privat eller inkognito fönster för att öppna https://ea.azure.com.
- Om du ser felet _Ogiltig användare_ när du använder ett Microsoft-konto kan det bero på att du har flera Microsoft-konton. Det som du försöker logga in med är inte den primära e-postadressen.
Eller om du får felet _Ogiltig användare_ så kan det bero på att fel kontotyp användes när användaren lades till i registreringen. Det kan till exempel ha varit ett arbets- eller skolkonto i stället för ett Microsoft-konto. I det här exemplet bör du be en annan EA-administratör att lägga till rätt konto, eller så behöver du kontakta [supporten](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c).
  - Om du behöver kontrollera det primära aliaset går du till [https://account.live.com](https://account.live.com). Klicka sedan på **Din information** och sedan på **Hantera hur du loggar in på Microsoft**. Följ anvisningarna för att verifiera en alternativ e-postadress och hämta en kod för att få åtkomst till känslig information. Ange säkerhetskoden. Välj **Konfigurera senare** om du inte vill konfigurera tvåfaktorautentisering.
  - Du ser sidan **Hantera hur du loggar in på Microsoft** där du kan visa dina kontoalias. Kontrollera att det primära aliaset är det som du använder för att logga in på Azure-EA-portalen. Om det inte är det kan du göra aliaset till ditt primära alias. Eller så kan du använda det primära aliaset för Azure EA-portalen i stället.

## <a name="no-activation-email-received"></a>Inget e-postmeddelande för aktivering har tagits emot

En e-postmeddelande för aktivering från Azure EA-portalen skickas från *waep@microsoft.com* . Om du inte har fått något aktiveringsmeddelande kan du se om det finns i mappen för spam eller skräp. Det skickas med _service_subject för inbjudan att visa/hantera Microsoft Azure_. Det skickas till varje nyligen tillagd EA-administratör.

Om du är säker på att du har konfigurerats som EA-administratör behöver du inte vänta på att ta emot aktiveringsmeddelandet innan du loggar in på Azure EA-portalen. I stället kan du gå till https://ea.azure.com och logga in med din e-postadress (arbets-, skol- eller Microsoft-konto) och ditt lösenord.

## <a name="azure-ea-activation-faq"></a>Vanliga frågor och svar om Azure EA-aktivering

Det här avsnittet i artikeln beskriver lösningar på vanliga problem runt Azure EA-aktivering.

### <a name="i-would-like-to-add-a-new-ea-administrator-to-my-enrollment"></a>Jag vill lägga till en ny EA-administratör i min registrering

En ny företagsadministratör kan läggas till av befintliga företagsadministratörer. Om du är EA-administratör loggar du in på EA-portalen > Klicka på **Manage** (Hantera) > Klicka på **+ Add Administrator** (+ Lägg till administratör) i det övre högra hörnet för att lägga till en ny EA-administratör. Se till att du har rätt e-postadress och en önskad inloggningsmetod, till exempel via autentisering från arbete/skola eller Microsoft Live ID för att lägga till användarna.

Om du inte är EA-administratör kontaktar du EA-administratören i ditt företag för att begära att du läggs till i registreringen. När du har lagts till i registreringen får du ett aktiveringsmeddelande via e-post.

Men om EA-administratörerna inte kan hjälpa dig kan vi lägga till dig för deras räkning om du kan ge oss följande:
- registreringsnumret
- e-postadressen som ska läggas till samt autentiseringstyp (arbete/skola/MS)
- e-postgodkännande från EA-administratören.

När du har all information som krävs skickar du en begäran på [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

### <a name="i-would-like-to-update-the-first-ea-admin-on-the-enrollment"></a>Jag vill uppdatera den första EA-administratören för registreringen

Den första EA-administratören kan uppdateras i Volume Licensing Service Center genom att uppdatera meddelandekontakten och onlineadministratören på portalen. Det tar upp till 24 timmar för EA-portalen att uppdateras. När den är uppdaterad får den nya EA-administratören aktiveringsmeddelande via e-post.

Om du inte har åtkomst till VLSC-portalen eller om den första EA-administratören inte längre kan hantera registreringen och inte har någon åtkomst till EA-portalen skickar du en begäran på [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) om en uppdatering och ger följande information:
- Registreringsnummer
- E-postadress som ska läggas till samt autentiseringstyp (arbete/skola/MS)
- Orsak till att ändra första EA-administratör
- E-postgodkännande från den första EA-administratören

### <a name="my-current-ea-admin-is-no-longer-with-the-company"></a>Min nuvarande EA-administratör finns inte längre i företaget

En EA-registrering kan ha flera EA-administratörer. Du kan kontakta en annan EA-administratör för att lägga till ny EA-administratör/kontoinnehavare/avdelningsadministratör. Men om du inte är tydlig med vem som är EA-administratör i företaget eller om det inte finns någon annan tillgänglig EA-administratör för registreringen kontaktar du oss med följande information:
- Registreringsnummer
- E-postadress som ska läggas till samt autentiseringstyp (arbete/skola/MS)
- Ge information om att aktuell EA-administratör inte längre finns i företaget

Observera att om det finns andra EA-administratörer för registreringen kontaktar vi EA-administratörerna för att begära godkännande vid administrativa ändringar av registreringen.

### <a name="my-enrollment-is-showing-in-pending-status-how-do-i-activate-my-enrollment"></a>Min registrering visas i väntande status. Hur aktiverar jag min registrering?

Registreringar har väntande status om den första EA-administratören inte har loggat in på registreringen tidigare. Om du är EA-administratör loggar du in på Azure EA-portalen. På landningssidan med alla dina registreringsnummer kanske du inte ser din väntande registrering. Avmarkera ”aktiv”-rutan i det övre högra hörnet i EA-portalen – en åtgärd som visar den väntande registreringen. Klicka på registreringen för att få åtkomst till informationen och när du har nått sidan Manage (Hantera) för registreringen uppdateras statusen från Pending (Väntar) till Active (Aktiv).

### <a name="why-is-my-account-stuck-in-pending-status"></a>Varför är mitt konto fast i väntande status?

När nya kontoinnehavare läggs till i registreringen för första gången visas de alltid med beteckningen ”väntar” under status. När kontoinnehavaren får välkomstmeddelandet kan han/hon logga in och aktivera sitt konto. När du loggar in uppdateras kontostatusen från ”väntande” till ”aktiv”.

### <a name="i-received-an-error-when-signing-in-to-azure-ea-portal"></a>Jag har fått ett felmeddelande vid inloggning till Azure EA-portalen

Det finns några möjliga orsaker till ett felmeddelande på Azure EA-portalen när du loggar in, följ dessa felsökningssteg:

 1. Se till att du använder rätt URL-adress för EA-portalen på [https://ea.azure.com](https://ea.azure.com).
 1. Ta reda på om din åtkomst till Azure EA-portalen har lagts till som ett arbets- eller skolkonto eller Microsoft Live ID. Om du använder ditt arbetskonto anger du din e-postadress och ditt lösenord för arbete. Om du använder Microsoft Live ID anger du din Live ID-e-post och ditt Microsoft Live ID-lösenord. Om du har glömt ditt Microsoft Live ID-lösenord kan du återställa det på [https://account.live.com/password/reset](https://account.live.com/password/reset).
 1. Vi rekommenderar att du använder en privat webbläsare för att logga in så att inga cookies och ingen cache från tidigare/befintliga sessioner bevaras. Rensa cachen och använd privat surfläge/inkognitofönster för att öppna [https://ea.azure.com](https://ea.azure.com).
 1. Om du får ett felmeddelande om ogiltig användare när du använder ett Microsoft-konto kan det bero på att du har flera Microsoft-konton och att det du försöker logga in med inte är det primära aliaset. Gå till account.live.com om du vill kontrollera primärt alias:
    - Gå till ”Din information” > Hantera e-postadress eller telefonnummer för inloggning.
    - Följ anvisningarna på skärmen för att verifiera en alternativ e-postadress och hämta en kod för att få åtkomst till känslig information.
    - Ange säkerhetskoden.
    - Om du föredrar att konfigurera tvåstegsautentisering senare väljer du ”Konfigurera senare”.
    - Du hamnar på sidan Hantera dina kontoalias där du ser de kontoalias som du har. Dubbelkontrollera att du använder det primära aliaset när du loggar in på Azure EA-portalen. Om du inte gör det kan du göra den till ditt primära alias eller så använder du det primära aliaset för EA-portalen.

Om felsökningsstegen ovan misslyckades skickar du en begäran på [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) med information som:
- Webbläsare och version som används.
- Skärmbild av felmeddelandet.
- URL för sidan med fel.  
- Datum, tid och tidszon för när felet inträffar.
- Dessutom hjälper det om du får en loggfil. Här är stegen för att fånga en nätverksspårning med hjälp av informationen nedan:
  1. Öppna Internet Explorer.
  1. Tryck på F12, så öppnas en ruta längst ned i IE.
  1. Välj fliken **Nätverk**.
  1. Klicka på **Börja registrera**.
  1. Utför åtgärden som orsakar felet.
  1. När du får felmeddelandet klickar du på **Sluta registrera**.
  1. Spara filen och ta med informationen i supportbegäran.
  1. Se till att ange ditt registreringsnummer och din e-postadress i supportbegäran.

### <a name="what-is-the-difference-between-a-workschool-account-and-microsoft-account"></a>Vad är skillnaden mellan arbets-/skolkonto och Microsoft-konto?

**Microsoft-konto:** Konton som har associerats med Live ID på [https://signup.live.com](https://signup.live.com).

**Arbets-/skolkonto:** Bara tillgängligt för företag som har konfigurerat Active Directory med federation till molnet och där alla konton finns i en enda klientorganisation. Användare kan läggas till med auktoriseringstypen arbete/skola om företagets interna Active Directory är federerat i molnet.

  Från september 2016 tillåter Microsoft inte längre att arbets- eller skolkonton registreras som Microsoft-konton. Mer information finns i följande material: [https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/](https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/).

  Om organisationen inte är federerad i molnet kan du inte använda din e-postadress för arbete eller skola. Registrera eller skapa en ny e-postadress och registrera den som ett Microsoft-konto istället.

### <a name="i-forgot-my-password-to-azure-ea-portal"></a>Jag har glömt mitt lösenord till Azure EA-portalen

Om du har glömt ditt Microsoft Live ID-lösenord kan du återställa det på [https://account.live.com/password/reset](https://account.live.com/password/reset).

Om du har glömt ditt lösenord till arbetet kontaktar du företagets IT-administratör.

### <a name="i-have-a-valid-work-or-school-account-but-i-cant-add-it-to-the-ea-portal"></a>Jag har ett giltigt arbets- eller skolkonto men kan inte lägga till det i EA-portalen

Om du har ett arbets- eller skolkonto under en annan klientorganisation ändrar du behörighetsnivån under registreringsinformationssidan till ”Work or School Account Cross Tenant” (Arbets- eller skolkonto mellan klientorganisationer) så kan du lägga till kontot.

## <a name="next-steps"></a>Nästa steg

- Administratörer i Azure EA-portalen bör läsa [Administration i Azure EA-portalen](ea-portal-administration.md). Där finns mer information om vanliga administrativa uppgifter.
