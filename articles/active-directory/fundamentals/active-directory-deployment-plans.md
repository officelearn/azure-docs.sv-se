---
title: Azure Active Directory-distributionsplaner | Microsoft Docs
description: Innehåller vägledning slutpunkt till slutpunkt om hur du distribuerar Azure Active Directory-funktioner
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: overview
ms.date: 08/03/2018
ms.author: lizross
ms.openlocfilehash: 07d3915fd007c0827b885b0603eb176b9e408576
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39508370"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory-distributionsplaner
Vill du ha vägledning slutpunkt till slutpunkt om hur du distribuerar vissa av Azure Active Directory-funktionerna (Azure AD)? Följande distributionsplaner går igenom affärsvärde, planeringsöverväganden, design och operativa procedurer som krävs för att distribuera några av de vanligaste Azure AD-funktionerna. 

I dokumenten hittar du e-postmallar, systemarkitekturdiagram, vanliga testfall och mer. 

Vi vill gärna höra din feedback om dokumenten. Delta i den här korta [undersökningen](https://aka.ms/deploymentplanfeedback) om hur du tycker att dokumenten fungerade. 

|Scenario |Beskrivning |
|-|-|
|[Enkel inloggning](https://aka.ms/SSODPDownload)|Enkel inloggning underlättar åtkomst till alla appar och resurser du behöver för att göra affärer – du loggar bara in en gång, med ett enda användarkonto. När du har loggat in kan du gå från Microsoft Office till SalesForce till Box utan att behöva autentisera dig (till exempel att skriva ett lösenord) en andra gång.|
|[Användaretablering](https://aka.ms/UserProvisioningDPDownload)|Azure AD hjälper dig att automatisera skapande, underhåll och borttagning av användaridentiteter i molnprogram (SaaS), till exempel Dropbox, Salesforce, ServiceNow och mer.|
|[Multi-Factor Authentication](https://aka.ms/MFADPDownload)|Azure Multi-Factor Authentication (MFA) är Microsofts verifieringslösning i två steg. Med administratörsgodkända autentiseringsmetoder hjälper Azure MFA till att skydda din åtkomst till data och program, samtidigt som behovet av en enkel inloggningsprocess uppfylls.|
|[Villkorlig åtkomst](https://aka.ms/CADPDownload)|Med villkorlig åtkomst kan du implementera automatiserade beslut för åtkomstkontroll om vem som kan komma åt dina molnappar, baserat på villkor.|
|[ADFS till Pass Through Authentication](https://aka.ms/ADFSTOPTADPDownload) (Passeringsautentisering)|ADFS Pass Through Authentication (Passeringsautentisering) hjälper dina användare att logga in på både lokala och molnbaserade program med samma lösenord. Den här funktionen ger användarna en bättre upplevelse – ett lösenord mindre att komma ihåg, och kostnaderna för IT-supportavdelningen minskas eftersom användarna blir mindre benägna att glömma hur de ska logga in. När användare loggar in med Azure AD verifierar den här funktionen användarnas lösenord direkt mot din lokala Active Directory.|
|[ADFS till hash-synkronisering av lösenord](https://aka.ms/ADFSTOPHSDPDownload)|Med hash-synkronisering av lösenord synkroniseras hash-värden för användarlösenord från lokal Active Directory till Azure AD, så att Azure AD kan autentisera användare utan interaktion med lokal Active Directory|
|[Sömlös enkel inloggning](https://aka.ms/SeamlessSSODPDownload)|Sömlös enkel inloggning med Azure Active Directory (sömlös SSO med Azure AD) loggar automatiskt in användare när de är på sina företagsenheter som är anslutna till företagsnätverket. När du aktiverar den här funktionen behöver användare inte ange sina lösenord för att logga in på Azure AD, och vanligtvis behöver de inte ens skriva in sina användarnamn. Den här funktionen ger användarna enkel åtkomst till dina molnbaserade program utan några ytterligare lokala komponenter.|
|[Självåterställning av lösenord](https://aka.ms/SSPRDPDownload)|Självåterställning av lösenord hjälper användarna att återställa sina lösenord utan inblandning av administratören, när och var som helst.|
|[Azure AD-programproxy](https://aka.ms/AppProxyDPDownload)|I dag vill anställda vara produktiva var som helst, när som helst och från valfri enhet. De vill arbeta med sina egna enheter, oavsett om det gäller bärbara datorer, surfplattor eller telefoner. Anställda förväntar sig dessutom att kunna komma åt alla sina program, både SaaS-appar i molnet och företagsappar lokalt. Att ge åtkomst till lokala program har traditionellt inneburit virtuella privata nätverk (VPN) eller demilitariserade zoner (DMZ). De här lösningarna är inte bara komplexa och svåra att skydda, utan de är även dyra att konfigurera och hantera. Det finns ett bättre sätt! – Azure AD-programproxy|
