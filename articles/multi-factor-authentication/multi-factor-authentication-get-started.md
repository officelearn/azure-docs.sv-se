---
title: "Välja mellan moln- eller serverbaserat Azure MFA | Microsoft Docs"
description: "Välj den säkerhetslösning med multifaktorautentisering som passar dig bäst genom att fråga dig vad du vill skydda och var dina användare finns.  Välj sedan molnet, MFA Server eller AD FS."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: ec2270ea-13d7-4ebc-8a00-fa75ce6c746d
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/23/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 8c3cc27f1fa708371d415d00cdff59f77712cd41
ms.lasthandoff: 05/03/2017


---
# <a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>Välj den Azure Multi-Factor Authentication-lösning som passar dig
Eftersom det finns flera varianter av Azure Multi-Factor Authentication (MFA) måste vi ställa oss några frågor för att ta reda på vilken version som är lämpligast.  Dessa frågor är:

* [Vad vill du skydda](#what-am-i-trying-to-secure)
* [Var finns användarna](#where-are-the-users-located)
* [Vilka funktioner behöver jag?](#what-featured-do-i-need)

Följande avsnitt hjälper dig att besvara dessa frågor.

## <a name="what-am-i-trying-to-secure"></a>Vad vill du skydda?
För att kunna fastställa vilken tvåstegsverifierings-lösning som är rätt för dig måste vi först besvara frågan vad du försöker skydda med en andra autentiseringsmetod.  Är det ett program som finns i Azure?  Eller ett fjärråtkomstsystem?  Genom att fastställa vad vi vill skydda kan vi besvara frågan var multifaktorautentisering måste vara aktiverat.  

| Vad försöker du skydda? | MFA i molnet | MFA-server |
| --- |:---:|:---:|
| Första parts Microsoft-appar |● |● |
| Saas-appar i appgalleriet |● |  |
| Webbprogram publicerade via Azure AD App Proxy |● |  |
| IIS-program som inte är publicerade via Azure AD App Proxy | |● |
| Fjärråtkomst som VPN eller Fjärrskrivbordsgateway (RDG) | |● |

## <a name="where-are-the-users-located"></a>Var finns användarna?
En bedömning av var användarna finns kan därefter hjälpa till att fastställa rätt lösning, oavsett om det är i molnet eller lokalt med hjälp av MFA-servern.

| Användarplats | MFA i molnet | MFA-server |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD och lokalt AD med federation med AD FS |● |● |
| Azure AD och lokalt AD med DirSync, Azure AD Sync, Azure AD Connect – utan lösenordssynkronisering |● |● |
| Azure AD och lokalt AD med DirSync, Azure AD Sync, Azure AD Connect – med lösenordssynkronisering |● | |
| Lokalt Active Directory | |● |

## <a name="what-features-do-i-need"></a>Vilka funktioner behöver jag?
I följande tabell jämförs funktionerna som är tillgängliga med Multi-Factor Authentication i molnet och med Multi-Factor Authentication-servern.

| Funktion | MFA i molnet | MFA-server |
| --- |:---:|:---:|
| Meddelanden via mobilapp som en andra faktor | ● | ● |
| Verifieringskod via mobilapp som en andra faktor | ● | ● |
| Telefonsamtal som en andra faktor | ● | ● |
| Enkelriktad SMS som en andra faktor | ● | ● |
| Dubbelriktad SMS som en andra faktor | | ● |
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

Nu när vi har fastställt om Multi-Factor Authentication i molnet eller lokala MFA Server ska användas kan vi börja konfigurera och använda Azure Multi-Factor Authentication. **Välj den ikon som representerar ditt scenario!**

<center>




[![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>

