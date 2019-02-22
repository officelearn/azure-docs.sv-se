---
title: Distribution - planer i Azure Active Directory | Microsoft Docs
description: Slutpunkt till slutpunkt vägledning om hur du distribuerar många funktioner i Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7b34cfbd1dfd7936f3f0769b57b37003e1bfe03
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587930"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory-distributionsplaner
Vill du ha vägledning slutpunkt till slutpunkt om hur du distribuerar vissa av Azure Active Directory-funktionerna (Azure AD)? Följande distributionsplaner går igenom affärsvärde, planeringsöverväganden, design och operativa procedurer som krävs för att distribuera några av de vanligaste Azure AD-funktionerna. 

I dokumenten hittar du e-postmallar, systemarkitekturdiagram, vanliga testfall och mer. 

Vi vill gärna höra din feedback om dokumenten. Delta i den här korta [undersökningen](https://aka.ms/deploymentplanfeedback) om hur du tycker att dokumenten fungerade. 

|Scenario |Beskrivning |
|-|-|
|[Multi-Factor Authentication](https://aka.ms/MFADPDownload)|Azure Multi-Factor Authentication (MFA) är Microsofts verifieringslösning i två steg. Med administratörsgodkända autentiseringsmetoder hjälper Azure MFA till att skydda din åtkomst till data och program, samtidigt som behovet av en enkel inloggningsprocess uppfylls.|
|Villkorlig åtkomst [nedladdningsbara plan](https://aka.ms/CADPDownload) eller [online-avtal](https://aka.ms/deploymentplans/ca)|Med villkorlig åtkomst kan du implementera automatiserade beslut för åtkomstkontroll om vem som kan komma åt dina molnappar, baserat på villkor.|
|[Självåterställning av lösenord](https://aka.ms/SSPRDPDownload)|Självåterställning av lösenord hjälper användarna att återställa sina lösenord utan inblandning av administratören, när och var som helst.|
|[Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)|Azure AD Privileged Identity Management (PIM) hjälper dig att hantera Privilegierade administrativa roller i Azure AD Azure-resurser och andra Microsoft Online Services. PIM tillhandahåller lösningar som just-in-time-åtkomst, begäran godkännandearbetsflöden och helt integrerad åtkomst granskningar så att du kan identifiera, upptäcka och förhindra att skadliga aktiviteter av Privilegierade roller i realtid.|
|[Enkel inloggning](https://aka.ms/SSODPDownload)|Enkel inloggning underlättar åtkomst till alla appar och resurser du behöver för att göra affärer – du loggar bara in en gång, med ett enda användarkonto. När du har loggat in kan du gå från Microsoft Office till SalesForce till Box utan att behöva autentisera dig (till exempel att skriva ett lösenord) en andra gång.|
|[Sömlös enkel inloggning](https://aka.ms/SeamlessSSODPDownload)|Sömlös enkel inloggning med Azure Active Directory (sömlös SSO med Azure AD) loggar automatiskt in användare när de är på sina företagsenheter som är anslutna till företagsnätverket. När du aktiverar den här funktionen behöver användare inte ange sina lösenord för att logga in på Azure AD, och vanligtvis behöver de inte ens skriva in sina användarnamn. Den här funktionen ger användarna enkel åtkomst till dina molnbaserade program utan några ytterligare lokala komponenter.|
|[Åtkomstpanel](https://aka.ms/AccessPanelDPDownload)|Ger användarna en enkel hubb för att upptäcka och komma åt sina program. Aktivera dem kan bli mer produktiva med självbetjäningsfunktioner, till exempel möjligheten att begära åtkomst till nya appar och grupper, eller hantera åtkomst till dessa resurser å andras vägnar.|
|AD FS till Lösenordshashsynkronisering [nedladdningsbara plan](https://aka.ms/ADFSTOPHSDPDownload) eller [online-avtal](https://aka.ms/deploymentplans/adfs2phs)|Med hash-synkronisering av lösenord synkroniseras hash-värden för användarlösenord från lokal Active Directory till Azure AD, så att Azure AD kan autentisera användare utan interaktion med lokal Active Directory|
|AD FS att skicka via autentisering [nedladdningsbara plan](https://aka.ms/ADFSTOPTADPDownload) eller [online-avtal](https://aka.ms/deploymentplans/adfs2pta)|ADFS Pass Through Authentication (Passeringsautentisering) hjälper dina användare att logga in på både lokala och molnbaserade program med samma lösenord. Den här funktionen ger användarna en bättre upplevelse – ett lösenord mindre att komma ihåg, och kostnaderna för IT-supportavdelningen minskas eftersom användarna blir mindre benägna att glömma hur de ska logga in. När användare loggar in med Azure AD verifierar den här funktionen användarnas lösenord direkt mot din lokala Active Directory.|
|[Azure AD-programproxy](https://aka.ms/AppProxyDPDownload)|I dag vill anställda vara produktiva var som helst, när som helst och från valfri enhet. De vill arbeta med sina egna enheter, oavsett om det gäller bärbara datorer, surfplattor eller telefoner. Anställda förväntar sig dessutom att kunna komma åt alla sina program, både SaaS-appar i molnet och företagsappar lokalt. Att ge åtkomst till lokala program har traditionellt inneburit virtuella privata nätverk (VPN) eller perimeternätverk (DMZ). De här lösningarna är inte bara komplexa och svåra att skydda, utan de är även dyra att konfigurera och hantera. Det finns ett bättre sätt! – Azure AD-programproxy|
|[Användaretablering](https://aka.ms/UserProvisioningDPDownload)|Azure AD hjälper dig att automatisera skapande, underhåll och borttagning av användaridentiteter i molnprogram (SaaS), till exempel Dropbox, Salesforce, ServiceNow och mer.|
|[Etableringen av workday-driven inkommande användare](https://aka.ms/WorkdayDeploymentPlan)|Workday-driven inkommande Användaretablering till Active Directory skapar en grund för pågående identitetsstyrning och förbättrar kvaliteten på affärsprocesser som förlitar sig på auktoritativa identitetsdata. Med den här funktionen kan du smidigt hanterar identitetslivscykel för anställda och tillfällig arbetare genom att konfigurera regler som mappar sammanbindning-Kolumnlagringens-Leaver processer (till exempel nya rekrytera, avsluta, Transfer) för etablering IT-åtgärder (till exempel skapa, aktivera, Inaktivera och ta bort konton).|
