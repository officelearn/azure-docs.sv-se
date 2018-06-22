---
title: Vad är en baslinje-skydd i Azure Active Directory för villkorlig åtkomst? -preview | Microsoft Docs
description: Lär dig hur baslinjen protection garanterar att du har minst baslinjen säkerhetsnivån aktiverat i Azure Active Directory-miljö.
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
ms.date: 06/21/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 86b57a82573760ac73975e851b2bb4caf769845b
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308568"
---
# <a name="what-is-baseline-protection---preview"></a>Vad är baslinje protection? -Förhandsgranskning  

Identitet attacker har ökat med 300% under det senaste året. Om du vill skydda från allt större attacker, introducerar en ny funktion som kallas baslinjen skydd i Azure Active Directory (AD Azure). Baslinje protection är en uppsättning fördefinierade [principer för villkorlig åtkomst](active-directory-conditional-access-azure-portal.md). Syftet med dessa principer är att säkerställa att du har minst baslinjen säkerhetsnivån aktiverat i alla utgåvor av Azure AD. 

Den här artikeln ger en översikt över baslinjen skydd i Azure Active Directory.


 
## <a name="require-mfa-for-admins"></a>Kräva MFA för administratörer

Användare med åtkomst till behöriga konton har obegränsad åtkomst till din miljö. På grund av strömmen har dessa konton, bör du hantera dem med särskild försiktighet. En vanlig metod för att förbättra skyddet av Privilegierade konton är att kräva en säkrare form av verifiering för kontot när de används för att logga in. Du kan få en bättre verifiering för kontot genom att kräva multifaktorautentisering (MFA) i Azure Active Directory.  

**Kräva MFA för administratörer** är en baslinjeprincip som kräver MFA för directory följande roller: 

- Global administratör  

- SharePoint-administratör  

- Exchange-administratör  

- Administratör för villkorsstyrd åtkomst  

- Säkerhetsadministratör  


![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/01.png)

Den här baslinjeprincip ger dig alternativet för att undanta användare och grupper. Du kanske vill utesluta ett *[nödsituation åtkomst administratörskonto](active-directory-admin-manage-emergency-access-accounts.md)* att se till att du inte har låsts ute från klienten.


## <a name="enable-a-baseline-policy"></a>Aktivera en baslinjeprincip 

Grundläggande principer finns i förhandsgranskningen, är de som standard inte aktiverad. Du måste aktivera en princip manuellt om du vill aktivera den. När den här funktionen har nått allmän tillgänglighet, är principerna som standard aktiveras. Planerad Funktionsändring är anledningen till varför du måste dessutom att aktivera och inaktivera ett tredje alternativ för att ställa in tillståndet för en princip: **automatiskt i framtiden aktiverar principen**. Genom att välja det här alternativet kan låta du Microsoft bestämma när du vill aktivera en princip.      


**För att aktivera en baslinjeprincip:**  

1. Logga in på den [Azure-portalen](https://portal.azure.com) som global administratör, säkerhetsadministratör eller administratör för villkorlig åtkomst.

2. I den **Azure-portalen**, klicka på den vänstra navigeringsfält för, **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/02.png)

3. På den **Azure Active Directory** sidan den **hantera** klickar du på **villkorlig åtkomst**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-baseline-protection/03.png)

4. I listan med principer, klickar du på en princip som börjar med **baslinjeprincip:**. 

5. Om du vill aktivera principen **använda principen direkt**.

6. Klicka på **Spara**. 
 


  
 

## <a name="what-you-should-know"></a>Vad du bör känna till 

När du hanterar anpassade villkorliga åtkomstprinciper kräver en Azure AD Premium-licens, är baslinje principer tillgängliga i alla utgåvor av Azure AD.     

Directory-roller som ingår i baslinjeprincip är de mest Privilegierade rollerna i Azure AD. 

Om du har Privilegierade konton som används i skript, bör du ersätta dem med [hanteras Service identitet (MSI)](./managed-service-identity/overview.md) eller [service principal med certifikat](../azure-resource-manager/resource-group-authenticate-service-principal.md). Som en tillfällig lösning kan utesluta du specifika användarkonton från principen för baslinjen. 

Grundläggande principer tillämpas äldre autentisering flöden som POP, IMAP, äldre Office desktop client. 




## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst finns [Kom igång med villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö finns i [bästa praxis för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-best-practices.md). 
