---
title: Vad är Azure Active Directory (Azure AD)? | Microsoft Docs
description: Lär dig att använda Azure Active Directory för att utöka dina befintliga lokala identiteter till molnet eller utveckla integrerade Azure AD-program.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: overview
ms.date: 09/13/2018
ms.custom: it-pro
ms.openlocfilehash: 08d32a99e2f1a77bcc7a5900b47e6771c33ec356
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49393329"
---
# <a name="what-is-azure-active-directory"></a>Vad är Azure Active Directory?
Azure Active Directory (Azure AD) är Microsofts molnbaserade katalog- och identitetshanteringstjänst för flera klientorganisationer. Azure AD kombinerar viktiga katalogtjänster, åtkomsthantering för program och identitetsskydd i en enda lösning vilket utgör en standardbaserad plattform som hjälper utvecklare som ger åtkomstkontroll till deras appar, baserat på centraliserad policy och regler.

![Azure AD Connect Stack](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="benefits-of-azure-ad"></a>Förmåner hos Azure AD
Azure AD hjälper dig att:

-   Skapa och hantera en enda identitet för varje användare i hela företaget och synkronisera användare, grupper och enheter med [Azure AD Connect](../connect/active-directory-aadconnect.md).

-   Ge åtkomst med enkel inloggning till programmen, inklusive tusentals förintegrerade SaaS-appar eller ge säker fjärråtkomst till lokala SaaS-appar med [Azure AD-programproxy](../manage-apps/application-proxy.md).

-   Skydda programåtkomsten genom att tillämpa regelbaserad [multifaktorautentisering](../authentication/concept-mfa-howitworks.md) får både lokala program och molnprogram.

-   Förbättra användarnas produktivitet med [lösenordsåterställning via självbetjäning](../user-help/user-help-reset-password.md) och grupp- och programåtkomstbegäranden med [MyApps-portalen](../user-help/active-directory-saas-access-panel-introduction.md).

-   Dra nytta av den [höga tillgängligheten och tillgängligheten](https://docs.microsoft.com/azure/architecture/checklist/availability) i en global molnbaserad identitets- och åtkomsthanteringslösning i företagsklass.

## <a name="who-uses-azure-ad"></a>Vem använder Azure AD
Azure AD är avsedd för IT-administratörer, apputvecklare och för användare av Office 365, Azure eller Dynamics CRM Online.

- **IT-administratörer.** Azure AD ger en mer säker lösning för din organisation genom användning av starkare identitetshantering och åtkomst via enkel inloggning (SSO) till tusentals [molnbaserade SaaS-appar](../saas-apps/tutorial-list.md) och lokala appar. Tack vare de här apparna får du även molnbaserad appsäkerhet, smidig åtkomst, förbättrat samarbete och automatisering av identitetslivscykel för användare, vilket hjälper till att öka både säkerhet och efterlevnad.

    Med bara [Azure AD Connect](../connect/active-directory-aadconnect-get-started-express.md) kan du dessutom integrera Azure AD med en befintlig Windows Server Active Directory så att din organisation kan använda befintliga lokala identitetsinvesteringar för att hantera molnbaserad SaaS-appåtkomst.

- **För apputvecklare.** Azure AD hjälper dig fokusera på att skapa appar genom att låta dig integrera med en lösning för identitetshantering som används av miljontals organisationer runt om i världen.

- **För Office 365-, Azure- eller Dynamics CRM Online-kunder.** Du använder redan Azure AD. Varje Office 365-, Azure- och Dynamics CRM Online-klientorganisation är faktiskt en Azure AD-klientorganisation, så du kan omedelbart börja hantera dina användares åtkomst till dina integrerade molnappar.

## <a name="how-reliable-is-azure-ad"></a>Hur tillförlitligt är Azure AD?
Utformningen med flera klientorganisationer, geografisk distribution och hög tillgänglighet innebär att Azure AD uppfyller dina viktigaste affärsbehov. Azure AD som körs från 28 datacenter över hela världen med automatiserad redundansväxling. Det innebär att även om ett datacenter kraschar är kopior av data är aktiva i minst två mer regionalt spridda datacenter och tillgängliga för omedelbar åtkomst.

Mer information om serviceavtal finns i [Serviceavtal](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Välj en utgåva
Alla Microsoft Online-företagstjänster förlitar sig på Azure AD (Azure AD) för inloggning och andra identitetsbehov. Om du prenumererar på någon av Microsoft Online-företagstjänsterna (till exempel Office 365 eller Microsoft Azure) får du automatiskt Azure AD med åtkomst till alla de kostnadsfria funktionerna. Med Azure Active Directory Free-versionen kan du hantera användare och grupper, synkronisera med lokala kataloger och få enkel inloggning i Azure, Office 365 och tusentals populära SaaS-program, t.ex. Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox och många fler. 

För att förbättra din Azure AD-implementering kan du lägga till betalfunktioner genom att uppgradera till Azure Active Directory Basic-, Premium P1- eller Premium P2-versionerna. Azure AD-betalversioner byggs ovanpå din befintliga kostnadsfria katalog och ger funktioner i företagsklass såsom självbetjäning, förbättrad övervakning, säkerhetsrapportering, multifaktorautentisering (MFA) och säker åtkomst för din mobil personal.

> [!NOTE]
> Prisalternativ för de här versionerna finns i [prisinformation för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Azure Active Directory Premium P1, Premium P2 och Azure Active Directory Basic stöds för närvarande inte i Kina. Om du vill ha mer information om priser för Azure AD kan du kontakta Azure Active Directory-forumet.

- **Azure Active Directory Basic.** Den här utgåvan har utformats för arbete med repetitiva uppgifter som i första hand kräver åtkomst till molnet, och ger molnfokuserad programåtkomst och lösningar för identitetshantering med självbetjäning. Med Basic-versionen får du funktioner som ökar produktiviteten och minskar kostnaderna, som gruppbaserad åtkomsthantering, lösenordsåterställning med självbetjäning för molnprogram och Azure Active Directory-programproxy (för att publicera lokala webbprogram med Azure AD), som alla stöds av ett serviceavtal på företagsnivå med 99,9 procent drifttid.

- **Azure Active Directory Premium P1.** Azure Active Directory Premium-versionen har utformats för att ge kraft åt företag med mer krävande identitets- och åtkomsthanteringsbehov och ger funktionsrika identitetshanteringsfunktioner på företagsnivå samt gör det möjligt för hybridanvändare att få smidig åtkomst till lokala funktioner och molnfunktioner. Den här versionen innehåller allt du behöver för informationsarbetare och identitetsadministratörer i hybridmiljöer över programåtkomst, identitets- och åtkomsthantering (IAM) med självbetjäning, identitetsskydd och säkerhet i molnet. Den har stöd för avancerade administrations- och delegeringsresurser, till exempel dynamiska grupper och grupphantering via självbetjäning. Innehåller Microsoft Identity Manager (en lokal identitets- och hanteringssvit) och ger tillbakaskrivningsfunktioner för moln, vilket möjliggör lösningar som självåterställning av lösenord för dina lokala användare.

- **Azure Active Directory Premium P2.** Den här nya versionen är utformad med avancerat skydd för alla dina användare och administratörer. Den innehåller alla funktioner i Azure AD Premium P1 samt identitetsskydd och Privileged Identity Management. Azure Active Directory Identity Protection utnyttjar miljarder signaler för att ge riskbaserad villkorsbaserad åtkomst till dina program och viktiga företagsdata. Vi hjälper dig även att hantera och skydda privilegierade konton med Azure Active Directory Privileged Identity Management, så att du kan upptäcka, begränsa och övervaka administratörer och deras åtkomst till resurser och ge just-in-time-åtkomst vid behov.  

> [!NOTE]
> Ett antal Azure Active Directory-funktioner är tillgängliga i ”betala per användning-versioner”:<ul><li>**Azure Active Directory B2C.** Identitets- och åtkomsthanteringslösning för dina program som riktar sig mot konsumenter. For more information, see [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).</li><li>**Azure Multi-Factor Authentication.** Autentisering per användare eller per autentisering. Mer information finns i [Vad är Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md).

## <a name="as-an-admin-how-do-i-get-started"></a>Hur kommer jag igång som administratör?
Registrera dig för en kostnadsfri 30-dagars utvärderingsversion och distribuera din första molnlösning, se [utvärderingsversion av Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/).

## <a name="as-a-developer-how-do-i-get-started"></a>Som utvecklare, hur kommer jag igång?
Registrera dig för en kostnadsfri 30-dagars utvärderingsversion och börjar integrera dina appar med Azure AD, se [utvärderingsversion av Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/). Du hittar mer information i [Utvecklarguiden](../develop/azure-ad-developers-guide.md) för Azure Active Directory.

## <a name="next-steps"></a>Nästa steg
- [Läs mer om grunderna för identitets- och åtkomsthantering med Azure](identity-fundamentals.md).

- [Integrera Azure AD med Windows Server Active Directory](../hybrid/how-to-connect-install-express.md).
