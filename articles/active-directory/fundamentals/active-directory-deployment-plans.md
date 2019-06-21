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
ms.openlocfilehash: f59cae87af5f29d900159639798f9cd031f3d765
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302374"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory-distributionsplaner
Vill du ha vägledning slutpunkt till slutpunkt om hur du distribuerar vissa av Azure Active Directory-funktionerna (Azure AD)? Följande distributionsplaner går igenom affärsvärde, planeringsöverväganden, design och operativa procedurer som krävs för att distribuera några av de vanligaste Azure AD-funktionerna. 

I dokumenten hittar du e-postmallar, systemarkitekturdiagram, vanliga testfall och mer. 

Vi vill gärna höra din feedback om dokumenten. Delta i den här korta [undersökningen](https://aka.ms/deploymentplanfeedback) om hur du tycker att dokumenten fungerade. 

## <a name="include-the-right-stakeholders"></a>Inkludera rätt intressenter

När du startar distributionen planera för en ny funktion, är det viktigt att ha viktiga intressenter i hela organisationen. Vi rekommenderar att du identifiera och dokumentera den person eller personer som uppfyller kraven på var och en av följande roller och arbeta med dem för att fastställa deras inblandning i projektet.  

Roller som kan vara följande 

|Roll |Beskrivning |
|-|-|
|Slutanvändare|En representativ grupp användare för vilka funktionen kommer att implementeras. Ofta förhandsversioner ändringarna i ett pilotprogram.
|IT-chef för Support|IT organisation supportrepresentant som kan ge information om support för den här ändringen från ett perspektiv för supportavdelningen.  
|Identitet Architect eller Azure Global administratör|Identity management-teamet representant ansvarar för definierar hur den här ändringen justeras med grundläggande identity management-infrastruktur i din organisation.|
|Företagsägaren för program |Övergripande företagsägaren på de berörda program som kan innehålla åtkomsthantering.  Kan också ange kommentarer om användarupplevelsen och led i den här ändringen från en slutanvändares perspektiv.
|Security ägare|En representant från säkerhetsteamet som kan logga ut som planen som passar organisationens säkerhetskrav.|
|Compliance Manager|Personen i din organisation som ansvarar för att säkerställa efterlevnad med företagets, bransch eller statliga krav.|

**Nivåer för medverkan kan innehålla:**

- **R**esponsible för att implementera projektplanen och resultatet 

- **En**pproval om projektplanen och resultatet 

- **C**ontributor projektplanen och resultatet 

- **Jag**nformed om projektplanen och resultatet
 
## <a name="deployment-plans"></a>Planer för distribution



|Scenario |Beskrivning |
|-|-|
|[Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)|Azure Multi-Factor Authentication (MFA) är Microsofts verifieringslösning i två steg. Med administratörsgodkända autentiseringsmetoder hjälper Azure MFA till att skydda din åtkomst till data och program, samtidigt som behovet av en enkel inloggningsprocess uppfylls.|
|[Villkorlig åtkomst](https://aka.ms/deploymentplans/ca)|Du kan implementera automatiserade besluten om åtkomstkontroll för vem som kan komma åt dina molnappar baserat på villkor med villkorlig åtkomst.|
|[Självåterställning av lösenord](https://aka.ms/SSPRDPDownload)|Självåterställning av lösenord hjälper användarna att återställa sina lösenord utan inblandning av administratören, när och var som helst.|
|[Privileged Identity Management](https://aka.ms/deploymentplans/pim)|Azure AD Privileged Identity Management (PIM) hjälper dig att hantera Privilegierade administrativa roller i Azure AD Azure-resurser och andra Microsoft Online Services. PIM tillhandahåller lösningar som just-in-time-åtkomst, begäran godkännandearbetsflöden och helt integrerad åtkomst granskningar så att du kan identifiera, upptäcka och förhindra att skadliga aktiviteter av Privilegierade roller i realtid.|
|[Enkel inloggning](https://aka.ms/SSODPDownload)|Enkel inloggning underlättar åtkomst till alla appar och resurser du behöver för att göra affärer – du loggar bara in en gång, med ett enda användarkonto. När du har loggat in kan du gå från Microsoft Office till SalesForce till Box utan att behöva autentisera dig (till exempel att skriva ett lösenord) en andra gång.|
|[Sömlös enkel inloggning](https://aka.ms/SeamlessSSODPDownload)|Sömlös enkel inloggning med Azure Active Directory (sömlös SSO med Azure AD) loggar automatiskt in användare när de är på sina företagsenheter som är anslutna till företagsnätverket. När du aktiverar den här funktionen behöver användare inte ange sina lösenord för att logga in på Azure AD, och vanligtvis behöver de inte ens skriva in sina användarnamn. Den här funktionen ger användarna enkel åtkomst till dina molnbaserade program utan några ytterligare lokala komponenter.|
|[Åtkomstpanel](https://aka.ms/AccessPanelDPDownload)|Ger användarna en enkel hubb för att upptäcka och komma åt sina program. Aktivera dem kan bli mer produktiva med självbetjäningsfunktioner, till exempel möjligheten att begära åtkomst till nya appar och grupper, eller hantera åtkomst till dessa resurser å andras vägnar.|
|[ADFS till hash-synkronisering av lösenord](https://aka.ms/deploymentplans/adfs2phs)|Med hash-synkronisering av lösenord synkroniseras hash-värden för användarlösenord från lokal Active Directory till Azure AD, så att Azure AD kan autentisera användare utan interaktion med lokal Active Directory|
|[ADFS till Pass Through Authentication](https://aka.ms/deploymentplans/adfs2pta) (Passeringsautentisering)|ADFS Pass Through Authentication (Passeringsautentisering) hjälper dina användare att logga in på både lokala och molnbaserade program med samma lösenord. Den här funktionen ger användarna en bättre upplevelse – ett lösenord mindre att komma ihåg, och kostnaderna för IT-supportavdelningen minskas eftersom användarna blir mindre benägna att glömma hur de ska logga in. När användare loggar in med Azure AD verifierar den här funktionen användarnas lösenord direkt mot din lokala Active Directory.|
|[Azure AD-programproxy](https://aka.ms/deploymentplans/appproxy)|I dag vill anställda vara produktiva var som helst, när som helst och från valfri enhet. De vill arbeta med sina egna enheter, oavsett om det gäller bärbara datorer, surfplattor eller telefoner. Anställda förväntar sig dessutom att kunna komma åt alla sina program, både SaaS-appar i molnet och företagsappar lokalt. Att ge åtkomst till lokala program har traditionellt inneburit virtuella privata nätverk (VPN) eller perimeternätverk (DMZ). De här lösningarna är inte bara komplexa och svåra att skydda, utan de är även dyra att konfigurera och hantera. Det finns ett bättre sätt! – Azure AD-programproxy|
|[Användaretablering](https://aka.ms/UserProvisioningDPDownload)|Azure AD hjälper dig att automatisera skapande, underhåll och borttagning av användaridentiteter i molnprogram (SaaS), till exempel Dropbox, Salesforce, ServiceNow och mer.|
|[Etableringen av workday-driven inkommande användare](https://aka.ms/WorkdayDeploymentPlan)|Workday-driven inkommande Användaretablering till Active Directory skapar en grund för pågående identitetsstyrning och förbättrar kvaliteten på affärsprocesser som förlitar sig på auktoritativa identitetsdata. Med den här funktionen kan du smidigt hanterar identitetslivscykel för anställda och tillfällig arbetare genom att konfigurera regler som mappar sammanbindning-Kolumnlagringens-Leaver processer (till exempel nya rekrytera, avsluta, Transfer) för etablering IT-åtgärder (till exempel skapa, aktivera, Inaktivera och ta bort konton).|
