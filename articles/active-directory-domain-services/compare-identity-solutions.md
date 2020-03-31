---
title: Jämför Active Directory-baserade tjänster i Azure | Microsoft-dokument
description: I den här översikten jämför du de olika identitetserbjudandena för Active Directory Domain Services, Azure Active Directory och Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: d2495605cccf658b15e812fd85fd65671e84d15b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76544284"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>Jämföra självhanterade Active Directory Domain Services, Azure Active Directory och hanterade Azure Active Directory Domain Services

Om du vill ge program, tjänster eller enheter åtkomst till en central identitet finns det tre vanliga sätt att använda Active Directory-baserade tjänster i Azure. Det här valet i identitetslösningar ger dig flexibiliteten att använda den lämpligaste katalogen för organisationens behov. Om du till exempel oftast hanterar molnanvändare som kör mobila enheter kanske det inte är meningsfullt att skapa och köra din egen AD DS-identitetslösning (Active Directory Domain Services). I stället kan du bara använda Azure Active Directory.

Även om de tre Active Directory-baserade identitetslösningarna har ett gemensamt namn och en gemensam teknik är de utformade för att tillhandahålla tjänster som uppfyller olika kundkrav. På hög nivå är dessa identitetslösningar och funktionsuppsättningar:

* **AD DS (Active Directory Domain Services)** – LDAP-server (Enterprise-ready lightweight Directory Access Protocol) som tillhandahåller viktiga funktioner som identitet och autentisering, hantering av datorobjekt, grupprinciper och förtroenden.
    * AD DS är en central komponent i många organisationer med en lokal IT-miljö och tillhandahåller grundläggande autentisering av användarkonton och datorhanteringsfunktioner.
* **Azure Active Directory (Azure AD)** – Molnbaserad identitets- och hantering av mobila enheter som tillhandahåller användarkonto- och autentiseringstjänster för resurser som Office 365, Azure-portalen eller SaaS-program.
    * Azure AD kan synkroniseras med en lokal AD DS-miljö för att tillhandahålla en enda identitet till användare som arbetar inbyggt i molnet.
* **Azure Active Directory Domain Services (Azure AD DS)** – Tillhandahåller hanterade domäntjänster med en delmängd av heltkompatibla traditionella AD DS-funktioner som domänanslutning, grupprincip, LDAP- och Kerberos/ NTLM-autentisering.
    * Azure AD DS integreras med Azure AD, som i sig kan synkronisera med en lokal AD DS-miljö. Den här möjligheten utökar centrala identitetsanvändningsfall till traditionella webbprogram som körs i Azure som en del av en lift-and-shift-strategi.

Den här översiktsartikeln jämför och kontrasterar hur dessa identitetslösningar kan fungera tillsammans eller användas oberoende av varandra, beroende på organisationens behov.

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Azure AD DS och självhanterad AD DS

Om du har program och tjänster som behöver åtkomst till traditionella autentiseringsmekanismer som Kerberos eller NTLM finns det två sätt att tillhandahålla Active Directory Domain Services i molnet:

* En *hanterad* domän som du skapar med Azure Active Directory Domain Services (Azure AD DS). Microsoft skapar och hanterar de resurser som krävs.
* En *självhanterad* domän som du skapar och konfigurerar med traditionella resurser, till exempel virtuella datorer, Windows Server-gästoperatoroper och AD DS (Active Directory Domain Services). Du fortsätter sedan att administrera dessa resurser.

Med Azure AD DS distribueras och underhålls kärntjänstkomponenterna för dig av Microsoft som en *hanterad* domänupplevelse. Du distribuerar, hanterar, korrigeringar och säkrar inte AD DS-infrastrukturen för komponenter som virtuella datorer, Windows Server OS eller domänkontrollanter (DC).

Azure AD DS tillhandahåller en mindre delmängd av funktioner till traditionell självhanterad AD DS-miljö, vilket minskar en del av design- och hanteringskomplexiteten. Det finns till exempel inga AD-skogar, domän-, platser- och replikeringslänkar för att utforma och underhålla. För program och tjänster som körs i molnet och behöver åtkomst till traditionella autentiseringsmekanismer som Kerberos eller NTLM, ger Azure AD DS en hanterad domänupplevelse med minimal mängd administrativa omkostnader.

När du distribuerar och kör en självhanterad AD DS-miljö måste du underhålla alla associerade infrastruktur- och katalogkomponenter. Det finns ytterligare underhållskostnader med en självhanterad AD DS-miljö, men du kan sedan utföra ytterligare uppgifter som att utöka schemat eller skapa skogsförtroende.

Vanliga distributionsmodeller för en självhanterad AD DS-miljö som tillhandahåller identitet för program och tjänster i molnet är följande:

* **Fristående AD DS- Azure-datorer** konfigureras som domänkontrollanter och en separat AD DS-miljö för molnet skapas. Den här AD DS-miljön integreras inte med en lokal AD DS-miljö. En annan uppsättning autentiseringsuppgifter används för att logga in och administrera virtuella datorer i molnet.
* **Resursskogsdistribution** - Virtuella Azure-datorer konfigureras som domänkontrollanter och en AD DS-domän som är en del av en befintlig skog skapas. En förtroenderelation konfigureras sedan till en lokal AD DS-miljö. Andra virtuella Azure-datorer kan domänkoppling till den här resursskogen i molnet. Användarautentisering körs över en VPN/ ExpressRoute-anslutning till den lokala AD DS-miljön.
* **Utöka den lokala domänen till Azure** – Ett virtuellt Azure-nätverk ansluter till ett lokalt nätverk med hjälp av en VPN/ExpressRoute-anslutning. Virtuella Azure-datorer ansluter till det här virtuella Azure-nätverket, vilket gör att de kan ansluta sig till den lokala AD DS-miljön.
    * Ett alternativ är att skapa virtuella Azure-datorer och befordra dem som replikdomänkontrollanter från den lokala AD DS-domänen. Dessa domänkontrollanter replikeras via en VPN/ExpressRoute-anslutning till den lokala AD DS-miljön. Den lokala AD DS-domänen utökas effektivt till Azure.

I följande tabell beskrivs några av de funktioner som du kan behöva för din organisation och skillnaderna mellan en hanterad Azure AD DS-domän eller en självhanterad AD DS-domän:

| **Funktion** | **Azure AD DS** | **Självhanterad AD DS** |
| ----------- |:---------------:|:----------------------:|
| **Hanterad tjänst**                               | **&#x2713;** | **&#x2715;** |
| **Säkra distributioner**                            | **&#x2713;** | Administratören säkrar distributionen |
| **DNS-server**                                    | **&#x2713;** (hanterad tjänst) |**&#x2713;** |
| **Domän- eller företagsadministratörsbehörighet** | **&#x2715;** | **&#x2713;** |
| **Domänanslutning**                                   | **&#x2713;** | **&#x2713;** |
| **Domänautentisering med NTLM och Kerberos** | **&#x2713;** | **&#x2713;** |
| **Kerberos begränsad delegering**               | Resursbaserad | Resursbaserad & kontobaserad|
| **Anpassad organisationsenhetsstruktur**                           | **&#x2713;** | **&#x2713;** |
| **Grupprincip**                                  | **&#x2713;** | **&#x2713;** |
| **Schematillägg**                             | **&#x2715;** | **&#x2713;** |
| **AD-domän/skogsförtroende**                     | **&#x2713;** (endast envägs utgående skogsförtroende) | **&#x2713;** |
| **Säkert LDAP (LDAPS)**                           | **&#x2713;** | **&#x2713;** |
| **LDAP läsa**                                     | **&#x2713;** | **&#x2713;** |
| **LDAP skriva**                                    | **&#x2713;** (inom den hanterade domänen) | **&#x2713;** |
| **Geodistributioner**                   | **&#x2715;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS och Azure AD

Med Azure AD kan du hantera identiteten på enheter som används av organisationen och kontrollera åtkomsten till företagsresurser från dessa enheter. Användare kan också registrera sin personliga enhet (en bring-your-own (BYO)-modell med Azure AD, som ger enheten en identitet. Azure AD autentiserar sedan enheten när en användare loggar in på Azure AD och använder enheten för att komma åt säkra resurser. Enheten kan hanteras med hjälp av MDM-programvara (Mobile Device Management) som Microsoft Intune. Med den här hanteringsmöjligheten kan du begränsa åtkomsten till känsliga resurser till hanterade och principkompatibla enheter.

Traditionella datorer och bärbara datorer kan också ansluta till Azure AD. Den här mekanismen erbjuder samma fördelar med att registrera en personlig enhet med Azure AD, till exempel för att tillåta användare att logga in på enheten med sina företagsautentiseringsuppgifter.

Azure AD-anslutna enheter ger dig följande fördelar:

* Enkel inloggning (SSO) till program som skyddas av Azure AD.
* Företagsprincipkompatibel roaming med användarinställningar på olika enheter.
* Åtkomst till Windows Store för företag med företagsautentiseringsuppgifter.
* Windows Hello för företag.
* Begränsad åtkomst till appar och resurser från enheter som är kompatibla med företagets policy.

Enheter kan anslutas till Azure AD med eller utan en hybriddistribution som innehåller en lokal AD DS-miljö. I följande tabell beskrivs vanliga modeller för enhetsägarskap och hur de normalt skulle anslutas till en domän:

| **Typ av enhet**                                        | **Enhetsplattformar**             | **Mekanism**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| Personliga enheter                                          | Windows 10, iOS, Android, Mac OS | Azure AD-registrerad    |
| Organisationsägd enhet som inte är ansluten till lokalt AD DS | Windows 10                       | Azure AD-ansluten        |
| Organisationsägd enhet som är ansluten till en lokal AD DS  | Windows 10                       | Hybrid Azure AD-ansluten |

På en Azure AD-ansluten eller registrerad enhet sker användarautentisering med hjälp av moderna OAuth / OpenID Connect-baserade protokoll. Dessa protokoll är utformade för att fungera över internet, så är bra för mobila scenarier där användare får tillgång till företagsresurser var som helst.

Med Azure AD DS-anslutna enheter kan program använda Kerberos- och NTLM-protokollen för autentisering, så kan stödja äldre program som migreras för att köras på virtuella Azure-datorer som en del av en strategi för lyft och skift. I följande tabell beskrivs skillnader i hur enheterna representeras och kan autentisera sig mot katalogen:

| **Aspekt**                      | **Azure AD-ansluten**                                 | **Azure AD DS-ansluten**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| Enhet som styrs av            | Azure AD                                            | Azure AD DS-hanterad domän                                                |
| Representation i katalogen | Enhetsobjekt i Azure AD-katalogen            | Datorobjekt i den hanterade Azure AD DS-domänen                        |
| Autentisering                  | OAuth / OpenID Connect-baserade protokoll              | Kerberos- och NTLM-protokoll                                               |
| Hantering                      | MDM-programvara (Mobile Device Management) som Intune | Grupprincip                                                              |
| Nätverk                      | Fungerar över internet                             | Måste vara anslutet till, eller peered med, det virtuella nätverket där den hanterade domänen distribueras |
| Perfekt för...                    | Mobila eller stationära slutanvändare                  | Virtuella serverdämpningsmaskiner som distribueras i Azure                                              |

## <a name="next-steps"></a>Nästa steg

Om du vill komma igång med att använda Azure AD DS [skapar du en Azure AD DS-hanterad domän med Azure-portalen][tutorial-create].

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
