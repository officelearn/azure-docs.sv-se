---
title: Hälsa och användarsekretes för Azure AD Connect | Microsoft-dokument
description: Det här dokumentet beskriver användarnas sekretess med Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58cddcde71e6e86b9abe07eb4200f13ad55ea659
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253668"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Användarsekretess och Azure AD Connect-hälsotillstånd 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Den här artikeln behandlar Azure AD Connect Health och användarsekretess.  Information om Azure AD Connect och användarnas sekretess finns i artikeln [här](reference-connect-user-privacy.md).

## <a name="user-privacy-classification"></a>Sekretessklassificering för användare
Azure AD Connect Health hör till **dataprocessorkategorin** för GDPR-klassificering. Som dataprocessorpipeline tillhandahåller tjänsten databehandlingstjänster till viktiga partner och slutkonsumenter. Azure AD Connect Health genererar inte användardata och har ingen oberoende kontroll över vilka personuppgifter som samlas in och hur de används. Datahämtning, aggregering, analys och rapportering i Azure AD Connect Health baseras på befintliga lokala data. 

## <a name="data-retention-policy"></a>Policy för lagring av data
Azure AD Connect Health genererar inte rapporter, utför analyser eller ger insikter efter 30 dagar. Azure AD Connect Health lagrar, bearbetar eller behåller därför inte data efter 30 dagar. Den här designen är kompatibel med GDPR-reglerna, Microsofts sekretessefterlevnadsregler och Azure AD-datalagringsprinciper. 

Servrar med aktiva **hälsotjänstdata är inte uppdaterade** **felvarningar** under över 30 dagar i följd tyder på att inga data har nått Connect Health under den tidsperioden. Dessa servrar inaktiveras och visas inte i Connect Health-portalen. Om du vill aktivera servrarna igen måste du avinstallera och [installera om hälsoagenten](how-to-connect-health-agent-install.md). Observera att detta inte gäller **varningar** med samma varningstyp. Varningar anger att partiella data saknas från den server som du har aviseringsavisering. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Inaktivera datainsamling och dataövervakning i Azure AD Connect Health
Med Azure AD Connect Health kan du stoppa datainsamlingen för varje enskild övervakad server eller för en instans av en övervakad tjänst. Du kan till exempel stoppa datainsamling för enskilda ADFS-servrar (Active Directory Federation Services) som övervakas med Azure AD Connect Health. Du kan också stoppa datainsamling för hela ADFS-instansen som övervakas med Hjälp av Azure AD Connect Health. När du väljer att göra det tas motsvarande servrar bort från Azure AD Connect Health-portalen, efter att datainsamlingen har stoppats. 

>[!IMPORTANT]
> Du behöver antingen Azure AD Global Administrator-behörigheter eller deltagarrollen i RBAC för att ta bort övervakade servrar från Azure AD Connect Health.
>
> Att ta bort en server- eller tjänstinstans från Azure AD Connect Health är inte en reversibel åtgärd. 

### <a name="what-to-expect"></a>Vad kan man förvänta sig?
Om du stoppar datainsamling och dataövervakning för en enskild övervakad server eller en instans av en övervakad tjänst bör du tänka på följande:

- När du tar bort en instans av en övervakad tjänst tas instansen bort från azure AD Connect Health övervakningstjänstlistan i portalen. 
- När du tar bort en övervakad server eller en instans av en övervakad tjänst avinstalleras eller tas inte hälsoagenten bort från servrarna. Hälsoagenten är konfigurerad för att inte skicka data till Azure AD Connect Health. Du måste avinstallera hälsoagenten manuellt på tidigare övervakade servrar.
- Om du inte har avinstallerat hälsoagenten innan du utför det här steget kan felhändelser visas på de servrar som är relaterade till hälsoagenten.
- Alla data som tillhör instansen av den övervakade tjänsten tas bort enligt Microsoft Azure Data Retention Policy.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Inaktivera datainsamling och dataövervakning för en instans av en övervakad tjänst
Se [hur du tar bort en tjänstinstans från Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Inaktivera datainsamling och dataövervakning för en övervakad server
Se [hur du tar bort en server från Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Inaktivera datainsamling och dataövervakning för alla övervakade tjänster i Azure AD Connect Health
Azure AD Connect Health ger också möjlighet att stoppa datainsamling av **alla** registrerade tjänster i klienten. Vi rekommenderar noggrant övervägande och fullständig bekräftelse av alla globala administratörer innan du vidtar åtgärden. När processen har påbörjats slutar Connect Health-tjänsten att ta emot, bearbeta och rapportera data från alla dina tjänster. Befintliga data i Connect Health-tjänsten kommer att lagras i högst 30 dagar.
Om du vill stoppa datainsamlingen av en viss server följer du stegen vid radering av specifika servrar. Om du vill stoppa datainsamlingen för klienten följer du följande steg för att stoppa datainsamling och ta bort alla tjänster från klienten.

1. Klicka på **Allmänna inställningar** under konfiguration i huvudbladet. 
2. Klicka på **knappen Stoppa datainsamling** högst upp på bladet. De andra alternativen för klientkonfigurationsinställningar inaktiveras när processen startar.  
 
   ![Stoppa datainsamling](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. Kontrollera listan över inbyggda tjänster som påverkas genom att stoppa datainsamlingar. 
4. Ange det exakta klientnamnet för att aktivera knappen **Ta bort** åtgärd
5. Klicka på **Ta bort** för att utlösa borttagning av alla tjänster. Connect Health slutar ta emot, bearbeta, rapportera data som skickas från dina inbyggda tjänster. Hela processen kan ta upp till 24 timmar. Observera att det här steget inte är reversibelt. 
6. När processen är klar ser du inte några registrerade tjänster i Connect Health längre. 

   ![Efter att datainsamlingen stoppats](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Återaktivera datainsamling och dataövervakning i Azure AD Connect Health
Om du vill aktivera övervakningen i Azure AD Connect Health för en tidigare borttagen övervakad tjänst måste du avinstallera och [installera om hälsoagenten](how-to-connect-health-agent-install.md) på alla servrar.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Återaktivera datainsamling och dataövervakning för alla övervakade tjänster

Datainsamlingen på klienten kan återupptas i Azure AD Connect Health. Vi rekommenderar noggrant övervägande och fullständig bekräftelse av alla globala administratörer innan du vidtar åtgärden.

>[!IMPORTANT]
> Följande steg kommer att vara tillgängliga efter 24 timmars inaktivera åtgärd.
> När du har aktiverat datainsamling kommer de presenterade insikts- och övervakningsdata i Connect Health inte att visa några äldre data som samlats in tidigare. 

1. Klicka på **Allmänna inställningar** under konfiguration i huvudbladet. 
2. Klicka på **Knappen Aktivera datainsamling** högst upp på bladet. 
 
   ![Aktivera datainsamling](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. Ange det exakta klientnamnet för att aktivera knappen **Aktivera.**
4. Klicka på **Aktivera** knappen för att bevilja behörighet för datainsamling i Anslut hälsotjänst. Ändringen kommer att tillämpas inom kort. 
5. Följ [installationsprocessen](how-to-connect-health-agent-install.md) för att installera om agenten på de servrar som ska övervakas och tjänsterna kommer att finnas i portalen.  


## <a name="next-steps"></a>Nästa steg
* [Läs Microsofts sekretesspolicy på Säkerhetscenter](https://www.microsoft.com/trustcenter)
* [Azure AD Connect och användarsekretess](reference-connect-user-privacy.md)

