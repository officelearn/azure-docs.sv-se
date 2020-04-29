---
title: Publicerings guide för Azure Marketplace SaaS-program
description: Steg-för-steg-guide och publicera check listor för publicering av SaaS-program till Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: dsindona
ms.openlocfilehash: 3a3cbe26b9fa82e236acbfd0c7e8ca3e81a7aa75
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82117241"
---
# <a name="saas-applications-offer-publishing-guide"></a>Publicerings guide för SaaS-program

SaaS-program kan publiceras på Marketplace med tre olika anrop till åtgärd: "kontakta mig", prova nu, "och" Hämta det nu. " Den här guiden förklarar dessa tre alternativ, inklusive krav för var och en. 

## <a name="offer-overview"></a>Erbjudande översikt  

SaaS-program finns i Microsoft AppSource och Azure Marketplace.  Både butiker support lista, utvärdering och Transact-erbjudanden.

**Lista:**  Publicerings alternativet för listan består av en kontakt mig erbjudande typ och används när det inte är möjligt att delta i en utvärderings-eller transaktions nivå. Fördelen med den här metoden är att den gör det möjligt för utgivare med en lösning på marknaden att omedelbart börja ta emot leads som kan inaktive ras i avtal för att öka din verksamhet.  
**Utvärderings version/transaktion:**  Kunden har möjlighet att köpa eller begära en utvärdering av din lösning direkt. Att tillhandahålla en utvärderings version ökar den engagemang nivån som kunderna erbjuder och gör det möjligt för kunderna att utforska din lösning innan de köper. Med en utvärderings version får du bättre möjligheter till befordran i butiker, och du bör förvänta dig mer och rikare leads från kund engagemang. Test versioner måste innehålla kostnads fri support under minst under utvärderings periodens varaktighet.  

| Erbjudande för SaaS-appar | Affärs krav | Tekniska krav |  
| --- | --- | --- |  
| **Kontakta oss** | Ja | Nej |  
| **PowerBI/Dynamics** | Ja | Ja (Azure AD-integrering) |  
| **SaaS-appar**| Ja | Ja (Azure AD-integrering) |     

## <a name="saas-list"></a>Lista över SaaS

Anropet till åtgärden för en SaaS-lista utan utvärderings version och ingen fakturerings funktion är "kontakta mig". 

Du behöver inte konfigurera Azure Active Directory för att visa en lista över ett SaaS-program. 

|Krav  |Information  |
|---------|---------|
|Din app är ett SaaS-erbjudande  |   Din lösning är ett SaaS erbjudande och du erbjuder en SaaS-produkt med flera innehavare.      |


## <a name="saas-trial"></a>SaaS-utvärdering

Du kan tillhandahålla en lösning eller app med hjälp av en SaaS-baserad (Software-as-a-Service)-baserad utvärderings version. Kostnads fria utvärderings versioner kan visas som ett svars konto med begränsad användning eller en begränsad tids period. 


|Krav  |Information  |
|---------|---------|
|Din app är ett SaaS-erbjudande  |   Din lösning är ett SaaS erbjudande och du erbjuder en SaaS-produkt med flera innehavare.      |
|Din app är AAD-aktiverad     |   Kunden kommer att omdirigeras till din domän och du kommer att använda det direkt med kunden       |


## <a name="saas-trial-technical-requirements"></a>Tekniska krav för SaaS-utvärdering

De tekniska kraven för SaaS-program är enkla. Utgivare måste bara vara integrerade med Azure Active Directory (Azure AD) som ska publiceras. Azure AD-integrering med program dokumenteras väl och Microsoft tillhandahåller flera SDK: er och resurser för att åstadkomma detta.  

För att starta rekommenderar vi att du har en prenumeration som är dedikerad för din Azure Marketplace-publicering, så att du kan isolera arbetet från andra initiativ. När det är gjort kan du börja distribuera ditt SaaS-program i den här prenumerationen för att starta utvecklings arbetet.  

Den bästa Azure Active Directory dokumentationen, exempel och rikt linjer finns på följande platser: 

* [Guide för Azure Active Directory utvecklare](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integrera med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Azure-översikt – säkerhet och identitet](https://azure.microsoft.com/roadmap/?category=security-identity)

För video kurser, se följande:

* [Azure Active Directory autentisering med Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Azure Active Directory Identity Technical pärmsida-del 1 av 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Azure Active Directory identitet teknisk pärmsida, del 2 av 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Skapa appar med Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Microsoft Azure videor fokuserat på Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Kostnads fri Azure Active Directory utbildning finns på  
* [Innehålls serie för IT-proffs Microsoft Azure: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Dessutom tillhandahåller Azure Active Directory en plats för att söka efter tjänst uppdateringar   
* [Azure AD service-uppdateringar](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Aktivera försök med hjälp av Azure Active Directory  

Microsoft autentiserar alla Marketplace-användare med Azure AD, så när en autentiserad användare klickar på din utvärderings lista i Marketplace och omdirigeras till din utvärderings miljö kan du etablera användaren direkt i en utvärderings version utan att behöva ett ytterligare inloggnings steg. Den token som din app tar emot från Azure AD under autentiseringen innehåller värdefull användar information som du kan använda för att skapa ett användar konto i din app, så att du kan automatisera etablerings upplevelsen och öka sannolikheten för konvertering. Mer information om token finns i [exempel-token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) .

Använd Azure AD för att aktivera 1-Klicka på autentisering till din app eller utvärdering gör följande:  
* Effektiviserar kund upplevelsen från Marketplace till utvärderings version.  
* Bibehåller känslan av en produkt upplevelse, även när användaren omdirigeras från Marketplace till din domän eller utvärderings miljö.  
* Minskar sannolikheten för genom gång av omdirigering eftersom det inte finns något ytterligare inloggnings steg.  
* Minskar distributions barriärerna för den stora populationen av Azure AD-användare.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certifiera din Azure AD-integrering för Marketplace  

Certifiera din Azure AD-integrering på några olika sätt, beroende på om ditt program är en enskild klient eller flera innehavare, och om du är nybörjare på Azure AD federerad enkel inloggning (SSO) eller redan stöder det.  

**För program med flera klienter:**  

Om du redan har stöd för Azure AD gör du följande:
1.    Registrera ditt program i Azure Portal
2.    Aktivera funktionen för stöd för flera innehavare i Azure AD för att få en utvärderings upplevelse med ett enda klick. Mer detaljerad information finns [här](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Om du är nybörjare på den federerade INLOGGNINGen i Azure AD gör du följande: 
1.  Registrera ditt program i Azure Portal
2.  Utveckla SSO med Azure AD med [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) eller [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Aktivera stöd för flera innehavare i AAD för att få en utvärderings version med en mer detaljerad information som du hittar [här](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**För program med en enda klient använder du något av följande alternativ:**  
* Lägga till användare i din katalog som gäst användare med [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Manuellt etablera utvärderingar för kunder genom att använda "kontakta mig"
* Utveckla en test enhet per kund
* Bygg en app med flera klient exempel demonstrationer med SSO

## <a name="saas-subscriptions"></a>SaaS-prenumerationer

Använd SaaS-appens erbjudande typ för att låta kunden köpa din SaaS-baserade, tekniska lösning som en prenumeration. Följande krav måste uppfyllas för din SaaS-app:
- Pris och fakturera tjänsten på ett fast pris (per månad eller per år) eller per användar taxa.
- Ange en metod för att uppgradera eller avbryta tjänsten när som helst.
Microsoft är värd för handels transaktionen. Microsoft fakturerar kunden å dina vägnar. Om du vill erbjuda en SaaS-app som en prenumeration måste du integrera med API: er för SaaS-utförande.  Tjänsten måste ha stöd för etablering, uppgradering och annullering.

| Krav | Information |  
|:--- |:--- |  
|Fakturering och mätning | Ditt erbjudande är priss ätts baserat på den pris modell du väljer innan du publicerar (fast pris eller per användare).  Om du använder den fasta pris modellen kan du också ta med ytterligare dimensioner som används för att debitera kunder för användning som inte ingår i den fasta taxan. |  
|Annullering | Ditt erbjudande kan avbrytas av kunden när som helst. |  
|Transaktions landnings sida | Du är värd för en Azure-anpassad transaktions landnings sida där användare kan skapa och hantera sitt SaaS-tjänstkonto. |   
| Prenumerations-API | Du exponerar en tjänst som kan interagera med SaaS-prenumerationen för att skapa, uppdatera och ta bort ett användar konto och en tjänste plan. Viktiga API-ändringar måste stödjas inom 24 timmar. Icke-kritiska API-ändringar frigörs regelbundet. |  

>[!Note]
>Partner kanal för Cloud solution providers (CSP) är nu tillgängligt.  Se [leverantörer av moln lösningar](./cloud-solution-providers.md) för mer information om marknadsföring av ditt erbjudande via Microsoft CSP partner-kanaler.

## <a name="next-steps"></a>Nästa steg
Om du inte redan har gjort det,

* [Lär dig mer](https://azuremarketplace.microsoft.com/sell) om Marketplace.

Om du vill registrera dig i Partner Center börjar du med att skapa ett nytt erbjudande eller arbeta med en befintlig:

* [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra ditt erbjudande.
* Mer information finns i [skapa ett SaaS program erbjudande](./partner-center-portal/create-new-saas-offer.md) .
