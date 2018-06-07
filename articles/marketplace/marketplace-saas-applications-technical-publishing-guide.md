---
title: Azure Marketplace SaaS-program Technical publicering Guide
description: Stegvisa instruktioner och publishing checklistor för publicering av SaaS-program på Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ms.author: pabutler
ms.openlocfilehash: 2ac8119e36843e38e334fb5772ea4ade9962b4f9
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809479"
---
# <a name="saas-applications-technical-publishing-guide"></a>SaaS-program tekniska publiceringsguide

Välkommen till guiden Publicera Azure Marketplace SaaS-program Technical. Den här guiden är avsedd att hjälpa kandidat och befintliga utgivare för att visa en lista över sina program och tjänster i Azure Marketplace med SaaS-program som erbjuder. 

För att bättre förstå hur du publicerar ett SaaS-erbjudande, är den här guiden uppdelad i följande avsnitt:
* Översikt över erbjudande
* Affärskrav
* Tekniska krav
* Process för att publicera
* Använda Azure Active Directory för att aktivera försök
* Certifierar din Azure AD-integrering för Marketplace

## <a name="offer-overview"></a>Översikt över erbjudande  

SaaS-program finns i både Azure-skyltfönster i följande tabell beskrivs de aktuella tillgängliga alternativ:

| Storefront alternativet | Visar en lista över | Utvärderingsversion/Transact |  
| --- | --- | --- |  
| AppSource | Ja (kontakta mig) | Ja (PowerBI/Dynamics) |
| Azure Marketplace | Nej | Ja (SaaS-appar) |   

**Lista:** lista publiceringsalternativ består av en kontakta mig erbjudandetypen och används när en utvärderingsversion eller transaktionsnivå deltagande inte är möjligt. Fördelen med den här metoden är att du kan utgivare med en lösning på marknaden omedelbart tar emot leads som kan omvandlas till erbjudanden för att öka din verksamhet.  
**Utvärderingsversion/Transact:** kunden har alternativet att köpa direkt eller begära en utvärderingsversion för lösningen. Tillhandahålla en utvärderingsversion upplevelse ökar engagement erbjuds kunder och ger kunder möjlighet att utforska din lösning innan du köper. Du vill ha bättre risken för befordran i skyltfönster med en utvärderingsversion upplevelse och förväntat mer och mer omfattande leads från uppdrag. Försök måste åtminstone innehålla kostnadsfri support under hela utvärderingsperioden.  

| Erbjudande för SaaS-appar | Affärskrav | Tekniska krav |  
| --- | --- | --- |  
| **Kontakta oss** | Ja | Nej |  
| **PowerBI / Dynamics** | Ja | Ja (Azure AD-integrering) |  
| **SaaS-appar**| Ja | Ja (Azure AD-integrering) |     

Mer information om Marketplace-skyltfönster och en beskrivning av varje publiceringsalternativ finns i [Marketplace Publisher Guide](https://aka.ms/sellerguide) och [publiceringsalternativ](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide#select-a-publishing-option).

## <a name="business-requirements"></a>Affärskrav
SaaS erbjuder affärsbehov kan utföras parallellt med de tekniska kraven. De flesta affärsbehov och information som samlas in när du skapar SaaS-erbjudandet i partnerportalen molnet. Kraven är följande: 
* Acceptera deltagande principer
* Integrering med Microsoft 
* Identifiera det erbjudande målgrupp
* Definiera och avgöra leadhanteringen som ska användas
* Ställa in sekretesspolicy och användningsvillkor
* Definiera supportkontakter  

Mer information du hittar du i avsnittet [krav för marketplace-publicering](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide#prerequisites-for-marketplace-publishing)

## <a name="technical-requirements"></a>Tekniska krav

Tekniska krav för SaaS-program är enkelt. Utgivare krävs endast att integreras med Azure Active Directory (AD Azure) som ska publiceras. Azure AD-integrering med program är väl dokumenterat och Microsoft tillhandahåller flera SDK-verktyg och resurser för att åstadkomma detta.  

Om du vill starta, rekommenderar vi att du har en prenumeration som är dedikerad för din Azure Marketplace-publicering, så att du kan isolera arbete från andra initiativ. När det är klart kan du börja distribuera SaaS-program i den här prenumerationen att starta utvecklingsarbetet.  

Bästa dokumentationen för Azure Active Directory, exempel och vägledning finns på följande platser: 

* [Azure Active Directory-Guide för utvecklare](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integrera med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Plan för Azure - säkerhet och identitet](https://azure.microsoft.com/roadmap/?category=security-identity)

Videokurser, kontrollerar du följande:

* [Azure Active Directory-autentisering med Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Azure Active Directory-identitet tekniska genomgång - del 1 av 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Azure Active Directory-identitet tekniska genomgång - del 2 av 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Skapa appar med Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Microsoft Azure-videor fokuserar på Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Gratis Azure Active Directory-utbildning finns på  
* [Microsoft Azure för IT-proffs innehåll serien: Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Azure Active Directory tillhandahåller dessutom en plats för att söka efter uppdateringar av tjänsten   
* [Tjänsten för Azure AD-uppdateringar](https://azure.microsoft.com/updates/?product=active-directory)

Du kan använda följande resurser för support:
* [MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="publishing-process"></a>Process för att publicera

SaaS publiceringsprocessen har både tekniska och affärskrav steg.  De flesta av det arbete som görs utveckla och integrera Azure Active Directory kan göras parallellt med arbete som krävs för att uppfylla kraven i erbjudandet. Flesta företags behov är en del av SaaS App Offer konfigurationen partnerportalen molnet.  
Följande diagram visar publishing Huvudsteg för utvärderingsversionen/Transact erbjuder:  

![SaaS publishing steg](./media/marketplace-saas-applications-technical-publishing-guide/saaspublishingsteps.png)  

I följande tabell beskrivs var och en av de publishing Huvudsteg:  

| Publiceringen | Beskrivning |   
| --- | --- |  
| **Skapa SaaS-program** | Logga in på partnerportalen molnet, Välj **ny**, och välj sedan den **SaaS-appar** erbjuder. |  
| **Skapa integrering med Azure AD** | Följ de tekniska kraven som beskrivs i föregående avsnitt för att integrera din SaaS erbjudande med Azure AD. |  
| **Ange inställningar för erbjudande**| Ange alla SaaS erbjudande inledande information. Erbjuder ID och erbjuda namn som du vill använda. |     
| **Ange den tekniska informationen** | Ange teknisk information om erbjudandet. För SaaS-program lösningens URI och typ av knapp som erbjudandet, förvärv (ledigt, spår eller kontakta mig) krävs. |  
| **Testa Drive(Optional)** | Det här är en valfri typ av utvärderingsversion, behövs oftast för andra typer av Marketplace erbjuder. Det kan du ha utvärderingsversion som distribuerats i utgivarens prenumerationer kontra slutkunden. |  
| **Ange erbjudande Storefront material**| I det här avsnittet kommer utgivaren länka och överför logotyper, marknadsföringsmaterial, juridiska dokument och konfigurera Leads hanteringssystemet. |
| **Ange erbjudande kontakter** | Ange både tekniker kontakter och kontaktinformation för Support för SaaS-erbjudandet. |  
| **Kontrollera SaaS appen Azure AD-integrering** | Innan du skickar din SaaS-app för publicering, måste du kontrollera att appen är integrerat med Azure AD |  
| **Publicera erbjudandet**| När erbjudandet och tekniska resurserna har slutförts, kan du skicka erbjudandet. Detta startar publiceringsprocessen, där mallen lösningen är testas, verifiera, certifierad och godkänts för publicering. |

## <a name="using-azure-active-directory-to-enable-trials"></a>Använda Azure Active Directory för att aktivera försök  

Microsoft verifierar alla Marketplace-användare med Azure AD, därför när en autentiserad användare klickar på via din utvärderingsversion registrering i Marketplace och omdirigeras till din utvärderingsversion miljö, du kan etablera användaren direkt till en utvärderingsversion utan en ytterligare inloggning steg. Den token som din app som tar emot från Azure AD under autentiseringen innehåller värdefulla användarinformation som du kan använda för att skapa ett användarkonto i din app så att du kan automatisera etablering upplevelse och öka sannolikheten för konvertering. Mer information om token finns [exempel token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) .

Använda Azure AD för att aktivera 1-Klicka autentisering till din app eller utvärderingsversion gör följande:  
* Förenklar kundupplevelsen från Marketplace till test.  
* Underhåller känslan av en i produkten upplevelse även när användaren omdirigeras från Marketplace till din domän eller utvärderingsversion miljö.  
* Minskar sannolikheten för nedläggning på omdirigering eftersom det inte finns ytterligare tecken i ett steg.  
* Minskar distribution hinder för stor del av Azure AD-användare.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certifierar din Azure AD-integrering för Marketplace  

Du kan certifiera din Azure AD-integrering på några olika sätt, beroende på om ditt program är en klient eller flera innehavare, och om du är nybörjare till Azure AD federerad enkel inloggning (SSO) eller redan stöder den.  

**För program med flera klienter:**  

Om du redan har stöd för Azure AD, gör du följande:
1.  Registrera ditt program i Azure-portalen
2.  Aktivera supportfunktionen för flera innehavare i Azure AD för att hämta en testperiod för en enda klickning. Mer information hittar du [här](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Om du är nybörjare på Azure AD federerad enkel inloggning gör du följande: 
1.  Registrera ditt program i Azure-portalen
2.  Utveckla enkel inloggning med Azure AD med hjälp av [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) eller [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Aktivera stöd för flera innehavare funktion i AAD för att hämta en enda klickning utvärderingsversionen mer information hittar du [här](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**För stöd för en innehavare program, använder du något av följande alternativ:**  
* Lägga till användare i din katalog som gästanvändare med [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Etablera manuellt försök för kunder med hjälp av kontakta mig
* Utveckla en per-kund Test-enhet
* Skapa en flera innehavare demo exempelapp med enkel inloggning

