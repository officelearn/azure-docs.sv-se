---
title: Teknisk publiceringsguide för Azure Marketplace SaaS Applications
description: Steg-för-steg-guide och publiceringschecklista för publicering av SaaS-program på Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
author: keithcharlie
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: kevidal
ms.openlocfilehash: 641297ea74a08dea163cf768b8e9b245348824a4
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80544287"
---
# <a name="saas-applications-offer-publishing-guide"></a>Publiceringsguiden för SaaS-programerbjudanden

SaaS-program kan publiceras på marknaden med tre olika uppmaningar: "Kontakta mig", "Prova nu" och "Hämta det nu". I den här guiden förklaras dessa tre alternativ, inklusive krav för varje. 

## <a name="offer-overview"></a>Översikt över erbjudandet  

SaaS-program är tillgängliga i båda Azure Storefronts I följande tabell beskrivs de aktuella tillgängliga alternativen:

| Alternativ för skyltfönster | Lista | Rättegång/Transact |  
| --- | --- | --- |  
| AppSource | Ja | Ja |
| Azure-marknadsplats | Inga | Ja |   

**Lista:**  Publiceringsalternativet Notering består av en kontaktperson-erbjudandetyp och används när ett deltagande på utvärderings- eller transaktionsnivå inte är möjligt. Fördelen med detta tillvägagångssätt är att det gör det möjligt för utgivare med en lösning på marknaden att omedelbart börja ta emot leads som kan förvandlas till affärer för att öka din verksamhet.  
**Utvärdering/transaktion:**  Kunden har möjlighet att direkt köpa eller begära en provperiod för din lösning. Genom att tillhandahålla en utvärderingsupplevelse ökar engagemangsnivån som erbjuds kunderna och gör det möjligt för kunderna att utforska din lösning innan de köper. Med en trial-upplevelse får du bättre chanser till marknadsföring i skyltfönster, och du bör förvänta dig fler och rikare leads från kundengagemang. Utvärderingsversioner måste innehålla kostnadsfri support åtminstone under hela provperioden.  

| Erbjudandet om SaaS-appar | Affärskrav | Tekniska krav |  
| --- | --- | --- |  
| **Kontakta oss** | Ja | Inga |  
| **PowerBI / Dynamik** | Ja | Ja (Azure AD-integrering) |  
| **SaaS-appar**| Ja | Ja (Azure AD-integrering) |     

## <a name="saas-list"></a>SaaS-lista

Uppmaningen till en SaaS-lista utan utvärderingsversion och ingen faktureringsfunktion är "Kontakta mig". 

Du behöver inte konfigurera Azure Active Directory för att lista ett SaaS-program. 

|Krav  |Information  |
|---------|---------|
|Din app är ett SaaS-erbjudande  |   Din lösning är ett SaaS-erbjudande och du erbjuder en multitenant SaaS-produkt.      |


## <a name="saas-trial"></a>SaaS-testversion

Du tillhandahåller en lösning eller app med en SaaS-baserad testversion som är kostnadsfri för att försöka, programvara som en tjänst. Kostnadsfria proverbjudanden kan presenteras som ett testkonto med begränsad användning eller begränsad varaktighet. 


|Krav  |Information  |
|---------|---------|
|Din app är ett SaaS-erbjudande  |   Din lösning är ett SaaS-erbjudande och du erbjuder en multitenant SaaS-produkt.      |
|Appen är AAD-aktiverad     |   Kunden kommer att dirigeras om till din domän och du kommer att handla med kunden direkt       |


## <a name="saas-trial-technical-requirements"></a>Tekniska krav för SaaS-test

De tekniska kraven för SaaS-applikationer är enkla. Utgivare behöver bara integreras med Azure Active Directory (Azure AD) som ska publiceras. Azure AD-integrering med program är väl dokumenterad och Microsoft tillhandahåller flera SDK:er och resurser för att åstadkomma detta.  

Till att börja med rekommenderar vi att du har en prenumeration dedikerad för din Azure Marketplace-publicering, så att du kan isolera arbetet från andra initiativ. När detta är gjort kan du börja distribuera ditt SaaS-program i den här prenumerationen för att starta utvecklingsarbetet.  

Den bästa Azure Active Directory-dokumentationen, exempel och vägledning finns på följande platser: 

* [Guide för Azure Active Directory-utvecklare](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integrera med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Azure-färdplan – säkerhet och identitet](https://azure.microsoft.com/roadmap/?category=security-identity)

För video tutorials, granska följande:

* [Azure Active Directory-autentisering med Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Teknisk information om Azure Active Directory-identitet – del 1 av 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Teknisk information om Azure Active Directory-identitet – del 2 av 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Skapa appar med Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Microsoft Azure-videor fokuserade på Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Kostnadsfri Azure Active Directory-utbildning finns på  
* [Innehållsserie för Microsoft Azure för IT-proffs: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Dessutom tillhandahåller Azure Active Directory en webbplats för att söka efter tjänstuppdateringar   
* [Uppdateringar av Azure AD-tjänsten](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Använda Azure Active Directory för att aktivera utvärderingsversioner  

Microsoft autentiserar alla Marketplace-användare med Azure AD, så när en autentrad användare klickar igenom utvärderingsversionen på Marketplace och omdirigeras till utvärderingsmiljön kan du etablera användaren direkt i en utvärderingsversion utan att kräva ytterligare inloggningssteg. Den token som din app tar emot från Azure AD under autentisering innehåller värdefull användarinformation som du kan använda för att skapa ett användarkonto i din app, så att du kan automatisera etableringsupplevelsen och öka sannolikheten för konvertering. Mer information om token finns i [Exempeltoken](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) .

Använda Azure AD för att aktivera 1-klicksautentisering till din app eller Utvärderingsversion gör följande:  
* Effektiviserar kundupplevelsen från Marketplace till Utvärderingsversion.  
* Bibehåller känslan av en "produktupplevelse" även när användaren omdirigeras från Marketplace till din domän eller utvärderingsmiljö.  
* Minskar risken för nedläggning vid omdirigering eftersom det inte finns ett ytterligare inloggningssteg.  
* Minskar distributionshinder för den stora populationen av Azure AD-användare.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certifiera din Azure AD-integrering för Marketplace  

Certifiera din Azure AD-integrering på några olika sätt, beroende på om ditt program är en klient eller flera innehavare, och om du är ny i Azure AD-federerad enkel inloggning (SSO) eller redan stöder det.  

**För program med flera innehavare:**  

Om du redan har stöd för Azure AD gör du följande:
1.  Registrera ditt program i Azure-portalen
2.  Aktivera supportfunktionen för flera innehavare i Azure AD för att få en utvärderingsversion med ett klick. Mer specifik information finns [här](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Om du inte har gjort något nytt för Azure AD-federerade SSO gör du följande: 
1.  Registrera ditt program i Azure-portalen
2.  Utveckla SSO med Azure AD med [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) eller [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Aktivera multi-arrende stöd funktionen i AAD för att få "ett klick" provupplevelse Mer specifik information finns [här](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**Använd något av följande alternativ för program med en klient:**  
* Lägga till användare i din katalog som gästanvändare med [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Manuellt etablera utvärderingsversioner för kunder med hjälp av "Kontakta mig"
* Utveckla en "testkörning" per kund
* Skapa en exempeldemoapp för flera innehavare med SSO

## <a name="saas-subscriptions"></a>SaaS-prenumerationer

Använd SaaS app erbjudandetyp för att göra det möjligt för kunden att köpa din SaaS-baserade, tekniska lösning som en prenumeration. Följande krav måste uppfyllas för din SaaS-app:
- Pris och fakturera tjänsten till en fast (månadsvis eller årlig), eller till en per användare pris.
- Ange en metod för att uppgradera eller avbryta tjänsten när som helst.
Microsoft är värd för handelstransaktionen. Microsoft fakturerar din kund för din räkning. Om du vill erbjuda en SaaS-app som en prenumeration måste du integrera med SaaS uppfyllelse API:er.  Tjänsten måste stödja etablering, uppgradering och avbokning.

| Krav | Information |  
|:--- |:--- |  
|Fakturering och mätning | Ditt erbjudande är prissatt baserat på den prismodell du väljer innan du publicerar (schablonbelopp eller per användare).  Om du använder schablonmodellen kan du eventuellt inkludera ytterligare dimensioner som används för att debitera kunder för användning som inte ingår i schablonpriset. |  
|Annullering | Ditt erbjudande kan när som helst annulleras av kunden. |  
|Sidan Transaktionsmålsida | Du är värd för en Azure-sida för sammärkta transaktioner där användare kan skapa och hantera sitt SaaS-tjänstkonto. |   
| API för prenumeration | Du exponerar en tjänst som kan interagera med SaaS-prenumerationen för att skapa, uppdatera och ta bort ett användarkonto och en serviceplan. Kritiska API-ändringar måste stödjas inom 24 timmar. Icke-kritiska API-ändringar kommer att släppas med jämna mellanrum. |  

>[!Note]
>Opt-in (Cloud Solution Providers) partnerkanal är nu tillgänglig.  Se [Cloud Solution Providers](./cloud-solution-providers.md) för mer information om marknadsföring ditt erbjudande via Microsoft CSP partnerkanaler.

## <a name="next-steps"></a>Nästa steg
Om du inte redan har gjort det,

- [Registrera dig](https://azuremarketplace.microsoft.com/sell) på marknaden.

Om du är registrerad och skapar ett nytt erbjudande eller arbetar med ett befintligt,

- [Logga in på Cloud Partner Portal](https://cloudpartner.azure.com) för att skapa eller slutföra ditt erbjudande.
- Mer information finns i [Erbjudandet om Azure SaaS-program.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer)
