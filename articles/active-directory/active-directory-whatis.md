---
title: Vad är Azure Active Directory (AD Azure)? | Microsoft Docs
description: Använda Azure Active Directory för att utöka din befintliga lokala identiteter till molnet eller utveckla Azure AD integrerat program.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.custom: it-pro
ms.openlocfilehash: 003ce2edda3e2069eb7e05f58ecc2e208c818946
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="what-is-azure-active-directory"></a>Vad är Azure Active Directory?
Azure Active Directory (AD Azure) är Microsofts flera innehavare, molnbaserad katalog och identity management-tjänsten som kombinerar core katalogtjänster, hantering av åtkomst och identitetsskydd i en enda lösning. Azure AD erbjuder också en omfattande, standardbaserad plattform som gör att utvecklare kan leverera åtkomstkontroll till sina program baserat på centraliserade princip och regler.

![Azure AD Connect Stack](./media/active-directory-whatis/Azure_Active_Directory.png)

- **För IT-administratörer.** Azure AD innehåller en mer säker lösning för din organisation genom att använda starkare Identitetshantering och enkel inloggning (SSO) åtkomst till tusentals [molnbaserade SaaS-appar](active-directory-saas-tutorial-list.md) och lokala appar. Via de här apparna får du också molnbaserade appsäkerhet, sömlös åtkomst, förbättrad samarbete och automatisering av livscykeln identitet för dina anställda, vilket ökar både säkerhet och efterlevnad.

    Dessutom med just [fyra klick](./connect/active-directory-aadconnect-get-started-express.md), du kan integrera Azure AD med en befintlig Windows Server Active Directory, så att organisationen använda din befintliga lokala investeringar för identitet till att hantera molnbaserade SaaS-appen åtkomst till .

- **För utvecklare av program.** Azure AD, kan du fokusera på att bygga din apps genom att du integrera med en lösning för Identitetshantering som används av miljontals organisationer över hela världen.

- **För Office 365, Azure eller Dynamics CRM Online-kunder.** Du använder redan Azure AD. Varje Office 365, Azure och Dynamics CRM Online-klient är faktiskt en Azure AD-klient, så att du kan starta direkt att hantera dina anställda åtkomst till dina integrerade molnappar.

## <a name="how-reliable-is-azure-ad"></a>Hur tillförlitlig är Azure AD?
Flera innehavare, fördelade hög tillgänglighet utformning av Azure AD-innebär att du kan använda det för dina viktigaste affärsbehov. Kör utanför 28 Datacenter runtom i världen med automatisk redundans, har du bekvämt att veta att Azure AD har hög tillförlitliga och som även om ett datacenter kraschar kopior av katalogen data är live i minst två mer regionalt spridda datacenter och är tillgängliga för direktåtkomst.

Mer information om servicenivåavtal finns [serviceavtal](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Välj utgåva
Alla Microsoft Online-tjänster är beroende av Azure Active Directory (Azure AD) för inloggning och andra identitet. Om du prenumererar på någon av Microsoft Online-företagstjänster (till exempel Office 365 eller Microsoft Azure) kan hämta du Azure AD med åtkomst till alla funktioner som ledigt. Med Azure Active Directory Free edition kan hantera användare och grupper, synkroniseras med lokala kataloger kan hämta enkel inloggning mellan Azure och Office 365 tusentals populära SaaS-program som Salesforce, Workday, Concur, DocuSign, Google Apps, rutan, ServiceNow, Dropbox och mycket mer. 

Du kan lägga till betalda funktioner med hjälp av Azure Active Directory Basic, Premium P1 och Premium P2-versioner för att förbättra din Azure Active Directory. Azure Active Directory betald versioner är byggda på en befintlig gratis directory tillhandahåller klassen funktioner utsträckning självbetjäning, förbättrad övervakning, rapportering om säkerhet, Multi-Factor Authentication (MFA) och säker åtkomst för den mobila arbetsstyrkan för företag.

> [!NOTE]
> Alternativen prisnivå av dessa versioner finns [Azure Active Directory-priser](https://azure.microsoft.com/pricing/details/active-directory/). Azure Active Directory Premium P1 Premium P2 och Azure Active Directory Basic stöds inte för närvarande i Kina. Mer information om priser för Azure AD kan du kontakta Azure Active Directory-forumet.
>

* **Azure Active Directory Basic** -utformad för projektanställda med molnet första behov, den här versionen innehåller molnet till Central programidentitet för åtkomst och självbetjäningsportalen lösningar för hantering. Med Basic-versionen av Azure Active Directory får du funktioner som ökar produktiviteten och minskar kostnaderna, som gruppbaserad åtkomsthantering, lösenordsåterställning med självbetjäning för molnprogram och Azure Active Directory Application Proxy (för att publicera lokala webbprogram med Azure Active Directory), som alla stöds av ett serviceavtal på företagsnivå med 99,9 procent drifttid.
* **Azure Active Directory Premium P1** – utformade för att gör det lättare för organisationer med flera krävande identitets- och hanteringsbehov, Azure Active Directory Premium edition lägger till funktioner för hantering av funktioner på företagsnivå identitet och låter användare av hybrid sömlöst få åtkomst till lokala och molntjänster funktioner. Den här versionen innehåller allt du behöver för informationsarbetare och identitet administratörer i hybridmiljöer över identitetsskydd programåtkomst, självbetjäning identitets- och åtkomsthantering (IAM) och säkerhet i molnet. Det stöder avancerade administration och delegering resurser som dynamiska grupper och grupphantering via självbetjäning. Den innehåller Microsoft Identity Manager (ett lokalt identitets- och management suite) samt molnet återskrivning funktioner för att aktivera lösningar som Självbetjäning för återställning av lösenord för lokala användare.
* **Azure Active Directory Premium P2** -utformad med avancerat skydd för alla användare och administratörer, erbjudandet nya innehåller alla funktioner i Azure AD Premium P1 samt identitetsskydd och Privileged Identity Management. Azure Active Directory-identitetsskydd utnyttjar miljarder signalerar att tillhandahålla risk-baserad villkorlig åtkomst till dina program och kritiska företagsdata. Vi kan också hjälpa dig att hantera och skydda Privilegierade konton med Azure Active Directory Privileged Identity Management så att du kan identifiera begränsa, och övervaka administratörer och deras åtkomst till resurser och ger just-in-time-åtkomst vid behov.  

> [!NOTE]
> Ett antal Azure Active Directory-funktioner är tillgängliga via ”betala per användning” versioner:
>
> * Active Directory B2C är identitets- och hanteringslösning för dina konsumentinriktade program. Mer information finns i [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * Azure Multi-Factor Authentication kan användas via per användare eller per autentiseringsproviders. Mer information finns i [vad är Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)
>

## <a name="how-can-i-get-started"></a>Hur kan jag igång?

**Om du är IT-administratör:**

* [Prova!](https://azure.microsoft.com/trial/get-started-active-directory/) -Du kan registrera dig för en kostnadsfri 30-dagars utvärderingsversion idag och distribuera din första molnlösning under fem minuter med hjälp av den här länken

* Läs [komma igång med Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium) för tips och råd om hur du hämtar en Azure AD-klient igång snabbt

**Om du utvecklar:**
 
* Kolla in den [Guide för utvecklare](active-directory-developers-guide.md) till Azure Active Directory

* [Starta en utvärdering av](https://azure.microsoft.com/trial/get-started-active-directory/) – registrera dig för en kostnadsfri 30-dagars utvärderingsversion idag och starta integrera dina appar med Azure AD

## <a name="next-steps"></a>Nästa steg
[Lär dig mer om grunderna i Azure identitets- och åtkomsthantering](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
