---
title: Konfigurera namngivna platser i Azure Active Directory | Microsoft Docs
description: Lär dig hur du konfigurerar namngivna platser.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.component: protection
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f739917b201d5255716d22930d7c4bd9e6602f37
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224525"
---
# <a name="configure-named-locations-in-azure-active-directory"></a>Konfigurera namngivna platser i Azure Active Directory

Du kan använda namngivna platser för att sätta etiketter betrodda IP-adressintervall i din organisation. Azure Active Directory använder namngivna platser i samband med:

- Identifieringen av [riskhändelser](active-directory-reporting-risk-events.md) att minska antalet rapporterade falska positiva identifieringar.  

- [Platsbaserad villkorlig åtkomst](active-directory-conditional-access-locations.md).


Den här artikeln förklarar hur du kan konfigurera namngivna platser i din miljö.


## <a name="entry-points"></a>Startpunkter

Du kan komma åt namngivna plats konfigurationssidan i den **Security** på Azure Active Directory-sidan genom att klicka på:

![Startpunkter](./media/active-directory-named-locations/34.png)

- **Villkorlig åtkomst:**

    - I den **hantera** klickar du på **namngivna platser**.
    
        ![Kommandot namngivna platser](./media/active-directory-named-locations/06.png)

- **Riskfyllda inloggningar:**

    - I verktygsfältet högst upp, klickar du på **Lägg till kända IP-adressintervall**.

       ![Kommandot namngivna platser](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>Konfigurationsexempel

**Konfigurera en namngiven plats:**

1. Logga in på den [Azure-portalen](https://portal.azure.com) som global administratör.

2. I den vänstra rutan klickar du på **Azure Active Directory**.

    ![Azure Active Directory-länken i den vänstra rutan](./media/active-directory-named-locations/01.png)

3. På den **Azure Active Directory** sidan den **Security** klickar du på **villkorlig åtkomst**.

    ![Kommandot villkorlig åtkomst](./media/active-directory-named-locations/05.png)


4. På den **villkorlig åtkomst** sidan den **hantera** klickar du på **namngivna platser**.

    ![Kommandot namngivna platser](./media/active-directory-named-locations/06.png)


5. På den **namngivna platser** klickar du på **ny plats**.

    ![Kommandot ny plats](./media/active-directory-named-locations/07.png)


6. På den **New** gör följande:

    ![Det nya bladet](./media/active-directory-named-locations/61.png)

    a. I den **namn** skriver ett namn för din namngivna plats.

    b. I den **IP-intervall** skriver ett IP-adressintervall. IP-intervallet måste ligga inom det *Classless Inter-Domain Routing* (CIDR)-format.  

    c. Klicka på **Skapa**.



## <a name="next-steps"></a>Nästa steg

Mer information finns i:

- [Villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- [Platsvillkor i Azure Active Directory villkorlig åtkomst](active-directory-conditional-access-locations.md)

- [Azure Active Directory-riskhändelser](active-directory-reporting-risk-events.md).

- [Rapporten om riskfyllda inloggningar i Azure Active Directory-portalen](active-directory-reporting-security-risky-sign-ins.md).  
