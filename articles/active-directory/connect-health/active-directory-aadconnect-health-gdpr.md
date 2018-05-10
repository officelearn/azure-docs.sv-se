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
ms.openlocfilehash: 5fedbac439636b56da217e7babd30820bce7b342
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
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

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Inaktivera insamling av data och övervakning för en instans av en övervakad tjänst
Se [ta bort en instans av tjänsten från Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Inaktivera insamling av data och övervakning för övervakade servern
Se [ta bort en server från Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Inaktivera insamling av data och övervakning för alla övervakade tjänster i Azure AD Connect Health
Azure AD Connect Health ger också möjlighet att stoppa datainsamlingen av **alla** registrerade tjänster i klienten. Vi rekommenderar noggrant övervägande och fullständig bekräftelse av alla globala administratörer innan du vidtar åtgärden. När processen börjar stoppas Connect Health-tjänsten får, bearbetning och rapportera några data av alla tjänster. Befintliga data i Connect Health-tjänsten kommer att sparas i mer än 30 dagar.
Om du vill stoppa insamling av specifika servrar följer du stegen vid borttagning av specifika servrar. Följ anvisningarna nedan om du vill stoppa insamling av data och ta bort alla tjänster för innehavaren om du vill stoppa insamling av tenant-wise.

1.  Klicka på **allmänna inställningar** under konfigurationen i huvudbladet. 
2.  Klicka på **stoppa datainsamling** knappen överst på bladet. Andra alternativ i konfigurationsinställningarna för klienten kommer att inaktiveras när processen startar.  
 
 ![Stoppa datainsamlingen](./media/active-directory-aadconnect-health-gdpr/gdpr4.png)
  
3.  Kontrollera listan över publicerats så tjänster som påverkas genom att stoppa datasamlingar. 
4.  Ange exakt klientnamnet för att aktivera den **ta bort** åtgärdsknappen
5.  Klicka på **ta bort** att utlösa borttagningen av alla tjänster. Ansluta hälsa stoppas tar emot, bearbetning, rapportering av alla data som skickas från publicerats så-tjänster. Hela processen med kan ta upp till 24 timmar. Observera att det här steget inte går att ångra. 
6.  När processen är klar visas inte alla tjänster som är registrerade i Connect Health längre. 

 ![När datainsamling har stoppats](./media/active-directory-aadconnect-health-gdpr/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Aktivera insamling av data och övervakning i Azure AD Connect Health
Om du vill återaktivera övervakning i Azure AD Connect Health för en övervakad tjänst som tidigare har tagits bort, måste du avinstallera och [återinstallera hälsoagenten](active-directory-aadconnect-health-agent-install.md) på alla servrar.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Aktivera insamling av data och övervakning för alla övervakade tjänster

Insamling av tenant-Wise kan återupptas i Azure AD Connect Health. Vi rekommenderar noggrant övervägande och fullständig bekräftelse av alla globala administratörer innan du vidtar åtgärden.

>[!IMPORTANT]
> Följande steg blir tillgänglig efter 24 timmar inaktivera åtgärd.
> När du har aktiverat för insamling av visas inte några äldre data som samlas in innan i presenterades insikt och övervakningsdata i Connect Health. 

1.  Klicka på **allmänna inställningar** under konfigurationen i huvudbladet. 
2.  Klicka på **Aktivera datainsamling** knappen överst på bladet. 
 
 ![Aktivera datainsamling](./media/active-directory-aadconnect-health-gdpr/gdpr6.png)
 
3.  Ange det exakta klientnamnet att aktivera den **aktivera** knappen.
4.  Klicka på **aktivera** för att bevilja behörighet för datainsamlingen i Connect Health-tjänsten. Ändringen kommer att tillämpas inom kort. 
5.  Följ den [installationsprocessen](active-directory-aadconnect-health-agent-install.md) installera om agenten på servrarna som ska övervakas och tjänsterna som kommer att finnas i portalen.  


## <a name="next-steps"></a>Nästa steg
* [Granska Microsoft Privacy-policy på Säkerhetscenter](https://www.microsoft.com/trustcenter)
* [Azure AD Connect och sekretess för alla användare](../../active-directory/connect/active-directory-aadconnect-gdpr.md)

