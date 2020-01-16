---
title: Komma igång med Azure EA-portalen
description: Den här artikeln förklarar hur Azure EA-kunder använder Azure EA-portalen.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: ace3c251d979a67666d2aaf01dca01e257bed66b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992236"
---
# <a name="get-started-with-the-azure-ea-portal"></a>Komma igång med Azure EA-portalen

Den här artikeln hjälper direkta och indirekta Azure EA-kunder att börja använda [Azure EA-portalen](https://ea.azure.com) med grundläggande information om:

- Hur Azure EA-portalen är strukturerad.
- Roller som används i Azure EA-portalen.
- Hur du börjar skapa prenumerationer.
- Analysera kostnader i Azure EA-portalen och Azure-portalen.

Här är en video som visar en fullständig introduktionssession för Azure EA-portalen:

[Introduktionsvideo för Azure EA-portalen ](https://www.youtube.com/watch?v=OiZ1GdBpo-I)

>[!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-ea-portal-hierarchy"></a>Hierarki för Azure EA-portalen

Hierarkin för Azure EA-portalen består av:

**Microsoft Azure EA-portalen** – Azure EA-portalen är en onlinebaserad hanteringsportal som hjälper dig att hantera kostnader för dina Azure EA-tjänster. Du använder den för att skapa en Azure EA-hierarki, däribland avdelningar, konton och prenumerationer. Du kan även använda den för att stämma av kostnaderna för dina förbrukade tjänster, ladda ned användningsrapporter och visa prislistor. Dessutom kan du skapa API-nycklar för din registrering.

**Avdelningar** – du skapar avdelningar för att hjälpa till att segmentera kostnader i logiska grupper och sedan ange en budget eller kvot på avdelningsnivå.

**Konton** – konton är organisationsenheter i Azure EA-portalen och används för att hantera prenumerationer. Konton används även för rapportering.

**Prenumerationer** – prenumerationer är den minsta enheten i Azure EA-portalen. De är containrar för Azure-tjänster som hanteras av tjänstadministratören.

Följande diagram illustrerar enkla Azure EA-hierarkier.

![Diagram över enkla Azure EA-hierarkier](./media/ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Roller för företagsanvändare

För att administrera Azure-tjänsterna i din registrering finns det fem olika administrativa användar roller i företaget:

- Företagsadministratör
- Avdelningsadministratör
- Kontoägare
- Tjänstadministratör
- Meddelande kontakt

Roller används för att utföra uppgifter i två olika Microsoft Azure-portaler. Azure EA-portalen (https://ea.azure.com) används för att hjälpa dig att hantera fakturering och kostnader. Azure-portalen (https://portal.azure.com) används för att hantera Azure-tjänster.

Användarroller är associerade med ett användarkonto. För att verifiera användarens äkthet måste varje användare ha ett giltigt arbets-, skol- eller Microsoft-konto. Se till att varje konto är associerat med en e-postadress som är aktivt övervakad. Kontomeddelanden skickas till e-postadressen.

När du konfigurerar användare kan du tilldela flera arbets-, skol- eller Microsoft-konton till rollen Företagsadministratör. Du kan dock bara tilldela ett arbets-, skol- eller Microsoft-konto till rollen Kontoägare. Dessutom kan ett enskilt arbets-, skol- eller Microsoft-konto tilldelas både rollen Företagsadministratör och Kontoägare.

### <a name="enterprise-administrator"></a>Företagsadministratör

Rollen Företagsadministratör har den högsta åtkomstnivån. Användare med den rollen kan:

- Hantera konton och kontoägare
- Hantera andra företagsadministratörer
- Hantera avdelningsadministratörer
- Hantera meddelandekontakter
- Visa användning för alla konton
- Visa odebiterade avgifter för alla konton

Du kan ha flera företagsadministratörer i en företagsregistrering. Du kan bevilja skrivskyddad åtkomst till företagsadministratörer. De ärver alla rollen avdelningsadministratör.

### <a name="department-administrator"></a>Avdelningsadministratör

Användare med den rollen kan:

- Skapa och hantera avdelningar
- Skapa nya kontoägare
- Visa användningsinformation för de avdelningar som de hanterar
- Visa kostnader om de har beviljats nödvändig behörighet

Du kan ha flera avdelningsadministratörer för varje företagsregistrering.

Du kan bevilja avdelningsadministratörer skrivskyddad åtkomst. Om du vill bevilja skrivskyddad åtkomst redigerar eller skapar du en ny avdelningsadministratör och anger alternativet för skrivskyddat till **Ja**.

### <a name="account-owner"></a>Kontoägare

Användare med den rollen kan:

- Skapa och hantera prenumerationer
- Hantera tjänstadministratörer
- Visa användning för prenumerationer

För varje konto krävs ett unikt arbets-, skol- eller Microsoft-konto. Mer information om administrativa roller i Azure EA-portalen finns i [Förstå administrativa roller för Azure Enterprise-avtal i Azure](understand-ea-roles.md).

### <a name="service-administrator"></a>Tjänstadministratör

Tjänstadministratören har behörighet att hantera tjänster i Azure-portalen och tilldela användare till rollen som medadministratör.

### <a name="notification-contact"></a>Meddelande kontakt

Meddelande kontakten får användnings meddelanden som rör registreringen.

## <a name="activate-your-enrollment"></a>Aktivera din registrering

För att aktivera din tjänst öppnar den första företagsadministratören Azure EA-portalen på [https://ea.azure.com](https://ea.azure.com) och loggar in med e-postadressen från inbjudningsmeddelandet.

Om du har kon figurer ATS som EA-administratör behöver du inte ta emot aktiverings meddelandet för att logga in på Azures EA-Portal. Du kan fortsätta till [https://ea.azure.com](https://ea.azure.com) och logga in med din e-postadress (antingen arbets-, skol-eller Live-ID) och lösen ord.

Om du har fler än en registrering väljer du en som ska aktiveras. Som standard visas endast aktiva registreringar. Om du vill visa registreringshistoriken avmarkerar alternativet **Aktiv** överst till höger i Azure EA-portalen.

Under Registrering visar statusen **Aktiv**.

![Exempel som visar en aktiv registrering](./media/ea-portal-get-started/ea-enrollment-status.png)

Endast befintliga Azure-företagsadministratörer kan skapa andra företagsadministratörer.

### <a name="create-another-enterprise-admin"></a>Skapa en till företagsadministratör

- Logga in på [Azure EA-portalen](https://ea.azure.com), gå till **Hantera** > **Registreringsinformation** och klicka på **+ Lägg till administratör** i det övre högra hörnet på sidan.

Kontrollera att du har användarens e-postadresser och önskad autentiseringsmetod, till exempel arbets- eller skolautentisering eller Microsoft-konto. Du behöver informationen för att lägga till en användare.

Om du inte är EA-administratören kan du kontakta en EA-administratör för att begära att den lägger till dig i en registrering. När du har blivit tillagd i en registrering får du ett aktiveringsmeddelande via e-post.

Om din EA-administratör inte kan hjälpa dig skapar du en [supportbegäran i Azure EA-portalen](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Ange följande information:

- Registreringsnummer
- E-postadress som ska läggas till samt autentiseringstyp (arbets-, skol- eller Microsoft-konto)
- E-postgodkännande från en befintlig EA-administratör
  - Om den befintliga EA-administratören inte är tillgänglig kontaktar du din partner eller programvarurådgivare och begär att de ändrar kontaktuppgifterna via VLSC-verktyget.

Mer information om administrativa företagsroller finns i [Förstå administrativa roller för Azure Enterprise-avtal i Azure](understand-ea-roles.md).

## <a name="create-an-azure-ea-department"></a>Skapa en Azure EA-avdelning

Företagsadministratörer och avdelningsadministratörer använder avdelningar för att organisera och rapportera om Azure-tjänster för företag samt användning per avdelning och kostnadsställe. Företagsadministratören kan:

- Lägga till och ta bort avdelningar
- Associera ett konto med en avdelning
- Skapa avdelningsadministratörer
- Tillåta att avdelningsadministratörer visar pris och kostnader

En avdelningsadministratör kan lägga till nya konton till sina avdelningar. De kan ta bort konton från sina avdelningar men inte från registreringen.

Så här lägger du till en avdelning:

1. I det vänstra navigeringsområdet klickar du på **Hantera**.
2. Klicka på fliken **Avdelning** följt av **+ Lägg till avdelning**, och ange sedan nödvändig information.
3. Avdelningsnamnet är det enda obligatoriska fältet. Det måste innehålla minst tre tecken.
4. När du är klar klickar du på **Lägg till**.

## <a name="add-a-department-admin"></a>Lägga till en avdelningsadministratör

När en avdelning skapas kan Azure-företagsadministratören lägga till avdelningsadministratörer och associera var och en av dem med en avdelning. Avdelningsadministratören kan:

- Skapa andra avdelningsadministratörer
- Visa och redigera avdelningsegenskaper såsom namn och kostnadsställe
- Lägga till ett konto för sina avdelningar
- Ta bort konton från sina avdelningar
- Ladda ned användningsinformation för sina avdelningar
- Visa månatlig användning och avgifter för sin avdelning om en företagsadministratör har gett dem behörighet <sup>1</sup>

### <a name="to-add-a-department-admin"></a>Lägga till en avdelningsadministratör

Som företagsadministratör:

1. I det vänstra navigeringsområdet klickar du på **Hantera**.
2. Klicka på fliken **Avdelning** och klicka sedan på avdelningen.
3. Klicka på **+ Lägg till administratör** och lägg till nödvändig information.
4. För skrivskyddad åtkomst anger du alternativet **Skrivskyddad** till **Ja** och klickar sedan på **Lägg till**.

![Exempel som visar dialogrutan Lägg till avdelningsadministratör](./media/ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Ange skrivskyddad åtkomst

Du kan bevilja avdelningsadministratörer skrivskyddad åtkomst. När du skapar en ny avdelningsadministratör:

- Ange alternativet för skrivskydd till **Ja**.

Så här redigerar du en befintlig avdelningsadministratör:

1. Välj en avdelning och klicka sedan på pennsymbolen intill den **avdelningsadministratör** som du vill redigera.
2. Ange alternativet för skrivskydd till **Ja**. Klicka sedan på **Spara**.

Användare med rollen företagsadministratör får automatiskt behörighet som avdelningsadministratör.

<sup>1</sup> Om du fick behörighet att visa avdelningens månatliga användning och avgifter men inte kan se dem kontaktar du din partner.

## <a name="add-an-account"></a>Lägga till ett konto

Konto- och prenumerationsstrukturen påverkar hur de administreras och hur de visas på dina fakturor och rapporter. Exempel på en typisk organisation är att strukturera utifrån affärsdivisioner, funktionella team och geografiska platser.

Så här lägger du till ett konto:

1. I Azure EA-portalen klickar du på **Hantera** i det vänstra navigeringsområdet.
2. Klicka på fliken **Konto**. På sidan **Konto** klickar du sedan på **+ Lägg till konto**.
3. Välj en avdelning eller låt den vara otilldelad, och välj sedan önskad autentiseringstyp.
4. Skriv ett användarvänligt namn som ska användas för att identifiera kontot i rapportering.
5. Ange **kontoägarens e-postadress** som ska associeras med det nya kontot.
6. Bekräfta e-postadressen och klicka sedan på **Lägg till**.

![Exempel som visar listan över konton och alternativet + Lägg till konto](./media/ea-portal-get-started/create-ea-add-an-account.png)

Du kan lägga till ett till konto genom att klicka på **Lägg till ett annat konto** eller genom att klicka på **Lägg till** i det nedre högra hörnet i det vänstra verktygsfältet.

Så här bekräftar du kontoägarskapet:

1. Logga in på Azure EA-portalen.
1. Bekräfta kontoägarskapet genom att visa statusen. Statusen bör ändras från **Väntar** till **Start-/slutdatum**. Start-/slutdatum är det datum då användaren först loggade in samt avtalets slutdatum.
1. När varningsmeddelandet visas måste kontoinnehavaren klickar på **Continue** (Fortsätt) för att aktivera kontot första gången kontoinnehavaren loggar in på Azure EA-portalen.


## <a name="change-account-owner"></a>Ändra kontoägare

Företagsadministratörer kan använda Azure EA-portalen för att överföra ägarskapet för prenumerationskonto i en registrering. Åtgärden flyttar alla prenumerationer från ett källanvändarkonto till ett målanvändarkonto.

Viktiga punkter om överföring av användarkontoinformation:

- Det finns stöd för överföringar från ett arbets- eller skolkonto till ett annat arbets- eller skolkonto.
- Det finns stöd för överföringar från ett Microsoft-konto till ett arbets- eller skolkonto.
- Det finns inte stöd för överföringar från ett arbets- eller skolkonto till ett Microsoft-konto.
- Det finns stöd för överföringar från ett Microsoft-konto till ett annat Microsoft-konto. Målkontot måste vara ett giltigt Azure Commerce-konto för att vara ett giltigt mål för överföringar. För nya konton uppmanas du att skapa ett Azure Commerce-konto när du loggar in på Azure EA-portalen. För befintliga konton måste du först skapa en ny Azure-prenumeration innan kontot blir berättigat.
- När du slutför en prenumerationsöverföring uppdaterar Microsoft kontoägaren.

Principer för rollbaserad åtkomstkontroll:

- Endast överföringar av Azure-prenumerationer mellan två organisations-ID:n i samma klientorganisation bevarar befintliga Azure-principer för rollbaserad åtkomstkontroll (RBAC), rolltilldelningar för tjänstadministratör samt rolltilldelningar för medadministratör. Andra prenumerationsöverföringar leder till att dina RBAC-principer och rolltilldelningar för tjänstadministratör och medadministratör går förlorade. Principer och administratörsroller överförs inte mellan olika kataloger. Tjänstadministratörer uppdateras till ägare av målkontot.
- När du utför prenumerationsöverföringar mellan två organisations-ID:n i samma klientorganisation bevaras RBAC-principer och befintliga roller för tjänstadministratör och medadministratör.

Innan du ändrar en kontoägare:

1. Visa fliken **Konto** och identifiera källkontot. Källkontot måste vara aktivt.
2. Identifiera målkontot. Det måste vara aktivt.

Så här överför du kontoägarskap för alla prenumerationer:

1. I det vänstra navigeringsområdet klickar du på **Hantera**.
2. Klicka på fliken **Konto** och hovra över ett konto.
3. Klicka på symbolen ändring av kontoägare till höger. Symbolen ser ut som en person.
4. Välj ett berättigat konto och klicka på **Nästa**.
5. Bekräfta överföringen och klicka på **Skicka**.

![Bild som visar symbolen för ändring av kontoägare](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Så här överför du kontoägarskap för en enskild prenumeration:

1. I det vänstra navigeringsområdet klickar du på **Hantera**.
2. Klicka på fliken **Konto** och hovra över ett konto.
3. Klicka på symbolen för överföring av prenumerationer till höger. Symbolen ser ut som en sida.
4. Välj en berättigad prenumeration och klicka på **Nästa**.
5. Bekräfta överföringen och klicka på **Skicka**.

![Bild som visar symbolen för överföring av prenumerationer](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

Här är en video som visar användarhantering i Azure EA-portalen:

[Video om användarhantering i Azure EA-portalen](https://www.youtube.com/watch?v=621jVkvmwm8)

>[!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Skapa en prenumeration

Kontoägare kan visa och hantera prenumerationer. Du kan använda prenumerationer för att ge team i din organisation åtkomst till utvecklingsmiljöer och projekt. Det kan till exempel gälla testning, produktion, utveckling och mellanlagring. När du skapar olika prenumerationer för varje programmiljö hjälper du till att skydda varje miljö. Du kan även tilldela ett annat tjänstadministratörskonto för varje prenumeration. Du kan associera prenumerationer med valfritt antal tjänster. Kontoägaren skapar prenumerationer och tilldelar ett tjänstadministratörskonto till varje prenumeration i sitt konto.

### <a name="add-a-subscription"></a>Lägga till en prenumeration

Använd följande information för att lägga till en prenumeration.

Första gången du lägger till en prenumeration på ditt konto uppmanas du att godkänna MOSA-avtalet och en prisplan. Även om de inte gäller för Enterprise-avtalskunder behövs de för att du ska kunna skapa din prenumeration. Tillägget till din Microsoft Azure Enterprise-avtalsregistrering ersätter ovanstående, och din kontraktsmässiga relation ändras inte. När du uppmanas väljer du den ruta som anger att du godkänner villkoren.

Alla nya prenumerationer skapas med det standardmässiga prenumerationsnamnet _Microsoft Azure Enterprise_. Du kan uppdatera prenumerationsnamnet för att skilja det från de andra prenumerationerna i din registrering. Det är även bra för att säkerställa att namnet går att känna igen i rapporter på företagsnivå.

Så här lägger du till en prenumeration:

1. I Azure EA-portalen loggar du in på kontot.
2. Klicka på fliken **Admin** och sedan på **Prenumeration** längst upp på sidan.
2. Kontrollera att du är inloggad som kontoägare för kontot.
3. Klicka på **+ Lägg till prenumeration** och sedan på **Köp**.
  Första gången du lägger till en prenumeration på ett konto måste du ange dina kontaktuppgifter. När du lägger till ytterligare prenumerationer läggs dina kontaktuppgifter till.
4. Klicka på **Prenumerationer**, välj den prenumeration som du skapade och klicka sedan på **Redigera prenumerationsdetaljer**.
5. Uppdatera **Prenumerationsnamn** och **Tjänstadministratör** och markera sedan kryssrutan.
  Prenumerationsnamnet visas på rapporter och är namnet på det projekt som är associerat med prenumerationen i utvecklingsportalen.

Det kan ta upp till 24 timmar innan nya prenumerationer visas i prenumerationslistan. När du har skapat en prenumeration kan du:

- [Redigera prenumerationsdetaljer](https://account.azure.com/Subscriptions)
- [Hantera prenumerationstjänster](https://portal.azure.com/#home)

## <a name="transfer-ea-subscription-to-pay-as-you-go-subscription"></a>Överföra EA-prenumeration till Betala per användning-prenumeration

Om du vill överföra en EA-prenumeration till en enskild prenumeration med Betala per användning-priser måste du skapa en ny supportbegäran i Azure EA-portalen. Du skapar en supportbegäran genom att klicka på **+ Ny supportbegäran** i området Hjälp och support.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Associera ett befintligt konto med din Betala per användning-prenumeration

Om du redan har ett befintligt Microsoft Azure-konto i Microsoft Azure-portalen anger du det associerade Microsoft-kontot eller arbets- eller skolkontot för att kunna associera det med din Enterprise-avtalsregistrering.

### <a name="associate-an-existing-account"></a>Associera ett befintligt konto

1. Klicka på **Manage** (Hantera) i EA-portalen.
1. Klicka på fliken **Account** (Konto).
1. Klicka på **+Add an account** (+Lägg till konto).
1. Ange det Microsoft-konto eller arbets- eller skolkonto som är associerat med det befintliga kontot.
1. Bekräfta det Microsoft-konto eller arbets- eller skolkonto som är associerat med det befintliga kontot.
1. Ange ett namn du vill använda för att identifiera det här kontot i rapportering.
1. Klicka på **Lägg till**.
1. Du kan lägga till ytterligare ett konto genom att välja alternativet **+Add an Account** (+Lägg till konto) igen eller gå tillbaka till startsidan genom att klicka på knappen **Administrator** (Administratör).
1. Om du klickar för att visa sidan **Account** (Konto) visas den nyligen tillagda sidan med statusen **Pending** (Väntar).

### <a name="confirm-account-ownership"></a>Bekräfta kontoägarskapet

1. Logga in på det e-postkonto som är associerat med det Microsoft-konto eller arbets- eller skolkonto du har angett.
1. Öppna e-postmeddelandet med titeln _"Invitation to Activate your Account on the Microsoft Azure Service from Microsoft Volume Licensing"_ (Inbjudan till att aktivera ditt konto på Microsoft Azure-tjänsten från Microsoft Volume Licensing).
1. Klicka på länken **Log into the Microsoft Azure Enterprise Portal** (Logga in på Microsoft Azure Enterprise Portal) i inbjudan.
1. Klicka på **Logga in**.
1. Ange ditt Microsoft-konto eller arbets- eller skolkonto och lösenord för att logga in och bekräfta kontoägarskapet.

### <a name="azure-marketplace"></a>Azure Marketplace

De flesta prenumerationer konverteras från Betala per användning-miljö till Enterprise Azure, men inte Azure Marketplace-tjänster. För att kunna ha en enda vy över alla prenumerationer och avgifter rekommenderar vi att du lägger till Azure Marketplace-tjänsterna till EA-portalen:

1. Klicka på **Hantera** i det vänstra navigeringsfältet.
1. Klicka på fliken **Hantera** (Registrering).
1. Visa avsnittet Enrollment Detail (Användningsinformation).
1. Till höger om Azure Marketplace-fältet klickar du på pennikonen för aktivering och trycker på **Save** (Spara).

Kontoinnehavaren kan nu köpa Azure Marketplace-prenumerationer som tidigare ägdes i Betala per användning.

När nya Azure Marketplace-prenumerationer aktiveras under din registrering avbryter du de Marketplace-prenumerationer skapade i Betala per användning-miljön. Det här steget är viktigt så att dina Marketplace-prenumerationer inte får en dålig status när ditt Betala per användning-betalningsmedel upphör.

### <a name="msdn"></a>MSDN

MSDN-prenumerationer konverteras automatiskt till MSDN Dev/Test och EA-erbjudandet förlorar eventuell befintlig betalningskredit.

### <a name="azure-in-open"></a>Azure i Open

Om du kopplar en Azure i Open-prenumeration till ett EA blir alla ej förbrukade Azure i Open-krediter ogiltiga. Vi rekommenderar att kunder förbrukar all sin kredit i en Azure i Open-prenumeration innan kontot läggs till i deras EA, för att undvika att krediter blir ogiltiga.  

### <a name="accounts-with-support-subscriptions"></a>Konton med supportprenumerationer

Observera att när du lägger till befintliga konton i EA-portalen som har en supportprenumeration (och inte redan har en EA-supportprenumerationer) så överförs inte MOSA-supportprenumerationen automatiskt och support måste köpas på nytt i EA. Du får en respitperiod för supporttäckning till slutet av efterföljande månad så att du har tid att beställa support på nytt.

## <a name="view-usage-summary-and-download-reports"></a>Visa användningssammanfattning och nedladdning av rapporter

Företagsadministratörer kan visa en sammanfattning av sina användningsdata, förbrukat ekonomiskt åtagande samt avgifter som är kopplade till ytterligare användning i Azure EA-portalen. Avgifter presenteras på sammanfattningsnivå för alla konton och prenumerationer.

Visa detaljerad användning för specifika konton:

Ladda ned rapporten med användningsinformation. Klicka på **rapporter** och klicka sedan på fliken **Hämta användning** . I listan över rapporter klickar du på **Ladda ned** för den månads rapport som du vill hämta.

Rapporten inkluderar inga tillämpliga skatter. Det kan finnas en svarstid på upp till åtta timmar från den tidpunkt då användningen började gälla till dess att den återspeglas i rapporten.

Så här visar du rapporter och diagram för användningssammanfattning:

1. I Azure EA-portalen går du till det vänstra navigeringsområdet, klickar på **Rapporter** och visar fliken **Användningssammanfattning**.  
  ![Skapa och visa användningssammanfattning och ladda ned rapporter](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports.png)
2. Välj en åtagandeperiod.
3. Växla mellan **M** (månatlig) och **C** (anpassad, Custom) längst upp till höger på sidan för att visa **Användningssammanfattning** med anpassade startdatum och slutdatum.  
  ![Skapa och visa användningssammanfattning och ladda ned rapporter i anpassad vy](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
4. Välj en period eller månad i diagrammet om du vill visa mer information.
5. Diagrammet visar användning månad över månad med en analys av utnyttjad användning, tjänstöverförbrukning, separat debiterade avgifter samt Marketplace-avgifter.
6. För den valda månaden filtrerar du efter avdelningar, konton och prenumerationer nedanför diagrammet.
7. Växla mellan **Avgifter efter tjänster** och **Avgifter efter hierarki**.
8. Expandera och dölj **Azure-tjänst**, **Separat debiterade avgifter** respektive **Azure Marketplace** om du vill se information.

Här är en video om hur du visar användning:

[Video om användning i Azure EA-portalen](https://www.youtube.com/watch?v=Cv2IZ9QCn9E)

>[!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Ladda ned CSV-rapporter

Företagsadministratörer använder sidan Nedladdning av månadsrapport för att ladda ned flera rapporter som CSV-filer. De omfattar:

- Saldo och avgifter
- Användningsinformation
- Marketplace-debiteringar
- Prisdokument

Så här laddar du ned rapporter:


1. I Azure EA-portalen klickar du på **Rapporter**.
2. Klicka på **Ladda ned användning** längst upp på sidan.
3. Välj **Ladda ned** intill månadens rapport.

Det kan finnas en svarstid på upp till fem dagar från det gällande användningsdatumet till dess att användningen visas i rapporten.

Användare som laddar ned CSV-filer med Safari till Excel kan få formateringsfel. Undvik fel genom att öppna filen med ett redigeringsprogram.

![Exempel som visar sidan Ladda ned användning](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

Här är en video som visar hur du laddar ned användningsinformation:

[Video om användning i Azure EA-portalen](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Nedladdning av avancerad rapport

Om du vill skapa rapporter för specifika datumintervall eller konton kan du välja nedladdning av avancerad rapport. Från och med 30 augusti 2016 ändras formatet på utdatafilen från .xlsx till .csv för att hantera större uppsättningar av poster.

1. Välj **Advanced Report Download** (Nedladdning av avancerad rapport).
1. Välj **Appropriate Date Range** (Lämpligt datumintervall).
1. Välj **Appropriate Accounts** (Lämpliga konton).
1. Välj **Request Usage Data** (Begär användningsdata).
1. Välj knappen **Refresh** (Uppdatera) tills rapportens status uppdateras till **Download** (Ladda ned).
1. Ladda ned rapporten.

## <a name="ea-term-glossary"></a>EA-ordlista

- **Konto**: en organisationsenhet på den Azure EA-portal som används för att administrera prenumerationer och använda för rapportering.
- **Konto ägare**: personen som identifierats för att hantera prenumerationer och tjänst administratörer på Microsoft Azure. Den kan visa användningsdata för det här kontot och dess associerade prenumerationer.
- **Ändrings prenumeration**: ett enda år eller en coterminous prenumeration under registrerings ändringen.
- **Åtagande**: åtagande av ett årligt penning belopp för Microsoft Azure tjänster till ett rabatterat pris för användning mot denna förskotts betalning.
- **Avdelnings administratör**: den eller de personer som identifieras för att hantera avdelningar, skapa nya konton och konto ägare, Visa användnings information för de avdelningar som de hanterar och Visa kostnader när de beviljas behörigheter.
- **Registrerings nummer**: en unik identifierare som tillhandahålls av Microsoft för att identifiera den specifika registrering som är associerad med ett Enterprise-avtal.
- **Företags administratör**: den eller de personer som identifieras för att hantera avdelningar och avdelnings ägare och konton och konto ägare på Microsoft Azure. De kan hantera företagsadministratörer och visa data, fakturerade kvantiteter och ej fakturerade kostnader för alla konton och prenumerationer associerade med företagsregistreringen.
- **Enterprise Agreement**: ett licens avtal för kunder med centraliserad inköp som vill standardisera hela organisationen på Microsoft-teknik och underhålla en infrastruktur för informations teknik på en Microsoft-programvara.
- **Registrering av företags avtal**: en registrering i Enterprise Agreement-programmet som tillhandahåller Microsoft-produkter i volym med rabatterat pris.
- **Microsoft-konto**: en webbaserad tjänst som gör att deltagande-platser kan autentisera en användare med en enda uppsättning autentiseringsuppgifter.
- **Microsoft Azure företags registrerings ändring (ändring av registrering)** : en ändring som signerats av ett företag, vilket ger dem åtkomst till Microsoft Azure som en del av företagets registrering.
- **Azure EA-portalen**: portalen som används av våra företags kunder för att hantera sina Microsoft Azure konton och tillhör ande prenumerationer.
- **Antal förbrukade resurser**: antalet för en enskild Microsoft Azure tjänst som användes under en månad.
- **Tjänst administratör**: personen som identifierats för att få åtkomst till och hantera prenumerationer och utvecklings projekt på AZUREs EA-Portal.
- **Prenumeration**: representerar en Azure EA Portal-prenumeration och är en behållare med Microsoft Azure tjänster som hanteras av samma tjänst administratör.
- **Arbets-eller skol konto**: för organisationer som har konfigurerat Active Directory med Federation till molnet och alla konton finns på en enda klient.

### <a name="enrollment-statuses"></a>Registreringsstatusar:

- **Väntar**: registrerings administratören måste logga in på Azure-EA-portalen. När du har loggat in byter registreringen till aktiv status.
- **Aktiv**: registreringen är aktiv och konton och prenumerationer kan skapas i Azure-EA-portalen. Registreringen är aktiv tills Enterprise-avtalets slutdatum.
- **Obegränsad utökad period**: en obegränsad förlängnings period sker efter det att slutdatumet för företags avtalet har passerat. Med den kan EA-kunder som har valt den utökade perioden fortsätta använda Azure på obegränsad tid i slutet av deras Enterprise-avtal. Innan EA-registreringen når Enterprise-avtalets slutdatum bör registreringsadministratören besluta om registreringen ska förnyas genom att lägga till ytterligare ekonomiskt åtagande överföra till en ny registrering, migrera till Microsoft Online Subscription Program (MOSP), eller bekräfta inaktivering av alla tjänster associerade med registreringen.
- **Utgånget**: EA-kunden har valt slut på den utökade termen och EA-registreringen har nått sitt slutdatum för företags avtal, registreringen upphör att gälla och alla tillhör ande tjänster kommer att inaktive ras.
- **Överfört**: registreringar där alla associerade konton och tjänster har överförts till en ny registrering visas med statusen överförd. Observera att registreringar inte överförs automatiskt om ett nytt registreringsnummer genereras vid förnyelsen. Det tidigare registreringsnumret måste tas med i kundens förnyelseuppgifter för att underlätta en automatisk överföring.

## <a name="get-started-on-azure-ea-faq"></a>Vanliga frågor och svar om att komma igång med Azure EA

Det här dokumentet innehåller information om vanliga frågor ställda av kunder under registreringsprocessen.  

### <a name="can-i-associate-my-existing-azure-account-to-enterprise-enrollment"></a>Kan jag koppla mitt Azure-konto till företagsregistrering?

Ja, det kan du. Viktigt att notera är att alla Azure-prenumerationer som du är kontoinnehavare för konverteras till ditt Enterprise-avtal. Det här omfattar prenumerationer som använder månadskredit (t.ex. Visual Studio, AzurePass, MPN och BizSpark), vilket betyder att du förlorar månadskredit genom att göra det.

### <a name="i-accidentally-associated-my-existing-azure-account-with-enterprise-enrollment-as-a-result-i-lost-my-monthly-credit-is-it-possible-to-get-my-monthly-credit-back"></a>Jag har råkat associera mitt befintliga Azure-konto med företagsregistrering. Därför har jag förlorat min månadskredit. Kan jag få tillbaka min månadskredit?

Om du vill återställa din individuella Azure-förmån för Visual Studio-prenumeration när du har autentiserats som EA-kontoinnehavare, efter att ha använt samma inloggning för EA som för din Visual Studio-prenumeration måste du göra något av följande:
1. Ta bort den här kontoinnehavaren från EA-portalen, efter att ha tagit bort eller flyttat Azure-prenumerationer som användaren äger och låta användaren registrera sig för de individuella Azure-förmånerna för Visual Studio på nytt.
 ELLER
1. Ta bort Visual Studio-prenumeranten från administrationswebbplatsen i VLSC och tilldela prenumerationen igen, den här gången med en annan inloggning. De kan sedan registrera sig för sina individuella Azure-förmåner för Visual Studio.

### <a name="what-type-of-subscription-should-i-create"></a>Vilken typ av prenumeration ska jag skapa?

EA-portalen har två typer av prenumerationer för företagskunder:

- Microsoft Azure Enterprise – passar för:
  - All produktionsanvändning
  - Bästa priser baserat på infrastrukturutgifter
  - Mer information finns på https://azure.microsoft.com/pricing/enterprise-agreement/
- Enterprise Dev/Test – passar för:
  - Alla utvecklar-/testarbetsbelastningar för grupper
  - Medelstor till stor enskild utvecklar-/testarbetsbelastning
  - Åtkomst till särskilda MSDN-avbildningar och priser för förmånstjänster
  - Mer information finns på https://azure.microsoft.com/offers/ms-azr-0148p/

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>Kan jag överföra äganderätten till prenumerationen till ett annat konto?

Ja, det är möjligt att överföra äganderätten till prenumerationen till ett annat konto. Exempel: Om konto A har tre prenumerationer kan företagsadministratören överföra en prenumeration till konto B, en till konto C och en till konto D eller alla till konto E.

Du kan gå till EA och klicka på Manage > Account (Hantera > Konto), hovra över **Account** (Konto) (längst till höger) så visas alternativet Transfer Ownership (Överför ägarskap) (porträttikon) och Transfer Subscription (Överför prenumeration) (listikon).

Det här alternativet visas bara för aktiva konton.

### <a name="i-see-subscription-name-defaults-to-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Jag ser att erbjudandenamn används som standard för prenumerationsnamn, ska jag ändra prenumerationsnamnet till något meningsfullt för min organisation?

Den typ av erbjudande du väljer används som standard för alla prenumerationer. Vi rekommenderar att du ändrar prenumerationsnamnet till något som gör det lätt för dig att spåra prenumerationen.

**Så här byter du namn:**
1. Logga in på [https://account.windowsazure.com](https://account.windowsazure.com).
1. Klicka på prenumerationslistan.
1. Välj Prenumeration.
1. Klick på ikonen **Hantera prenumeration**.
1. Redigera prenumerationsinformation.

### <a name="how-can-i-track-cost-incurred-by-cost-center"></a>Hur kan jag spåra kostnader som uppstått i kostnadsställe?

För att kunna spåra kostnader per kostnadsställe måste du definiera kostnadsställe på någon av följande nivåer:
- Avdelning
- Konto
- Prenumeration

Utifrån dina behov kan du använda samma kostnadsställe för att spåra användning och kostnader kopplade till ett visst kostnadsställe.

Om du till exempel vill spåra kostnaden för ett särskilt projekt där flera avdelningar är inblandade kan du vilja använda kostnadsstället på en prenumerationsnivå för att spåra användningen och kostnaden.

Du kan inte definiera kostnadsställe på tjänstnivå och om du vill spåra användning på tjänstnivå kan du använda taggfunktionen som finns på tjänstnivå.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Hur spårar jag användning och utgifter av olika avdelningar i min organisation?

Du kan skapa så många avdelningar du behöver under din EA-registrering. För att kunna spåra användningen på rätt sätt måste du se till att prenumerationer inte delas mellan olika avdelningar.

När avdelning och prenumeration har skapats kan du se information flöda i användningsrapporten som hjälper till att spåra användning och hantera kostnader/utgifter på avdelningsnivå.

Du kan också få åtkomst till användning via API – detaljerad information och exempelkod finns på [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI).

### <a name="can-i-set-the-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Kan jag ange utgiftskvot och få aviseringar när jag närmar mig min gräns?

Du kan ange utgiftskvot på avdelningsnivå och systemet meddelar dig automatiskt när utgiftsgränsen når 50 %, 75 %, 90 % och 100% av kvoten du definierar.

Om du vill definiera din utgiftskvot klickar du på den avdelning du vill lägga en utgiftsgräns för och klickar på redigeringsikonen. Klicka på **Save** (Spara) för att spara informationen.

### <a name="i-used-resource-groups-rgs-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Jag har använt resursgrupper för att implementera RBAC och spåra användning, hur visar jag den tillhörande användningsinformationen?

Information som ”resursgrupper” och ”tagg” spåras (om den används) på tjänstnivå och informationen är tillgänglig i nedladdningsfilen i CSV-format med detaljerad användning, som kan laddas ned från Azure EA-portalen [https://ea.azure.com/report/downloadusage](https://ea.azure.com/report/downloadusage).

Du kan också få åtkomst till användning via API – detaljerad information och exempelkod finns på [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI).

Observera att du bara kan använda taggar på resurser som stöder Resource Manager-åtgärder. Om du har skapat en virtuell dator, ett virtuellt nätverk eller lagring via den klassiska distributionsmodellen (t.ex. via den klassiska portalen) kan du inte använda en tagg på den resursen. Du måste distribuera dessa resurser igen via Resource Manager för att stödja taggning. Alla andra resurser stöder taggning.

### <a name="can-i-perform-analyses-using-power-bi"></a>Kan jag göra analyser med Power BI?

Ja. Med Microsoft Azure Enterprise-innehållspaketet för Power BI kan du snabbt importera och analysera Azure-förbrukningen för företagsdistributionen för Enterprise-registreringen. Ta reda på vilken avdelning, vilket konto eller prenumeration som stod för den största användningen, vilken tjänst som ditt företag utnyttjade mest eller spåra utgifts- och användningstrender.

**Navigera till Power BI-webbplatsen:**

 1. Logga in med ett giltigt arbets- eller skolkonto.
    - Arbets- eller skolkontot kan vara detsamma eller ett annat än det som används för att komma åt registreringen via Azure EA-portalen.
 1. På instrumentpanelen för tjänster väljer du:
    - Microsoft Azure Enterprise-panelen.
    - Klicka på **anslut**.
 1. På skärmen Connect to Azure Enterprise (Anslut till Azure Enterprise) väljer du:
    - URL till Azure-miljön: [https://ea.azure.com](https://ea.azure.com).
    - Antal månader: Välj ett värde mellan 1 och 36.
    - Registreringsnummer: ange registreringsnumret.
    - Klicka på **Next**.
 1. Ange API-nyckel i rutan Authentication Key (Autentiseringsnyckel). Du kan hämta API-nyckeln i Azure EA-portalen under fliken ”Download Usage” (Ladda ned användning). Klicka på **API Access Key** (API-åtkomstnyckel).
    - Kopiera och klistra in nyckeln i rutan för kontonyckel.
    - Det tar cirka 5-30 minuter att läsa in data i Power BI beroende på datamängdens storlek.

Power BI-rapportering är tillgängligt för EA-direktkunder, partner och indirekta kunder som kan visa faktureringsinformation.

## <a name="next-steps"></a>Nästa steg

- Administratörer i Azure EA-portalen bör läsa [Administration i Azure EA-portalen](ea-portal-administration.md). Där finns mer information om vanliga administrativa uppgifter.
- Om du behöver hjälp med att felsöka problem med Azure EA-portalen kan du läsa [Felsöka åtkomst till Azure EA-portalen](ea-portal-troubleshoot.md).
- Information om Azure EA-registreringsguiden finns i [Azure EA-registreringsguiden](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide).
