---
title: Azure MFA-servern eller tjänsten, lokalt eller i molnet?
description: Som en Azure AD-administratör, måste du förstå vilken version av MFA som jag bör distribuera?
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 0d68c88bdad63bb022babcc4a6ee4ee7c59ce58a
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158462"
---
# <a name="which-version-of-azure-mfa-is-right-for-my-organization"></a>Vilken version av Azure MFA är rätt för min organisation?

Innan du kan bestämma var och hur du distribuerar Multi-Factor Authentication (MFA), som du behöver att besvara tre grundläggande frågor.

* [Vad vill du skydda](#what-am-i-trying-to-secure)
* [Var finns användarna](#where-are-the-users-located)
* [Vilka funktioner behöver jag?](#what-features-do-i-need)

Var och en av följande avsnitt innehåller information som hjälper dig att svara på ovanstående frågor.

## <a name="what-am-i-trying-to-secure"></a>Vad vill du skydda?

För att fastställa rätt verifieringslösning först först du besvara frågan vad du försöker skydda med ytterligare en autnetiseringsfaktor. Är det ett program som finns i Azure? Eller ett fjärråtkomstsystem? Genom att fastställa vad du försöker skydda kan du besvara frågan var Multifaktorautentisering måste vara aktiverat.

| Vad försöker du skydda? | MFA i molnet | MFA-server |
| --- |:---:|:---:|
| Första parts Microsoft-appar |● |● |
| Saas-appar i appgalleriet |● |  |
| Webbprogram publicerade via Azure AD App Proxy |● |  |
| IIS-program som inte är publicerade via Azure AD App Proxy | |● |
| Fjärråtkomst som VPN eller Fjärrskrivbordsgateway (RDG) | ● | ● |

## <a name="where-are-the-users-located"></a>Var finns användarna?

Därefter fastställer du var användarna i organisationen är placerad hjälper dig att fastställa rätt lösning för dig, i molnet eller lokalt med hjälp av MFA-servern.

| Användarplats | MFA i molnet | MFA-server |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD och lokalt AD med federation med AD FS |● |● |
| Azure AD och lokalt AD med hjälp av Azure AD Connect – inga hash-synkronisering av lösenord eller direktautentisering |● |● |
| Azure AD och lokalt AD med hjälp av Azure AD Connect – med lösenordshash-synkronisering eller direktautentisering |● | |
| Lokalt Active Directory | |● |

## <a name="what-features-do-i-need"></a>Vilka funktioner behöver jag?

I följande tabell jämförs funktionerna som är tillgängliga med Multi-Factor Authentication i molnet och med Multi-Factor Authentication-servern.

| Funktion | MFA i molnet | MFA-server |
| --- |:---:|:---:|
| Meddelanden via mobilapp som en andra faktor | ● | ● |
| Verifieringskod via mobilapp som en andra faktor | ● | ● |
| Telefonsamtal som en andra faktor | ● | ● |
| Enkelriktad SMS som en andra faktor | ● | ● |
| Maskinvarutoken som en andra faktor | | ● |
| Applösenord för Office 365-klienter som inte stöder MFA | ● | |
| Administratörskontroll över autentiseringsmetoder | ● | ● |
| PIN-läge | | ● |
| Bedrägerivarning | ● | ● |
| MFA-rapporter | ● | ● |
| Engångsförbikoppling | | ● |
| Anpassade hälsningar för telefonsamtal | ● | ● |
| Anpassningsbar nummerpresentation för telefonsamtal | ● | ● |
| Tillförlitliga IP-adresser | ● | ● |
| MFA sparas för betrodda enheter | ● | |
| Villkorlig åtkomst | ● | ● |
| Cache |  | ● |

## <a name="next-steps"></a>Nästa steg

Nu när du vet skillnaden mellan Azure Multi-Factor Authentication i molnet och den lokala MFA-servern är det dags att konfigurera och använda Azure Multi-Factor Authentication. **Välj den ikon som motsvarar ditt scenario**

<center>

[![MFA i molnet](./media/concept-mfa-whichversion/cloud2.png)](howto-mfa-getstarted.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [![MFA-server](./media/concept-mfa-whichversion/server2.png)](howto-mfaserver-deploy.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </center>
