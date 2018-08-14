---
title: Vad är Azure Active Directory (Azure AD)? | Microsoft Docs
description: Använd Azure Active Directory för att utöka dina befintliga lokala identiteter till molnet eller utveckla integrerade Azure AD-program.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/09/2018
ms.custom: it-pro
ms.openlocfilehash: 5087f759d682382bc22b5f95f462fe0f08619cc8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450002"
---
# <a name="what-is-azure-active-directory"></a>Vad är Azure Active Directory?
Azure Active Directory (Azure AD) är Microsofts molnbaserade katalogtjänst för identitetshantering för flera innehavare som kombinerar viktiga katalogtjänster, åtkomsthantering för program och identitetsskydd i en och samma lösning. Azure AD erbjuder även en bred, standardbaserad plattform som ger utvecklare möjlighet att förse sina program med åtkomstkontroll, baserat på en centraliserad princip och regler.

![Azure AD Connect Stack](./media/active-directory-whatis/Azure_Active_Directory.png)

- **För IT-administratörer.** Azure AD ger en mer säker lösning för din organisation genom användning av starkare identitetshantering och åtkomst via enkel inloggning (SSO) till tusentals [molnbaserade SaaS-appar](../saas-apps/tutorial-list.md) och lokala appar. Tack vare de här apparna får du även molnbaserad appsäkerhet, smidig åtkomst, förbättrat samarbete och automatisering av identitetslivscykel för anställda, vilket hjälper till att öka både säkerhet och efterlevnad.

    Med bara [fyra klick](./../connect/active-directory-aadconnect-get-started-express.md) kan du dessutom integrera Azure AD med en befintlig Windows Server Active Directory så att din organisation kan använda befintliga lokala identitetsinvesteringar för att hantera molnbaserad SaaS-appåtkomst.

- **För apputvecklare.** Azure AD gör att du kan fokusera på att skapa appar genom att låta dig integrera med en lösning för identitetshantering som används av miljontals organisationer runt om i världen.

- **För Office 365-, Azure- eller Dynamics CRM Online-kunder.** Du använder redan Azure AD. Varje Office 365-, Azure- och Dynamics CRM Online-klientorganisation är faktiskt en Azure AD-klientorganisation, så du kan omedelbart börja hantera dina anställdas åtkomst till dina integrerade molnappar.

## <a name="how-reliable-is-azure-ad"></a>Hur tillförlitligt är Azure AD?
Utformningen med flera klientorganisationer, geo-distribution och hög tillgänglighet innebär att Azure AD uppfyller dina viktigaste affärsbehov. Tack vare 28 datacenter som körs över hela världen med automatisk redundans är Azure AD mycket pålitligt – även om ett datacenter skulle sluta fungera finns kopior av dina katalogdata live på minst två ytterligare regionalt spridda datacenter och kan kommas åt direkt.

Mer information om serviceavtal finns i [Serviceavtal](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Välj en utgåva
Alla Microsoft Online-företagstjänster förlitar sig på Azure Active Directory (Azure AD) för inloggning och andra identitetsbehov. Om du prenumererar på någon av Microsoft Online-företagstjänsterna (till exempel Office 365 eller Microsoft Azure) får du Azure AD med åtkomst till alla de kostnadsfria funktionerna. Med Azure Active Directory Free-versionen kan du hantera användare och grupper, synkronisera med lokala kataloger och få enkel inloggning i Azure, Office 365 och tusentals populära SaaS-program, t.ex. Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox och många fler. 

För att förbättra ditt Azure Active Directory kan du lägga till betalfunktioner med hjälp av Azure Active Directory Basic-, Premium P1- och Premium P2-versionerna. Azure Active Directory-betalversioner byggs ovanpå din befintliga kostnadsfria katalog och ger funktioner i företagsklass såsom självbetjäning, förbättrad övervakning, säkerhetsrapportering, multifaktorautentisering (MFA) och säker åtkomst för din mobil personal.

> [!NOTE]
> Prisalternativ för de här versionerna finns i [prisinformation för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Azure Active Directory Premium P1, Premium P2 och Azure Active Directory Basic stöds för närvarande inte i Kina. Om du vill ha mer information om priser för Azure AD kan du kontakta Azure Active Directory-forumet.
>

* **Azure Active Directory Basic** – Den här utgåvan har utformats för arbete med repetitiva uppgifter som i första hand kräver åtkomst till molnet, och ger molnfokuserad programåtkomst och lösningar för identitetshantering med självbetjäning. Med Basic-versionen av Azure Active Directory får du funktioner som ökar produktiviteten och minskar kostnaderna, som gruppbaserad åtkomsthantering, lösenordsåterställning med självbetjäning för molnprogram och Azure Active Directory-programproxy (för att publicera lokala webbprogram med Azure Active Directory), som alla stöds av ett serviceavtal på företagsnivå med 99,9 procent drifttid.
* **Azure Active Directory Premium P1** – Azure Active Directory Premium-versionen har utformats för att ge kraft åt företag med mer krävande identitets- och åtkomsthanteringsbehov och ger funktionsrika identitetshanteringsfunktioner på företagsnivå samt gör det möjligt för hybridanvändare att få smidig åtkomst till lokala funktioner och molnfunktioner. Den här versionen innehåller allt du behöver för informationsarbetare och identitetsadministratörer i hybridmiljöer över programåtkomst, identitets- och åtkomsthantering (IAM) med självbetjäning, identitetsskydd och säkerhet i molnet. Den har stöd för avancerade administrations- och delegeringsresurser, till exempel dynamiska grupper och grupphantering via självbetjäning. Innehåller Microsoft Identity Manager (en lokal identitets- och hanteringssvit) och ger tillbakaskrivningsfunktioner för moln, vilket möjliggör lösningar som självåterställning av lösenord för dina lokala användare.
* **Azure Active Directory Premium P2** – Den här nya versionen är utformad med avancerat skydd för alla dina användare och administratörer. Den innehåller alla funktioner i Azure AD Premium P1 samt identitetsskydd och Privileged Identity Management. Azure Active Directory Identity Protection utnyttjar miljarder signaler för att ge riskbaserad villkorsbaserad åtkomst till dina program och viktiga företagsdata. Vi hjälper dig även att hantera och skydda privilegierade konton med Azure Active Directory Privileged Identity Management, så att du kan upptäcka, begränsa och övervaka administratörer och deras åtkomst till resurser och ge just-in-time-åtkomst vid behov.  

> [!NOTE]
> Ett antal Azure Active Directory-funktioner är tillgängliga i ”betala per användning-versioner”:
>
> * Active Directory B2C är en identitets- och åtkomsthanteringslösning för dina program som riktar sig mot konsumenter. Mer information finns i [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * Azure Multi-Factor Authentication kan användas via per användare- eller per autentisering-providrar. Mer information finns i [Vad är Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md)
>

## <a name="how-can-i-get-started"></a>Hur kommer jag igång?

**Om du är IT-administratör:**

* [Prova!](https://azure.microsoft.com/trial/get-started-active-directory/) – du kan registrera dig för en kostnadsfri 30-dagars utvärderingsversion och distribuera din första molnlösning inom fem minuter med hjälp av den här länken

* Läs [Kom igång med Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium) för att få tips och råd om hur du kör igång en Azure AD-klientorganisation snabbt

**Om du är utvecklare:**
 
* Kolla in [Guiden för utvecklare](../develop/azure-ad-developers-guide.md) till Azure Active Directory

* [Starta en utvärdering](https://azure.microsoft.com/trial/get-started-active-directory/) – registrera dig för en kostnadsfri 30-dagars utvärderingsversion i dag och börja integrera dina appar med Azure AD

## <a name="next-steps"></a>Nästa steg
[Läs mer om grunderna för identitets- och åtkomsthantering med Azure](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
