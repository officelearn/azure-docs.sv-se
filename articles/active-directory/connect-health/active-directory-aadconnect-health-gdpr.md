---
title: Azure AD Connect Health och Användarsekretess | Microsoft Docs
description: Det här dokumentet beskriver Användarsekretess med Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: billmath
ms.openlocfilehash: bf26e91308cfec0dc8ede20e683919b5764a4868
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Användarsekretess och Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Den här artikeln handlar om Azure AD Connect Health och användarens integritet.  Information om Azure AD Connect och användaren sekretess finns i artikeln [här](../../active-directory/connect/active-directory-aadconnect-gdpr.md).

## <a name="user-privacy-classification"></a>Användaren sekretess klassificering
Azure AD Connect Health hamnar i den **dataprocessor** kategori av BNPR klassificering. Som en dataprocessor pipeline tillhandahåller tjänsten databearbetning tjänster för viktiga partners och end konsumenter. Azure AD Connect Health genererar inte användardata och har ingen oberoende kontroll över vilka personuppgifter samlas in och hur den används. Hämtning av data, sammanställning, analys och rapportering i Azure AD Connect Health baseras på den befintliga lokala data. 

## <a name="data-retention-policy"></a>Datakvarhållningsprincip
Azure AD Connect Health inte generera rapporter, utföra analyser eller ger inblick i efter 30 dagar. Därför Azure AD Connect Health inte lagra, bearbeta eller behålla alla data efter 30 dagar. Den här designen är kompatibel med BNPR förordningar, Microsoft sekretessbestämmelser efterlevnad och datakvarhållningsprinciper i Azure AD. 

Servrar med aktiv **hälsotjänstinformationen är inte uppdaterad** **fel** aviseringar för över 30 dagar i följd föreslår att inga data har nått Connect Health under det angivna tidsintervallet. Dessa servrar ska inaktiveras och visas inte i Connect Health-portalen. Om du vill återaktivera servrar, måste du avinstallera och [återinstallera hälsoagenten](active-directory-aadconnect-health-agent-install.md). Observera att detta inte gäller för **varningar** med samma aviseringstyp. Varningar visar att ofullständiga data saknas från den server som du får notifieringar för. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Inaktivera insamling av data och övervakning i Azure AD Connect Health
Azure AD Connect Health kan du stoppa datainsamlingen för övervakade servrarna eller för en instans av en övervakad tjänst. Du kan exempelvis stoppa datainsamling för enskilda servrar i AD FS (Active Directory Federation Services) som övervakas med hjälp av Azure AD Connect Health. Du kan också avbryta datainsamling för en hel ADFS-instans som övervakas med hjälp av Azure AD Connect Health. När du väljer att göra det tas motsvarande servrar bort från Azure AD Connect Health-portalen när du har stoppat datainsamlingen. 

>[!IMPORTANT]
> Du behöver behörigheter som Global administratör för Azure AD eller deltagarrollen i RBAC ta bort övervakade servrar från Azure AD Connect Health.
>
> Ta bort en server eller tjänstinstansen från Azure AD Connect Health är inte en omvändbar åtgärd. 

### <a name="what-to-expect"></a>Vad som händer?
Om du avbryter insamling av data och övervakning för en enskild övervakade server eller en instans av en övervakad tjänst, Tänk på följande:

- När du tar bort en instans av en övervakad tjänst tas instansen bort från Azure AD Connect Health övervakning listan i portalen. 
- När du tar bort en övervakade server eller en instans av en övervakad tjänst är inte Health-agenten avinstalleras eller tas bort från dina servrar. Health-agenten är konfigurerad att inte skicka data till Azure AD Connect Health. Du måste avinstallera Health-agenten på tidigare övervakade servrar manuellt.
- Om du inte har avinstallerat Hälsoagenten innan du utför det här steget uppstå felhändelser på servrar som rör Hälsoagenten.
- Alla data som tillhör instansen på den övervakade tjänsten tas bort enligt policyn för datalagring i Microsoft Azure.

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Inaktivera insamling av data och övervakning för övervakade servern
Se [ta bort en server från Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Inaktivera insamling av data och övervakning för en instans av en övervakad tjänst
Se [ta bort en instans av tjänsten från Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).


## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Aktivera insamling av data och övervakning i Azure AD Connect Health
Om du vill återaktivera övervakning i Azure AD Connect Health för en övervakad tjänst som tidigare har tagits bort, måste du avinstallera och [återinstallera hälsoagenten](active-directory-aadconnect-health-agent-install.md) på alla servrar.


## <a name="next-steps"></a>Nästa steg
* [Granska Microsoft Privacy-policy på Säkerhetscenter](https://www.microsoft.com/trustcenter)
* [Azure AD Connect och sekretess för alla användare](../../active-directory/connect/active-directory-aadconnect-gdpr.md)

