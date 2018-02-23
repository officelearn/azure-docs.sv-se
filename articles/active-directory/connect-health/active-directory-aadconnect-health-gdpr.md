---
title: "Azure AD Connect Health och allmänna Data Protection förordning | Microsoft Docs"
description: "Det här dokumentet beskriver hur du skaffar BNPR kompatibilitet med Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2018
ms.author: billmath
ms.openlocfilehash: b9a0b9027bbead00300040186e453933b3a7f46b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect-health"></a>BNPR efterlevnad och Azure AD Connect Health 

[Allmänna Data Protection förordning (BNPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm) är en Europeiska unionen (EU) data protection och sekretess lag. BNPR inför nya regler på företag, myndigheter, icke-vinst och andra organisationer som erbjuder varor och tjänster till personer i EU eller samla in och analysera data som är knutna till Europa boende. 

Microsoftprodukter och tjänster finns i dag som hjälper dig att uppfylla kraven BNPR. Läs mer om Microsoft Privacy-policy på [Säkerhetscenter](https://www.microsoft.com/trustcenter).

Azure AD Connect Health övervakar dina lokala identitetstjänst infrastruktur och synkronisering. Det ger dig insikter och hämtar aviseringar. Microsoft strävar efter att BNPR kompatibilitet för molntjänster när tvingande börjar kan 2018 och ge BNPR-relaterade garantier i dess avtal åtaganden. 

>[!NOTE] 
> Den här artikeln beskrivs BNPR efterlevnad i Azure AD Connect Health. Mer information om BNPR efterlevnad i Azure AD Connect finns [BNPR efterlevnad och Azure AD Connect](../../active-directory/connect/active-directory-aadconnect-gdpr.md).

## <a name="gdpr-classification"></a>BNPR klassificering
Azure AD Connect Health hamnar i den **dataprocessor** kategori av BNPR klassificering. Som en dataprocessor pipeline tillhandahåller tjänsten databearbetning tjänster för viktiga partners och end konsumenter. Azure AD Connect Health genererar inte användardata och har ingen oberoende kontroll över vilka personuppgifter samlas in och hur den används. Hämtning av data, sammanställning, analys och rapportering i Azure AD Connect Health baseras på den befintliga lokala data. 

## <a name="data-retention-policy"></a>Datakvarhållningsprincip
Azure AD Connect Health inte generera rapporter, utföra analyser eller ger inblick i efter 30 dagar. Därför Azure AD Connect Health inte lagra, bearbeta eller behålla alla data efter 30 dagar. Den här designen är kompatibel med BNPR förordningar, Microsoft sekretessbestämmelser efterlevnad och datakvarhållningsprinciper i Azure AD. 
 
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
Se [ta bort en server från Azure AD Connect Health](active-directory-aadconnect-health-operations.md#to-delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Inaktivera insamling av data och övervakning för en instans av en övervakad tjänst
Se [ta bort en instans av tjänsten från Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).


## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Aktivera insamling av data och övervakning i Azure AD Connect Health
Om du vill återaktivera övervakning i Azure AD Connect Health för en övervakad tjänst som tidigare har tagits bort, måste du avinstallera och [återinstallera hälsoagenten](active-directory-aadconnect-health-agent-install.md) på alla servrar.


## <a name="next-steps"></a>Nästa steg
* [Granska Microsoft Privacy-policy på Säkerhetscenter](https://www.microsoft.com/trustcenter)
* [Azure AD Connect och BNPR](../../active-directory/connect/active-directory-aadconnect-gdpr.md)
* [Azure AD Connect Health-åtgärder](active-directory-aadconnect-health-operations.md)
