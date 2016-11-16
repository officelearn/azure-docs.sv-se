---
title: Azure MFA moln kontra server | Microsoft Docs
description: "Välj den Multi-Factor Authentication-säkerhetslösning som passar dig bäst genom att fråga dig vad du vill skydda och var dina användare finns.  Välj sedan molnet, MFA Server eller AD FS."
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
ms.date: 10/14/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 72347099d980f2ca73f39f984787197e1f87e45a


---
# <a name="choose-the-azure-multifactor-authentication-solution-for-you"></a>Välj den Azure Multi-Factor Authentication-lösning som passar dig
Eftersom det finns flera varianter av Azure Multi-Factor Authentication (MFA) måste vi ställa oss några frågor för att ta reda på vilken version som är lämpligast.  Dessa frågor är:

* [Vad vill du skydda](#what-am-i-trying-to-secure)
* [Var finns användarna](#where-are-the-users-located)
* [Vilka funktioner behöver jag?](#what-featured-do-i-need)

Följande avsnitt hjälper dig att besvara dessa frågor.

## <a name="what-am-i-trying-to-secure"></a>Vad vill du skydda?
För att kunna fastställa vilken tvåstegsverifierings-lösning som är rätt för dig måste vi först besvara frågan vad du försöker skydda med en andra autentiseringsmetod.  Är det ett program som finns i Azure?  Eller ett fjärråtkomstsystem?  Genom att fastställa vad vi vill skydda kan vi besvara frågan var multifaktorautentisering måste vara aktiverat.  

| Vad försöker du skydda? | Multi-Factor Authentication i molnet | Multi-Factor Authentication Server |
| --- |:---:|:---:|
| Första parts Microsoft-appar |● |● |
| Saas-appar i appgalleriet |● |● |
| IIS-program publicerade via Azure AD App Proxy |● |● |
| IIS-program som inte är publicerade via Azure AD App Proxy | |● |
| Fjärråtkomst som VPN eller Fjärrskrivbordsgateway (RDG) | |● |

## <a name="where-are-the-users-located"></a>Var finns användarna?
En bedömning av var användarna finns kan därefter hjälpa till att fastställa rätt lösning, oavsett om det är i molnet eller lokalt med hjälp av MFA-servern.

| Användarplats | Multi-Factor Authentication i molnet | Multi-Factor Authentication Server |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD och lokalt AD med federation med AD FS |● |● |
| Azure AD och lokalt AD med DirSync, Azure AD Sync, Azure AD Connect – utan lösenordssynkronisering |● |● |
| Azure AD och lokalt AD med DirSync, Azure AD Sync, Azure AD Connect – med lösenordssynkronisering |● | |
| Lokalt Active Directory | |● |

## <a name="what-features-do-i-need"></a>Vilka funktioner behöver jag?
I följande tabell jämförs funktionerna som är tillgängliga med Multi-Factor Authentication i molnet och med Multi-Factor Authentication-servern.

| Multi-Factor Authentication i molnet | Multi-Factor Authentication Server |
| --- |:---:|:---:|
| Meddelanden via mobilapp som en andra faktor |● |
| Verifieringskod via mobilapp som en andra faktor |● |
| Telefonsamtal som en andra faktor |● |
| Enkelriktad SMS som en andra faktor |● |
| Dubbelriktad SMS som en andra faktor | |
| Maskinvarutoken som en andra faktor | |
| Applösenord för klienter som inte stöder MFA |● |
| Administratörskontroll över autentiseringsmetoder |● |
| PIN-läge | |
| Bedrägerivarning |● |
| MFA-rapporter |● |
| Engångsförbikoppling | |
| Anpassade hälsningar för telefonsamtal |● |
| Anpassningsbar nummerpresentation för telefonsamtal |● |
| Tillförlitliga IP-adresser |● |
| MFA sparas för betrodda enheter |● |
| Villkorlig åtkomst |● |
| Cache | |

Nu när vi har fastställt om Multi-Factor Authentication i molnet eller lokala MFA Server ska användas kan vi börja konfigurera och använda Azure Multi-Factor Authentication. **Välj den ikon som representerar ditt scenario!**

<center>




[![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>



<!--HONumber=Nov16_HO2-->


