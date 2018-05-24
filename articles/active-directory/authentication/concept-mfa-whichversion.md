---
title: Välja mellan moln- eller serverbaserat Azure MFA | Microsoft Docs
description: Välj den säkerhetslösning med multifaktorautentisering som passar dig bäst genom att fråga dig vad du vill skydda och var dina användare finns.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 10/02/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 8314d72aa2cc6787d3f65dd48cd693a0ac332c0a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>Välj den Azure Multi-Factor Authentication-lösning som passar dig
Eftersom det finns flera varianter av Azure Multi-Factor Authentication (MFA) måste vi ställa oss några frågor för att ta reda på vilken version som är lämpligast.  Dessa frågor är:

* [Vad vill du skydda](#what-am-i-trying-to-secure)
* [Var finns användarna](#where-are-the-users-located)
* [Vilka funktioner behöver jag?](#what-features-do-i-need)

Följande avsnitt hjälper dig att besvara dessa frågor.

## <a name="what-am-i-trying-to-secure"></a>Vad vill du skydda?
För att kunna fastställa vilken tvåstegsverifierings-lösning som är rätt för dig måste vi först besvara frågan vad du försöker skydda med en andra autentiseringsmetod.  Är det ett program som finns i Azure?  Eller ett fjärråtkomstsystem?  Genom att fastställa vad vi vill skydda kan vi besvara frågan var multifaktorautentisering måste vara aktiverat.  

| Vad försöker du skydda? | MFA i molnet | MFA-server |
| --- |:---:|:---:|
| Första parts Microsoft-appar |● |● |
| Saas-appar i appgalleriet |● |  |
| Webbprogram publicerade via Azure AD App Proxy |● |  |
| IIS-program som inte är publicerade via Azure AD App Proxy | |● |
| Fjärråtkomst som VPN eller Fjärrskrivbordsgateway (RDG) | ● | ● |

## <a name="where-are-the-users-located"></a>Var finns användarna?
En bedömning av var användarna finns kan därefter hjälpa till att fastställa rätt lösning, oavsett om det är i molnet eller lokalt med hjälp av MFA-servern.

| Användarplats | MFA i molnet | MFA-server |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD och lokalt AD med federation med AD FS |● |● |
| Azure AD och lokal AD använder DirSync, Azure AD Sync, Azure AD Connect – utan hash-synkronisering av lösenord eller direktautentisering |● |● |
| Azure AD och lokal AD använder DirSync, Azure AD Sync, Azure AD Connect – med hash-synkronisering av lösenord eller direktautentisering |● | |
| Lokalt Active Directory | |● |

## <a name="what-features-do-i-need"></a>Vilka funktioner behöver jag?
I följande tabell jämförs funktionerna som är tillgängliga med Multi-Factor Authentication i molnet och med Multi-Factor Authentication-servern.

| Funktion | MFA i molnet | MFA-server |
| --- |:---:|:---:|
| Meddelanden via mobilapp som en andra faktor | ● | ● |
| Verifieringskod via mobilapp som en andra faktor | ● | ● |
| Telefonsamtal som en andra faktor | ● | ● |
| Enkelriktad SMS som en andra faktor | ● | ● |
| Dubbelriktad SMS som en andra faktor | | ●  (Inaktuell)| 
| Maskinvarutoken som en andra faktor | | ● |
| Applösenord för Office 365-klienter som inte stöder MFA | ● | |
| Administratörskontroll över autentiseringsmetoder | ● | ● |
| PIN-läge | | ● |
| Bedrägerivarning |● | ● |
| MFA-rapporter |● | ● |
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
