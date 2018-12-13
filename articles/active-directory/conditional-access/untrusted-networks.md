---
title: Så här kräver du multifaktorautentisering (MFA) för åtkomst från ej betrodda nätverk med villkorlig åtkomst i Azure Active Directory (Azure AD) | Microsoft Docs
description: Lär dig mer om att konfigurera en princip för villkorlig åtkomst i Azure Active Directory (Azure AD) för för åtkomstförsök från ej betrodda nätverk.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: c40db6c253899d7aab21d277e93b23dd0c6feb97
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314014"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Anvisningar: Kräva MFA för åtkomst från ej betrodda nätverk med villkorlig åtkomst   

Azure Active Directory (Azure AD) möjliggör enkel inloggning till enheter, appar och tjänster från var som helst. Användarna kan komma åt dina appar i molnet, inte bara från din organisations nätverk, utan även från alla ej betrodd plats på Internet. Vanliga bästa praxis för åtkomst från ej betrodda nätverk är att kräva multifaktorautentisering (MFA).

Den här artikeln ger dig den information du behöver att konfigurera en princip för villkorlig åtkomst som kräver MFA för åtkomst från ej betrodda nätverk. 

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med: 

- Den [grundbegreppen](overview.md) av Azure AD villkorsstyrd åtkomst 
- Den [bästa praxis](best-practices.md) för att konfigurera principer för villkorlig åtkomst i Azure portal



## <a name="scenario-description"></a>Scenariobeskrivning

Om du vill balans mellan säkerhet och produktivitet, kan det vara tillräckliga för att du endast kräva ett lösenord för inloggningar från din organisations nätverk. För åtkomst från en ej betrodd nätverksplats, det finns dock en ökad risk som inte är inloggningar som utförs av behöriga användare. För att åtgärda det här problemet kan du blockera åtkomst från ej betrodda nätverk. Du kan också kan du också kräva multifaktorautentisering (MFA) för att få tillbaka ytterligare trygghet som ett försök gjordes av är tillförlitligt ägare för kontot. 

Med Azure AD villkorlig åtkomst, kan du lösa det här kravet med en enda princip som tilldelar åtkomst: 

- Till valda molnappar

- För valda användare och grupper  

- Att kräva multifaktorautentisering 

- När åtkomst kommer från: 

    - En plats som inte är betrodd


## <a name="implementation"></a>Implementering

Den stora utmaningen med det här scenariot är att översätta *åtkomst från en ej betrodd nätverksplats* till ett villkor för villkorlig åtkomst. I en princip för villkorlig åtkomst kan du konfigurera den [platser villkor](location-condition.md) kunna hantera scenarier som är relaterade till nätverksplatser. Villkoret platser kan du välja namngivna platser som är logiska grupperingar av IP-adressintervall, länder och regioner.  

Normalt kan äger din organisation en eller flera adressintervall, till exempel 199.30.16.0 - 199.30.16.24.
Du kan konfigurera en namngiven plats genom att:

- Anger det här intervallet (199.30.16.0/24) 

- Tilldela ett beskrivande namn som **företagsnätverket** 


I stället att definiera vilka alla platser är som inte är betrodd, kan du:

- Inkludera valfri plats 

    ![Villkorlig åtkomst](./media/untrusted-networks/02.png)

- Uteslut alla betrodda platser 

    ![Villkorlig åtkomst](./media/untrusted-networks/01.png)



## <a name="policy-deployment"></a>Distribution av princip

Med den metod som beskrivs i den här artikeln kan du nu konfigurera en princip för villkorlig åtkomst för ej betrodda platser. För att säkerställa att din princip fungerar som förväntat, är rutin att testa den innan du distribuerar det till produktion. Använd helst en test-klient för att kontrollera om den nya principen fungerar som avsett. Mer information finns i [så här distribuerar du en ny princip](best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om villkorlig åtkomst, se [vad är villkorlig åtkomst i Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)