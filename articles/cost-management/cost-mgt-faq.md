---
title: Vanliga frågor om Cloudyn i Azure | Microsoft Docs
description: Den här artikeln innehåller svar på några vanliga frågor om Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: troubleshooting
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: faa7181ebc7b886fbe06e4ac01c704016d4b0c51
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230101"
---
# <a name="frequently-asked-questions-for-cloudyn"></a>Vanliga frågor om Cloudyn

Den här artikeln tar upp några vanliga frågor om Cloudyn. Om du har frågor om Cloudyn kan du ställa dem i [vanliga frågor och svar om Cloudyn](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-cloudyn-cost-management?forum=Cloudyn).

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Hur kan jag för att lösa vanliga problem med klientinstallationen indirekt enterprise?

När du använder Cloudyn-portalen första gången kan du se följande meddelanden, om du har ett Enterprise-avtal eller är en molnlösningsleverantör:

- "Den angivna API-nyckeln är inte en registrerings nyckel på högsta nivån som visas i guiden **Konfigurera Cloudyn** .
- ”Direktregistrering – inte” visas i portalen för Enterprise-avtal.
- ”Inga användningsdata hittades för de senaste 30 dagarna. Kontakta din återförsäljare för att försäkra markup har aktiverats för ditt Azure-konto ”visas i Cloudyn-portalen.

Föregående meddelanden indikerar att du har köpt ett Azure Enterprise-avtal genom en återförsäljare eller molntjänstleverantör. Återförsäljaren eller molntjänstleverantören måste aktivera _pålägg_ för ditt Azure-konto för att du ska kunna se dina data i Cloudyn.

Så här löser du problemen:

1. Återförsäljaren måste aktivera _pålägg_ för ditt konto. Mer information finns i [guiden för indirekt kundregistrering](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Du genererar Azure-nyckeln för Enterprise-avtal för användning med Cloudyn. Instruktioner finns i [lägga till ditt Azure EA](quick-register-ea.md#register-with-cloudyn) eller [så hittar du ditt EA-registrerings-ID och API-nyckel](https://youtu.be/u_phLs_udig).

Det är bara Azure-tjänstadministratörer som kan aktivera Cloudyn. Det räcker inte att vara medadministratör.

Innan du kan generera API-nyckeln för Azures Enterprise-avtal för att konfigurera Cloudyn, måste du aktivera fakturerings-API:n för Azure genom att följa anvisningarna i:

- [Overview of Reporting APIs for Enterprise customers](../billing/billing-enterprise-api.md) (Översikt över rapporterings-API:er för Enterprise-kunder)
- [Microsoft Azure enterprise portal Reporting API](https://ea.azure.com/helpdocs/reportingAPI) (Rapporterings-API för Microsoft Azure Enterprise Portal) under **Enabling data access to the API** (Aktivera dataåtkomst till API:et)


Du kanske även behöver ge avdelningsadministratörer, kontoägare och Enterprise-administratörer behörigheter att _visa debiteringar_ med fakturerings-API:et.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Varför ser jag optimering rekommendationer?

Rekommendationen information är bara tillgänglig för konton som är aktiverade. Du kan inte se någon rekommendations information i **optimerings** rapport kategorier för konton som är *inaktiverade*, inklusive:

- Optimering Manager
- Storleksoptimering
- Ineffektivitet

Om du inte ser några data för optimering rekommendation, är förmodligen ha du konton som är inaktiverade. Om du vill aktivera ett konto som du behöver registrera den med dina autentiseringsuppgifter för Azure.

Aktivera ett konto:

1.  Klicka på **Settings** (Inställningar) uppe till höger i Cloudyn-portalen och välj **Cloud Accounts** (Molnkonton).
2.  På fliken Microsoft Azure konton letar du efter konton som har en **inaktive rad** prenumeration.
3.  Till höger om ett konto som inte har inaktiverats klickar du på **redigerings** symbolen som liknar en penna.
4.  Din klient-ID och tariff-ID identifieras automatiskt. Klicka på **Nästa**.
5.  Du är omdirigeras till Azure-portalen. Logga in på portalen och godkänna Cloudyn insamlaren för att få åtkomst till dina Azure-data.
6.  Sedan omdirigeras du till sidan Cloudyn Accounts Management och din prenumeration har uppdaterats med statusen **aktiv** konto. Den visar en grön bock symbol.
7.  Om du inte ser en grön bockmarkering symbol för en eller flera prenumerationer, betyder det att du inte har behörighet att skapa en reader-appen (CloudynCollector) för prenumerationen. En användare med högre behörighet för prenumerationen måste du upprepa steg 3 och 4.  

När du har slutfört föregående steg kan du visa rekommendationer för optimering inom en till två dagar. Dock kan det ta upp till fem dagar innan fullständig optimering data är tillgängliga.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Hur aktiverar jag pausat eller utelåst användare?

Först ska vi titta på det vanligaste scenariot som gör det möjligt för användar konton att hämta *initiallySuspended*.

> Admin1 kan vara en provider för Microsoft Cloud lösning eller Enterprise-avtal användare. Organisationen är redo att börja använda Cloudyn.  Han registreras via Azure portal och loggar in på Cloudyn-portalen. När den person som registrerar Cloudyn-tjänsten och loggar in på Cloudyn-portalen blir admin1 *primär administratör*. Admin1 skapar inte alla användarkonton. Med hjälp av Cloudyn-portalen skapar de dock Azure-konton och konfigurerar en hierarki. Admin1 informerar admin2, en innehavaradministratör, som de behöver registrera med Cloudyn och logga in på Cloudyn-portalen.
>
> Admin2 registreras via Azure portal. Men när de försöker logga in på Cloudyn-portalen, får de ett fel meddelande om att deras konto har **pausats**. Den primära administratörskonto Admin1, är ett meddelande om detta konto. Admin1 måste aktivera Admin2's-konto och bevilja *åtkomst till administratörs entiteten* för lämpliga entiteter och ger användar hanterings åtkomst och aktiva användar kontot.


Om du får en avisering med en begäran om att tillåta åtkomst för en användare måste du aktivera användarkontot.

Aktivera användarkontot:

1. Logga in på Cloudyn genom att använda Azure administrativa användarkontot som du använde för att ställa in Cloudyn. Eller logga in med ett användarkonto som har beviljats administratörsbehörighet.
2. Välj kugg hjuls symbolen i det övre högra hörnet och välj **användar hantering**.
3. Hitta användaren, Välj pennan och sedan redigera användaren.
4. Under **användar status**ändrar du status från **inaktive rad till** **aktiv**.

Cloudyn-användarkontot ansluta med hjälp av enkel inloggning från Azure. Om en användare mistypes sitt lösenord, kanske de blir utelåst från Cloudyn, även om de har fortfarande åtkomst till Azure.

Om du ändrar din e-postadress i Cloudyn från standard adressen i Azure kan ditt konto bli utelåst. Det kan Visa "status initiallySuspended". Kontakta en annan administratör om du vill återställa ditt konto om ditt konto är låst.

Vi rekommenderar att du skapar minst två Cloudyn-administratörskonton om något av konton som hämtar utelåsta.

Om du inte logga in på Cloudyn-portalen, kontrollerar du att du använder rätt Webbadress för att logga in på Cloudyn. Använd [https://azure.cloudyn.com](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Undvik att använda Cloudyn Direct URL https://app.cloudyn.com.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Hur aktiverar jag inaktiverade konton med autentiseringsuppgifter för Azure?

När dina Azure-konton har identifierats av Cloudyn, tillhandahålla kostnadsdata direkt i rapporter baserade på kostnaden. Men för Cloudyn att tillhandahålla data för användning och prestanda behöver du registrera dina Azure-autentiseringsuppgifter för konton. Instruktioner finns i [lägga till ett konto eller uppdatera en prenumeration](activate-subs-accounts.md#add-an-account-or-update-a-subscription).

Välj Redigera symbolen till höger om namnet på kontot, inte prenumerationen för att lägga till Azure-autentiseringsuppgifter för ett konto i Cloudyn-portalen.

Innan dina Azure-autentiseringsuppgifter har lagts till i Cloudyn visas kontot som _avaktiverat_.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Hur lägger jag till flera konton och entiteter i en befintlig prenumeration?

Ytterligare enheter används för att lägga till ytterligare Enterprise-avtal i en Cloudyn-prenumeration. Mer information finns i [skapa och hantera entiteter](tutorial-user-access.md#create-and-manage-entities).

För CSP: er:

Om du vill lägga till ytterligare CSP-konton till en entitet väljer du **MSP-åtkomst** i stället för **Enterprise** när du skapar den nya entiteten. Om ditt konto registreras som ett Enterprise-avtal och du vill lägga till CSP-autentiseringsuppgifter, kan Cloudyn supportpersonal behöva ändra inställningarna för ditt konto. Om du är en betald Azure-prenumerant kan skapa du en ny supportbegäran i Azure-portalen. Välj **Hjälp + Support**och välj sedan **nytt support ärende**.

## <a name="currency-symbols-in-cloudyn-reports"></a>Valutasymboler i Cloudyn-rapporter

Du kan ha flera Azure-konton med hjälp av olika valutor. Kostnad-rapporter i Cloudyn visa men inte mer än en valutatyp per rapport.

Om du har flera prenumerationer som använder olika valutor visas en överordnad entitet och dess underordnade entiteter-valutor i USD **$** . Vår föreslagna bästa praxis är att undvika att använda olika valutor i samma entitetshierarki. Alla dina prenumerationer som är ordnade i en entitet kan med andra ord ska använda samma valuta.

Cloudyn identifierar din Enterprise Agreement-prenumeration valuta automatiskt och visas korrekt i rapporter.  Cloudyn visar dock bara USD **$** för CSP-och webb-Direct Azure-konton.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Vad är Cloudyn data Uppdatera tidslinjer?

Cloudyn innehåller följande datauppdatering tidslinjer:

- **Initial**: när du har konfigurerat kan det ta upp till 24 timmar innan kostnads data visas i Cloudyn. Det kan också ta upp till 10 dagar för Cloudyn samlar in tillräckligt med data för att visa storleksrekommendationer.
- **Dagligen**: från den tionde dagen till slutet av varje månad bör Cloudyn Visa dina data uppdaterade från föregående dag efter cirka UTC + 3 nästa dag.
- **Varje månad: från**den första dagen till den tionde dagen i varje månad kan Cloudyn bara visa dina data i slutet av föregående månad.

Cloudyn bearbetar data om föregående dag då fullständiga data från föregående dag är tillgänglig. Föregående dags data är vanligtvis tillgängliga i Cloudyn genom om UTC + 3 varje dag. Vissa data, som taggar, kan det ta ytterligare 24 timmar att bearbeta.

Data för den aktuella månaden är inte tillgängligt för samlingen i början av varje månad. Under perioden Slutför tjänstleverantörer sin fakturering för den föregående månaden. Den föregående månadens data visas i Cloudyn-5 till 10 dagar efter början av varje månad. Du kan se bara amorterade kostnader från den föregående månaden under denna tid. Du kan inte se dagliga fakturering och data. När data blir tillgängligt bearbetar Cloudyn den retroaktivt. Efter bearbetning visas alla månatliga data mellan den femte och tionde dagen i varje månad.

Om det finns en fördröjning som skickar data från Azure till Cloudyn, registreras fortfarande data i Azure. Data överförs till Cloudyn när anslutningen återupprättas.

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>Kostnad variationer i Cloudyn-kostnadsrapporter

Kostnadsrapporter kan visa kostnaden variationer när molntjänstleverantörer skickar uppdaterade filer för fakturering. Kostnaderna varierar inträffa när nya filer tas emot från en molntjänstleverantör utanför den vanliga dagliga och månatliga reporting schema. Kostnad ändringar inte beror på Cloudyn-omberäkning.

Under hela månaden är alla fakturering filer som skickas av din molntjänstleverantör en uppskattning av dina dagliga kostnader. Ibland data uppdateras ofta – ibland flera gånger per dag. Uppdateringarna är oftare med AWS än Azure. Kostnad summor hållas stabil när faktureringsberäkning för den föregående månaden är klar och den slutgiltiga fakturering filen tas emot. Vanligtvis av 10 i månaden.

Ändringar sker när du får kostnadsjusteringar från din molntjänstleverantör. Ta emot krediter är ett exempel. Ändringar kan inträffa flera månader efter den relevanta månaden har stängts. Ändringarna visas varje gång en omberäkning görs av din molntjänstleverantör. Cloudyn uppdaterar dess historiska data för att se till att alla justeringar räknas. Den kontrollerar också att kostnaderna visas korrekt i den rapporterar.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Hur kan en direkt CSP konfigurerar Cloudyn-åtkomst för indirekta CSP-kunder eller partner?

Instruktioner finns i [Konfigurera indirekt CSP-åtkomst i Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Vad som orsakar menyalternativet optimering visas?

När du har lagt till Azure Resource Manager åtkomst och data samlas in bör du se **optimerings** alternativet. Information om hur du aktiverar Azure Resource Manager åtkomst finns i [Hur gör jag för att aktivera inaktiverade konton med Azure-autentiseringsuppgifter?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cloudyn-agent-based"></a>Baseras Cloudyn-agenten?

Nej. Agenter som inte används. Azure-dator måttdata för virtuella datorer har samlats in från Microsoft Insights-API. Om du vill samla in mått data från virtuella Azure-datorer som de behöver ha inställningarna för startdiagnostik aktiverat.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Visar mer än en AD-klient per rapport i Cloudyn-rapporter?

Ja. Du kan [skapa en motsvarande moln konto enhet](tutorial-user-access.md#create-and-manage-entities) för varje AD-klient som du har. Du kan visa alla dina data för Azure AD-klient och andra molnleverantörer för plattform som du har, inklusive Amazon Web Services och Google Cloud Platform.
