---
title: Azure AD Connect-molnetablering stöds topologier och scenarier
description: I det här avsnittet beskrivs förkraven och maskinvarukraven för molnetablering.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 386af46bbee623d37bc914d2ee9130c914c6c885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620876"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Azure AD Connect-molnetablering stöds topologier och scenarier
I den här artikeln beskrivs olika lokala och Azure Active Directory(Azure AD) topologier som använder Azure AD Connect-molnetablering. Den här artikeln innehåller endast konfigurationer och scenarier som stöds.

> [!IMPORTANT]
> Microsoft stöder inte ändring eller drift av Azure AD Connect-molnetablering utanför de konfigurationer eller åtgärder som dokumenteras formellt. Någon av dessa konfigurationer eller åtgärder kan resultera i ett inkonsekvent eller icke-stödt tillstånd för Azure AD Connect-molnetablering. Därför kan Microsoft inte tillhandahålla teknisk support för sådana distributioner.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Saker att komma ihåg om alla scenarier och topologier
Följande är en lista med information att tänka på när du väljer en lösning.

- Användare och grupper måste identifieras unikt i alla skogar
- Matchning mellan skogar sker inte med molnetablering
- En användare eller grupp får bara representeras en gång i alla skogar
- Källankaret för objekt väljs automatiskt.  Den använder ms-DS-ConsistencyGuid om sådan finns, annars används ObjectGUID.
- Du kan inte ändra attributet som används för källankare.

## <a name="single-forest-single-azure-ad-tenant"></a>Enkel skog, en enda Azure AD-klient
![Topologi för en enskild skog och en enda klient](media/plan-cloud-provisioning-topologies/single-forest.png)

Den enklaste topologin är en enda lokal skog, med en eller flera domäner och en enda Azure AD-klientorganisation.  Ett exempel på det här scenariot finns [i Självstudiekurs: En enskild skog med en enda Azure AD-klientorganisation](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>Multi-forest, single Azure AD-klient
![Topologi för en multi-skog och en enda klient](media/plan-cloud-provisioning-topologies/multi-forest.png)

En vanlig topologi är en multipel AD-skogar, med en eller flera domäner och en enda Azure AD-klientorganisation.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Befintlig skog med Azure AD Connect, ny skog med molnetablering
![Topologi för en enskild skog och en enda klient](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

Det här scenariot är topologi liknar scenariot med flera skogar, men det här innebär en befintlig Azure AD Connect-miljö och sedan tar in en ny skog med Azure AD Connect-molnetablering.  Ett exempel på det här scenariot finns [i Självstudiekurs: En befintlig skog med en enda Azure AD-klientorganisation](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>Lotsa Azure AD Connect-molnetablering i en befintlig hybrid-AD-skog
![Topologi för en enskild skog](media/plan-cloud-provisioning-topologies/migrate.png) och en enda klient Pilotscenariot innebär att det finns både Azure AD Connect och Azure AD Connect-molnetablering i samma skog och omfångar användare och grupper därefter. Ett objekt ska endast vara i omfattning i ett av verktygen. 

Ett exempel på det här scenariot finns [i Självstudiekurs: Pilot Azure AD Connect-molnetablering i en befintlig synkroniserad AD-skog](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)

