---
title: Hur till - Konfigurera Azure Active Directory villkorliga åtkomstprinciper för åtkomstförsök från ej betrodda nätverk | Microsoft Docs
description: Lär dig hur du konfigurerar en princip för villkorlig åtkomst i Azure Active Directory (AD Azure) till för åtkomstförsök från ej betrodda nätverk.
services: active-directory
keywords: villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 952a3a3952a96c7125e0b0dbe770b72c17a57101
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232604"
---
# <a name="how-to-configure-conditional-access-policies-for-access-attempts-from-untrusted-networks"></a>Så här: Konfigurera principer för villkorlig åtkomst för åtkomstförsök från ej betrodda nätverk   

I mobile första, molnet först världen aktiverar Azure Active Directory (AD Azure) enkel inloggning till enheter, appar och tjänster från var som helst. Till följd av detta, kan användarna komma åt dina molnappar inte bara från nätverket, men även från en ej betrodd plats på Internet. Med [villkorlig åtkomst i Azure Active Directory (AD Azure)](active-directory-conditional-access-azure-portal.md), du kan styra hur behöriga användare kan komma åt dina molnappar. Ett vanligt krav i den här kontexten är att styra åtkomstförsök som initieras från ej betrodda nätverk. Den här artikeln ger dig den information du behöver att konfigurera en princip för villkorlig åtkomst som hanterar det här kravet. 

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med: 

- De grundläggande principerna för villkorlig åtkomst i Azure AD 
- Konfigurera principer för villkorlig åtkomst i Azure-portalen

Se:

- [Vad är villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md) – en översikt av villkorlig åtkomst 

- [Snabbstart: Kräver MFA för specifika appar med Azure Active Directory för villkorlig åtkomst](active-directory-conditional-access-app-based-mfa.md) – få viss erfarenhet av att konfigurera principer för villkorlig åtkomst. 


## <a name="scenario-description"></a>Scenariobeskrivning

Det kan vara tillräckliga för att du bara behöver dina användare att autentiseras med ett lösenord för att master balansen mellan säkerhet och produktivitet. När en åtkomst görs från en ej betrodd nätverksplats är dock en ökad risk som inloggningar inte utförs av behöriga användare. För att adressera detta kan du blockera åtkomstförsök från ej betrodda nätverk. Alternativt kan du också kräva multifaktorautentisering (MFA) för att få tillbaka ytterligare försäkran om att ett försök gjordes av kontot legitima ägare. 

Du kan åtgärda det här kravet med en enda princip som beviljar åtkomst med Azure AD villkorlig åtkomst: 

- Till valda molnappar

- För valda användare och grupper  

- Att kräva multifaktorautentisering 

- När en åtkomst görs från: 

    - En plats som inte är betrodd


## <a name="considerations"></a>Överväganden

Utmaning i det här scenariot är att översätta *när ett åtkomstförsök görs från en plats som inte är betrodd* till ett villkor för villkorlig åtkomst. I en princip för villkorlig åtkomst konfigurerar du den [platser villkoret](active-directory-conditional-access-locations.md) att hantera scenarier som är relaterade till nätverksplatser. Villkoret platser kan du välja [med namnet platser](active-directory-conditional-access-locations.md#named-locations), som representerar logiska grupperingar av IP-adressintervall, länder och regioner.  

Normalt äger din organisation en eller flera adressintervall, till exempel 199.30.16.0 - 199.30.16.24.
Du kan konfigurera en namngiven plats genom att:

- Anger intervallet (199.30.16.0/24) 

- Tilldela ett beskrivande namn som **företagsnätverket** 


I stället att definiera vilka alla platser är som inte är betrodd, kan du:

- Ta med 

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-untrusted-networks/02.png)

- Undanta alla betrodda platser 

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-untrusted-networks/01.png)



## <a name="implementation"></a>Implementering

Med den metod som beskrivs i den här artikeln kan du nu konfigurera en princip för villkorlig åtkomst för ej betrodda platser. Du bör alltid testa din princip innan den distribueras ut i produktionen för att kontrollera att den fungerar som förväntat. Det bästa är bör om möjligt du göra din första testerna i en Testklient. Mer information finns i [hur ska du distribuera en ny princip](active-directory-conditional-access-best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om villkorlig åtkomst kan se [vad är villkorlig åtkomst i Azure Active Directory?](active-directory-conditional-access-azure-portal.md)