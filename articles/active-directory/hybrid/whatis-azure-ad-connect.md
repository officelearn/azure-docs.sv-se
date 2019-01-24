---
title: Vad är Azure AD Connect och Connect Health? | Microsoft Docs
description: Beskriver de verktyg som används för att synkronisera och övervaka din lokala miljö med Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: get-started-article
ms.date: 11/28/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 1c18200bb36b75a07d7b26e3ea0016ec35efdd87
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54460515"
---
# <a name="what-is-azure-ad-connect"></a>Vad är Azure AD Connect?

Azure AD Connect är Microsoft-verktyget som har utformats för att uppfylla och uppnå dina hybrididentitetsmål.  Den tillhandahåller följande funktioner:
    
- [Synkronisering av lösenordshash](whatis-phs.md) – En inloggningsmetod som synkroniserar en hash av en användares lokala AD-lösenord med Azure AD.
- [Direktautentisering](how-to-connect-pta.md) – En inloggningsmetod som gör att användare kan använda samma lösenord lokalt och i molnet men inte kräver den ytterligare infrastrukturen hos en federerad miljö.
- [Federationsintegrering](how-to-connect-fed-whatis.md) – Federation är en valfri del av Azure AD Connect som kan användas för att konfigurera en hybridmiljö med hjälp av en lokal AD FS-infrastruktur. Det har även AD FS-hanteringsfunktioner som certifikatsförnyelse och ytterligare AD FS-serverdistributioner.
- [Synkronisering](how-to-connect-sync-whatis.md) – Ansvarar för att skapa användare, grupper och andra objekt.  Den ser även till att identitetsinformationen för dina lokala användare och grupper matchar molnet.  Den här synkroniseringen omfattar även lösenordshasher.
-   [Hälsoövervakning](whatis-hybrid-identity-health.md) – Azure AD Connect Health kan tillhandahålla robust övervakning och en central plats i Azure-portalen för att övervaka den här aktiviteten. 


![Vad är Azure AD Connect?](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Vad är Azure AD Connect Health?

Azure Active Directory (Azure AD) Connect Health tillhandahåller gedigen övervakning av den lokala identitetsinfrastrukturen. Det hjälper dig att upprätthålla en tillförlitlig anslutning till Office 365 och Microsoft Online Services.  Den här tillförlitligheten uppnås genom att övervakningsfunktioner tillhandahålls för dina viktiga identitetskomponenter. Dessutom blir de viktiga datapunkterna om dessa komponenter lättillgängliga.

Informationen visas på [Azure AD Connect Health-portalen](https://aka.ms/aadconnecthealth). Använd Azure AD Connect Health-portalen till att visa aviseringar, prestandaövervakning, användningsanalys och annan information. Med Azure AD Connect Health har du åtkomst till all hälsoinformation om dina viktiga identitetskomponenter på samma ställe.

![Vad är Azure AD Connect Health?](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>Varför ska jag använda Azure AD Connect?
Om du integrerar dina lokala kataloger med Azure AD kan du hjälpa dina användare att bli mer produktiva genom att tillhandahålla en gemensam identitet för åtkomst både till molnet och lokala resurser. Användare och organisationer kan dra nytta av följande:

* Användarna kan använda samma identitet för att komma åt lokala program och molntjänster som Office 365.
* Ett enda verktyg är allt som krävs för att tillhandahålla en enkel distributionsupplevelse för synkronisering och inloggning.
* Innehåller de senaste funktionerna för dina scenarier. Azure AD Connect ersätter äldre versioner av identitetsintegrationsverktyg som DirSync och Azure AD Sync. Mer information finns i [Jämförelse av katalogintegreringsverktyg för hybrididentitet](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Varför ska jag använda Azure AD Connect Health?
Med Azure AD hjälper du dina användare att bli mer produktiva genom att tillhandahålla en gemensam identitet för åtkomst både till molnet och lokala resurser. Det är en utmaning att göra miljön tillförlitlig så att användarna kan komma åt dessa resurser.  Azure AD Connect Health hjälper till att övervaka och ge insyn i din lokala identitetsinfrastruktur och säkerställer därmed miljöns tillförlitlighet. Det enda du behöver göra är att installera en agent på alla lokala identitetsservrar.


## <a name="next-steps"></a>Nästa steg

- [Maskinvara och krav](how-to-connect-install-prerequisites.md) 
- [Standardinställningar](how-to-connect-install-express.md)
- [Anpassade inställningar](how-to-connect-install-custom.md)
- [Installera Azure AD Connect Health-agenter](how-to-connect-health-agent-install.md) 
