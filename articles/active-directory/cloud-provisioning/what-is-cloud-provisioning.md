---
title: Vad är Azure AD Connect molnetablering. | Microsoft Docs
description: Beskriver Azure AD Connect-molnetablering.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a86d34fca9a88b0df601533a0f3de1cc97ad1a2f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80050590"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>Vad är Azure AD Connect-molnetablering?
Azure AD Connect-molnetablering är en ny Microsoft-agent som utformats för att uppfylla och uppnå dina hybrididentitetsmål för synkronisering av användare, grupper och kontakter till Azure AD.  Den kan användas tillsammans med Azure AD Connect-synkronisering och ger följande fördelar:
    
- Stöd för synkronisering till en Azure AD-klient från en frånkopplad Active Directory-skogsmiljö med flera skogar: De vanliga scenarierna omfattar sammanslagning & förvärv, där det förvärvade företagets AD-skogar är isolerade från moderbolagets AD-skogar och företag som historiskt har haft flera AD-skogar.
- Förenklad installation med lågviktsetableringsagenter: Agenterna fungerar som en brygga från AD till Azure AD, med all synkroniseringskonfiguration som hanteras i molnet. 
- Flera etableringsagenter kan användas för att förenkla distributioner med hög tillgänglighet, särskilt viktigt för organisationer som förlitar sig på synkronisering av lösenord hash från AD till Azure AD.


![Vad är Azure AD Connect?](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Hur skiljer sig Azure AD Connect-molnetablering från Azure AD Connect-synkronisering?
Med Azure AD Connect-molnetablering är etablering från AD till Azure AD orkestrerad i Microsoft Online Services. En organisation behöver bara distribuera en lättviktsagent som fungerar som en brygga mellan Azure AD och AD i sin lokala och IaaS-värdbaserade miljö. Etableringskonfigurationen lagras i Azure AD och hanteras som en del av tjänsten.

Följande tabell innehåller en jämförelse mellan Azure AD Connect och Azure AD Connect-molnetablering:

| Funktion | Azure Active Directory Connect-synkronisering| Azure Active Directory Connect molnetablering |
|:--- |:---:|:---:|
|Ansluta till en enda lokal AD-skog|● |● |
| Ansluta till flera lokala AD-skogar |● |● |
| Ansluta till flera frånkopplade lokala AD-skogar | |● |
| Installationsmodell för lätta agenter | |● |
| Flera aktiva agenter för hög tillgänglighet | |● |
| Ansluta till LDAP-kataloger|●| | 
| Stöd för användarobjekt |● |● |
| Stöd för gruppobjekt |● |● |
| Stöd för kontaktobjekt |● |● |
| Stöd för enhetsobjekt |● | |
| Tillåt grundläggande anpassning för attributflöden |● |● |
| Sychronize Exchange online attribut |● |● |
| Synkronisera tilläggsattribut 1-15 |● |● |
| Synkronisera kunddefinierade AD-attribut (katalogtillägg) |● | |
| Stöd för synkronisering av lösenord hash |●|●|
| Stöd för direktautentisering |●||
| Stöd för federation |●|●|
| Smidig enkel inloggning|● |●|
| Stöder installation på en domänkontrollant |● |● |
| Stöd för Windows Server 2012 och Windows Server 2012 R2 |● |● |
| Filtrera på domäner/OUs/grupper |● |● |
| Filtrera på objektens attributvärden |● | |
| Tillåt att en minimal uppsättning attribut synkroniseras (MinSync) |● |● |
| Tillåt borttagning av attribut som flödar från AD till Azure AD |● |● |
| Tillåt avancerad anpassning av attributflöden |● | |
| Stöd för tillbakaskrivning (lösenord, enheter, grupper) |● | |
| Support för Azure AD Domain Services|● | |
| [Tillbakaskrivning av hybrid- utbyte](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Stöd för mer än 50 000 objekt per AD-domän |● | |

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Installera molnetablering](how-to-install.md)
