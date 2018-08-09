---
title: Hur – konfigurera Azure Active Directory villkorliga åtkomstprinciper för åtkomstförsök från ej betrodda nätverk | Microsoft Docs
description: Lär dig mer om att konfigurera en princip för villkorlig åtkomst i Azure Active Directory (Azure AD) för för åtkomstförsök från ej betrodda nätverk.
services: active-directory
keywords: villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst
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
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 5ddde65b2a68e71d86af6ce3dcd2847736cf5823
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627193"
---
# <a name="how-to-configure-conditional-access-policies-for-access-attempts-from-untrusted-networks"></a>Så här: Konfigurera principer för villkorlig åtkomst för åtkomstförsök från ej betrodda nätverk   

I en mobil- och molnorienterade värld, Azure Active Directory (Azure AD) som möjliggör enkel inloggning till enheter, appar och tjänster från var som helst. Till följd av detta, kan användarna komma åt dina appar i molnet inte bara från din organisations nätverk, utan även från alla ej betrodd plats på Internet. Med [villkorlig åtkomst i Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), du kan styra hur behöriga användare kan komma åt dina appar i molnet. Ett vanligt krav i det här sammanhanget är att kontrollera åtkomstförsök som initieras från ej betrodda nätverk. Den här artikeln ger dig den information du behöver att konfigurera en princip för villkorlig åtkomst som hanterar det här kravet. 

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med: 

- De grundläggande principerna för villkorlig åtkomst i Azure AD 
- Konfigurera principer för villkorlig åtkomst i Azure portal

Se:

- [Vad är villkorlig åtkomst i Azure Active Directory](../active-directory-conditional-access-azure-portal.md) – en översikt av villkorlig åtkomst 

- [Snabbstart: Kräva MFA för specifika appar med villkorlig åtkomst i Azure Active Directory](app-based-mfa.md) – om du vill få lite erfarenhet och konfigurerar principer för villkorlig åtkomst. 


## <a name="scenario-description"></a>Scenariobeskrivning

Det kan vara tillräckliga för att du bara behöver dina användare ska kunna autentiseras med ett lösenord för att bemästra balans mellan säkerhet och produktivitet. När ett åtkomstförsök görs från en ej betrodd nätverksplats, det finns dock en ökad risk som inte är inloggningar som utförs av behöriga användare. För att åtgärda det här problemet kan du blockera åtkomstförsök från ej betrodda nätverk. Du kan också kan du också kräva multifaktorautentisering (MFA) för att få tillbaka ytterligare trygghet som ett försök gjordes av är tillförlitligt ägare för kontot. 

Med Azure AD villkorlig åtkomst, kan du lösa det här kravet med en enda princip som tilldelar åtkomst: 

- Till valda molnappar

- För valda användare och grupper  

- Att kräva multifaktorautentisering 

- När åtkomst försök från: 

    - En plats som inte är betrodd


## <a name="considerations"></a>Överväganden

Den stora utmaningen med det här scenariot är att översätta *när ett åtkomstförsök görs från en plats som inte är betrodd* till ett villkor för villkorlig åtkomst. I en princip för villkorlig åtkomst kan du konfigurera den [platser villkor](location-condition.md) kunna hantera scenarier som är relaterade till nätverksplatser. Villkoret platser kan du välja namngivna platser, som representerar logiska grupperingar av IP-adressintervall, länder och regioner.  

Normalt kan äger din organisation en eller flera adressintervall, till exempel 199.30.16.0 - 199.30.16.24.
Du kan konfigurera en namngiven plats genom att:

- Anger det här intervallet (199.30.16.0/24) 

- Tilldela ett beskrivande namn som **företagsnätverket** 


I stället att definiera vilka alla platser är som inte är betrodd, kan du:

- Ta med 

    ![Villkorlig åtkomst](./media/untrusted-networks/02.png)

- Uteslut alla betrodda platser 

    ![Villkorlig åtkomst](./media/untrusted-networks/01.png)



## <a name="implementation"></a>Implementering

Med den metod som beskrivs i den här artikeln kan du nu konfigurera en princip för villkorlig åtkomst för ej betrodda platser. Du bör alltid testa din princip innan den distribueras ut i produktionen för att se till att den fungerar som förväntat. Vi rekommenderar bör om möjligt du göra dina inledande tester i en test-klient. Mer information finns i [hur ska du distribuera en ny princip](best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om villkorlig åtkomst, se [vad är villkorlig åtkomst i Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)