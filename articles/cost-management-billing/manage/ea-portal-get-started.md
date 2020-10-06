---
title: Komma igång med Azure Enterprise-portalen
description: Den här artikeln förklarar hur kunder med Azure Enterprise-avtal (Azure EA) använder Azure Enterprise-portalen.
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 0404e3b268f5797e34b3818df8eebeefbe68a1f3
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371957"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Komma igång med Azure Enterprise-portalen

Den här hjälper direkta och indirekta kunder med Azure Enterprise-avtal (Azure EA) med att börja använda [Azure Enterprise-portalen](https://ea.azure.com). Du får grundläggande information om:

- Strukturen hos Azure Enterprise-portalen.
- Roller som används i Azure Enterprise-portalen.
- Skapande av prenumerationer.
- Kostnadsanalys i Azure Enterprise-portalen och i Azure-portalen.

Titta på den här videon för att se en fullständig registreringssession för Azure Enterprise-portalen:

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-enterprise-portal-hierarchy"></a>Hierarkin för Azure Enterprise-portalen

Hierarkin för Azure Enterprise-portalen består av:

- **Azure Enterprise-portalen** – en onlinehanteringsportal där du kan hantera kostnader för dina Azure EA-tjänster. Du kan:

  - Skapa en Azure EA-hierarki med avdelningar, konton och prenumerationer.
  - Stämma av kostnaderna för dina förbrukade tjänster, ladda ned användningsrapporter och visa prislistor.
  - Skapa API-nycklar för din registrering.

- **Avdelningar** hjälper dig att segmentera kostnader i logiska grupperingar. Med avdelningar kan du ange en budget eller kvot på avdelningsnivå.

- **Konton** är organisationsenheter i Azure Enterprise-portalen. Du kan använda konton för att hantera prenumerationer och komma åt rapporter.

- **Prenumerationer** är den minsta enheten i Azure Enterprise-portalen. De är containrar för Azure-tjänster som hanteras av tjänstadministratören.

Följande diagram illustrerar enkla Azure EA-hierarkier.

![Diagram över enkla Azure EA-hierarkier](./media/ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Roller för företagsanvändare

Följande administrativa användarroller ingår i din företagsregistrering:

- Företagsadministratör
- Avdelningsadministratör
- Kontoägare
- Tjänstadministratör
- Meddelandekontakt

Roller används i två olika portaler för att slutföra uppgifter. Du använder [Azure Enterprise-portalen](https://ea.azure.com) för att hantera fakturering och kostnader samt [Azure-portalen](https://portal.azure.com) för att hantera Azure-tjänster.

Användarroller är associerade med ett användarkonto. För att verifiera användarens äkthet måste varje användare ha ett giltigt arbets-, skol- eller Microsoft-konto. Se till att varje konto är associerat med en e-postadress som är aktivt övervakad. Kontomeddelanden skickas till e-postadressen.

När du konfigurerar användare kan du ge flera konton rollen som företagsadministratör. Dock kan endast ett konto ha rollen som kontoinnehavare. Du kan även tilldela både rollen företagsadministratör och kontoinnehavare till ett och samma konto.

### <a name="enterprise-administrator"></a>Företagsadministratör

Användare med den här rollen har den högsta åtkomstnivån. De kan:

- Hantera konton och kontoinnehavare.
- Hantera andra företagsadministratörer.
- Hantera avdelningsadministratörer.
- Hantera meddelandekontakter.
- Visa användning för alla konton.
- Visa odebiterade avgifter för alla konton.

Du kan ha flera företagsadministratörer i en företagsregistrering. Du kan bevilja skrivskyddad åtkomst till företagsadministratörer. De ärver alla rollen avdelningsadministratör.

### <a name="department-administrator"></a>Avdelningsadministratör

Användare med den här rollen kan:

- Skapa och hantera avdelningar.
- Skapa nya kontoinnehavare.
- Visa användningsinformation för de avdelningar som de hanterar.
- Visa kostnader om de har nödvändig behörighet.

Du kan ha flera avdelningsadministratörer för varje företagsregistrering.

Du kan bevilja avdelningsadministratörer skrivskyddad åtkomst när du redigerar eller skapar en ny avdelningsadministratör. Ange alternativet för skrivskydd till **Ja**.

### <a name="account-owner"></a>Kontoägare

Användare med den här rollen kan:

- Skapa och hantera prenumerationer.
- Hantera tjänstadministratörer.
- Visa användning för prenumerationer.

För varje konto krävs ett unikt arbets-, skol- eller Microsoft-konto. Mer information om administrativa roller i Azure Enterprise-portalen finns i [Förstå administrativa roller för Azure Enterprise-avtal i Azure](understand-ea-roles.md).

### <a name="service-administrator"></a>Tjänstadministratör

Rollen tjänstadministratör har behörighet att hantera tjänster i Azure-portalen och tilldela användare till rollen som medadministratör.

### <a name="notification-contact"></a>Meddelandekontakt

Meddelandekontakten får aviseringar om användning som gäller registreringen.

## <a name="activate-your-enrollment"></a>Aktivera din registrering

För att aktivera din tjänst öppnar den första företagsadministratören [Azure Enterprise-portalen](https://ea.azure.com) och loggar in med e-postadressen från inbjudningsmeddelandet.

Om du har konfigurerats som företagsadministratör behöver du inte få aktiveringsmeddelandet. Gå till [Azure Enterprise-portalen](https://ea.azure.com) och logga in med e-postadressen och lösenordet för ditt arbets-, skol- eller Microsoft-konto.

Om du har fler än en registrering väljer du en som ska aktiveras. Som standard visas endast aktiva registreringar. Om du vill visa registreringshistoriken avmarkerar du alternativet **Aktiv** överst till höger i Azure Enterprise-portalen.

Under **Registrering** visar statusen **Aktiv**.

![Exempel som visar en aktiv registrering](./media/ea-portal-get-started/ea-enrollment-status.png)

Endast befintliga Azure-företagsadministratörer kan skapa andra företagsadministratörer.

### <a name="create-another-enterprise-administrator"></a>Skapa en till företagsadministratör

Så här lägger du till ytterligare en företagsadministratör:

1. Logga in på [Azure Enterprise-portalen](https://ea.azure.com).
1. Gå till **Hantera** > **Registreringsinformation**.
1. Välj **+ Lägg till administratör** längst upp till höger.

Kontrollera att du har användarens e-postadress och önskad autentiseringsmetod, till exempel arbets-, skol- eller Microsoft-konto.

Om du inte är företagsadministratören kan du kontakta en företagsadministratör och be att den lägger till dig i en registrering. När du har blivit tillagd i en registrering får du ett aktiveringsmeddelande via e-post.

Om din företagsadministratör inte kan hjälpa dig skapar du en [supportbegäran i Azure Enterprise-portalen](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Ange följande information:

- Registreringsnummer
- E-postadress som ska läggas till samt autentiseringstyp (arbets-, skol- eller Microsoft-konto)
- E-postgodkännande från en befintlig företagsadministratör
  - Om den befintliga företagsadministratören inte är tillgänglig kontaktar du din partner eller programvarurådgivare och begär att de ändrar kontaktuppgifterna via VLSC-verktyget (Volume Licensing Service Center).

Mer information om administrativa företagsroller finns i [Förstå administrativa roller för Azure Enterprise-avtal i Azure](understand-ea-roles.md).

## <a name="create-an-azure-enterprise-department"></a>Skapa en Azure Enterprise-avdelning

Företagsadministratörer och avdelningsadministratörer använder avdelningar för att organisera och rapportera om Azure-tjänster för företag samt användning per avdelning och kostnadsställe. Företagsadministratören kan:

- Lägga till och ta bort avdelningar.
- Associera ett konto med en avdelning.
- Skapa avdelningsadministratörer.
- Tillåta att avdelningsadministratörer visar pris och kostnader.

En avdelningsadministratör kan lägga till nya konton till sina avdelningar. De kan ta bort konton från sina avdelningar men inte från registreringen.

Så här lägger du till en avdelning:

1. Logga in på Azure Enterprise-portalen.
1. I det vänstra fönstret väljer du **Hantera**.
1. Välj fliken **Avdelning** och sedan **+ Lägg till avdelning**.
1. Ange informationen.
   Avdelningsnamnet är det enda obligatoriska fältet. Det måste innehålla minst tre tecken.
1. Välj **Lägg till** när det är klart.

## <a name="add-a-department-administrator"></a>Lägga till en avdelningsadministratör

När en avdelning har skapats kan företagsadministratören lägga till avdelningsadministratörer och associera var och en av dem med en avdelning. Avdelningsadministratörer kan utföra följande åtgärder för sina avdelningar:

- Skapa andra avdelningsadministratörer
- Visa och redigera avdelningsegenskaper såsom namn och kostnadsställe
- Lägga till konton
- Ta bort konton
- Ladda ned användningsinformation
- Visa månatlig användning och avgifter <sup>1</sup>

> <sup>1</sup> En företagsadministratör måste bevilja dessa behörigheter. Om du fick behörighet att visa avdelningens månatliga användning och avgifter men inte kan se dem kontaktar du din partner.

### <a name="to-add-a-department-administrator"></a>Så här lägger du till en avdelningsadministratör

Som företagsadministratör:

1. Logga in på Azure Enterprise-portalen.
1. I det vänstra fönstret väljer du **Hantera**.
1. Välj fliken **Avdelning** och välj sedan avdelningen.
1. Välj **+ Lägg till administratör** och lägg till nödvändig information.
1. För skrivskyddad åtkomst anger du alternativet **Skrivskyddad** till **Ja** och väljer sedan **Lägg till**.

![Exempel som visar dialogrutan Lägg till avdelningsadministratör](./media/ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Ange skrivskyddad åtkomst

Du kan bevilja avdelningsadministratörer skrivskyddad åtkomst.

- När du skapar en ny avdelningsadministratör anger du alternativet för skrivskydd till **Ja**.

- Så här redigerar du en befintlig avdelningsadministratör:
   1. Välj en avdelning och välj sedan pennsymbolen intill den **avdelningsadministratör** som du vill redigera.
   1. Ange alternativet för skrivskydd till **Ja** och välj sedan **Spara**.

Företagsadministratörer får automatiskt behörighet som avdelningsadministratör.

## <a name="add-an-account"></a>Lägga till ett konto

Strukturen för konton och prenumerationer påverkar hur de administreras och hur de visas på dina fakturor och rapporter. Exempel på typiska organisationsstrukturer är affärsdivisioner, funktionella team och geografiska platser.

Så här lägger du till ett konto:

1. I Azure Enterprise-portalen väljer du **Hantera** i det vänstra navigeringsområdet.
1. Välj fliken **Konto**. På sidan **Konto** väljer du **+ Lägg till konto**.
1. Välj en avdelning eller låt den vara otilldelad, och välj sedan önskad autentiseringstyp.
1. Ange ett användarvänligt namn som identifierar kontot i rapportering.
1. Ange **kontoinnehavarens e-postadress** som ska associeras med det nya kontot.
1. Bekräfta e-postadressen och välj sedan **Lägg till**.

![Exempel som visar listan över konton och alternativet + Lägg till konto](./media/ea-portal-get-started/create-ea-add-an-account.png)

Om du vill lägga till ytterligare ett konto väljer du **Lägg till ett annat konto** eller väljer **Lägg till** i det nedre högra hörnet i det vänstra verktygsfältet.

Så här bekräftar du kontoägarskapet:

1. Logga in på Azure Enterprise-portalen.
1. Visa statusen.

   Statusen bör ändras från **Väntar** till **Start-/slutdatum**. Start-/slutdatum är det datum då användaren först loggade in samt avtalets slutdatum.
1. När **varningsmeddelandet** visas måste kontoinnehavaren välja **Fortsätt** för att aktivera kontot första gången kontoinnehavaren loggar in på Azure Enterprise-portalen.

## <a name="change-account-owner"></a>Ändra kontoägare

Företagsadministratörer kan använda Azure Enterprise-portalen för att överföra ägarskapet för prenumerationskonto i en registrering. Åtgärden flyttar alla prenumerationer från ett källanvändarkonto till ett målanvändarkonto.

Tänk på den här viktiga informationen när du överför konton:

- Du kan göra dessa överföringar:
  - Från ett arbets- eller skolkonto till ett annat arbets- eller skolkonto.
  - Från ett Microsoft-konto till ett arbets- eller skolkonto.
  - Från ett Microsoft-konto till ett annat Microsoft-konto.

    Målkontot måste vara ett giltigt Azure Commerce-konto för att vara ett giltigt mål för överföringar. För nya konton uppmanas du att skapa ett Azure Commerce-konto när du loggar in på Azure Enterprise-portalen. För befintliga konton måste du först skapa en ny Azure-prenumeration innan kontot blir berättigat.

- Du kan inte göra en överföring från ett arbets- eller skolkonto till ett Microsoft-konto.

- När du slutför en prenumerationsöverföring uppdaterar Microsoft kontoägaren.

Förstå dessa principer för rollbaserad åtkomst i Azure (Azure RBAC):

- När du utför prenumerationsöverföringar mellan två organisations-ID:n i samma klientorganisation bevaras Azure RBAC-principer och befintliga roller för tjänstadministratör och medadministratör.
- Andra prenumerationsöverföringar leder till att dina Azure RBAC-principer och rolltilldelningar går förlorade.
- Principer och administratörsroller överförs inte mellan olika kataloger. Tjänstadministratörer uppdateras till ägare av målkontot.

Innan du ändrar en kontoägare:

1. I Azure Enterprise-portalen visar du fliken **Konto** och identifierar källkontot. Källkontot måste vara aktivt.
1. Identifiera målkontot och kontrollera att det är aktivt.

Så här överför du kontoägarskap för alla prenumerationer:

1. Logga in på Azure Enterprise-portalen.
1. I det vänstra navigeringsfältet väljer du **Hantera**.
1. Välj fliken **Konto** och hovra över ett konto.
1. Välj ikonen för ändring av kontoägare till höger. Ikonen ser ut som en person.
1. Välj ett berättigat konto och välj sedan **Nästa**.
1. Bekräfta överföringen och välj **Skicka**.

![Bild som visar symbolen för ändring av kontoägare](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Så här överför du kontoägarskap för en enskild prenumeration:

1. Logga in på Azure Enterprise-portalen.
1. I det vänstra navigeringsfältet väljer du **Hantera**.
1. Välj fliken **Konto** och hovra över ett konto.
1. Välj ikonen för överföring av prenumerationer till höger. Ikonen ser ut som en sida.
1. Välj en berättigad prenumeration och välj sedan **Nästa**.
1. Bekräfta överföringen och välj sedan **Skicka**.

![Bild som visar symbolen för överföring av prenumerationer](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

Titta på den här videon för att se användarhantering i Azure Enterprise-portalen:

> [!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Skapa en prenumeration

Kontoägare kan visa och hantera prenumerationer. Du kan använda prenumerationer för att ge team i din organisation åtkomst till utvecklingsmiljöer och projekt. Det kan till exempel gälla testning, produktion, utveckling och mellanlagring.

När du skapar olika prenumerationer för varje programmiljö hjälper du till att skydda varje miljö.

- Du kan även tilldela ett annat tjänstadministratörskonto för varje prenumeration.
- Du kan associera prenumerationer med valfritt antal tjänster.
- Kontoägaren skapar prenumerationer och tilldelar ett tjänstadministratörskonto till varje prenumeration i sitt konto.

### <a name="add-a-subscription"></a>Lägga till en prenumeration

Använd följande information för att lägga till en prenumeration.

Första gången du lägger till en prenumeration i ditt konto uppmanas du att godkänna prenumerationsavtalet för Microsoft Online (MOSA) och en prisplan. Även om de inte gäller för Enterprise-avtalskunder krävs MOSA och prisplanen för att du ska kunna skapa din prenumeration. Tillägget till din Microsoft Azure Enterprise-avtalsregistrering ersätter ovanstående, och din kontraktsmässiga relation ändras inte. När du uppmanas väljer du den ruta som anger att du godkänner villkoren.

_Microsoft Azure Enterprise_ är standardnamnet när en prenumeration skapas. Du kan ändra namnet för att skilja det från andra prenumerationer i din registrering samt för att se till att det går att känna igen i rapporter för företagsnivå.

Så här lägger du till en prenumeration:

1. I Azure Enterprise-portalen loggar du in på kontot.
1. Välj fliken **Admin** och sedan **Prenumeration** längst upp på sidan.
1. Kontrollera att du är inloggad som kontoägare för kontot.
1. Välj **+ Lägg till prenumeration** och sedan **Köp**.

   Första gången du lägger till en prenumeration på ett konto måste du ange dina kontaktuppgifter. När du lägger till ytterligare prenumerationer läggs dina kontaktuppgifter till.

1. Välj **Prenumerationer** och välj sedan den prenumeration som du skapade.
1. Välj **Redigera prenumerationsinformation**.
1. Redigera **Prenumerationsnamn** och **Tjänstadministratör** och markera sedan kryssrutan.

   Prenumerationsnamnet visas i rapporter. Det är namnet på det projekt som är associerat med prenumerationen i utvecklingsportalen.

Det kan ta upp till 24 timmar innan nya prenumerationer visas i prenumerationslistan. När du har skapat en prenumeration kan du:

- [Redigera prenumerationsdetaljer](https://account.azure.com/Subscriptions)
- [Hantera prenumerationstjänster](https://portal.azure.com/#home)

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Överföra en Enterprise-prenumeration till en Betala per användning-prenumeration

Om du vill överföra en Enterprise-prenumeration till en enskild prenumeration med Betala per användning-priser måste du skapa en ny supportbegäran i Azure Enterprise-portalen. Du skapar en supportbegäran genom att välja **+ Ny supportbegäran** i området **Hjälp och support**.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Associera ett befintligt konto med din Betala per användning-prenumeration

Om du redan har ett befintligt Microsoft Azure-konto i Azure-portalen anger du det associerade skol-, arbets- eller Microsoft-kontot för att associera det med din Enterprise-avtalsregistrering.

### <a name="associate-an-existing-account"></a>Associera ett befintligt konto

1. I Azure Enterprise-portalen väljer du **Hantera**.
1. Välj fliken **Konto**.
1. Välj **+ Lägg till ett konto**.
1. Ange det arbets-, skol- eller Microsoft-konto som är associerat med det befintliga Azure-kontot.
1. Bekräfta det konto som är associerat med det befintliga Azure-kontot.
1. Ange ett namn du vill använda för att identifiera det här kontot i rapportering.
1. Välj **Lägg till**.
1. Du kan lägga till ytterligare ett konto genom att välja alternativet **+ Lägg till ett konto** igen eller gå tillbaka till startsidan genom att välja knappen **Admin**.
1. Om du visar sidan **Konto** visas det nyligen tillagda kontot med statusen **Väntar**.

### <a name="confirm-account-ownership"></a>Bekräfta kontoägarskapet

1. Logga in på det e-postkonto som är associerat med det arbets-, skol- eller Microsoft-konto som du angav.
1. Öppna e-postmeddelandet med titeln _"Invitation to Activate your Account on the Microsoft Azure Service from Microsoft Volume Licensing"_ (Inbjudan till att aktivera ditt konto på Microsoft Azure-tjänsten från Microsoft Volume Licensing).
1. Välj länken **Logga in på Microsoft Azure Enterprise Portal** i inbjudan.
1. Välj **Logga in**.
1. Ange ditt arbets-, skol- eller Microsoft-konto samt lösenordet för att logga in och bekräfta kontoägarskapet.

### <a name="azure-marketplace"></a>Azure Marketplace

De flesta prenumerationer kan konverteras från Betala per användning-miljön till Azure Enterprise-avtal, men inte Azure Marketplace-tjänster. För att du ska få en enda vy över alla prenumerationer och avgifter rekommenderar vi att du lägger till Azure Marketplace-tjänsterna till Azure Enterprise-portalen.

1. Logga in på Azure Enterprise-portalen.
1. Välj **Hantera** i det vänstra navigeringsfältet.
1. Välj fliken **Registrering**.
1. Visa avsnittet **Registreringsinformation**.
1. Till höger om Azure Marketplace-fältet väljer du pennikonen för att aktivera. Välj **Spara**.

Kontoinnehavaren kan nu köpa valfria Azure Marketplace-tjänster som tidigare ägdes i Betala per användning-prenumerationen.

När de nya Azure Marketplace-prenumerationerna har aktiverats under din Azure EA-registrering avbryter du de Azure Marketplace-tjänster som skapades i Betala per användning-miljön. Det här steget är viktigt så att dina Azure Marketplace-prenumerationer inte får en dålig status när ditt Betala per användning-betalningsmedel upphör.

### <a name="msdn"></a>MSDN

MSDN-prenumerationer konverteras automatiskt till MSDN Dev/Test, och Azure EA-erbjudandet förlorar eventuell befintlig betalningskredit.

### <a name="azure-in-open"></a>Azure i Open

Om du associerar en Azure i Open-prenumeration med ett Enterprise-avtal blir alla ej förbrukade Azure i Open-krediter ogiltiga. Vi rekommenderar därför att du förbrukar all kredit i en Azure i Open-prenumeration innan du lägger till kontot i ditt Enterprise-avtal.  

### <a name="accounts-with-support-subscriptions"></a>Konton med supportprenumerationer

Om ditt Enterprise-avtal inte har en supportprenumeration och du lägger till ett befintligt konto med en supportprenumeration till Azure Enterprise-portalen överförs inte din MOSA-supportprenumeration automatiskt. Du behöver då på nytt köpa en supportprenumeration i Azure EA under respitperioden – senast i slutet av efterföljande månad.

## <a name="view-usage-summary-and-download-reports"></a>Visa användningssammanfattning och nedladdning av rapporter

Företagsadministratörer kan visa en sammanfattning av användningsdata, förbrukad Azure-förskottsbetalning samt avgifter som är kopplade till ytterligare användning i Azure Enterprise-portalen. Avgifter presenteras på sammanfattningsnivå för alla konton och prenumerationer.

Om du vill visa detaljerad användning för specifika konton laddar du ned rapporten med användningsinformation:

1. Logga in på Azure Enterprise-portalen.
1. Välj **Rapporter**.
1. Välj fliken **Ladda ned användning**.
1. I listan över rapporter väljer du **Ladda ned** för den månadsrapport som du vill hämta.

   > [!NOTE]
   > Rapporten med användningsinformation inkluderar inga tillämpliga skatter.
   >
   > Det kan finnas en svarstid på upp till åtta timmar från den tidpunkt då användningen började gälla till dess att den återspeglas i rapporten.

Så här visar du rapporter och diagram för användningssammanfattning:

1. Logga in på Azure Enterprise-portalen.

1. Välj en förskottsbetalningsperiod.

   Om du vill ändra datumintervallet för **Användningssammanfattning** kan du växla från **M** (månatligen) till **C** (anpassad, Custom) längst upp till höger på sidan och sedan ange anpassade startdatum och slutdatum.

   ![Skapa och visa användningssammanfattning och ladda ned rapporter i anpassad vy](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. Om du vill visa mer information kan du välja en period eller månad i diagrammet.

   - Diagrammet visar användning månad över månad med en analys av utnyttjad användning, tjänstöverförbrukning, separat debiterade avgifter samt Azure Marketplace-avgifter.
   - För den valda månaden kan du använda fälten nedanför diagrammet för att filtrera efter avdelningar, konton och prenumerationer.
   - Du kan växla mellan **Avgifter efter tjänster** och **Avgifter efter hierarki**.
   - Visa information från **Azure-tjänst**, **Separat debiterade avgifter** och **Azure Marketplace** genom att expandera respektive avsnitt.

Titta på den här videon för att se hur du visar användning:

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Ladda ned CSV-rapporter

Företagsadministratörer använder sidan Nedladdning av månadsrapport för att ladda ned följande rapporter som CSV-filer:

- Saldo och avgifter
- Användningsinformation
- Azure Marketplace-avgifter
- Prisdokument

Så här laddar du ned rapporter:

1. Välj **Rapporter** i Azure Enterprise-portalen.
2. Välj **Ladda ned användning** längst upp på sidan.
3. Välj **Ladda ned** intill månadens rapport.

   > [!NOTE]
   > Det kan finnas en svarstid på upp till fem dagar från det gällande användningsdatumet till dess att användningen visas i rapporten.
   >
   > Användare som laddar ned CSV-filer med Safari till Excel kan få formateringsfel. Undvik fel genom att öppna filen med ett redigeringsprogram.

![Exempel som visar sidan Ladda ned användning](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

Titta på den här videon för att se hur du laddar ned användningsinformation:

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Nedladdning av avancerad rapport

Du kan använda nedladdning av avancerad rapport om du vill få rapporter som omfattar specifika datumintervall eller konton. Utdatafilen är i CSV-format för att tillgodose stora uppsättningar av poster.

1. I Azure Enterprise-portalen väljer du **Nedladdning av avancerad rapport**.
1. Välj ett lämpligt datumintervall och lämpliga konton.
1. Välj **Request Usage Data** (Begär användningsdata).
1. Välj knappen **Uppdatera** tills rapportens status uppdateras till **Ladda ned**.
1. Ladda ned rapporten.

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>Ladda ned användningsrapporter och faktureringsinformation för en tidigare registrering

Du kan ladda ned användningsrapporter och faktureringsinformation för en tidigare registrering efter att en registreringsöverföring har skett. Historisk rapportering finns i både Azure Enterprise-portalen och i Cost Management.

Azure Enterprise-portalen filtrerar bort inaktiva registreringar från vyn. Du behöver avmarkera kryssrutan **Aktiva** om du vill visa inaktiva överförda registreringar.  

![Om rutan Aktiva avmarkeras kan användaren se inaktiva registreringar](./media/ea-portal-get-started/unchecked-active-box.png)

## <a name="azure-ea-term-glossary"></a>Ordlista för Azure EA

- **Konto**: En organisationsenhet i Azure Enterprise-portalen. Den används för att administrera prenumerationer samt för rapportering.
- **Kontoinnehavare**: Den person som hanterar prenumerationer och tjänstadministratörer i Azure. Den kan visa användningsdata för det här kontot och dess associerade prenumerationer.
- **Ändringsprenumeration**: En ettårig eller sammanfallande prenumeration under registreringsändringen.
- **Förskottsbetalning**: Förskottsbetalning av ett årligt penningbelopp för Azure-tjänster till ett rabatterat förskottsbetalningspris för användning mot denna förskottsbetalning.
- **Avdelningsadministratör**: Den person som hanterar avdelningar, skapar nya konton och kontoinnehavare, visar användningsinformation för de avdelningar som den hanterar samt kan visa kostnader när den beviljas behörigheter.
- **Registreringsnummer**: En unik identifierare som tillhandahålls av Microsoft för att identifiera den specifika registrering associerad med ett Enterprise-avtal.
- **Företagsadministratör**: Den person som hanterar avdelningar, avdelningsägare, konton och kontoinnehavare i Azure. De kan hantera företagsadministratörer och visa data, fakturerade kvantiteter och ej fakturerade kostnader för alla konton och prenumerationer associerade med företagsregistreringen.
- **Enterprise-avtal**: Ett Microsoft-licensavtal för kunder med centraliserade inköp som vill standardisera hela organisationen på Microsoft-teknik och underhålla en IT-infrastruktur på en standard med Microsoft-programvara.
- **Enterprise-avtalsregistrering**: En registrering i Enterprise-avtalsprogrammet som tillhandahåller Microsoft-produkter i volym till rabatterade priser.
- **Microsoft-konto**: En webbaserad tjänst som gör att deltagande webbplatser kan autentisera en användare med en enskild uppsättning autentiseringsuppgifter.
- **Microsoft Azure Enterprise-registreringsändring (registreringsändring)** : En ändring som har signerats av ett företag, vilket ger åtkomst till Azure som en del av deras företagsregistrering.
- **Azure Enterprise-portalen**: Den portal som våra företagskunder använder för att hantera sina Azure-konton och tillhörande prenumerationer.
- **Förbrukad resurskvantitet**: Kvantiteten för en enskild Azure-tjänst som användes under en månad.
- **Tjänstadministratör**: Den person som kommer åt och hanterar prenumerationer och utvecklingsprojekt i Azure Enterprise-portalen.
- **Prenumeration**: Representerar en Azure Enterprise-portalprenumeration och är en container för Azure-tjänster som hanteras av samma tjänstadministratör.
- **Arbets- eller skolkonto**: För organisationer som har konfigurerat Active Directory med federation till molnet och där alla konton finns i en enda klientorganisation.

### <a name="enrollment-statuses"></a>Registreringsstatusar

- **Nytt**: Den här statusen tilldelas till en registrering som har skapats inom 24 timmar och kommer att uppdateras till väntande status inom 24 timmar.
- **Väntar**: Registreringsadministratören måste logga in på Azure Enterprise-portalen. Efter inloggningen växlar registreringen till aktiv status.
- **Aktiv**: Registreringen är aktiv och konton och prenumerationer kan skapas i Azure Enterprise-portalen. Registreringen är aktiv tills Enterprise-avtalets slutdatum.
- **Obegränsad utökad giltighet**: En obegränsad utökad giltighet sker efter Enterprise-avtalets slutdatum. Med den kan Azure EA-kunder som har valt den utökade perioden fortsätta använda Azure-tjänster på obegränsad tid i slutet av deras Enterprise-avtal.

   Innan Azure EA-registreringen når Enterprise-avtalets slutdatum bör registreringsadministratören välja vilket av följande alternativ som ska ske:

  - Förnya registreringen genom att utöka Azure-förskottsbetalningen.
  - Överföra till en ny registrering.
  - Migrera till MOSP-programmet (Microsoft Online Subscription program).
  - Bekräfta inaktiveringen av alla tjänster som är associerade med registreringen.
- **Upphört**: Azure EA-kunden avanmäls från den utökade giltigheten, och Azure EA-registreringen har nått Enterprise-avtalets slutdatum. Registreringen upphör att gälla och alla associerade tjänster kommer att inaktiveras.
- **Överförd**: Registreringar där alla associerade konton och tjänster har överförts till en ny registrering visas med en överförd status.
  >[!NOTE]
  > Registreringar överförs inte automatiskt om ett nytt registreringsnummer genereras under förnyandet. Du måste inkludera ditt tidigare registreringsnummer i dina förnyelseuppgifter för att medge automatisk överföring.

## <a name="get-started-on-azure-ea---faq"></a>Komma igång med Azure EA – Vanliga frågor och svar

Det här avsnittet innehåller information om vanliga frågor som ställts av kunder under registreringsprocessen.  

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>Kan jag associera mitt befintliga Azure-konto med Azure EA-registrering?

Ja. Alla Azure-prenumerationer som du är kontoinnehavare för konverteras till ditt Enterprise-avtal. Bland dessa ingår prenumerationer som använder månatlig kredit, till exempel Visual Studio, AzurePass, MPN, BizSpark och fler. Du kommer att förlora månadskredit när du konverterar sådana prenumerationer.

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>Jag associerade av misstag mitt befintliga Azure-konto med Azure EA-registrering. Därför har jag förlorat min månadskredit. Kan jag få tillbaka min månadskredit?

Om du har loggat in som Azure EA-kontoinnehavare med samma autentiseringsuppgifter som för din Visual Studio-prenumeration kan du återställa din enskilda Azure-förmån för Visual Studio-prenumeration genom att utföra någon av följande åtgärder:

- Ta bort kontoinnehavaren från Azure Enterprise-portalen efter att du har tagit bort eller flyttat eventuella associerade Azure-prenumerationer. Registrera dig sedan för enskilda Visual Studio Azure-förmåner på nytt.
- Ta bort Visual Studio-prenumeranten från administrationsplatsen i VLSC och tilldela om prenumerationen till ett konto med andra autentiseringsuppgifter den här gången. Registrera dig sedan för enskilda Visual Studio Azure-förmåner på nytt.

### <a name="what-type-of-subscription-should-i-create"></a>Vilken typ av prenumeration ska jag skapa?

Azure Enterprise-portalen har två typer av prenumerationer för företagskunder:

- Microsoft Azure Enterprise, som passar för:
  - All produktionsanvändning
  - Bästa priser baserat på infrastrukturutgifter

  [Kontakta Azure-säljavdelningen](https://azure.microsoft.com/pricing/enterprise-agreement/) om du vill få mer information.

- Enterprise Dev/Test, som passar för:
  - Alla utvecklar-/testarbetsbelastningar
  - Medelstor till stor enskild utvecklar-/testarbetsbelastning
  - Åtkomst till särskilda MSDN-avbildningar och priser för förmånstjänster

  Mer information finns i [Enterprise Dev/Test-erbjudande](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>Kan jag överföra äganderätten till prenumerationen till ett annat konto?

Ja, du kan överföra äganderätten till prenumerationen till ett annat konto. Exempel: Om konto A har tre prenumerationer kan företagsadministratören överföra en prenumeration till konto B, en till konto C och en till konto D. Det går även att överföra alla prenumerationer till konto E.

Så här överför du prenumerationer:

1. I Azure Enterprise-portalen väljer du **Hantera** > **Konto**.
1. Hovra över **Konto** längst till höger för att se alternativen **Överför ägarskap** (personikonen) och **Överför prenumeration** (listikonen). De här alternativen är endast synliga för aktiva konton.

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Mitt prenumerationsnamn är detsamma som namnet på erbjudandet. Ska jag ändra prenumerationsnamnet till något som är meningsfullt för min organisation?

När du skapar en prenumeration blir namnet som standard den erbjudandetyp som du väljer. Vi rekommenderar att du ändrar prenumerationsnamnet till något som gör det lätt för dig att spåra prenumerationen.

Så här ändrar du namnet:

1. Logga in på [https://account.windowsazure.com](https://account.windowsazure.com).
1. Välj prenumerationslistan.
1. Välj den prenumeration som du vill redigera.
1. Välj ikonen **Hantera prenumeration**.
1. Redigera prenumerationsinformation.

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>Hur spårar jag kostnader som uppstått på ett kostnadsställe?

För att spåra kostnader per kostnadsställe behöver du definiera kostnadsstället på någon av följande nivåer:

- Avdelning
- Konto
- Prenumeration

Utifrån dina behov kan du använda samma kostnadsställe för att spåra användning och kostnader som är associerade med ett visst kostnadsställe.

Om du till exempel vill spåra kostnaden för ett särskilt projekt där flera avdelningar är inblandade kan du definiera kostnadsstället på en prenumerationsnivå för att spåra användningen och kostnaderna.

Du kan inte definiera ett kostnadsställe på servicenivå. Om du vill spåra användning på servicenivå kan du använda funktionen _Tagg_, som finns på servicenivå.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Hur spårar jag användning och utgifter av olika avdelningar i min organisation?

Du kan skapa så många avdelningar som du behöver under din Azure EA-registrering. För att kunna spåra användningen på rätt sätt ska du se till att prenumerationer inte delas mellan olika avdelningar.

När du har skapat avdelningar och prenumerationer kan du se data i användningsrapporten. Den här informationen kan hjälpa dig att spåra användning samt hantera kostnader och utgifter på avdelningsnivå.

Du kan även komma åt användningsdata via rapporterings-API:et. Detaljerad information och exempelkod finns i [REST-API:er för Azure Enterprise](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-rest-apis).

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Kan jag ange en utgiftskvot och få aviseringar när jag närmar mig min gräns?

Du kan ange en utgiftskvot på avdelningsnivå, så meddelar systemet dig automatiskt när utgiftsgränsen når 50 %, 75 %, 90 % och 100 % av den kvot som du definierar.

Du definierar utgiftskvoten genom att välja en avdelning och sedan välja redigeringsikonen. När du har redigerat informationen för utgiftsgräns väljer du **Spara**.

### <a name="i-used-resource-groups-to-implement-azure-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Jag använde resursgrupper för att implementera Azure RBAC och spåra användning. Hur visar jag associerad användningsinformation?

Om du använder _resursgrupper_ och _taggar_ spåras den här informationen på servicenivå, och du kan komma åt den i nedladdningsfilen (CSV) för detaljerad användning. Se [ladda ned användningsrapport](https://ea.azure.com/report/downloadusage) i Azure Enterprise-portalen.

Du kan även komma åt användning via API. Detaljerad information och exempelkod finns i [REST-API:er för Azure Enterprise](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-rest-apis).

> [!NOTE]
> Du kan endast använda taggar på resurser som stöder Azure Resource Manager-åtgärder. Om du har skapat en virtuell dator, ett virtuellt nätverk eller lagring via den klassiska distributionsmodellen (t.ex. via den klassiska portalen) kan du inte använda en tagg på den resursen. Du måste distribuera dessa resurser igen via Resource Manager för att stödja taggning. Alla andra resurser stöder taggning.

### <a name="can-i-perform-analyses-using-power-bi"></a>Kan jag göra analyser med Power BI?

Ja. Med Microsoft Azure Enterprise-innehållspaketet för Power BI kan du:

- Snabbt importera och analysera Azure-förbrukning för din företagsregistrering.
- Ta reda på vilken avdelning, vilket konto eller vilken prenumeration som förbrukade mest användning.
- Lära dig vilken tjänst som din organisation använde mest.
- Spåra utgifts- och användningstrender.

Så här använder du Power BI:

1. Gå till Power BI-webbplatsen.
1. Logga in med ett giltigt arbets- eller skolkonto.

   Arbets- eller skolkontot kan vara detsamma eller ett annat än det som används för att nå registreringen via Azure Enterprise-portalen.
1. På instrumentpanelen för tjänster väljer du Microsoft Azure Enterprise-panelen och sedan **Anslut**.
1. På skärmen **Anslut till Azure Enterprise** anger du:
    - URL till Azure-miljön: [https://ea.azure.com](https://ea.azure.com)
    - Antal månader: mellan 1 och 36
    - Registreringsnummer: ditt registreringsnummer
1. Välj **Nästa**.
1. Ange API-nyckel i rutan **Kontonyckel**.

   Du hittar API-nyckeln i Azure Enterprise-portalen. Titta på fliken **Ladda ned användning** och välj sedan **API-åtkomstnyckel**. Kopiera den och klistra in nyckeln i rutan **Kontonyckel** i Power BI.

Beroende på datamängdens storlek kan det ta mellan fem och trettio minuter för data att läsas in Power BI.

Power BI-rapportering är tillgängligt för Azure EA-direktkunder, partner och indirekta kunder som kan visa faktureringsinformation.

## <a name="next-steps"></a>Nästa steg

- Administratörer i Azure Enterprise-portalen bör läsa [Administration i Azure Enterprise-portalen](ea-portal-administration.md). Där finns mer information om vanliga administrativa uppgifter.
- Om du behöver hjälp med att felsöka problem med Azure Enterprise-portalen kan du läsa [Felsöka åtkomst till Azure Enterprise-portalen](ea-portal-troubleshoot.md).
- En Azure EA-registreringsguide finns i [Azure EA-registreringsguiden (PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide).
