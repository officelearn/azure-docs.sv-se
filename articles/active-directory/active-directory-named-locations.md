---
title: Med namnet platser i Azure Active Directory | Microsoft Docs
description: "Genom att konfigurera med namnet platser kan du undvika med IP-adresser som ägs av organisationen generera falska positiva identifieringar för Impossible reser till ovanliga platser risk händelsetyp."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: da437908509e40386ed23863648bd6956b308186
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="named-locations-in-azure-active-directory"></a>Sökvägarna i Azure Active Directory

Du kan använda funktionen sökvägarna i Azure Active Directory för att sätta etiketter tillförlitliga IP-adressintervall i ditt företag. I din miljö kan du använda sökvägarna i kontexten för identifiering av [riskerar händelser](active-directory-reporting-risk-events.md). Funktionen hjälper till att minska antalet rapporterade falska positiva identifieringar för den *Impossible resa till ovanliga platser* riskerar händelsetyp. 

## <a name="configuration"></a>Konfiguration

Så här konfigurerar du en namngiven plats:

1. Logga in på den [Azure-portalen](https://portal.azure.com) som global administratör.

2. I den vänstra rutan klickar du på **Azure Active Directory**.

    ![Azure Active Directory-länken i den vänstra rutan](./media/active-directory-named-locations/01.png)

3. På den **Azure Active Directory** blad i den **säkerhet** klickar du på **villkorlig åtkomst**.

    ![Kommandot villkorlig åtkomst](./media/active-directory-named-locations/05.png)


4. På den **villkorlig åtkomst** blad i den **hantera** klickar du på **med namnet platser**.

    ![Kommandot namngivna platser](./media/active-directory-named-locations/06.png)


5. På den **med namnet platser** bladet, klickar du på **ny plats**.

    ![Kommandot plats](./media/active-directory-named-locations/07.png)


6. På den **ny** bladet gör du följande:

    ![Det nya bladet](./media/active-directory-named-locations/56.png)

    a. I den **namn** Skriv ett namn för din namngiven plats.

    b. I den **IP-adressintervall** skriver ett IP-adressintervall. IP-intervallet måste vara i den *Classless Inter-Domain Routing* (CIDR)-format.  

    c. Klicka på **Skapa**.



## <a name="what-you-should-know"></a>Vad du bör känna till

**Massuppdateringar**: när du skapar eller uppdaterar sökvägarna för massuppdateringar, kan du överföra eller hämta en CSV-fil med IP-adressintervall. Överföra lägger till IP-adressintervall i filen i listan i stället för att skriva över listan.

![Länkar överföring och hämtning](./media/active-directory-named-locations/09.png)


**Begränsningar**: du kan definiera högst 60 sökvägarna med en IP-adressintervall som är tilldelade till var och en av dem. Om du har en namngiven plats som har konfigurerats kan definiera du upp till 500 IP-adressintervall för den.


## <a name="next-steps"></a>Nästa steg

Läs mer om riskhändelser i [Azure Active Directory riskhändelser](active-directory-reporting-risk-events.md).

