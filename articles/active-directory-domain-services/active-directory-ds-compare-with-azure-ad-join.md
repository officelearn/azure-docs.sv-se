---
title: "Jämföra Azure AD-anslutning och Azure Active Directory Domain Services | Microsoft Docs"
description: "Välja mellan Azure AD-anslutning och Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 31a71d36-58c1-4839-b958-80da0c6a77eb
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 8f34688c53dc909b0ec97df34bbfc7a24209a314
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="choose-between-azure-active-directory-join-and-azure-active-directory-domain-services"></a>Välj mellan Azure Active Directory-koppling och Azure Active Directory Domain Services
Den här artikeln beskrivs skillnaderna mellan Azure Active Directory (AD)-anslutning och Azure AD Domain Services och hjälper till att du väljer, baserat på dina användningsfall.

## <a name="azure-ad-registered-and-azure-ad-joined-devices"></a>Registrerad i Azure AD och Azure AD-anslutna enheter
Azure AD kan du hantera identiteten för enheter som används av din organisation och kontrollera åtkomsten till företagets resurser från dessa enheter. Användarna kan registrera sina personliga enheter (bring your own) med Azure AD, som innehåller enheten med en identitet. Därefter kan Azure AD autentisera enheten när en användare loggar in på Azure AD och använder enheten för att komma åt skyddade resurser. Dessutom kan du hantera enheten med hantering av mobilenheter (MDM)-program som Microsoft Intune. Den här funktionen kan du begränsa åtkomsten till känsliga resurser från hanterade och policy-kompatibla enheter.

Du kan också ansluta till organisation företagsägda enheter till Azure AD. Den här mekanismen har samma fördelar med att registrera en personlig enhet med Azure AD. Dessutom kan användare logga in på enheten med sina företagsuppgifter. Azure AD-anslutna enheter ger följande fördelar:
* Enkel inloggning (SSO) till program som skyddas av Azure AD
* Enterprise princip-kompatibel roaming av användarinställningar över enheter.
* Åtkomst till Windows Store för företag med företagets autentiseringsuppgifter.
* Windows Hello för företag
* Begränsad åtkomst till appar och resurser från enheter som är kompatibla med företagets policy.

| **Typ av enhet** | **Enhetsplattformar** | **Mekanism** |
|:---| --- | --- |
| Personliga enheter | Windows 10-, iOS, Android, Mac OS x | Azure AD som registrerade |
| Organisation för företagsägda enheter som inte är anslutna till lokala AD | Windows 10 | Azure AD ansluten |
| Organisation för företagsägda enheter som är anslutna till en lokal AD | Windows 10 | Azure AD-hybridlösning ansluten |

På en Azure AD domänanslutna eller registrerade enheter, användarautentisering händer med moderna OAuth/OpenID Connect baserat protokoll. Dessa protokoll fungerar via internet och är perfekt för mobila scenarier där användarna kommer åt företagsresurser från valfri plats.


## <a name="domain-join-to-azure-ad-domain-services-managed-domains"></a>Anslut till domän till Azure AD Domain Services hanterade domäner
Azure AD Domain Services innehåller en hanterad AD-domän i Azure-nätverk. Du kan ansluta datorer till hanterade domänen med hjälp av traditionella domänanslutning mekanismer. Windows-klienten (Windows 7, Windows 10) och Windows Server-datorer kan anslutas till den hanterade domänen. Du kan dessutom också koppla Linux och Mac OS x-datorer i den hanterade domänen. När du ansluter en dator till en AD-domän kan användare logga in på den datorn med sina företagsuppgifter. Dessa datorer kan hanteras med hjälp av Grupprincip, alltså tillämpa kompatibilitet med organisationens principer.

På en domänansluten dator sker autentisering av användare med hjälp av NTLM eller Kerberos-autentiseringsprotokoll. Domänanslutna datorer måste fri domänkontrollanterna i den hanterade domänen för användarautentisering ska fungera. Därför domänanslutna datorer måste vara i samma virtuella nätverk som den hanterade domänen. Alternativt kan domänanslutna datorer måste vara anslutna till den hanterade domänen via ett peered virtuellt nätverk eller en plats-till-plats VPN/ExpressRoute-anslutning. Den här mekanismen är därför inte ett bra val för enheter som mobila eller ansluta till resurser från utanför företagsnätverket.

> [!NOTE]
> Det är tekniskt möjligt att ansluta till en lokal klient-arbetsstation till den hanterade domänen via en plats-till-plats VPN eller ExpressRoute-anslutning. Men för slutanvändarenheter som vi rekommenderar starkt du använder antingen registrera enheten med Azure AD (personliga enheter) eller ansluta enheten till Azure AD (enheter). Den här mekanismen fungerar bättre via internet och slutanvändarna kan arbeta överallt. Azure AD Domain Services är perfekt för Windows eller Linux-Server virtuella datorer i ditt virtuella Azure-nätverk, som är distribuerad på dina program.


## <a name="summary---key-differences"></a>Sammanfattning - viktiga skillnader
| **Aspekt** | **Azure AD Join** | **Azure AD Domain Services** |
|:---| --- | --- |
| Enheten som styrs av | Azure AD | Azure AD Domain Services-hanterad domän |
| Representation i katalogen | Enhetsobjekt i Azure AD-katalog. | Datorobjekt i den hanterade AAD-DS-domänen. |
| Autentisering | OAuth/OpenID Connect baserat protokoll | Kerberos, NTLM-protokoll |
| Hantering | Hantering av mobilenheter (MDM)-programvara som Intune | Grupprincip |
| Nätverk | Fungerar över internet | Kräver att datorer på samma virtuella nätverk som den hanterade domänen.|
| Bra för... | Slutanvändarens mobila eller stationära enheter | Servern virtuella datorer i Azure |


## <a name="next-steps"></a>Nästa steg
### <a name="learn-more-about-azure-ad-domain-services"></a>Lär dig mer om Azure AD Domain Services
* [Översikt över Azure AD Domain Services](active-directory-ds-overview.md)
* [Funktioner](active-directory-ds-features.md)
* [Distributionsscenarier](active-directory-ds-scenarios.md)
* [Ta reda på om Azure AD Domain Services passar dina användningsfall](active-directory-ds-comparison.md)
* [Förstå hur Azure AD Domain Services synkroniseras med Azure AD-katalogen](active-directory-ds-synchronization.md)

### <a name="learn-more-about-azure-ad-join"></a>Lär dig mer om Azure AD Join
* [Introduktion till hantering av enheter i Azure Active Directory](../active-directory/device-management-introduction.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Kom igång med Azure AD Domain Services
* [Aktivera Azure AD Domain Services med Azure-portalen](active-directory-ds-getting-started.md)
