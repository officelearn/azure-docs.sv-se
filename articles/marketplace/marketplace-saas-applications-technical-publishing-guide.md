---
title: Azure Marketplace SaaS-program Technical publicera Guide
description: Stegvis guide och publicera checklistor för publicering av SaaS-program på Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: keithcharlie
manager: nunoc
editor: keithcharlie
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: keithcharlie
ms.openlocfilehash: f1a6825cdfab2375f1a6f1858ef14684d374dae2
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123490"
---
# <a name="saas-applications-offer-publishing-guide"></a>SaaS-program erbjuder publiceringsguide

SaaS-program kan publiceras i marketplace med tre olika anrop till åtgärden: ”kontakta mig”, ”Prova nu” och ”hämta nu”. Den här guiden förklarar dessa tre alternativ, inklusive krav för var och en. 

## <a name="offer-overview"></a>Erbjudande-översikt  

SaaS-program är tillgängliga i både Azure-butiker i följande tabell beskrivs de aktuella tillgängliga alternativen:

| Storefront alternativet | Visa en lista över | Utvärdering/Transact |  
| --- | --- | --- |  
| AppSource | Ja (kontakta mig) | Ja (PowerBI/Dynamics) |
| Azure Marketplace | Nej | Ja (SaaS-appar) |   

**Lista:** lista publiceringsalternativ består av en kontakt mig erbjudandetypen och används när en utvärderingsversion - eller transaktionsnivå deltagande inte är möjlig. Fördelen med den här metoden är att den kan utgivare med en lösning på marknaden kan omedelbart börja ta emot leads som kan omvandlas till erbjudanden för att öka din verksamhet.  
**Utvärdering/transaktion:** kunden har alternativet att köpa eller begär en utvärderingsversion för din lösning direkt. Att ge en utvärderingsversion ökar engagement erbjuds för kunder och ger kunderna möjlighet att utforska din lösning innan du köper. Du får bättre risken för befordran i butiker med en utvärderingsversion upplevelse, och du kan förvänta dig mer och mer omfattande leads i lika kundrelationer. Utvärderingsversioner måste innehålla kostnadsfri support minst för hela utvärderingsperioden är slut.  

| Erbjudandet för SaaS-appar | Affärskrav | Tekniska krav |  
| --- | --- | --- |  
| **Kontakta oss** | Ja | Nej |  
| **PowerBI / Dynamics** | Ja | Ja (Azure AD-integrering) |  
| **SaaS-appar**| Ja | Ja (Azure AD-integrering) |     

## <a name="saas-list"></a>SaaS-lista

Din uppmaning till en SaaS-lista med inga utvärderingsversion och inga fakturering funktioner är ”kontakta mig”. 

Du behöver inte konfigurera Azure Active Directory om du vill visa ett SaaS-program. 

|Krav  |Information  |
|---------|---------|
|Din app är en SaaS erbjudande  |   Din lösning är en SaaS erbjudande och du erbjuda en multitenant SaaS-produkt.      |


## <a name="saas-trial"></a>SaaS-utvärdering

Du anger en lösning eller en app med en kostnadsfri-till-försök, programvara som tjänst (SaaS)-baserade utvärderingsversion. Kostnadsfria utvärderingsversion erbjudanden kan ges som ett utvärderingskonto för begränsad användning eller begränsad varaktighet. 


|Krav  |Information  |
|---------|---------|
|Din app är en SaaS erbjudande  |   Din lösning är en SaaS erbjudande och du erbjuda en multitenant SaaS-produkt.      |
|Din app är AAD aktiverad     |   Kunden kommer att vara till din domän och du kommer transact hos kunden direkt       |


## <a name="saas-trial-technical-requirements"></a>SaaS utvärderingsversion tekniska krav

De tekniska kraven för SaaS-program är enkel. Utgivare krävs endast för att att integreras med Azure Active Directory (Azure AD) som ska publiceras. Azure AD-integrering med program är väl dokumenterat och Microsoft tillhandahåller flera SDK: er och resurser för att åstadkomma detta.  

För att starta, rekommenderar vi att du har en prenumeration för din Azure Marketplace-publicering, så att du kan isolera arbete från andra initiativ. När det är klart kan du börja distribuera din SaaS-program i den här prenumerationen att starta utvecklingsarbeten.  

Bästa Azure Active Directory-dokumentation, exempel och vägledning finns på följande platser: 

* [Utvecklarhandbok för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integrera med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Azure-översikt - säkerhet och identitet](https://azure.microsoft.com/roadmap/?category=security-identity)

Videokurser, kontrollerar du följande:

* [Azure Active Directory-autentisering med Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Azure Active Directory Identity teknisk genomgång – del 1 av 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Azure Active Directory Identity teknisk genomgång – del 2 av 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Skapa appar med Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Microsoft Azure-videor som fokuserar på Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Kostnadsfri utbildning för Azure Active Directory finns på  
* [Microsoft Azure för IT-proffs serien: Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Azure Active Directory tillhandahåller dessutom en plats för att söka efter uppdateringar av tjänsten   
* [Tjänstuppdateringar för Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Använda Azure Active Directory för att aktivera utvärderingar  

Microsoft autentiserar alla Marketplace-användare med Azure AD, kan därför när en autentiserad användare klickar sig igenom dina utvärderingspubliceringar i Marketplace och omdirigeras till din utvärderingsmiljö, ge användaren en utvärderingsversion utan att kräva en ytterligare inloggning steg. Den token som appen tar emot från Azure AD under autentiseringen innehåller värdefulla användarinformation som du kan använda för att skapa ett användarkonto i din app, så att du kan automatisera etablering upplevelse och öka sannolikheten för konvertering. Mer information om token finns i [exempel token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) .

Aktivera 1 klick autentisering i din app eller en utvärderingsversion med hjälp av Azure AD gör följande:  
* Förenklar kundupplevelsen från Marketplace-utvärderingsversionen.  
* Underhåller känslan av en i produkten upplevelse även när användaren omdirigeras från Marketplace till din domän eller utvärderingsmiljö.  
* Minskar sannolikheten för nedläggning på omdirigering eftersom det inte finns ytterligare logga in ett steg.  
* Minskar hinder för distribution för stora befolkningen i Azure AD-användare.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certifiera din Azure AD-integrering för Marketplace  

Du kan certifiera din Azure AD-integrering på några olika sätt, beroende på om ditt program är en enda klient eller flera innehavare och om du är nybörjare till Azure AD-federerad enkel inloggning (SSO), eller redan stöder den.  

**För program med flera klienter:**  

Om du redan har stöd för Azure AD, gör du följande:
1.  Registrera ditt program i Azure portal
2.  Aktivera funktionen stöd för flera innehavare i Azure AD för att få en testperiod för en enda klickning. Mer specifik information kan hittas [här](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Om du är nybörjare på Azure AD federerad SSO, gör du följande: 
1.  Registrera ditt program i Azure portal
2.  Utveckla enkel inloggning med Azure AD via [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) eller [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Aktivera stöd för flera innehavare funktionen i AAD för att hämta utvärderingsversionen för ”klick” mer specifikt finns [här](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**För enstaka klientorganisationer program, använder du någon av följande alternativ:**  
* Lägga till användare i din katalog som gästanvändare med [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Etablera manuellt utvärderingar för kunder med hjälp av kontakta mig
* Utveckla en per-kund Test-enhet
* Skapa en app med flera innehavare exempel demo med enkel inloggning

## <a name="saas-subscriptions"></a>SaaS-prenumerationer

Aktivera kunden att köpa din SaaS-baserade, teknisk lösning som en prenumeration med hjälp av erbjudandetypen för SaaS-app. Följande krav måste uppfyllas för SaaS-app:
- Pris- och faktura tjänsten enligt ett fast, månatliga pris.
- Ange en metod för att uppgradera eller säga upp tjänsten när som helst.
Microsoft är värd för handel transaktionen. Microsoft fakturerar kunden å dina vägnar. Om du vill använda faktura en SaaS-App som en prenumeration, måste du aktivera du egen prenumeration management-tjänsten API. Din prenumeration management-tjänstens API måste kommunicera direkt med Azure Resource Manager API: er. Din prenumeration management-tjänstens API måste stödja Tjänstetablering, uppgradering och avbryta.

| Krav | Information |  
|:--- |:--- |  
|Fakturering och mätning | Ditt erbjudande debiteras med en månatlig fast pris. Användningsbaserade priser och användningsbaserad ”true up” funktioner stöds inte just nu. |  
|Annullering | Erbjudandet är avbrytbar av kunden när som helst. |  
|Landningssida för transaktion | Du vara värd för en landningssidan för Azure anpassade transaktion där användare kan skapa och hantera sina SaaS-tjänstkontot. |   
| Prenumeration API | Du kan exponera en tjänst som kan interagera med SaaS-prenumeration för att skapa, uppdatera och ta bort ett användaravtal för kontot och tjänsten. Viktiga API-ändringar måste stödjas inom 24 timmar. Icke-kritiska API-ändringar släpps regelbundet. |  

## <a name="next-steps"></a>Nästa steg
Om du inte redan gjort det, 

- [Registrera](https://azuremarketplace.microsoft.com/sell) i marketplace

Om du är registrerad och skapar ett nytt erbjudande eller arbetar på en befintlig

- [Logga in på partnerportalen i molnet](https://cloudpartner.azure.com) att skapa eller slutföra ditt erbjudande
