---
title: Komma igång med Azure EA-portalen
description: Den här artikeln förklarar hur Azure EA-kunder använder Azure EA-portalen.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 1882b283f376a1bb8706132263c83e1a24ec0705
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900938"
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

![Diagram över enkla Azure EA-hierarkier](./media/billing-ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Roller för företagsanvändare

För administration av Azure-tjänsterna i din registrering finns fyra olika administrativa roller för företagsanvändare:

- Företagsadministratör
- Avdelningsadministratör
- Kontoägare
- Tjänstadministratör

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

För varje konto krävs ett unikt arbets-, skol- eller Microsoft-konto. Mer information om administrativa roller i Azure EA-portalen finns i [Förstå administrativa roller för Azure Enterprise-avtal i Azure](billing-understand-ea-roles.md).

### <a name="service-administrator"></a>Tjänstadministratör

Tjänstadministratören har behörighet att hantera tjänster i Azure-portalen och tilldela användare till rollen som medadministratör.

## <a name="activate-your-enrollment"></a>Aktivera din registrering

För att aktivera din tjänst öppnar den första företagsadministratören Azure EA-portalen på [https://ea.azure.com](https://ea.azure.com) och loggar in med e-postadressen från inbjudningsmeddelandet.

Om du har fler än en registrering väljer du en som ska aktiveras. Som standard visas endast aktiva registreringar. Om du vill visa registreringshistoriken avmarkerar alternativet **Aktiv** överst till höger i Azure EA-portalen.

Under Registrering visar statusen **Aktiv**.

![Exempel som visar en aktiv registrering](./media/billing-ea-portal-get-started/ea-enrollment-status.png)

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

Mer information om administrativa företagsroller finns i [Förstå administrativa roller för Azure Enterprise-avtal i Azure](billing-understand-ea-roles.md).

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

![Exempel som visar dialogrutan Lägg till avdelningsadministratör](./media/billing-ea-portal-get-started/ea-create-add-department-admin.png)

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

![Exempel som visar listan över konton och alternativet + Lägg till konto](./media/billing-ea-portal-get-started/create-ea-add-an-account.png)

Du kan lägga till ett till konto genom att klicka på **Lägg till ett annat konto** eller genom att klicka på **Lägg till** i det nedre högra hörnet i det vänstra verktygsfältet.

Så här bekräftar du kontoägarskapet:

1. Logga in på Azure EA-portalen.
2. Bekräfta kontoägarskapet genom att visa statusen. Statusen bör ändras från **Väntar** till **Start-/slutdatum**. Start-/slutdatum är det datum då användaren först loggade in samt avtalets slutdatum.


## <a name="change-account-owner"></a>Ändra kontoägare

Företagsadministratörer kan använda Azure EA-portalen för att överföra ägarskapet för prenumerationskonto i en registrering. Åtgärden flyttar alla prenumerationer från ett källanvändarkonto till ett målanvändarkonto.

Viktiga punkter om överföring av användarkontoinformation:

- Det finns stöd för överföringar från ett arbets- eller skolkonto till ett annat arbets- eller skolkonto.
- Det finns stöd för överföringar från ett Microsoft-konto till ett arbets- eller skolkonto.
- Det finns inte stöd för överföringar från ett arbets- eller skolkonto till ett Microsoft-konto.
- Det finns stöd för överföringar från ett Microsoft-konto till ett annat Microsoft-konto. Målkontot måste vara ett giltigt Azure Commerce-konto för att vara ett giltigt mål för överföringar. För nya konton uppmanas du att skapa ett Azure Commerce-konto när du loggar in på Azure EA-portalen. För befintliga konton måste du först skapa en ny Azure-prenumeration innan kontot blir berättigat.
- När du slutför en prenumerationsöverföring uppdaterar Microsoft kontoägaren.

RBAC-principer:

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

![Bild som visar symbolen för ändring av kontoägare](./media/billing-ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Så här överför du kontoägarskap för en enskild prenumeration:

1. I det vänstra navigeringsområdet klickar du på **Hantera**.
2. Klicka på fliken **Konto** och hovra över ett konto.
3. Klicka på symbolen för överföring av prenumerationer till höger. Symbolen ser ut som en sida.
4. Välj en berättigad prenumeration och klicka på **Nästa**.
5. Bekräfta överföringen och klicka på **Skicka**.

![Bild som visar symbolen för överföring av prenumerationer](./media/billing-ea-portal-get-started/ea-transfer-subscriptions.png)

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

## <a name="transfer-pay-as-you-go-subscription-to-ea-subscription"></a>Överföra Betala per användning-prenumeration till EA-prenumeration

Om du vill överföra en enskild prenumeration med Betala per användning-priser till en EA-prenumeration måste du skapa en ny supportbegäran i Azure-portalen. Du skapar en supportbegäran genom att klicka på **+ Ny supportbegäran** i området Hjälp och support.


## <a name="view-usage-summary-and-download-reports"></a>Visa användningssammanfattning och nedladdning av rapporter

Företagsadministratörer kan visa en sammanfattning av sina användningsdata, förbrukat ekonomiskt åtagande samt avgifter som är kopplade till ytterligare användning i Azure EA-portalen. Avgifter presenteras på sammanfattningsnivå för alla konton och prenumerationer.

Visa detaljerad användning för specifika konton

Ladda ned rapporten med användningsinformation. Klicka på **Rapporter** och sedan på fliken **Ladda ned användning**. I listan över rapporter klickar du på **Ladda ned** för den månadsrapport som du vill hämta.

Rapporten inkluderar inga tillämpliga skatter. Det kan finnas en svarstid på upp till åtta timmar från den tidpunkt då användningen började gälla till dess att den återspeglas i rapporten.

Så här visar du rapporter och diagram för användningssammanfattning:

1. I Azure EA-portalen går du till det vänstra navigeringsområdet, klickar på **Rapporter** och visar fliken **Användningssammanfattning**.  
  ![](./media/billing-ea-portal-get-started/create-ea-view usage-summary-and-download-reports.png)
2. Välj en åtagandeperiod.
3. Växla mellan **M** (månatlig) och **C** (anpassad, Custom) längst upp till höger på sidan för att visa **Användningssammanfattning** med anpassade startdatum och slutdatum.  
  ![](./media/billing-ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
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

![Exempel som visar sidan Ladda ned användning](./media/billing-ea-portal-get-started/create-ea-download-csv-reports.png)

Här är en video som visar hur du laddar ned användningsinformation:

[Video om användning i Azure EA-portalen](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

## <a name="schedule-an-onboarding-call"></a>Schemalägga ett introduktionssamtal

Om du vill schemalägga en personlig introduktionssession med en kund skapar du en supportbegäran i [supporten för Azure EA-portalen](https://support.microsoft.com/supportrequestform/e114582c-4e51-af46-10b1-1f0cc141e133). Välj **Introduktion** som **Problemkategori**.

## <a name="next-steps"></a>Nästa steg
- Administratörer i Azure EA-portalen bör läsa [Administration i Azure EA-portalen](billing-ea-portal-administration.md). Där finns mer information om vanliga administrativa uppgifter.
- Om du behöver hjälp med att felsöka problem med Azure EA-portalen kan du läsa [Felsöka åtkomst till Azure EA-portalen](billing-ea-portal-troubleshoot.md).
