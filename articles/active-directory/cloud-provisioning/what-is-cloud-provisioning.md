---
title: Vad är Azure AD Connect Cloud etableringen. | Microsoft Docs
description: Beskriver Azure AD Connect Cloud-etablering.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e88f6f344c77109ee01cd6b0332138518f70270d
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793788"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>Vad är Azure AD Connect Cloud revisioner?
Azure AD Connect Cloud etableringen är en ny Microsoft-Agent som är utformad för att möta och uppnå dina hybrid identitets mål för synkronisering av användare, grupper och kontakter till Azure AD.  Den kan användas tillsammans med Azure AD Connect Sync och ger följande fördelar:
    
- Stöd för synkronisering till en Azure AD-klient från en frånkopplad Active Directory skogs miljö: de vanliga scenarierna omfattar Fusion & förvärv, där det förvärvade företagets AD-skogar är isolerade från moder bolagets AD skogar och företag som har historiskt haft flera AD-skogar tidigare.
- Förenklad installation med låg vikts etablerings agenter: agenterna fungerar som en brygga från AD till Azure AD, med all synkroniserad konfiguration som hanteras i molnet. 
- Flera etablerings agenter kan användas för att förenkla distributioner med hög tillgänglighet, särskilt viktiga för organisationer som förlitar sig på lösen ords-hash-synkronisering från AD till Azure AD.


![Vad är Azure AD Connect?](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Hur skiljer Azure AD Connect moln etablering från Azure AD Connect Sync?
Med Azure AD Connect Cloud etableringen dirigeras etableringen från AD till Azure AD till Microsoft Online Services. En organisation behöver bara distribuera, i sin lokala och IaaS miljö, en Lightweight-agent som fungerar som en brygga mellan Azure AD och AD. Etablerings konfigurationen lagras i Azure AD och hanteras som en del av tjänsten.

Följande tabell innehåller en jämförelse mellan Azure AD Connect och Azure AD Connect moln etablering:

| Funktion | Azure Active Directory Connect Synch| Azure Active Directory Connect moln etablering |
|:--- |:---:|:---:|
|Ansluta till en enda lokal AD-skog|● |● |
| Ansluta till flera lokala AD-skogar |● |● |
| Anslut till flera frånkopplade lokala AD-skogar | |● |
| Installations modell för Lightweight agent | |● |
| Flera aktiva agenter för hög tillgänglighet | |● |
| Anslut till LDAP-kataloger|●| | 
| Stöd för användar objekt |● |● |
| Stöd för grupp objekt |● |● |
| Stöd för kontakt objekt |● |● |
| Stöd för enhets objekt |● | |
| Tillåt grundläggande anpassning av Attribute-flöden |● |● |
| Synkronisera kunddefinierade AD-attribut (katalog tillägg) |● | |
| Stöd för synkronisering av lösen ords-hash |●|●|
| Stöd för direktautentisering |●||
| Stöd för Federation |●|●|
| Smidig enkel inloggning|● |●|
| Stöder installation på en domänkontrollant |● |● |
| Stöd för Windows Server 2012 och Windows Server 2012 R2 |● |● |
| Filtrera på domäner/organisationsenheter/grupper |● |● |
| Filtrera baserat på objektens attributvärden |● | |
| Tillåt att en minimal uppsättning attribut synkroniseras (MinSync) |● |● |
| Tillåt borttagning av attribut som flödar från AD till Azure AD |● |● |
| Tillåt avancerad anpassning av attributflöden |● | |
| Stöd för tillbakaskrivning (lösen ord, enheter, grupper) |● | |

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud revisioner?](what-is-cloud-provisioning.md)
