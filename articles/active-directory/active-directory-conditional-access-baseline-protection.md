---
title: Vad är en baslinje-skydd i Azure Active Directory för villkorlig åtkomst | Microsoft Docs
description: Lär dig hur baslinjen protection garanterar att du har minst baslinjen säkerhetsnivån aktiverat i din miljö.
services: active-directory
keywords: villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 25ae4db2cd4f2a2cea74c428a272c6868acaa5c5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35249366"
---
# <a name="what-is-baseline-protection"></a>Vad är baslinje protection?  

Identitet attacker har ökat med 300% under det senaste året. Om du vill skydda från allt större attacker, introducerar en ny funktion som kallas baslinjen skydd i Azure Active Directory (AD Azure). Baslinje skydd är en uppsättning fördefinierade villkorliga åtkomstprinciper. Syftet med dessa principer är att säkerställa att du har minst baslinjen säkerhetsnivån aktiverat i din miljö. 

Du måste aktivera baslinjen principer om du vill aktivera dem i förhandsversionen. Efter allmän tillgänglighet dessa principer är aktiverat som standard. 

Den första baslinjen skydd kräver MFA för privilegierade konton. Angripare som får kontroll över Privilegierade konton kan göra enorm skada, så det är viktigt att skydda dessa konton först. Följande Privilegierade roller finns i omfånget för principen: 

- Global administratör  

- SharePoint-administratör  

- Exchange-administratör  

- Administratör för villkorsstyrd åtkomst  

- Säkerhetsadministratör  


![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/01.png)

## <a name="how-to-get-started"></a>Så här kommer du igång 

Aktivera baslinjeprincip:  

1. Logga in på den [Azure-portalen](https://portal.azure.com) som global administratör, säkerhetsadministratör eller administratör för villkorlig åtkomst.

2. I den **Azure-portalen**, klicka på den vänstra navigeringsfält för, **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/02.png)

3. På den **Azure Active Directory** sidan den **hantera** klickar du på **villkorlig åtkomst**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-baseline-protection/03.png)

4. I listan med principer, klickar du på **baslinjeprincip: kräver MFA för administratörer (förhandsgranskning)**. 

5. Om du vill aktivera principen **använda principen direkt**.

6. Klicka på **Spara**. 
 

Principen baslinjen ger dig möjlighet att undanta användare och grupper. Du kanske vill utesluta ett *[nödsituation åtkomst administratörskonto](active-directory-admin-manage-emergency-access-accounts.md)* att se till att du inte har låsts ute från klienten.
  
 

## <a name="what-you-should-know"></a>Vad du bör känna till 

Directory-roller som ingår i baslinjeprincip är de mest Privilegierade rollerna i Azure AD. Utifrån feedback kan andra ingå i framtiden. 

Om du har konton med administratörsbehörighet i skript, bör du använda [hanteras Service identitet (MSI)](managed-service-identity/overview.md) eller [service principal (med certifikat)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) i stället. Som en tillfällig lösning kan undanta du specifika användarkonton från din baslinjeprincip. 

Principen gäller för äldre autentisering flöden som POP, IMAP, äldre Office desktop client. 

## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst finns [Kom igång med villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö finns i [bästa praxis för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-best-practices.md). 
