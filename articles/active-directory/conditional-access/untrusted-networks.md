---
title: Kräv MFA från ej betrodda nätverk - Azure Active Directory
description: Lär dig hur du konfigurerar en princip för villkorlig åtkomst i Azure Active Directory (Azure AD) för åtkomstförsök från ej betrodda nätverk.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cedec7f0bd51460796d8138f8d481d2982098f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74379996"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Så här: Kräv MFA för åtkomst från icke betrodda nätverk med villkorlig åtkomst   

Azure Active Directory (Azure AD) möjliggör enkel inloggning på enheter, appar och tjänster var som helst. Användarna kan komma åt dina molnappar inte bara från organisationens nätverk, utan även från alla oönskade Internet-platser. En vanlig metod för åtkomst från icke betrodda nätverk är att kräva multifaktorautentisering (MFA).

Den här artikeln ger dig den information du behöver för att konfigurera en princip för villkorlig åtkomst som kräver MFA för åtkomst från ej betrodda nätverk. 

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du är bekant med: 

- De [grundläggande begreppen](overview.md) azure AD villkorlig åtkomst 
- De [bästa metoderna](best-practices.md) för att konfigurera principer för villkorlig åtkomst i Azure-portalen

## <a name="scenario-description"></a>Scenariobeskrivning

Om du vill styra balansen mellan säkerhet och produktivitet kan det vara tillräckligt att du bara behöver ett lösenord för inloggningar från organisationens nätverk. För åtkomst från en icke betrodd nätverksplats finns det dock en ökad risk för att inloggningar inte utförs av legitima användare. För att åtgärda detta problem kan du blockera åtkomst från ej betrodda nätverk. Alternativt kan du också kräva multifaktorautentisering (MFA) för att få tillbaka ytterligare garantier för att ett försök gjordes av den legitima ägaren av kontot. 

Med Azure AD-villkorlig åtkomst kan du åtgärda det här kravet med en enda princip som ger åtkomst: 

- Till valda molnappar
- För valda användare och grupper  
- Kräver multifaktorautentisering 
- När åtkomsten kommer från: 
   - En plats som inte är betrodd

## <a name="implementation"></a>Implementering

Utmaningen med det här scenariot är att översätta *åtkomst från en icke betrodd nätverksplats* till ett villkor för villkorlig åtkomst. I en princip för villkorlig åtkomst kan du konfigurera [platsvillkoret](location-condition.md) för att hantera scenarier som är relaterade till nätverksplatser. Med platsvillkoret kan du välja namngivna platser, som är logiska grupperingar av IP-adressintervall, länder och regioner.  

Vanligtvis äger din organisation ett eller flera adressintervall, till exempel 199.30.16.0 - 199.30.16.15.
Du kan konfigurera en namngiven plats genom att:

- Ange detta intervall (199.30.16.0/28) 
- Tilldela ett beskrivande namn, till exempel **Företagsnätverk** 

I stället för att försöka definiera vilka platser som inte är betrodda kan du:

- Inkludera valfri plats 

   ![Villkorlig åtkomst](./media/untrusted-networks/02.png)

- Exkludera alla betrodda platser 

   ![Villkorlig åtkomst](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>Distribution av princip

Med den metod som beskrivs i den här artikeln kan du nu konfigurera en princip för villkorlig åtkomst för ej betrodda platser. För att se till att din princip fungerar som förväntat är den rekommenderade bästa metoden att testa den innan du distribuerar den till produktion. Använd helst en testklient för att kontrollera om den nya principen fungerar som avsett. Mer information finns i [Så här distribuerar du en ny princip](best-practices.md#how-should-you-deploy-a-new-policy). 

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om villkorlig åtkomst läser du [Vad är villkorlig åtkomst i Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
