---
title: Azure AD Connect Health och användarnas integritet | Microsoft Docs
description: Det här dokumentet beskriver Användarsekretess med Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: billmath
ms.openlocfilehash: bda199e5affb2ffad7285eb6e55ed18b46f92801
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54477948"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Användarsekretess och Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Den här artikeln innehåller Azure AD Connect Health och användaren sekretess.  Information om Azure AD Connect och användaren sekretess finns i artikeln [här](reference-connect-user-privacy.md).

## <a name="user-privacy-classification"></a>Användaren sekretess klassificering
Azure AD Connect Health hamnar i den **registerförare** kategorin för GDPR-klassificering. Som en registerförare pipeline tillhandahåller tjänsten bearbetning av tjänster till viktiga partner och konsumenter i slutet. Azure AD Connect Health genererar inte användardata och har ingen oberoende kontroll över vilka personliga data samlas in och hur de används. Datahämtning, aggregering, analys och rapportering i Azure AD Connect Health baseras på befintliga lokala data. 

## <a name="data-retention-policy"></a>Datakvarhållningsprincip
Azure AD Connect Health inte generera rapporter, utföra analyser, och ge insikter äldre än 30 dagar. Därför kan Azure AD Connect Health inte lagra, bearbeta eller kvarhålla data äldre än 30 dagar. Den här designen är kompatibel med GDPR bestämmelser, Microsoft sekretessbestämmelser för efterlevnad och Azure AD-principer för kvarhållning av data. 

Servrar med aktiv **hälsotjänstinformationen är inte uppdaterad** **fel** aviseringar för över 30 dagar i rad föreslå att inga data har nått Connect Health under det angivna tidsintervallet. Servrarna ska inaktiveras och visas inte i Connect Health-portalen. Om du vill återaktivera servrarna, måste du avinstallera och [återinstallera hälsoagenten](how-to-connect-health-agent-install.md). Observera att detta inte gäller för **varningar** med samma aviseringstyp. Varningar visar att ofullständiga data saknas från den server som du aviseras för. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Inaktivera insamling av data och övervakning i Azure AD Connect Health
Azure AD Connect Health gör det möjligt för dig att stoppa datainsamlingen för övervakade servrarna eller för en instans av en övervakad tjänst. Du kan exempelvis stoppa insamling av data för enskilda ADFS (Active Directory Federation Services)-servrar som övervakas med hjälp av Azure AD Connect Health. Du kan också stoppa insamling av data för en hel AD FS-instans som övervakas med hjälp av Azure AD Connect Health. När du väljer att göra det motsvarande servrar tas bort från Azure AD Connect Health-portalen efter datainsamling. 

>[!IMPORTANT]
> Du behöver behörighet för Global administratör för Azure AD eller rollen deltagare i RBAC att ta bort övervakade servrar från Azure AD Connect Health.
>
> Ta bort en server eller tjänstinstans från Azure AD Connect Health är inte en ångra åtgärden. 

### <a name="what-to-expect"></a>Vad som händer?
Om du avbryter datainsamling och övervakning för en enskild övervakade server eller en instans av en övervakad tjänst, Tänk på följande:

- När du tar bort en instans av en övervakad tjänst tas instansen bort från listan Azure AD Connect Health övervakning tjänst i portalen. 
- När du tar bort en övervakade server eller en instans av en övervakad tjänst är Health-agenten inte avinstalleras eller tas bort från dina servrar. Hälsoagenten är konfigurerad att inte skicka data till Azure AD Connect Health. Du måste manuellt avinstallera Health-agenten på tidigare övervakade servrar.
- Om du inte har avinstallerat Hälsoagenten innan du utför det här steget uppstå felhändelser på servrarna som rör Hälsoagenten.
- Alla data som hör till instansen av den övervakade tjänsten tas bort enligt policyn för datalagring i Microsoft Azure.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Inaktivera insamling av data och övervakning för en instans av en övervakad tjänst
Se [ta bort en instans av tjänsten från Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Inaktivera insamling av data och övervakning för en övervakad server
Se [ta bort en server från Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Inaktivera insamling av data och övervakning för alla övervakade tjänster i Azure AD Connect Health
Azure AD Connect Health ger också möjlighet att stoppa datainsamlingen av **alla** registrerats tjänster i klientorganisationen. Vi rekommenderar noggrann överväganden och fullständig bekräftelse av alla globala administratörer innan du vidtar åtgärden. När processen börjar att Connect Health-tjänsten slutar den ta emot, bearbetning och rapporterar inga data för alla dina tjänster. Befintliga data i Connect Health-tjänsten ska behållas i mer än 30 dagar.
Om du vill stoppa insamling av specifika servrar följer du stegen i borttagning av specifika servrar. Följ stegen nedan om du vill stoppa insamling av data och ta bort alla tjänster på klienten om du vill stoppa insamling av tenant-wise.

1.  Klicka på **allmänna inställningar** under konfigurationen i huvudbladet. 
2.  Klicka på **stoppa datainsamling** knappen överst på bladet. De andra alternativen för konfigurationsinställningar för klient kommer att inaktiveras när processen startar.  
 
 ![Stoppa datainsamling](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3.  Kontrollera listan över integrerade tjänster som påverkas genom att stoppa datasamlingar. 
4.  Ange exakt klientnamnet för att aktivera den **ta bort** &
5.  Klicka på **ta bort** att utlösa borttagningen av alla tjänster. Connect Health slutar att ta emot, bearbetning, rapporterar inga data skickas från dina integrerade tjänster. Hela processen med att kan ta upp till 24 timmar. Observera att det här steget inte kan ångras. 
6.  När processen är klar visas inte några registrerade tjänster i Connect Health längre. 

 ![När datainsamling har stoppats](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Aktivera insamling av data och övervakning i Azure AD Connect Health
Om du vill återaktivera övervakning i Azure AD Connect Health för en tidigare borttagna övervakade tjänsten, måste du avinstallera och [återinstallera hälsoagenten](how-to-connect-health-agent-install.md) på alla servrar.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Aktivera insamling av data och övervakning för alla övervakade tjänster

Insamling av tenant-Wise kan återupptas i Azure AD Connect Health. Vi rekommenderar noggrann överväganden och fullständig bekräftelse av alla globala administratörer innan du vidtar åtgärden.

>[!IMPORTANT]
> Följande steg blir tillgänglig när åtgärden har inaktiverat 24 timmar.
> När du har aktiverat för insamling av visar uppvisade insikter och övervakning av data i Connect Health inte några äldre data som samlas in innan. 

1.  Klicka på **allmänna inställningar** under konfigurationen i huvudbladet. 
2.  Klicka på **Aktivera datainsamling** knappen överst på bladet. 
 
 ![Aktivera datainsamling](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3.  Ange exakt klientnamnet för att aktivera den **aktivera** knappen.
4.  Klicka på **aktivera** knappen för att bevilja behörighet för datainsamlingen i Connect Health-tjänsten. Ändringen kommer att tillämpas inom kort. 
5.  Följ den [installationsprocessen](how-to-connect-health-agent-install.md) att installera om agenten på servrar som ska övervakas och tjänster kommer att finnas i portalen.  


## <a name="next-steps"></a>Nästa steg
* [Granska Microsoft Privacy principen på Säkerhetscenter](https://www.microsoft.com/trustcenter)
* [Azure AD Connect och Användarsekretess](reference-connect-user-privacy.md)

