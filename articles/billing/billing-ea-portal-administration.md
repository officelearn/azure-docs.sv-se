---
title: Administration i Azure EA-portalen
description: I den här artikeln förklaras några vanliga uppgifter som administratörer utför i Azure EA-portalen.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/07/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 21e9d4af783ed5d9eb3ace1c8b5189163b89f8b0
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035216"
---
# <a name="azure-ea-portal-administration"></a>Administration i Azure EA-portalen

I den här artikeln förklaras några vanliga uppgifter som administratörer utför i Azure EA-portalen (https://ea.azure.com). Azure EA-portalen är en onlinehanteringsportal där kunder kan hantera kostnaden för sina Azure EA-tjänster. Grundläggande information om Azure EA-portalen finns i artikeln [Kom igång med Azure EA-portalen](billing-ea-portal-get-started.md).

## <a name="add-a-new-enterprise-administrator"></a>Lägga till en ny företagsadministratör

Företagsadministratörer har fullständig behörighet i hanteringen av en Azure EA-registrering. Den första Azure EA-administratören skapades när EA-avtalet tecknades. Du kan dock när som helst lägga till och ta bort administratörer. Det är bara befintliga administratörer som kan lägga till nya administratörer. Du kan läsa mer om att lägga till fler företagsadministratörer i [Skapa en ny företagsadministratör](billing-ea-portal-get-started.md#create-another-enterprise-admin). Mer information om roller och uppgifter för faktureringsprofiler finns i [Roller och uppgifter för faktureringsprofiler](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-user-state-from-pending-to-active"></a>Uppdatera användarstatus från Väntar till Aktiv

När nya kontoinnehavare först läggs till i en Azure EA-registrering är deras status _väntande_. När en ny kontoinnehavare får sitt aktiveringsmail kan kontoinnehavaren logga in och aktivera sitt konto. När kontoinnehavaren aktiverar sitt konto uppdateras kontostatusen från _väntande_ till _aktiv_. Nya användare kan uppmanas att ange sitt för- och efternamn och skapa ett handelskonto. I så fall måste personen ange den nödvändiga informationen innan kontot aktiveras.

## <a name="add-a-department-admin"></a>Lägga till en avdelningsadministratör

När en Azure EA-administratör skapar en avdelning så kan Azure-företagsadministratören lägga till avdelningsadministratörer och koppla dem till en avdelning. En avdelningsadministratör kan skapa nya konton. Nya konton behövs till att skapa Azure EA-prenumerationer.

Du kan läsa mer om att lägga till en avdelningsadministratör under [Skapa en Azure EA-avdelningsadministratör](billing-ea-portal-get-started.md#add-a-department-admin).

## <a name="enterprise-user-roles"></a>Roller för företagsanvändare

Du kan administrera kostnader och användning för Azure EA i Azure EA-portalen. Det finns tre huvudsakliga roller i Azure EA-portalen:

- EA-administratör
- Avdelningsadministratör
- Kontoinnehavare

De olika rollerna har olika åtkomstnivå och behörighet.

Du kan läsa mer om användarroller under Roller för företagsanvändare.

## <a name="add-an-azure-ea-account"></a>Lägga till ett Azure EA-konto

Azure EA-konton är en organisationsenhet i Azure EA-portalen som används till att administrera prenumerationer och skapa rapporter. Om du ska kunna komma åt och använda Azure-tjänster måste du eller någon annan skapa ett konto åt dig.

Du kan läsa mer om Azure-konton under Lägga till ett konto.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Överföra ett företagskonto till en ny registrering

Tänk på följande när du ska överföra ett företagskonto till en ny registrering:

- Det är bara de konton som anges i förfrågan som överförs. Om du väljer alla konton så överförs samtliga.
- Källregistreringen behåller statusen aktiv eller utvidgad. Du kan fortsätta att använda registreringen tills den upphör att gälla.

### <a name="effective-transfer-date"></a>Effektivt överföringsdatum

Det effektiva överföringsdatumet kan vara antingen startdatumet för registreringen du vill överföra till eller något senare datum. Den registrering du överför till är _målregistreringen_. När kontot har överförts behålls all användningsinformation för kontot från tiden innan det effektiva överföringsdatumet i registreringen du överför från. Registreringen du överför från är _källregistreringen_.  Användning i källregistreringen debiteras mot det ekonomiska åtagandet eller som en överförbrukning. Användning efter det effektiva överföringsdatumet överförs till den nya registreringen och debiteras där.

Du kan retroaktivt tidigarelägga en kontoöverföring ända tillbaka till målregistreringens startdatum. Alternativt till källregistreringens effektiva startdatum.

### <a name="monetary-commitment"></a>Ekonomiskt åtagande

Ekonomiska åtaganden kan inte överföras mellan registreringar. Saldon för ekonomiska åtaganden är avtalsmässigt bundna till den registrering där åtagandet beställdes. Ekonomiska åtaganden överförs inte när konton eller registreringar överförs.

### <a name="services-affected"></a>Inverkan på tjänster

Kontoöverföringar medför inga tjänsteavbrott. De kan genomföras samma dag som förfrågan inkommer om du anger all nödvändig information.

### <a name="prerequisites"></a>Nödvändiga komponenter

När du begär en kontoöverföring ska du ange följande information:


- Kontonamnet och id:t för innehavaren av det konto som ska överföras
- För källregistreringen, registreringsnumret och kontot som ska överföras
- För målregistreringen, registreringsnumret som överföringen ska göras till
- Det effektiva datumet för kontoöverföringen kan vara målregistreringens startdatum eller något senare datum

Här är några andra saker du bör tänka på innan en kontoöverföring:

- Du behöver godkännande från en EA-administratör för mål- och källregistreringen
  - I en del fall kan Microsoft begära ytterligare godkännande från en EA-administratör för källregistreringen
- Om en kontoöverföring inte uppfyller dina behov kan du överväga en registreringsöverföring.
- Kontoöverföringen överför alla tjänster och prenumerationer som tillhör aktuella konton.
- När överföringen är slutförd visas det överförda kontot som inaktivt under källregistreringen.
- Datum för kontoöverföring kan retroaktivt ändras till valfritt datum efter målregistreringens startdatum.
- Kontot visar ett slutdatum som motsvarar aktuellt överföringsdatum för källregistreringen och startdatum för målregistreringen.
- All användning som utförts på kontot innan det aktuella överföringsdatumet finns kvar under källregistreringen.


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Överföra en företagsregistrering till en ny registrering

När du begär att en hel företagsregistrering ska överföras till en ny registrering utförs följande åtgärder:

- Alla tjänster, prenumerationer, konton, avdelningar och hela registreringsstrukturen överförs, inklusive alla EA-avdelningsadministratörer.
- Registreringsstatus ställs in som _Överförd_. Den överförda registreringen är bara tillgänglig för rapporter om historisk användning.
- Du kan inte lägga till roller eller prenumerationer i en överförd registrering. Statusen Överförd förhindrar ytterligare användning mot registreringen.
- Eventuella saldon för ekonomiska åtaganden i registreringen går förlorade, inklusive framtida avtal.

### <a name="effective-transfer-date"></a>Effektivt överföringsdatum

Det effektiva överföringsdatumet kan vara antingen startdatumet för registreringen du vill överföra till målregistreringen eller något senare datum.

Användning i källregistreringen debiteras mot det ekonomiska åtagandet eller som en överförbrukning. Användning efter det effektiva överföringsdatumet överförs till den nya registreringen och debiteras där.

### <a name="effective-transfer-date-in-the-past"></a>Tidigarelagda effektiva överföringsdatum

Du kan retroaktivt tidigarelägga en kontoöverföring ända tillbaka till målregistreringens startdatum. Alternativt ända till källregistreringens effektiva startdatum.

### <a name="monetary-commitment"></a>Ekonomiskt åtagande

Ekonomiska åtaganden kan inte överföras mellan registreringar. Saldon för ekonomiska åtaganden är avtalsmässigt bundna till den registrering där åtagandet beställdes. Ekonomiska åtaganden överförs inte när konton eller registreringar överförs.

### <a name="services-affected"></a>Inverkan på tjänster

Kontoöverföringar medför inga tjänsteavbrott. De kan genomföras samma dag som förfrågan inkommer om du anger all nödvändig information.

### <a name="prerequisites"></a>Nödvändiga komponenter

När du begär överföring av en registrering ska du ange följande information:

- För källregistreringen, registreringsnumret och kontot som ska överföras.
- För målregistreringen, registreringsnumret som överföringen ska göras till.
- Det effektiva datumet för överföringen av registreringen kan vara målregistreringens startdatum eller något senare datum. Det valda datumet påverkar inte användningen i eventuella fakturor för överförbrukning som redan har utfärdats.

Här är några andra saker du bör tänka på innan du överför en registrering:

- Du behöver godkännande från en EA-administratör för mål- och källregistreringen.
  - I en del fall kan Microsoft begära ytterligare godkännande från en EA-administratör för källregistreringen.
- Om överföring av en registrering inte uppfyller dina behov kan du överväga en kontoöverföring.
- Det är bara de konton du anger som överförs. Du kan begära att alla konton ska överföras.
- Källregistreringen behåller statusen aktiv/utvidgad. Du kan fortsätta att använda registreringen tills den upphör att gälla.

## <a name="change-account-owner"></a>Ändra kontoinnehavare

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



## <a name="close-an-azure-enterprise-enrollment"></a>Stänga en Azure EA-registrering

Om du vill stänga din Azure EA-registrering kan du göra så här:

- Avbryt alla prenumerationer som är kopplade till Azure EA i Azure-portalen.
- Kontakta din kundrådgivare eller partner och be dem att stänga ditt Azure Enterprise-avtal.

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

## <a name="manage-partner-notifications"></a>Hantera partnermeddelanden

Partneradministratörer kan hantera hur ofta de ska få användningsmeddelanden för sina registreringar. De får automatiskt ett meddelande om utestående saldo varje vecka. De kan ändra hur ofta enskilda meddelanden ska skickas till varje månad, varje vecka, varje dag eller aldrig.

Om ett meddelande till en användare uteblir gör du så här för att kontrollera att användarens meddelandeinställningar är korrekta.

1. Logga in på Azure EA-portalen som partneradministratör.
2. Klicka på **Hantera** och sedan på fliken **Partner**.
3. Granska listan med administratörer i avsnittet **Administratör**.
4. Om du vill redigera meddelandeinställningarna hovrar du över administratören och klickar på pennikonen.
5. Uppdatera meddelandefrekvensen och livscykelmeddelandena efter behov.
6. Lägg till en kontakt om det behövs och klicka på **Lägg till**.
7. Klicka på **Spara**.

![Exempel som visar rutan Lägg till kontakt ](./media/billing-ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="azure-sponsorship-offer"></a>Azure-sponsringserbjudandet
Azure-sponsringserbjudandet är ett begränsat, sponsrat Microsoft Azure-konto. Inbjudan skickas via e-post till ett begränsat antal kunder som väljs ut av Microsoft. Om du är berättigad till Microsoft Azure-sponsringserbjudandet så har du fått en e-postinbjudan till ditt konto-id.
Mer information finns i:

- Översikt över sponsringserbjudandet – https://azure.microsoft.com/en-us/offers/ms-azr-0143p/
- Portalen för sponsringssaldon – https://www.microsoftazuresponsorships.com/balance  
- Vanliga frågor och svar om sponsring – https://azuresponsorships-staging.azurewebsites.net/faq
- Supportbegäran angående aktivering av sponsring – http://aka.ms/azrsponsorship

## <a name="next-steps"></a>Nästa steg
- Läs om hur du kan spara pengar med [reservationer för virtuella datorer](billing-ea-portal-vm-reservations.md).
- Om du behöver hjälp med att felsöka problem med Azure EA-portalen kan du läsa [Felsöka åtkomst till Azure EA-portalen](billing-ea-portal-troubleshoot.md).
