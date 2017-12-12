---
title: "Översikt över Azure Active Directory Domain Services | Microsoft Docs"
description: "Översikt över Azure Active Directory Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 0d47178f-773e-45f9-9ff4-9e8cffa4ffa2
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 23506804ee41688bfebeb0674d99154ff1c77e61
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-ad-domain-services"></a>Azure Active Directory (AD) Domain Services
## <a name="overview"></a>Översikt
Azure Infrastructure Services kan du distribuera en mängd olika databehandling lösningar i ett flexibel sätt. Med Azure Virtual Machines, kan du distribuera nästan omedelbart och du betalar endast per minut. Använda stöd för Windows, Linux, SQL Server, Oracle, IBM, SAP och BizTalk, kan du distribuera alla arbetsbelastningar, alla språk på nästan alla operativsystem. Dessa fördelar kan du migrera äldre program som distribueras på lokalt till Azure för att spara på driftskostnader.

En viktig aspekt av migrera lokala program till Azure hanterar identitet måste dessa program. Directory-medvetna program kan förlitar sig på LDAP för Läs- eller skrivbehörighet till företagets katalog eller förlitar sig på Windows-integrerad autentisering (Kerberos eller NTLM-autentisering) att autentisera användare. Line-of-business (LOB)-program som körs på Windows Server distribueras vanligtvis på domänanslutna datorer så att de kan hanteras på ett säkert sätt med hjälp av en Grupprincip. Dessa beroenden på företagets identitetsinfrastrukturen måste matchas till 'lift och SKIFT' lokala program till molnet.

Administratörer oftare på något av följande lösningar för att uppfylla behoven identitet i sina program som distribueras i Azure:

* Distribuera en plats-till-plats VPN-anslutning mellan arbetsbelastningar som körs i Azure Infrastructure Services och i företagets katalog lokalt.
* Utöka den företagets infrastrukturen för AD-skog och domäner genom att ställa in domänkontrollanter för repliken med hjälp av Azure virtuella datorer.
* Distribuera en fristående domän i Azure med hjälp av domänkontrollanter som distribueras som virtuella Azure-datorer.

Dessa metoder drabbas av hög kostnad och administrativa kostnader. Administratörer behöver distribuera domänkontrollanter med hjälp av virtuella datorer i Azure. Dessutom kan behöver de för att hantera, skydda, korrigering, övervaka, säkerhetskopiering och felsöka dessa virtuella datorer. Beroendet av VPN-anslutningar till den lokala katalogen gör arbetsbelastningar som distribuerats i Azure för att vara utsatt för tillfälliga nätverket problem eller avbrott. Dessa nätverksavbrott leda i sin tur till lägre drifttid och minskade tillförlitlighet för dessa program.

Vi har utformats Azure AD Domain Services för att tillhandahålla ett enklare alternativ.

### <a name="watch-an-introductory-video"></a>Titta på en inledande video
<iframe width="560" height="315" src="https://www.youtube.com/embed/T1Nd9APNceQ" frameborder="0" allowfullscreen></iframe>


## <a name="introducing-azure-ad-domain-services"></a>Introduktion till Azure AD Domain Services
Azure AD Domain Services tillhandahåller hanterad domäntjänster, till exempel domänanslutning, gruppen princip, LDAP, Kerberos/NTLM-autentisering som är helt kompatibel med Windows Server Active Directory. Du kan använda tjänsterna domän utan att behöva att distribuera, hantera och korrigering av domänkontrollanter i molnet. Azure AD Domain Services kan integreras med befintliga Azure AD-klienten, vilket gör det möjligt för användarna att logga in med sina företagsuppgifter. Du kan dessutom använda befintliga grupper och konton för att säkra åtkomsten till resurser, vilket säkerställer en jämnare 'lift-och-SKIFT-lokala resurser till Azure Infrastructure Services.

Azure AD Domain Services-funktionen fungerar sömlöst oavsett om Azure AD-klienten är endast molnbaserad eller synkroniserade med din lokala Active Directory.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Azure AD Domain Services för endast molnbaserad organisationer
En molnbaserad Azure AD-klient (kallas ofta-hanterade klienter') har inte någon lokal identitet storleken. Med andra ord finns användarkonton, lösenord och gruppmedlemskap alla inbyggd i molnet – det vill säga skapas och hanteras i Azure AD. Överväg ett ögonblick att Contoso är en molnbaserad Azure AD-klient. I följande bild visas Contosos administratören har konfigurerat ett virtuellt nätverk i Azure Infrastructure Services. Program och serverarbetsbelastningar distribueras i det här virtuella nätverket i virtuella Azure-datorer. Eftersom Contoso är en molnbaserad klient, alla användaridentiteter, sina autentiseringsuppgifter och gruppmedlemskap skapas och hanteras i Azure AD.

![Azure AD Domain Services-översikt](./media/active-directory-domain-services-overview/aadds-overview.png)

Contosos IT-administratören kan aktivera Azure AD Domain Services för sina Azure AD-klient och välja att göra domäntjänster tillgängligt i det här virtuella nätverket. Därefter, Azure AD Domain Services tillhandahåller en hanterad domän och gör den tillgänglig i det virtuella nätverket. Alla användarkonton, gruppmedlemskap och autentiseringsuppgifter som är tillgängliga i Contosos Azure AD-klient är också tillgängliga i den här nya domänen. Den här funktionen gör det möjligt för användare i organisationen att logga in på domänen med sina företagsuppgifter – exempelvis när fjärransluta till domänanslutna datorer via fjärrskrivbord. Administratörer kan etablera åtkomst till resurser i domänen med befintliga gruppmedlemskap. Program som distribueras i virtuella datorer på det virtuella nätverket kan använda funktioner som domänanslutning, LDAP-Läs-, LDAP-bindning, NTLM och Kerberos-autentisering och en Grupprincip.

Några av de viktigaste egenskaperna i för den hanterade domänen som etableras av Azure AD DS är följande:

* Contosos IT-administratören inte behöver hantera, korrigera eller övervaka den här domänen eller domänkontrollanter för den här hanterade domänen.
* Det finns inget behov av att hantera AD-replikering för den här domänen. Användarkonton, gruppmedlemskap och autentiseringsuppgifter från Contosos Azure AD-klient är automatiskt tillgängliga i den här hanterade domänen.
* Eftersom domänen hanteras av Azure AD Domain Services, Contoso IT-administratören har inte behörighet för domänadministratör eller Företagsadministratörer på den här domänen.

### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Azure AD Domain Services för hybrid organisationer
Organisationer med en hybrid-IT-infrastruktur använda en blandning av molnresurser och lokala resurser. Dessa organisationer synkronisera identitetsinformation från sina lokala katalog till sina Azure AD-klient. Som hybrid organisationer se ut för att migrera mer av sina lokala program till molnet, speciellt äldre katalog-medvetna program, Azure AD Domain Services kan vara användbara för dem.

Litware Corporation har distribuerat [Azure AD Connect](../active-directory/active-directory-aadconnect.md), för att synkronisera identitetsinformation från sina lokala katalog till sina Azure AD-klient. ID-information som synkroniseras innehåller användarkonton, deras hashvärdena för autentiseringsuppgifterna för autentisering (synkronisering av lösenord) och gruppmedlemskap.

> [!NOTE]
> **Lösenordssynkronisering är obligatoriskt för hybrid organisationer att använda Azure AD Domain Services**. Det här kravet är eftersom användarnas autentiseringsuppgifter behövs i den hanterade domänen som tillhandahålls av Azure AD Domain Services för att autentisera användarna via metoder för NTLM eller Kerberos-autentisering.
>
>

![Azure AD Domain Services för Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

Föregående bild visar hur organisationer med en hybrid-IT-infrastruktur, till exempel Litware Corporation kan använda Azure AD Domain Services. Litwares program och serverarbetsbelastningar som kräver domäntjänster distribueras i ett virtuellt nätverk i Azure Infrastructure Services. Litware's IT-administratören kan aktivera Azure AD Domain Services för sina Azure AD-klient och välja att göra en hanterad domän som är tillgängliga i det här virtuella nätverket. Eftersom Litware är en organisation med en hybrid-IT-infrastruktur, synkroniseras användarkonton, grupper och autentiseringsuppgifter till sina Azure AD-klient från sina lokala katalog. Den här funktionen gör det möjligt för användare att logga in på domänen med sina företagsuppgifter – exempelvis när fjärransluta till datorer anslutna till domänen via fjärrskrivbord. Administratörer kan etablera åtkomst till resurser i domänen med befintliga gruppmedlemskap. Program som distribueras i virtuella datorer på det virtuella nätverket kan använda funktioner som domänanslutning, LDAP-Läs-, LDAP-bindning, NTLM och Kerberos-autentisering och en Grupprincip.

Några av de viktigaste egenskaperna i för den hanterade domänen som etableras av Azure AD DS är följande:

* Den hanterade domänen är en fristående domän. Det är inte en förlängning av Litwares lokal domän.
* Litware's IT-administratören inte behöver hantera, korrigering, eller övervaka domänkontrollanter för den här hanterade domänen.
* Det finns inget behov av att hantera AD-replikering till den här domänen. Användarkonton, gruppmedlemskap och autentiseringsuppgifter från Litwares lokal katalog synkroniseras till Azure AD via Azure AD Connect. Dessa användarkonton, gruppmedlemskap och autentiseringsuppgifter är automatiskt tillgängliga i den Hantera domänen.
* Eftersom domänen hanteras av Azure AD Domain Services, Litware's IT-administratören har inte behörighet för domänadministratör eller Företagsadministratörer på den här domänen.

## <a name="benefits"></a>Fördelar
Med Azure AD Domain Services, kan du få följande fördelar:

* **Enkel** – du kan uppfylla behoven identitet för virtuella datorer distribueras på Azure Infrastructure services med ett par enkla klick. Du behöver inte distribuera och hantera identitetsinfrastruktur i Azure eller installationen ansluten till din lokala identitetsinfrastruktur.
* **Integrerad** – Azure AD Domain Services är djupt integrerad med Azure AD-klienten. Du kan nu använda Azure AD som en integrerad, molnbaserad enterprise-katalog som caters enligt behov av både moderna applikationer och traditionella directory-medvetna program.
* **Kompatibel** – Azure AD DS bygger på beprövade företagsinfrastruktur klass av Windows Server Active Directory. Dina program kan därför inte förlita sig på en högre grad av kompatibilitet med Windows Server Active Directory-funktioner. Det finns för närvarande inte alla funktioner som är tillgängliga i Windows Server AD i Azure AD Domain Services. Dock är tillgängliga funktioner kompatibla med motsvarande Windows Server AD-funktioner som du förlita dig på i din lokala infrastruktur. LDAP, Kerberos, NTLM, Grupprincip och domän koppling funktionerna utgör en mogen erbjudande som har testats och förfinad över olika versioner av Windows Server.
* **Kostnadseffektiv** – du kan undvika belastningen för infrastruktur och hanteringsfunktioner som är associerad med att hantera identitetsinfrastrukturen för att stödja traditionell directory-medvetna program med Azure AD Domain Services. Du kan flytta dessa program till Azure Infrastructure Services och dra nytta av större besparingar på driftskostnader.


## <a name="next-steps"></a>Nästa steg
### <a name="learn-more-about-azure-ad-domain-services"></a>Lär dig mer om Azure AD Domain Services
* [Funktioner](active-directory-ds-features.md)
* [Distributionsscenarier](active-directory-ds-scenarios.md)
* [Ta reda på om Azure AD Domain Services passar dina användningsfall](active-directory-ds-comparison.md)
* [Förstå hur Azure AD Domain Services synkroniseras med Azure AD-katalogen](active-directory-ds-synchronization.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Kom igång med Azure AD Domain Services
* [Aktivera Azure AD Domain Services med Azure-portalen](active-directory-ds-getting-started.md)
