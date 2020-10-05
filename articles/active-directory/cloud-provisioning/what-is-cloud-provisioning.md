---
title: Vad är Azure AD Connect Cloud etableringen. | Microsoft Docs
description: Beskriver Azure AD Connect Cloud-etablering.
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
ms.openlocfilehash: 7a5a69bedb45f8037f3a97f5ebf4e2128930330a
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91728360"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>Vad är Azure AD Connect-molnetablering?
Azure AD Connect Cloud etableringen är en ny Microsoft-Agent som är utformad för att möta och uppnå dina hybrid identitets mål för synkronisering av användare, grupper och kontakter till Azure AD.  Den kan användas tillsammans med Azure AD Connect Sync och ger följande fördelar:
    
- Stöd för synkronisering till en Azure AD-klient från en frånkopplad Active Directory skogs miljö: de vanliga scenarierna omfattar fusions & förvärv, där det förvärvade företagets AD-skogar är isolerade från moder bolagets AD-skogar och företag som har historiskt haft flera AD-skogar.
- Förenklad installation med låg vikts etablerings agenter: agenterna fungerar som en brygga från AD till Azure AD, med all synkroniserad konfiguration som hanteras i molnet. 
- Flera etablerings agenter kan användas för att förenkla distributioner med hög tillgänglighet, särskilt viktiga för organisationer som förlitar sig på lösen ords-hash-synkronisering från AD till Azure AD.


![Vad är Azure AD Connect?](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Hur skiljer Azure AD Connect moln etablering från Azure AD Connect Sync?
Med Azure AD Connect Cloud etableringen dirigeras etableringen från AD till Azure AD till Microsoft Online Services. En organisation behöver bara distribuera, i sin lokala och IaaS miljö, en Lightweight-agent som fungerar som en brygga mellan Azure AD och AD. Etablerings konfigurationen lagras i Azure AD och hanteras som en del av tjänsten.

## <a name="azure-ad-connect-cloud-provisioning-video"></a>Video om Azure AD Connect Cloud Provisioning
Följande korta video ger en utmärkt overveiw av Azure AD Connect Cloud-etablering:

> [!VIDEO https://youtube/embed/mOT3ID02_YQ]


## <a name="comparison-between-azure-ad-connect-and-cloud-provisioning"></a>Jämförelse mellan Azure AD Connect-och moln etablering

Följande tabell innehåller en jämförelse mellan Azure AD Connect och Azure AD Connect moln etablering:

| Funktion | Azure Active Directory Connect synkronisering| Azure Active Directory Connect moln etablering |
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
| Sychronize Exchange Online-attribut |● |● |
| Synkronisera tilläggets attribut 1-15 |● |● |
| Synkronisera kunddefinierade AD-attribut (katalog tillägg) |● | |
| Stöd för synkronisering av lösen ords-hash |●|●|
| Stöd för direktautentisering |●||
| Stöd för Federation |●|●|
| Smidig enkel inloggning|● |●|
| Stöder installation på en domänkontrollant |● |● |
| Stöd för Windows Server 2012 och Windows Server 2012 R2 |● |● |
| Filtrera på domäner/organisationsenheter/grupper |● |● |
| Filtrera utifrån objekts attributvärden |● | |
| Tillåt att en minimal uppsättning attribut synkroniseras (MinSync) |● |● |
| Tillåt borttagning av attribut som flödar från AD till Azure AD |● |● |
| Tillåt avancerad anpassning av attributflöden |● | |
| Stöd för tillbakaskrivning (lösen ord, enheter, grupper) |● | |
| Azure AD Domain Services support|● | |
| [Exchange hybrid tillbakaskrivning](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Stöd för fler än 50 000 objekt per AD-domän |● | |

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Installera moln etablering](how-to-install.md)
