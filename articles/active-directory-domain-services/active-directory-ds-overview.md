---
title: Översikt över Azure Active Directory Domain Services | Microsoft Docs
description: Översikt över Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 0d47178f-773e-45f9-9ff4-9e8cffa4ffa2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: ergreenl
ms.openlocfilehash: c9833ac1dd6f5b046a89a1c2d9007e2cd5c9f813
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416712"
---
# <a name="azure-active-directory-ad-domain-services"></a>Azure Active Directory (AD) Domain Services
## <a name="overview"></a>Översikt
Azure Infrastructure Services kan du distribuera en lång rad databehandlingslösningar på ett flexibelt sätt. Med Azure Virtual Machines kan du distribuera nästan omedelbart och du betalar endast per minut. Med stöd för Windows, Linux, SQL Server, Oracle, IBM, SAP och BizTalk kan distribuera du valfri arbetsbelastning, valfritt språk på nästan vilket operativsystem. Dessa förmåner kan du migrera äldre program som distribuerats lokalt till Azure för att spara på driftskostnader.

En viktig aspekt av migrerar lokala program till Azure hanterar identitet behov av dessa program. Katalogbaserade appar förlitar sig på LDAP för Läs- eller skrivbehörighet till företagskatalogen eller förlitar sig på Windows-integrerad autentisering (Kerberos eller NTLM-autentisering) att autentisera användare. Line-of-business (LOB) program som körs på Windows Server distribueras vanligen på domänanslutna datorer, så att de kan hanteras på ett säkert sätt med hjälp av en Grupprincip. Dessa beroenden på företagsidentitet infrastrukturen måste matchas till ”lift and shift” på lokala program till molnet.

Administratörer oftare på något av följande lösningar för att uppfylla behoven identitet i sina program som distribueras i Azure:

* Distribuera en plats-till-plats VPN-anslutning mellan arbetsbelastningar som körs i Azure Infrastructure Services och företagskatalog lokala platser.
* Utöka infrastruktur för företagets AD-domän/skog genom att konfigurera replikeringsdomänkontrollanter med hjälp av Azure-datorer.
* Distribuera en fristående domän i Azure med hjälp av domänkontrollanter som distribueras som virtuella Azure-datorer.

Alla dessa metoder drabbas av höga kostnader och minimala administrationskostnader. Administratörer krävs för att distribuera domänkontrollanter med hjälp av virtuella datorer i Azure. Dessutom kan behöva de hantera, skydda, uppdatera, övervaka, säkerhetskopiera och felsöka dessa virtuella datorer. Förlita dig på VPN-anslutningar till en lokal katalog gör att arbetsbelastningar som distribuerats i Azure för att vara sårbara för tillfälliga nätverket problem och fel. Dessa nätverksavbrott leda i sin tur till lägre drifttid och lägre tillförlitlighet för dessa program.

Vi har utformat Azure AD Domain Services för att tillhandahålla ett enklare alternativ.

### <a name="watch-an-introductory-video"></a>En introduktionsvideo

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="introducing-azure-ad-domain-services"></a>Introduktion till Azure AD Domain Services

Azure AD Domain Services tillhandahåller hanterade domäntjänster, till exempel domänanslutning, grupp princip, LDAP, Kerberos/NTLM-autentisering som är helt kompatibla med Windows Server Active Directory. Du kan använda tjänsterna domän utan att behöva distribuera, hantera och korrigera domänkontrollanter i molnet. Azure AD Domain Services kan integreras med din befintliga Azure AD-klient, vilket gör det möjligt för användare att logga in med sina företagsuppgifter. Du kan dessutom använda befintliga grupper och konton för att säkra åtkomst till resurser, vilket säkerställer en jämnare 'lift-and-shift ”av lokala resurser till Azure Infrastructure Services.

Azure AD Domain Services-funktioner fungerar sömlöst oavsett om din Azure AD-klient är molnbaserad eller synkroniserade med din lokala Active Directory.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Azure AD Domain Services för endast molnbaserad organisationer

En molnbaserad Azure AD-klient (kallas ofta ”hanterade klienter”) inte har någon lokal identitet fotavtryck. Med andra ord finns användarkonton, lösenord och gruppmedlemskap alla inbyggd i molnet – det vill säga skapas och hanteras i Azure AD. Överväg att under en kort stund att Contoso är en molnbaserad Azure AD-klient. I följande bild visas Contosos administratören har konfigurerat ett virtuellt nätverk i Azure Infrastructure Services. Program och server-arbetsbelastningar har distribuerats i det här virtuella nätverket i Azure virtual machines. Eftersom Contoso är en endast molnbaserad klient, alla användaridentiteter, sina autentiseringsuppgifter och gruppmedlemskap skapas och hanteras i Azure AD.

![Azure AD Domain Services översikt](./media/active-directory-domain-services-overview/aadds-overview.png)

Contosos IT-administratören kan aktivera Azure AD Domain Services för sina Azure AD-klient och väljer att tillgängliggöra domäntjänster i det här virtuella nätverket. Därefter Azure AD Domain Services tillhandahåller en hanterad domän och gör dem tillgängliga i det virtuella nätverket. Alla användarkonton, gruppmedlemskap och autentiseringsuppgifter för användare i Contosos Azure AD-klient är också tillgängliga i den här nya domänen. Den här funktionen gör det möjligt för användare i organisationen att logga in på domänen med sina företagsuppgifter – till exempel när du ansluter via en fjärranslutning till domänanslutna datorer via fjärrskrivbord. Administratörer kan etablera åtkomst till resurser i domänen med befintliga gruppmedlemskap. Program som distribueras på virtuella datorer i det virtuella nätverket kan använda funktioner som domänanslutning, LDAP Läs, LDAP-bindning, NTLM och Kerberos-autentisering och Grupprincip.

Några designmönster för den hanterade domänen som tillhandahålls av Azure AD Domain Services är följande:

* Contosos IT-administratören inte behöver hantera, korrigera och övervaka den här domänen eller domänkontrollanter för den här hanterade domänen.
* Det finns inget behov att hantera AD-replikering för den här domänen. Användarkonton, gruppmedlemskap och autentiseringsuppgifter från Contosos Azure AD-klient blir automatiskt tillgängliga i den här hanterade domänen.
* Eftersom domänen hanteras av Azure AD Domain Services, Contoso IT-administratören har inte domänadministratör eller företagsadministratör behörighet på den här domänen.

### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Azure AD Domain Services för hybridorganisationer
Organisationer med en hybrid IT-infrastruktur använder en blandning av molnresurser och lokala resurser. Sådana organisationer synkronisera identitetsinformation från sina lokala katalog till deras Azure AD-klient. Som hybridorganisationer se ut för att migrera mer av sina lokala program till molnet, särskilt äldre katalogbaserade appar Azure AD Domain Services kan vara användbar för dem.

Litware Corporation har distribuerat [Azure AD Connect](../active-directory/hybrid/whatis-hybrid-identity.md), för att synkronisera identitetsinformation från sina lokala katalog till deras Azure AD-klient. ID-information som synkroniseras innehåller användarkonton, deras hashvärden för autentiseringsuppgifter för autentisering (lösenordshashsynkronisering) och gruppmedlemskap.

> [!NOTE]
> **Synkronisering av lösenordshash är obligatoriskt för hybridorganisationer att använda Azure AD Domain Services**. Det här kravet är eftersom användarnas autentiseringsuppgifter behövs i den hanterade domänen tillhandahålls av Azure AD Domain Services för att autentisera dessa användare via NTLM eller Kerberos autentiseringsmetoder.
>
>

![Azure AD Domain Services för Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

Föregående bild visar hur organisationer med en hybrid IT-infrastruktur, till exempel Litware Corporation, kan använda Azure AD Domain Services. Litwares program och server-arbetsbelastningar som kräver domäntjänster distribueras i ett virtuellt nätverk i Azure Infrastructure Services. Litware's IT-administratören kan aktivera Azure AD Domain Services för sina Azure AD-klient och välja att göra en hanterad domän som är tillgängliga i det här virtuella nätverket. Eftersom Litware är en organisation med en hybrid IT-infrastruktur, synkroniseras användarkonton, grupper och autentiseringsuppgifter till deras Azure AD-klient från sina lokala katalog. Den här funktionen gör det möjligt för användare att logga in på domänen med sina företagsuppgifter – exempelvis när fjärransluta till datorer anslutna till domänen via fjärrskrivbord. Administratörer kan etablera åtkomst till resurser i domänen med befintliga gruppmedlemskap. Program som distribueras på virtuella datorer i det virtuella nätverket kan använda funktioner som domänanslutning, LDAP Läs, LDAP-bindning, NTLM och Kerberos-autentisering och Grupprincip.

Några designmönster för den hanterade domänen som tillhandahålls av Azure AD Domain Services är följande:

* Den hanterade domänen är en fristående domän. Det är inte ett tillägg till Litwares lokal domän.
* Litware's IT-administratören inte behöver hantera, korrigera eller övervaka domänkontrollanter för den här hanterade domänen.
* Det finns inget behov att hantera AD-replikering till den här domänen. Användarkonton, gruppmedlemskap och autentiseringsuppgifter från en lokal katalog Litwares synkroniseras till Azure AD via Azure AD Connect. Dessa användarkonton, gruppmedlemskap och autentiseringsuppgifter blir automatiskt tillgängliga i den hanterade domänen.
* Eftersom domänen hanteras av Azure AD Domain Services, Litware's IT-administratören har inte domänadministratör eller företagsadministratör behörighet på den här domänen.

## <a name="benefits"></a>Fördelar
Med Azure AD Domain Services, kan du utnyttja följande fördelar:

* **Enkel** – du kan uppfylla identitet behoven hos virtuella datorer distribueras på Azure-infrastrukturtjänster med ett par enkla klick. Du behöver inte distribuera och hantera infrastruktur för Identitetshantering i Azure eller konfiguration ansluten till din lokala identitetsinfrastruktur.
* **Integrerad** – Azure AD Domain Services är djupt integrerad med Azure AD-klienten. Du kan nu använda Azure AD som en integrerad molnbaserad enterprise-katalog som caters i förhållande till både traditionella katalogbaserade appar och moderna program.
* **Kompatibel** – Azure AD Domain Services bygger på beprövade enterprise grade infrastruktur för Windows Server Active Directory. Ditt program kan därför förlitar sig på en högre grad av kompatibilitet med Windows Server Active Directory-funktioner. Inte alla funktioner som är tillgängliga i Windows Server AD är tillgängliga i Azure AD Domain Services. Dock är tillgängliga funktioner kompatibla med de motsvarande Windows Server AD-funktioner som du förlita dig på i din lokala infrastruktur. LDAP, Kerberos, NTLM, Grupprincip och domän join funktionerna utgör en mogen erbjudande som har testats och förfinat över olika versioner av Windows Server.
* **Kostnadseffektiv** – med Azure AD Domain Services, kan du undvika infrastruktur och belastningen som är associerad med att hantera identitetsinfrastrukturen för att stödja traditionella katalogbaserade appar. Du kan flytta dessa program till Azure Infrastructure Services och dra nytta av större besparingar med driftskostnader.


## <a name="next-steps"></a>Nästa steg
### <a name="learn-more-about-azure-ad-domain-services"></a>Läs mer om Azure AD Domain Services
* [Funktioner](active-directory-ds-features.md)
* [Distributionsscenarier](active-directory-ds-scenarios.md)
* [Ta reda på om Azure AD Domain Services passar dina användningsfall](active-directory-ds-comparison.md)
* [Förstå hur Azure AD Domain Services synkroniseras med din Azure AD-katalog](active-directory-ds-synchronization.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Kom igång med Azure AD Domain Services
* [Aktivera Azure AD Domain Services med Azure portal](active-directory-ds-getting-started.md)
