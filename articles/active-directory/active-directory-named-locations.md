---
title: Med namnet platser i Azure Active Directory | Microsoft Docs
description: "Lär dig vad heter platser är och hur de konfigureras."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b6f80cde24edcbec68309ba033d4da16ee97b731
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="named-locations-in-azure-active-directory"></a>Sökvägarna i Azure Active Directory

Du kan använda namngivna platser för att sätta etiketter tillförlitliga IP-adressintervall i din organisation. Azure Active Directory använder sökvägarna i samband med:

- Identifiering av [riskerar händelser](active-directory-reporting-risk-events.md) att minska antalet rapporterade falska positiva identifieringar.  

- [Plats-baserad villkorlig åtkomst](active-directory-conditional-access-locations.md).


Den här artikeln förklarar hur du kan konfigurera namngivna platser i din miljö.


## <a name="entry-points"></a>Startpunkter

Du kan komma åt konfigurationssidan namn i den **säkerhet** på Azure Active Directory-sidan genom att klicka på:

![Startpunkter](./media/active-directory-named-locations/34.png)

- **Villkorlig åtkomst:**

    - I den **hantera** klickar du på **med namnet platser**.
    
        ![Kommandot namngivna platser](./media/active-directory-named-locations/06.png)

- **Riskfyllda inloggningar:**

    - Klicka på i verktygsfältet högst upp **Lägg till kända IP-adressintervall**.

       ![Kommandot namngivna platser](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>Konfigurationsexempel

**Så här konfigurerar du en namngiven plats:**

1. Logga in på den [Azure-portalen](https://portal.azure.com) som global administratör.

2. I den vänstra rutan klickar du på **Azure Active Directory**.

    ![Azure Active Directory-länken i den vänstra rutan](./media/active-directory-named-locations/01.png)

3. På den **Azure Active Directory** sidan den **säkerhet** klickar du på **villkorlig åtkomst**.

    ![Kommandot villkorlig åtkomst](./media/active-directory-named-locations/05.png)


4. På den **villkorlig åtkomst** sidan den **hantera** klickar du på **med namnet platser**.

    ![Kommandot namngivna platser](./media/active-directory-named-locations/06.png)


5. På den **med namnet platser** klickar du på **ny plats**.

    ![Kommandot plats](./media/active-directory-named-locations/07.png)


6. På den **ny** gör följande:

    ![Det nya bladet](./media/active-directory-named-locations/56.png)

    a. I den **namn** Skriv ett namn för din namngiven plats.

    b. I den **IP-adressintervall** skriver ett IP-adressintervall. IP-intervallet måste vara i den *Classless Inter-Domain Routing* (CIDR)-format.  

    c. Klicka på **Skapa**.



## <a name="what-you-should-know"></a>Vad du bör känna till

**Massuppdateringar**: när du skapar eller uppdaterar sökvägarna för massuppdateringar, kan du överföra eller hämta en CSV-fil med IP-adressintervall. Överföra lägger till IP-adressintervall i filen i listan i stället för att skriva över listan.

![Länkar överföring och hämtning](./media/active-directory-named-locations/09.png)


**Begränsningar**: du kan definiera högst 60 sökvägarna med en IP-adressintervall som är tilldelade till var och en av dem. Om du har en namngiven plats som har konfigurerats kan definiera du upp till 500 IP-adressintervall för den.


## <a name="next-steps"></a>Nästa steg

Mer information om:

- **Risk händelser**, se [Azure Active Directory riskhändelser](active-directory-reporting-risk-events.md).

- **Villkorlig åtkomst**, se [villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- **Riskfyllda inloggningar rapporter**, se [riskfyllda inloggningar rapporten i Azure Active Directory-portalen](active-directory-reporting-security-risky-sign-ins.md).  
