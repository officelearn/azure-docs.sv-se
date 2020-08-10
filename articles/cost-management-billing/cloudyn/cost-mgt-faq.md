---
title: Vanliga frågor och svar om Cloudyn i Azure
description: Lär dig hur du använder Cloudyn-portalen för att lösa vanliga problem med indirekt Enterprise-konfiguration och få svar på andra vanliga frågor.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: b9d19923302a40985906fa3c2e0e183045a95860
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460464"
---
# <a name="frequently-asked-questions-for-cloudyn"></a>Vanliga frågor och svar om Cloudyn

Den här artikeln besvarar några vanliga frågor om Cloudyn. Om du har frågor om Cloudyn kan du ställa dem på [Vanliga frågor och svar om Cloudyn](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-cloudyn-cost-management?forum=Cloudyn).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Hur löser jag vanliga problem med indirekt Enterprise-konfiguration?

När du använder Cloudyn-portalen första gången kan du se följande meddelanden, om du har ett Enterprise-avtal eller är en molnlösningsleverantör:

- ”Den angivna API-nyckeln är inte en registreringsnyckel på toppnivå” visas i guiden **Konfigurera**.
- ”Direktregistrering – nej” visas i Enterprise-avtalsportalen.
- ”Inga användningsdata hittades för de senaste 30 dagarna. Kontakta återförsäljaren för att kontrollera om pålägg har aktiverats för ditt Azure-konto” visas i Cloudyn-portalen.

Föregående meddelanden indikerar att du har köpt ett Azure Enterprise-avtal genom en återförsäljare eller molntjänstleverantör. Återförsäljaren eller molntjänstleverantören måste aktivera _pålägg_ för ditt Azure-konto för att du ska kunna se dina data i Cloudyn.

Så här löser du problemen:

1. Återförsäljaren måste aktivera _pålägg_ för ditt konto. Mer information finns i [guiden för indirekt kundregistrering](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Du genererar Azure-nyckeln för Enterprise-avtal för användning med Cloudyn. Instruktioner finns i [Lägga till ditt Azure EA](quick-register-ea.md#register-with-cloudyn) eller [Hitta registrerings-ID och API-nyckel för EA](https://youtu.be/u_phLs_udig).

Det är bara Azure-tjänstadministratörer som kan aktivera Cloudyn. Det räcker inte att vara medadministratör.

Innan du kan generera API-nyckeln för Azures Enterprise-avtal för att konfigurera Cloudyn, måste du aktivera fakturerings-API:n för Azure genom att följa anvisningarna i:

- [Overview of Reporting APIs for Enterprise customers](../manage/enterprise-api.md) (Översikt över rapporterings-API:er för Enterprise-kunder)
- [Microsoft Azure enterprise portal Reporting API](https://ea.azure.com/helpdocs/reportingAPI) (Rapporterings-API för Microsoft Azure Enterprise Portal) under **Enabling data access to the API** (Aktivera dataåtkomst till API:et)


Du kanske även behöver ge avdelningsadministratörer, kontoägare och Enterprise-administratörer behörigheter att _visa debiteringar_ med fakturerings-API:et.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Varför ser jag inte Optimizer-rekommendationer?

Rekommendationsinformation är bara tillgänglig för konton som är aktiverade. Ingen rekommendationsinformation visas i **Optimizer**-rapportkategorier för konton som är *inaktiverade*, inklusive:

- Optimization Manager
- Storleksoptimering
- Ineffektivitet

Om du inte kan visa några Optimizer-rekommendationsdata har du troligen konton som inte är aktiverade. För att kunna aktivera ett konto måste du registrera det med dina Azure-autentiseringsuppgifter.

Så här aktiverar du ett konto:

1.    Klicka på **Settings** (Inställningar) uppe till höger i Cloudyn-portalen och välj **Cloud Accounts** (Molnkonton).
2.    På fliken Microsoft Azure konton letar du upp konton som har en **inaktiverad** prenumeration.
3.    Till höger om ett konto som inte har inaktiverats klickar du på symbolen **redigera**, som liknar en penna.
4.    Ditt klientorganisations-ID och avgifts-ID identifieras automatiskt. Klicka på **Nästa**.
5.    Du omdirigeras till Azure Portal. Logga in på portalen och auktorisera Cloudyn-insamlaren att få åtkomst till dina Azure-data.
6.    Sedan omdirigeras du till sidan för kontohantering i Cloudyn och din prenumeration uppdateras med kontostatusen **Aktiv**. En grön bock visas.
7.    Om du inte ser en grön bock för en eller flera av prenumerationerna betyder det att du inte har behörighet att skapa en läsarapp (CloudynCollector) för prenumerationen. En användare med högre behörighet för prenumerationen måste upprepa steg 3 och 4.  

När du har slutfört ovanstående steg kan du visa Optimizer-rekommendationer inom en till två dagar. Det kan dock ta upp till fem dagar innan fullständiga optimeringsdata är tillgängliga.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Hur aktiverar jag inaktiverade eller låsta användare?

Först ska vi titta på det vanligaste scenariot som gör att användarkonton blir *initiallySuspended*.

> Admin1 kan vara en Microsoft Cloud Solution Provider eller en Enterprise-avtalsanvändare. Organisationen är redo att börja använda Cloudyn.  Personen registrerar sig via Azure-portalen och loggar in på Cloudyn-portalen. Som den person som registrerar Cloudyn-tjänsten och loggar in på Cloudyn-portalen blir Admin1 den *primära administratören*. Admin1 skapar inte några användarkonton. Med hjälp av Cloudyn-portalen skapar dock denna person Azure-konton och konfigurerar en entitetshierarki. Admin1 informerar Admin2, en klientadministratör, att denne behöver registrera sig med Cloudyn och logga in på Cloudyn-portalen.
>
> Admin2 registrerar sig via Azure-portalen. Men när denna person försöker logga in på Cloudyn-portalen visas ett fel där det står att kontot är **inaktiverat**. Den primära administratören, Admin1, meddelas om inaktiveringen av kontot. Admin1 behöver aktivera Admin2-kontot och bevilja *entitetsåtkomst för administratörer* för lämpliga entiteter, tillåta användarhanteringsåtkomst samt aktivera användarkontot.


Om du får en avisering med en begäran om att tillåta åtkomst för en användare behöver du aktivera användarkontot.

Så här aktiverar du användarkontot:

1. Logga in på Cloudyn med det Azure-administrativa användarkonto som du använde för att konfigurera Cloudyn. Eller logga in med ett användarkonto som har beviljats administratörsåtkomst.
2. Välj kugghjulssymbolen uppe till höger och välj sedan **Användarhantering**.
3. Leta upp användaren, välj pennsymbolen och redigera sedan användaren.
4. Under **Användarstatus** ändrar du statusen från **Inaktiverad** till **Aktiv**.

Cloudyn-användarkonton ansluter med hjälp av enkel inloggning från Azure. Om användare anger sitt lösenord på ett felaktigt sätt kan de bli utelåsta från Cloudyn, även om de fortfarande kan komma åt Azure.

Om du ändrar din e-postadress i Cloudyn från standardadressen i Azure kan ditt konto bli utelåst. Det kan då visa ”status initiallySuspended”. Om ditt användarkonto är utelåst kan du kontakta en annan administratör för att återställa kontot.

Vi rekommenderar att du skapar minst två Cloudyn-administratörskonton ifall ett av kontona blir utelåst.

Om du inte kan logga in på Cloudyn-portalen ska du kontrollera att du använder rätt URL för att logga in på Cloudyn. Använd [https://azure.cloudyn.com](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Undvik att använda Cloudyn-direkt-URL:en `https://app.cloudyn.com`.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Hur aktiverar jag inaktiverade konton med Azure-autentiseringsuppgifter?

Så snart dina Azure-konton identifieras av Cloudyn tillhandahålls kostnadsdata omedelbart i kostnadsbaserade rapporter. För att Cloudyn ska kunna tillhandahålla användnings- och prestandadata behöver du dock registrera dina Azure-autentiseringsuppgifter för kontona. Instruktioner finns i [Lägg till ett konto eller uppdatera en prenumeration](activate-subs-accounts.md#add-an-account-or-update-a-subscription).

Om du vill lägga till Azure-autentiseringsuppgifter för ett konto går du till Cloudyn-portalen och väljer redigeringssymbolen till höger om kontonamnet, inte prenumerationen.

Innan dina Azure-autentiseringsuppgifter har lagts till i Cloudyn visas kontot som _inte aktiverat_.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Hur lägger jag till flera konton och entiteter i en befintlig prenumeration?

Ytterligare entiteter används för att lägga till ytterligare Enterprise-avtal i en Cloudyn-prenumeration. Mer information finns i [Skapa och hantera entiteter](tutorial-user-access.md#create-and-manage-entities).

För CSP:er:

Om du vill lägga till ytterligare CSP-konton till en entitet väljer du **MSP-åtkomst** i stället för **Enterprise** när du skapar den nya entiteten. Om ditt konto är registrerat som ett Enterprise-avtal och du vill lägga till CSP-autentiseringsuppgifter kan Cloudyn-supportpersonal behöva ändra dina kontoinställningar. Om du är Azure-betalprenumerant kan du skapa en ny supportbegäran i Azure-portalen. Välj **Hjälp + support** och sedan **Ny supportbegäran**.

## <a name="currency-symbols-in-cloudyn-reports"></a>Valutasymboler i Cloudyn-rapporter

Du kan ha flera Azure-konton med olika valutor. Men kostnadsrapporter i Cloudyn visar inte mer än en valutatyp per rapport.

Om du har flera prenumerationer som använder olika valutor visas en överordnad entitet och dess underordnade entitetsvalutor i USD **$** . Vi rekommenderar att du inte använder olika valutor i samma entitetshierarki. Med andra ord bör alla dina prenumerationer som är ordnade i en entitetsstruktur använda samma valuta.

Cloudyn identifierar automatiskt prenumerationsvalutan för ditt Enterprise-avtal och visar den korrekt i rapporter.  Cloudyn visar dock bara USD **$** för CSP- och Web Direct Azure-konton.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Vilka tidslinjer för datauppdatering gäller för Cloudyn?

Cloudyn har följande tidslinjer för datauppdatering:

- **Inledande**: När du har konfigurerat kan det ta upp till 24 timmar innan kostnadsdata visas i Cloudyn. Det kan även ta upp till 10 dagar innan Cloudyn har samlat in tillräckligt med data för att visa storleksrekommendationer.
- **Varje dag**: Från den tionde dagen till slutet av varje månad bör Cloudyn visa dina data fram till dagens datum från föregående dag efter cirka UTC + 3 nästa dag.
- **Månadsvis**: Från den första dagen till den tionde dagen varje månad visar Cloudyn kanske endast dina data till och med slutet av föregående månad.

Cloudyn bearbetar data för föregående dag när fullständiga data från föregående dag är tillgängliga. Föregående dags data är vanligtvis tillgängliga i Cloudyn från och med cirka UTC + 3 varje dag. Vissa data, till exempel taggar, kan ta ytterligare 24 timmar att bearbetas.

Data för den aktuella månaden är inte tillgängliga för insamling i början av varje månad. Under perioden slutför tjänstleverantörer sin fakturering för föregående månad. Föregående månads data visas i Cloudyn 5 till 10 dagar efter början av varje månad. Under den här tiden kan det hända att du endast ser upplupna kostnader från föregående månad. Du ser kanske inte dagliga fakturerings- eller användningsdata. När data blir tillgängliga bearbetar Cloudyn dem retroaktivt. Efter bearbetningen visas alla månatliga data mellan den femte dagen och den tionde dagen varje månad.

Om det uppstår en fördröjning när data skickas från Azure till Cloudyn registreras data fortfarande i Azure. Data överförs till Cloudyn när anslutningen återupprättas.

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>Kostnadsvariationer i Cloudyn-kostnadsrapporter

Kostnadsrapporter kan visa kostnadsvariationer när molntjänstleverantörer skickar uppdaterade faktureringsfiler. Kostnadsvariationer uppstår när nya filer tas emot från en molntjänstleverantör utanför det normala dagliga eller månatliga rapporteringsschemat. Kostnadsändringar beror inte på omberäkning från Cloudyn.

Under hela månaden är alla faktureringsfiler som skickas av din molntjänstleverantör en uppskattning av dina dagliga kostnader. Ibland uppdateras data ofta – i vissa fall flera gånger per dag. Uppdateringar sker oftare med AWS än med Azure. Totalkostnader bör vara stabila när faktureringsberäkningen för föregående månad är klar och den sista faktureringsfilen tas emot. Det sker vanligtvis den tionde dagen i månaden.

Ändringar sker när du får kostnadsjusteringar från din molntjänstleverantör. Mottagande av krediter är ett exempel. Ändringar kan ske månader efter att den relevanta månaden har stängts. Ändringar visas när en omberäkning görs av din molntjänstleverantör. Cloudyn uppdaterar sina historiska data för att se till att alla justeringar omberäknas. Det verifierar även att kostnaderna visas korrekt i sina rapporter.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Hur kan en direkt CSP konfigurera Cloudyn-åtkomst för indirekta CSP-kunder eller partner?

Instruktioner finns i [Konfigurera indirekt CSP-åtkomst i Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Varför visas Optimizer-menyobjektet?

När du har lagt till Azure Resource Manager-åtkomst och data samlas in bör du se alternativet **Optimizer**. Information om hur du aktiverar Azure Resource Manager-åtkomst finns i [Hur aktiverar jag inaktiverade konton med Azure-autentiseringsuppgifter?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cloudyn-agent-based"></a>Är Cloudyn agentbaserat?

Nej. Agenter används inte. Azure VM-måttdata för virtuella datorer samlas in från Microsoft Insights-API:et. Om du vill samla in måttdata från virtuella Azure-datorer måste de ha diagnostikinställningar aktiverade.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Visar Cloudyn-rapporter fler än en AD-klientorganisation per rapport?

Ja. Du kan [skapa en motsvarande molnkontoentitet](tutorial-user-access.md#create-and-manage-entities) för varje AD-klientorganisation som du har. Sedan kan du visa alla dina Azure AD-klientorganisationsdata och andra molnplattformsleverantörer, däribland Amazon Web Services och Google Cloud Platform.
