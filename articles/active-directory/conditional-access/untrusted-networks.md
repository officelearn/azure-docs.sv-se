---
title: Kräv MFA från ej betrodda nätverk – Azure Active Directory
description: Lär dig hur du konfigurerar en princip för villkorlig åtkomst i Azure Active Directory (Azure AD) till för åtkomst försök från ej betrodda nätverk.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b2f9a6e13fdc39ab18a2056fab15982aa9fb0e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88948172"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Gör så här: Kräv MFA för åtkomst från ej betrodda nätverk med villkorlig åtkomst   

Azure Active Directory (Azure AD) möjliggör enkel inloggning till enheter, appar och tjänster var som helst. Dina användare kan komma åt dina molnappar, inte bara från din organisations nätverk, utan även från en obetrodd Internet-plats. En vanlig metod för åtkomst från ej betrodda nätverk är att kräva multifaktorautentisering (MFA).

Den här artikeln innehåller den information du behöver för att konfigurera en princip för villkorlig åtkomst som kräver MFA för åtkomst från ej betrodda nätverk. 

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med: 

- [Grundläggande begrepp](overview.md) för villkorlig åtkomst i Azure AD 
- [Metod tips](best-practices.md) för att konfigurera principer för villkorlig åtkomst i Azure Portal

## <a name="scenario-description"></a>Scenariobeskrivning

För att kunna hantera balansen mellan säkerhet och produktivitet kan det vara tillräckligt för att du bara behöver ett lösen ord för inloggningar från din organisations nätverk. För åtkomst från en obetrodd nätverks plats finns det dock en ökad risk att inloggningar inte utförs av legitima användare. För att åtgärda detta kan du blockera åtkomst från ej betrodda nätverk. Du kan också kräva Multi-Factor Authentication (MFA) för att få tillbaka ytterligare garantier att ett försök har gjorts av kontots legitima ägare. 

Med villkorlig åtkomst i Azure AD kan du hantera det här kravet med en enda princip som ger åtkomst: 

- Till valda molnappar
- För valda användare och grupper  
- Kräver Multi-Factor Authentication 
- När åtkomsten härstammar från: 
   - En plats som inte är betrodd

## <a name="implementation"></a>Implementering

Utmaningen med det här scenariot är att översätta *åtkomst från en icke betrodd nätverks plats* till ett villkor för villkorlig åtkomst. I en princip för villkorlig åtkomst kan du konfigurera [plats villkoret](location-condition.md) för att hantera scenarier som är relaterade till nätverks platser. Med villkoret platser kan du välja namngivna platser, som är logiska grupperingar av IP-adressintervall, länder och regioner.  

Organisationen äger vanligt vis ett eller flera adress intervall, till exempel 199.30.16.0-199.30.16.15.
Du kan konfigurera en namngiven plats genom att:

- Ange det här intervallet (199.30.16.0/28) 
- Tilldela ett beskrivande namn, till exempel **företags nätverk** 

I stället för att försöka definiera vilka platser som inte är betrodda kan du:

- Ta med valfri plats 

   ![Villkorlig åtkomst](./media/untrusted-networks/02.png)

- Undanta alla betrodda platser 

   ![Villkorlig åtkomst](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>Distribution av princip

Med den metod som beskrivs i den här artikeln kan du nu konfigurera en princip för villkorlig åtkomst för ej betrodda platser. För att se till att principen fungerar som förväntat, är den rekommenderade rekommenderade metoden att testa den innan den distribueras till produktion. Vi rekommenderar att du använder en test klient för att kontrol lera om den nya principen fungerar som den ska. Mer information finns i [så här distribuerar du en ny princip](best-practices.md#how-should-you-deploy-a-new-policy). 

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om villkorlig åtkomst, se [Vad är villkorlig åtkomst i Azure Active Directory?](./overview.md)