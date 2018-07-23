---
title: Vanliga frågor om Azure Cost Management | Microsoft Docs
description: Innehåller svar på några vanliga frågor om Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/20/2018
ms.topic: troubleshooting
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 255056390cdbdbee49eba47f8168618929b386c8
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187265"
---
# <a name="frequently-asked-questions-for-azure-cost-management"></a>Vanliga frågor om Azure Cost Management

Den här artikeln tar upp några vanliga frågor om Azure Cost Management (även kallat Cloudyn). Om du har frågor om Cost Management kan du be dem på [frågor och svar om Azure Cost Management](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-azure-cost-management-by-cloudyn?forum=Cloudyn).

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Hur kan jag för att lösa vanliga problem med klientinstallationen indirekt enterprise?

När du börjar använda Cloudyn-portalen kan du se följande meddelanden om du använder en Enterprise-avtal eller Cloud Solution Provider (CSP):

- ”Den angivna API-nyckeln inte är en nyckel för övre nivå registrering” visas i den **ställa in Azure Cost Management** guiden.
- ”Direktregistrering – inte” visas i portalen för Enterprise-avtal.
- ”Inga användningsdata hittades för de senaste 30 dagarna. Kontakta din återförsäljare för att försäkra markup har aktiverats för ditt Azure-konto ”visas i Cloudyn-portalen.

Föregående meddelanden indikerar att du har köpt ett Azure Enterprise-avtal genom en återförsäljare eller molntjänstleverantör. Återförsäljaren eller CSP måste aktivera _markup_ för din Azure-konto så att du kan visa dina data i Cloudyn.

Så här löser du problemen:

1. Återförsäljaren måste aktivera _pålägg_ för ditt konto. Mer information finns i [guiden för indirekt kundregistrering](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Du skapar Azure Enterprise Agreement-nyckel för användning med Cloudyn. Anvisningar finns i [att lägga till din Azure EA](https://support.cloudyn.com/hc/en-us/articles/210429585-Adding-Your-AZURE-EA) eller [hitta Your EA Enrollment ID och API-nyckel](https://youtu.be/u_phLs_udig).

Endast en Azure-tjänstadministratör kan aktivera Cost Management. Det räcker inte att vara medadministratör.

Innan du kan generera Azure Enterprise-avtal API-nyckeln att ställa in Cloudyn, måste du aktivera Azure Billing-API genom att följa anvisningarna på:

- [Overview of Reporting APIs for Enterprise customers](../billing/billing-enterprise-api.md) (Översikt över rapporterings-API:er för Enterprise-kunder)
- [Microsoft Azure enterprise portal Reporting API](https://ea.azure.com/helpdocs/reportingAPI) (Rapporterings-API för Microsoft Azure Enterprise Portal) under **Enabling data access to the API** (Aktivera dataåtkomst till API:et)


Du kanske även behöver ge avdelningsadministratörer, kontoägare och Enterprise-administratörer behörigheter att _visa debiteringar_ med fakturerings-API:et.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Varför ser jag optimering rekommendationer?

Rekommendationen information är bara tillgänglig för konton som är aktiverade. Du kommer inte se någon rekommendation information i **optimering** rapportera kategorier för konton som är *inaktiverade*, inklusive:

- Optimering Manager
- Storleksoptimering
- Ineffektivitet

Om du inte ser några data för optimering rekommendation, är förmodligen ha du konton som är inaktiverade. Om du vill aktivera ett konto som du behöver registrera den med dina autentiseringsuppgifter för Azure.

Aktivera ett konto:

1.  Klicka på **Settings** (Inställningar) uppe till höger i Cloudyn-portalen och välj **Cloud Accounts** (Molnkonton).
2.  Gå till Microsoft Azure-konton och leta efter konton som har en **inaktiverade** prenumeration.
3.  Till höger om ett konto som är inaktiverade, klickar du på den **redigera** symbol som liknar en penna.
4.  Din klient-ID och tariff-ID identifieras automatiskt. Klicka på **Nästa**.
5.  Du är omdirigeras till Azure-portalen. Logga in på portalen och godkänna Cloudyn insamlaren för att få åtkomst till dina Azure-data.
6.  Nu ska du är omdirigeras till sidan för hantering av Cloudyn-konton och din prenumeration uppdateras med **active** kontostatus. Den visar en grön bock symbol.
7.  Om du inte ser en grön bockmarkering symbol för en eller flera prenumerationer, betyder det att du inte har behörighet att skapa en reader-appen (CloudynCollector) för prenumerationen. En användare med högre behörighet för prenumerationen måste du upprepa steg 3 och 4.  

När du har slutfört föregående steg kan du visa rekommendationer för optimering inom en till två dagar. Dock kan det ta upp till fem dagar innan fullständig optimering data är tillgängliga.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Hur aktiverar jag pausat eller utelåst användare?

Först ska vi titta på de vanligaste scenariot som orsakar användarkonton att hämta *initiallySuspended*.

> Admin1 kanske Microsoft Cloud Solution Provider- eller Enterprise Agreement-användare. Företaget är redo att börja använda Cost Management.  Han registreras via Azure portal och loggar in på Cloudyn-portalen. Som den person som registrerar Cost Management-tjänsten och loggar in på Cloudyn-portalen, han blir den *primära administratörskonto*. Admin1 skapar inte alla användarkonton. Men med Cloudyn-portalen kan han skapar Azure-konton och ställer in en entitetshierarki. Admin1 informerar Admin2, en Innehavaradministratör som han behöver för att registrera med Cost Management och logga in på Cloudyn-portalen.

> Admin2 registreras via Azure portal. Men när han försöker att logga in på Cloudyn-portalen, han får ett felmeddelande om kontot är **pausats**. Den primära administratörskonto Admin1, är ett meddelande om detta konto. Admin1 måste aktivera Admin2's konto och bevilja *entitet administratörsåtkomst* för enheterna som är lämplig och tillåter användaråtkomst för hantering och aktiv användarkontot.


Om du får en avisering med en begäran om att tillåta åtkomst för en användare måste du aktivera användarkontot.

Aktivera användarkontot:

1. Logga in på Cloudyn genom att använda Azure administrativa användarkontot som du använde för att ställa in Cloudyn. Eller logga in med ett användarkonto som har beviljats administratörsbehörighet.
2. Välj kugghjulet i det övre högra hörnet och välj **Användarhantering**.
3. Hitta användaren, Välj pennan och sedan redigera användaren.
4. Under **användarstatus**, ändra status från **pausad** till **Active**.

Cloudyn-användarkontot ansluta med hjälp av enkel inloggning från Azure. Om en användare mistypes sitt lösenord, kanske de blir utelåst från Cloudyn, även om de har fortfarande åtkomst till Azure.

Om du ändrar din e-postadress i Cloudyn från standardadress i Azure måste kan ditt konto blir utelåsta. Det visas ”status initiallySuspended”. Kontakta en annan administratör om du vill återställa ditt konto om ditt konto är låst.

Vi rekommenderar att du skapar minst två Cloudyn-administratörskonton om något av konton som hämtar utelåsta.

Om du inte logga in på Cloudyn-portalen, kontrollerar du att du använder rätt Azure Cost Management URL: en för att logga in på Cloudyn. Använd [ https://azure.cloudyn.com ](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Undvik att använda Cloudyn direkt URL: en https://app.cloudyn.com.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Hur aktiverar jag inaktiverade konton med autentiseringsuppgifter för Azure?

När dina Azure-konton har identifierats av Cloudyn, tillhandahålla kostnadsdata direkt i rapporter baserade på kostnaden. Men för Cloudyn att tillhandahålla data för användning och prestanda behöver du registrera dina Azure-autentiseringsuppgifter för konton. Anvisningar finns i [lägga till Azure Resource Manager](https://support.cloudyn.com/hc/en-us/articles/212784085-Adding-Azure-Resource-Manager).

Välj Redigera symbolen till höger om namnet på kontot, inte prenumerationen för att lägga till Azure-autentiseringsuppgifter för ett konto i Cloudyn-portalen.

Tills du dina autentiseringsuppgifter för Azure har lagts till Cloudyn kan kontot visas som _icke aktiverad_.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Hur lägger jag till flera konton och entiteter i en befintlig prenumeration?

Ytterligare enheter används för att lägga till ytterligare Enterprise-avtal i en Cloudyn-prenumeration. Följande länkar beskriver hur du lägger till ytterligare enheter:

- [Lägga till en entitet](https://support.cloudyn.com/hc/en-us/articles/212016145-Adding-an-Entity) artikel
- [Definiera din hierarki med kostnadsenheter](https://support.cloudyn.com/hc/en-us/articles/115005142529-Video-Defining-your-hierarchy-with-Cost-Entities) video

För CSP: er:

Om du vill lägga till ytterligare CSP-konton i en entitet, Välj **MSP åtkomst** i stället för **Enterprise** när du skapar den nya entiteten. Om ditt konto registreras som ett Enterprise-avtal och du vill lägga till CSP-autentiseringsuppgifter, kan Cloudyn supportpersonal behöva ändra inställningarna för ditt konto. Om du är en betald Azure-prenumerant kan skapa du en ny supportbegäran i Azure-portalen. Välj **hjälp + support**, och välj sedan **ny supportbegäran**.

## <a name="currency-symbols-in-cloudyn-reports"></a>Valutasymboler i Cloudyn-rapporter

Du kan ha flera Azure-konton med hjälp av olika valutor. Kostnad-rapporter i Cloudyn visa men inte mer än en valutatyp per rapport.

Om du har flera prenumerationer med hjälp av olika valutor, en överordnad enhet och dess underordnade entitet valutor visas i USD **$**. Vår föreslagna bästa praxis är att undvika att använda olika valutor i samma entitetshierarki. Alla dina prenumerationer som är ordnade i en entitet kan med andra ord ska använda samma valuta.

Cloudyn identifierar din Enterprise Agreement-prenumeration valuta automatiskt och visas korrekt i rapporter.  Dock Cloudyn bara visar USD **$** för CSP och Azure web-direct-konton.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Vad är Cloudyn data Uppdatera tidslinjer?

Cloudyn innehåller följande datauppdatering tidslinjer:

- **Inledande**: när du har skapat det kan ta upp till 24 timmar att visa kostnadsdata i Cloudyn. Det kan också ta upp till 10 dagar för Cloudyn samlar in tillräckligt med data för att visa storleksrekommendationer.
- **Dagliga**: tionde dag i slutet av varje månad, Cloudyn ska visa dina data uppdaterade från föregående dag efter om UTC + 3 nästa dag.
- **Månatliga**: från den första dagen till tionde dagen i varje månad Cloudyn kan visa dina data endast till slutet av månaden.

Cloudyn bearbetar data om föregående dag då fullständiga data från föregående dag är tillgänglig. Föregående dags data är vanligtvis tillgängliga i Cloudyn genom om UTC + 3 varje dag. Vissa data, som taggar, kan det ta ytterligare 24 timmar att bearbeta.

Data för den aktuella månaden är inte tillgängligt för samlingen i början av varje månad. Under perioden Slutför tjänstleverantörer sin fakturering för den föregående månaden. Den föregående månadens data visas i Cloudyn-5 till 10 dagar efter början av varje månad. Du kan se bara amorterade kostnader från den föregående månaden under denna tid. Du kan inte se dagliga fakturering och data. När data blir tillgängligt bearbetar Cloudyn den retroaktivt. Efter bearbetning visas alla månatliga data mellan den femte och tionde dagen i varje månad.

Om det finns en fördröjning som skickar data från Azure till Cloudyn, registreras fortfarande data i Azure. Data överförs till Cloudyn när anslutningen återupprättas.

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>Kostnad variationer i Cloudyn-kostnadsrapporter

Kostnadsrapporter kan visa kostnaden variationer när molntjänstleverantörer skickar uppdaterade filer för fakturering. Kostnaderna varierar inträffa när nya filer tas emot från en molntjänstleverantör utanför den vanliga dagliga och månatliga reporting schema. Kostnad ändringar inte beror på Cloudyn-omberäkning. 

Under hela månaden är alla fakturering filer som skickas av din molntjänstleverantör en uppskattning av dina dagliga kostnader. Ibland data uppdateras ofta – ibland flera gånger per dag. Uppdateringarna är oftare med AWS än Azure. Kostnad summor hållas stabil när faktureringsberäkning för den föregående månaden är klar och den slutgiltiga fakturering filen tas emot. Vanligtvis av 10 i månaden.

Ändringar sker när du får kostnadsjusteringar från din molntjänstleverantör. Ta emot krediter är ett exempel. Ändringar kan inträffa flera månader efter den relevanta månaden har stängts. Ändringarna visas varje gång en omberäkning görs av din molntjänstleverantör. Cloudyn uppdaterar dess historiska data för att se till att alla justeringar räknas. Den kontrollerar också att att kostnaderna visas korrekt i den rapporterar.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Hur kan en direkt CSP konfigurerar Cloudyn-åtkomst för indirekta CSP-kunder eller partner?

Anvisningar finns i [konfigurera indirekt CSP-åtkomst i Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Vad som orsakar menyalternativet optimering visas?

När du lägger till Azure Resource Manager-åtkomst och data samlas in, bör du se den **optimering** alternativet. Om du vill aktivera åtkomst till Azure Resource Manager finns i [hur aktiverar jag inaktiverade konton med autentiseringsuppgifter för Azure?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cost-managementcloudyn-agent-based"></a>Baseras Cost Management/Cloudyn-agenten?

Nej. Agenter som inte används. Azure-dator måttdata för virtuella datorer har samlats in från Microsoft Insights-API. Om du vill samla in mått data från virtuella Azure-datorer som de behöver ha inställningarna för startdiagnostik aktiverat.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Visar mer än en AD-klient per rapport i Cloudyn-rapporter?

Ja. Du kan [skapa en motsvarande cloud-kontoentiteten](tutorial-user-access.md#create-and-manage-entities) för varje AD-klient som du har. Du kan visa alla dina data för Azure AD-klient och andra molnleverantörer för plattform som du har, inklusive Amazon Web Services och Google Cloud Platform.
