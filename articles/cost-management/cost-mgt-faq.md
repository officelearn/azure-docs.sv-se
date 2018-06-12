---
title: Vanliga frågor om Azure kostnaden Management | Microsoft Docs
description: Innehåller svar på några vanliga frågor om Azure kostnaden Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/07/2018
ms.topic: troubleshooting
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 0742e1e96e03840f138dde2bca7b2bcda1e49dfe
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298417"
---
# <a name="frequently-asked-questions-for-azure-cost-management"></a>Vanliga frågor om Azure kostnaden Management

Den här artikeln tar några vanliga frågor om Azure kostnaden Management (även kallat Cloudyn). Om du har frågor om hantering av kostnaden kan du be dem vid [vanliga frågor och svar för Azure kostnaden Management](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-azure-cost-management-by-cloudyn?forum=Cloudyn).

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Hur kan jag för att lösa vanliga problem med indirekt enterprise?

När du börjar använda Cloudyn portalen kan du se följande meddelanden om du använder en Enterprise-avtal eller Cloud Solution Providers (CSP):

- ”Den angivna API-nyckeln inte är en nyckel på översta nivån registrering” visas i den **ställa in Azure kostnaden Management** guiden.
- ”Direktregistrering – inte” visas i portalen Enterprise-avtal.
- ”Inga användningsdata hittades för de senaste 30 dagarna. Kontakta din återförsäljare att försäkra markup har aktiverats för ditt Azure-konto ”visas på Cloudyn-portalen.

Föregående meddelanden indikerar att du har köpt ett Azure Enterprise-avtal genom en återförsäljare eller molntjänstleverantör. Återförsäljaren eller CSP måste aktivera _markup_ för din Azure-konto så att du kan visa dina data i Cloudyn.

Så här löser du problemen:

1. Återförsäljaren måste aktivera _pålägg_ för ditt konto. Mer information finns i [guiden för indirekt kundregistrering](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Du genererar nyckeln för användning med Cloudyn Azure-Företagsavtal. Instruktioner finns i [EA lägger till Azure](https://support.cloudyn.com/hc/en-us/articles/210429585-Adding-Your-AZURE-EA) eller [hitta din EA registrering ID och API-nyckeln](https://youtu.be/u_phLs_udig).

Endast en Azure-tjänstadministratör kan aktivera Cost Management. Det räcker inte att vara medadministratör.

Innan du kan generera Azure Enterprise-avtal API-nyckel att ställa in Cloudyn, måste du aktivera Azure Billing API genom att följa anvisningarna på:

- [Overview of Reporting APIs for Enterprise customers](../billing/billing-enterprise-api.md) (Översikt över rapporterings-API:er för Enterprise-kunder)
- [Microsoft Azure enterprise portal Reporting API](https://ea.azure.com/helpdocs/reportingAPI) (Rapporterings-API för Microsoft Azure Enterprise Portal) under **Enabling data access to the API** (Aktivera dataåtkomst till API:et)


Du kanske även behöver ge avdelningsadministratörer, kontoägare och Enterprise-administratörer behörigheter att _visa debiteringar_ med fakturerings-API:et.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Varför ser jag optimering rekommendationer

Rekommendation information är bara tillgängligt för konton som är aktiverade. Visas inte någon rekommendation information i **optimering** rapportera kategorier för konton som är *inaktiverade*, inklusive:

- Optimering Manager
- Storlek för optimering
- Ineffektiviteter

Om du inte ser några optimering rekommendation data, troligen har du konton som är inaktiverade. Om du vill aktivera ett konto som du behöver registrera den med dina autentiseringsuppgifter för Azure.

Aktivera ett konto:

1.  Klicka på **Settings** (Inställningar) uppe till höger i Cloudyn-portalen och välj **Cloud Accounts** (Molnkonton).
2.  Gå till Microsoft Azure-konton och leta efter konton som har en **inaktiverade** prenumeration.
3.  Klicka till höger för ett konto som är inaktiverade i **redigera** symbol som liknar en penna.
4.  Din klient-ID och hastighet identifieras automatiskt. Klicka på **Nästa**.
5.  Du ska omdirigeras till Azure-portalen. Logga in på portalen och auktorisera Cloudyn insamlaren åtkomst till Azure data.
6.  Sedan du ska omdirigeras till sidan för hantering av Cloudyn konton och din prenumeration har uppdaterats med **active** kontostatus. Här visas en grön bock symbol.
7.  Om du inte ser en grön bockmarkering symbol för en eller flera av prenumerationerna, innebär det att du inte har behörighet att skapa reader-appen (CloudynCollector) för prenumerationen. En användare med högre behörighet för prenumerationen måste du upprepa steg 3 och 4.  

När du har slutfört ovanstående steg kan du visa optimering rekommendationer inom en till två dagar. Dock kan det ta upp till fem dagar innan fullständig optimering data blir tillgängliga.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Hur aktiverar pausat eller utelåst användare?

Först ska vi titta på de vanligaste scenariot som orsakar användarkonton att hämta *initiallySuspended*.

> Admin1 kanske användare Microsoft Cloud Solution Provider eller Enterprise-avtal. Företaget är redo att börja använda hantering av kostnaden.  Han registreras via Azure portal och loggar in på portalen Cloudyn. Som den person som registrerar kostnaden Management-tjänsten och loggar in på portalen Cloudyn han blir den *primär administratör*. Admin1 skapar inte någon konto. Men med hjälp av Cloudyn-portalen han skapar Azure-konton och ställer in en enhetshierarki. Admin1 informerar Admin2 Innehavaradministratör som han måste registrera med hantering av kostnader och logga in på Cloudyn-portalen.

> Admin2 registrerar via Azure-portalen. Men när han försöker att logga in på portalen Cloudyn han får ett fel som anger sitt konto är **avbruten**. En primär administratör Admin1, meddelande om detta konto. Admin1 måste aktivera Admin2's konto och bevilja *entitet administratörsåtkomst* för lämpliga entiteter och låter användaråtkomst och aktiv användarkontot.


Om du får en avisering med en begäran om att tillåta åtkomst för en användare måste du aktivera användarkontot.

Aktivera användarkontot:

1. Logga in på Cloudyn med hjälp av Azure administrativa användarkontot som du använde för att ställa in Cloudyn. Eller logga in med ett konto som har beviljats administratörsbehörighet.
2. Välj symbolen växel i det övre högra hörnet och välj **Användarhantering**.
3. Sök efter användaren, markerar du den penna och sedan redigera användaren.
4. Under **användarstatus**, ändra status från **pausad** till **Active**.

Cloudyn användarkonton ansluter med hjälp av enkel inloggning från Azure. Om en användare mistypes sina lösenord, kan de blir utelåst från Cloudyn, även om de har fortfarande åtkomst till Azure.

Om du ändrar din e-postadress i Cloudyn från standardadressen i Azure, ditt konto kan hämta låst. Den kan visa ”status initiallySuspended”. Kontakta en annan administratör om du vill återställa ditt konto om ditt konto är låst.

Vi rekommenderar att du skapar minst två Cloudyn administratörskonton om ett konto blir låst.

Se till att du använder rätt Azure kostnaden Management URL: en för att logga in på Cloudyn om du inte logga in till Cloudyn-portalen. Använd [ https://azure.cloudyn.com ](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Undvik att använda Cloudyn direkt URL https://app.cloudyn.com.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Hur aktiverar inaktiverade konton med autentiseringsuppgifter för Azure?

Så snart som dina Azure-konton identifieras av Cloudyn anges kostnadsdata direkt i kostnad-baserade rapporter. Men för Cloudyn användnings- och prestandadata uppgifter behöver du registrera dina Azure-autentiseringsuppgifter för konton. Instruktioner finns i [lägga till Azure Resource Manager](https://support.cloudyn.com/hc/en-us/articles/212784085-Adding-Azure-Resource-Manager).

Lägg till Azure Markera autentiseringsuppgifterna för ett konto i portalen Cloudyn redigera symbolen till höger om namnet på kontot, inte prenumerationen.

Tills dina autentiseringsuppgifter för Azure läggs till Cloudyn kontot visas som _icke aktiverad_.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Hur lägger jag till flera konton och enheter till en befintlig prenumeration?

Ytterligare enheter används för att lägga till ytterligare Enterprise-avtal i en Cloudyn-prenumeration. Följande länkar beskriver hur du lägger till ytterligare enheter:

- [Lägga till en entitet](https://support.cloudyn.com/hc/en-us/articles/212016145-Adding-an-Entity) artikel
- [Definiera din hierarki med kostnaden entiteter](https://support.cloudyn.com/hc/en-us/articles/115005142529-Video-Defining-your-hierarchy-with-Cost-Entities) video

För CSP: er:

Om du vill lägga till ytterligare CSP-konton till en entitet, Välj **MSP åtkomst** i stället för **Enterprise** när du skapar den nya entiteten. Om ditt konto är registrerad som ett Enterprise-avtal och du vill lägga till CSP-autentiseringsuppgifter, kan Cloudyn supportpersonal behöva ändra inställningarna för ditt konto. Om du är en betald Azure-prenumerant kan skapa du en ny supportförfrågan i Azure-portalen. Välj **hjälp + support**, och välj sedan **ny supportbegäran**.

## <a name="currency-symbols-in-cloudyn-reports"></a>Tecken i Cloudyn rapporter

Du kan ha flera Azure-konton som använder olika valutor. Kostnadsrapporter i Cloudyn visa men inte mer än en valuta per rapport.

Om du har flera prenumerationer med hjälp av olika valutor en överordnad enhet och dess underordnade entiteten valutor visas i USD **$**. Vår föreslagna bästa praxis är att undvika att använda olika valutor i samma entitet hierarki. Med andra ord alla dina prenumerationer som är ordnade i en entitet som ska använda samma valuta.

Cloudyn identifierar din Enterprise-avtal prenumeration valuta automatiskt och visas korrekt i rapporter.  Dock Cloudyn bara visar USD **$** för CSP och Azure web direct-konton.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Vad är Cloudyn data Uppdatera tidslinjer?

Cloudyn har följande data Uppdatera tidslinjer:

- **Inledande**: när du har skapat det kan ta upp till 24 timmar att visa kostnadsdata i Cloudyn. Det kan också ta upp till 10 dagar för Cloudyn att samla in data om du vill visa sizing rekommendationer.
- **Dagliga**: från tionde dagen i slutet av varje månad, Cloudyn ska visa dina data uppdaterad från föregående dag efter om UTC + 3 nästa dag.
- **Månatliga**: från den första dagen till tionde dagen i månaden Cloudyn kan visa dina data endast till slutet på månaden.

Cloudyn bearbetar data om föregående dag då fullständiga data från föregående dag är tillgänglig. Föregående dag data finns vanligtvis i Cloudyn av om UTC + 3 varje dag. Vissa data, till exempel taggar kan vidta ytterligare 24 timmar att bearbeta.

Data för den aktuella månaden är inte tillgänglig för hämtning i början av varje månad. Under perioden Slutför leverantörer sina faktureringen för den föregående månaden. Föregående månad visas informationen i Cloudyn 5 10 dagar efter starten av varje månad. Under denna tid kan du se endast amorterade kostnader från föregående månad. Du kanske inte se dagliga fakturerings- eller användning av data. När data blir tillgängliga bearbetar Cloudyn den retroaktivt. Efter bearbetning visas alla månatliga data mellan den femte och tionde dagen i månaden.

Om det uppstår en fördröjning skicka data från Azure till Cloudyn, registreras fortfarande data i Azure. Data överförs till Cloudyn när anslutningen har återupprättats.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Hur kan en direkt CSP konfigurera Cloudyn åtkomst för indirekt CSP kunder eller partners?

Instruktioner finns i [konfigurera indirekt CSP-åtkomst i Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Vad som orsakar menyalternativet optimering ska visas?

När du lägger till Azure Resource Manager åtkomst och data som samlas in, bör du se den **optimering** alternativet. Om du vill aktivera åtkomst till Azure Resource Manager, se [hur aktiverar inaktiverade konton med autentiseringsuppgifter för Azure?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cost-managementcloudyn-agent-based"></a>Baseras kostnaden hantering/Cloudyn agent

Nej. Agenter används inte. Virtuella Azure-datorn mätvärden för virtuella datorer som har samlats in från Microsoft Insights API. Om du vill samla in mått data från Azure virtuella datorer som de behöver ha diagnostikinställningar aktiverad.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Visar Cloudyn rapporter flera AD-klient per rapport?

Ja. Du kan [skapar en motsvarande molnet konto entitet](tutorial-user-access.md#create-and-manage-entities) för varje AD-klient som du har. Du kan visa alla dina Azure AD-klientdata och andra molntjänstleverantörer plattform inklusive Amazon Web Services och Google Cloud Platform.
