---
title: Azure AD Connect Health and User Privacy | Microsoft Docs
description: This document describes user privacy with Azure AD Connect Health.
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
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76897087"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>User privacy and Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>This article deals with Azure AD Connect Health and user privacy.  For information on Azure AD Connect and user privacy see the article [here](reference-connect-user-privacy.md).

## <a name="user-privacy-classification"></a>User privacy classification
Azure AD Connect Health falls into the **data processor** category of GDPR classification. As a data processor pipeline, the service provides data processing services to key partners and end consumers. Azure AD Connect Health does not generate user data and has no independent control over what personal data is collected and how it is used. Data retrieval, aggregation, analysis, and reporting in Azure AD Connect Health are based on existing on-premises data. 

## <a name="data-retention-policy"></a>Policy för datalagring
Azure AD Connect Health does not generate reports, perform analytics, or provide insights beyond 30 days. Therefore, Azure AD Connect Health does not store, process, or retain any data beyond 30 days. This design is compliant with the GDPR regulations, Microsoft privacy compliance regulations, and Azure AD data retention policies. 

Servers with active **Health service data is not up to date** **error** alerts for over 30 consecutive days suggest that no data has reached Connect Health during that time span. These servers will be disabled and not shown in Connect Health portal. To re-enable the servers, you must uninstall and [reinstall the health agent](how-to-connect-health-agent-install.md). Please note that this does not apply to **warnings** with the same alert type. Warnings indicate that partial data is missing from the server you are alerted for. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Disable data collection and monitoring in Azure AD Connect Health
Azure AD Connect Health enables you to stop data collection for each individual monitored server or for an instance of a monitored service. For example, you can stop data collection for individual ADFS (Active Directory Federation Services) servers that are monitored using Azure AD Connect Health. You can also stop data collection for the entire ADFS instance that is being monitored using Azure AD Connect Health. När du väljer att göra det tas motsvarande servrar bort från Azure AD Connect Health Portal efter att data insamlingen stoppats. 

>[!IMPORTANT]
> Du behöver antingen global administratörs behörighet för Azure AD eller deltagar rollen i RBAC för att ta bort övervakade servrar från Azure AD Connect Health.
>
> Det går inte att ångra en åtgärd för att ta bort en server eller tjänst instans från Azure AD Connect Health. 

### <a name="what-to-expect"></a>Vad ska du vänta på?
Observera följande om du stoppar data insamling och övervakning för en enskild övervakad Server eller en instans av en övervakad tjänst:

- När du tar bort en instans av en övervakad tjänst tas instansen bort från listan Azure AD Connect Health övervaknings tjänster i portalen. 
- När du tar bort en övervakad Server eller en instans av en övervakad tjänst avinstalleras inte hälso agenten eller tas bort från dina servrar. Hälso agenten har kon figurer ATS för att inte skicka data till Azure AD Connect Health. Du måste avinstallera hälso agenten manuellt på tidigare övervakade servrar.
- Om du inte har avinstallerat hälso agenten innan du utför det här steget kan du se fel händelser på de servrar som är relaterade till hälso agenten.
- Alla data som hör till instansen av den övervakade tjänsten tas bort enligt principen Microsoft Azure Data bevarande.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Inaktivera insamling och övervakning av data för en instans av en övervakad tjänst
Se [hur du tar bort en tjänst instans från Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Inaktivera insamling och övervakning av data för en övervakad Server
Se [så här tar du bort en server från Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Inaktivera insamling och övervakning av data för alla övervakade tjänster i Azure AD Connect Health
Azure AD Connect Health ger också möjlighet att stoppa data insamling av **alla** registrerade tjänster i klienten. Vi rekommenderar att du noga överväger och fullständig bekräftelse på alla globala administratörer innan du vidtar åtgärden. När processen har börjat slutar Connect Health Service att ta emot, bearbeta och rapportera alla data för alla dina tjänster. Befintliga data i Connect Health Service kommer att behållas i högst 30 dagar.
Om du vill stoppa data insamlingen av en speciell Server följer du stegen vid borttagning av vissa servrar. Om du vill stoppa insamling av klient data följer du stegen nedan för att stoppa data insamling och ta bort alla tjänster för klienten.

1. Klicka på **allmänna inställningar** under konfiguration på huvud bladet. 
2. Klicka på knappen **stoppa data insamling** överst på bladet. De andra alternativen för klient konfigurations inställningar kommer att inaktive ras när processen startar.  
 
   ![Stoppa data insamling](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. Kontrol lera listan över onboarded Services som påverkas av att data samlingar stoppas. 
4. Ange det exakta klient organisations namnet för att aktivera knappen **ta bort** åtgärd
5. Klicka på **ta bort** för att utlösa borttagningen av alla tjänster. Connect Health upphör att ta emot, bearbeta, rapportera data som skickas från dina registrerade tjänster. Hela processen kan ta upp till 24 timmar. Observera att det här steget inte kan ångras. 
6. När processen har slutförts visas inga registrerade tjänster i Connect Health längre. 

   ![När data insamlingen stoppades](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Återaktivera insamling och övervakning av data i Azure AD Connect Health
Om du vill återaktivera övervakning i Azure AD Connect Health för en tidigare borttagen övervakad tjänst måste du avinstallera och [installera om hälso agenten](how-to-connect-health-agent-install.md) på alla servrar.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Återaktivera insamling och övervakning av data för alla övervakade tjänster

Insamling av klient data kan återupptas i Azure AD Connect Health. Vi rekommenderar att du noga överväger och fullständig bekräftelse på alla globala administratörer innan du vidtar åtgärden.

>[!IMPORTANT]
> Följande steg är tillgängliga efter 24 timmars inaktive rings åtgärder.
> När du har aktiverat data insamling visar de visade insikter och övervaknings data i Connect Health inte några tidigare insamlade data innan. 

1. Klicka på **allmänna inställningar** under konfiguration på huvud bladet. 
2. Klicka på knappen **Aktivera data insamling** överst på bladet. 
 
   ![Aktivera datainsamling](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. Ange det exakta klient organisations namnet för att aktivera knappen **Aktivera** .
4. Klicka på knappen **Aktivera** om du vill bevilja behörighet för data insamling i Connect Health Service. Ändringen kommer snart att gälla. 
5. Följ [installations processen](how-to-connect-health-agent-install.md) för att installera om agenten på de servrar som ska övervakas och tjänsterna kommer att finnas i portalen.  


## <a name="next-steps"></a>Nästa steg
* [Läs sekretess policyn för Microsoft på säkerhets Center](https://www.microsoft.com/trustcenter)
* [Azure AD Connect-och användar sekretess](reference-connect-user-privacy.md)

