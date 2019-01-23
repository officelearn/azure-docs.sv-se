---
title: Vad är en skydd vid baslinjen i Azure Active Directory villkorlig åtkomst? – förhandsversion| Microsoft Docs
description: Lär dig hur skydd vid baslinjen säkerställer att du behöver minst baslinje-säkerhetsnivå aktiverad i din Azure Active Directory-miljö.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 67f3299bcf5b852e05308a8e71004fcd613e83cf
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446281"
---
# <a name="what-is-baseline-protection-preview"></a>Vad är skydd vid baslinjen (förhandsversion)?  

Identitetsattacker har ökat med 300% under det senaste året. Om du vill skydda från attacker begripliga, lanserar en ny funktion som kallas skydd vid baslinjen i Azure Active Directory (AD Azure). Skydd vid baslinjen är en uppsättning fördefinierade [principer för villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md). Målet med dessa principer är att säkerställa att du har minst baslinje-säkerhetsnivå aktiverad i alla utgåvor av Azure AD. 

Den här artikeln ger en översikt över skydd vid baslinjen i Azure Active Directory.


 
## <a name="require-mfa-for-admins"></a>Kräva MFA för administratörer

Användare med åtkomst till behöriga konton har obegränsad åtkomst till din miljö. På grund av strömmen dessa konton, ska du hantera dem med särskild försiktighet. En vanlig metod för att förbättra skyddet av Privilegierade konton är att kräva en starkare form av verifiering för kontot när de används för att logga in. I Azure Active Directory, kan du få en starkare Kontoverifiering genom att kräva multifaktorautentisering (MFA).  

**Kräva MFA för administratörer** är en baslinjeprincip som kräver MFA för följande katalogroller: 

- Global administratör  

- SharePoint-administratör  

- Exchange-administratör  

- Administratör för villkorsstyrd åtkomst  

- Säkerhetsadministratör  


![Azure Active Directory](./media/baseline-protection/01.png)

Den här baslinjeprincip ger dig möjlighet att exkludera användare. Du kanske vill utesluta ett *[nödfall åtkomst administratörskonto](../users-groups-roles/directory-emergency-access.md)* att se till att du inte har låsts ute från klienten.


## <a name="enable-a-baseline-policy"></a>Aktivera en baslinjeprincip 

Grundläggande principer finns i förhandsversion, är de som standard inte aktiverat. Du måste manuellt aktivera en princip om du vill aktivera den. Om du uttryckligen aktivera baslinje-principer i förhandsversionsstadium förblir de aktiv när den här funktionen blir allmänt tillgänglig. Vilken beteendeförändring som planerat är orsaken till varför, dessutom för att aktivera och inaktivera måste du ha ett tredje alternativ att ställa in tillståndet för en princip: **Aktivera principen automatiskt i framtiden**. Genom att välja det här alternativet kan du lämna principerna inaktiveras under förhandsgranskning, men låta Microsoft aktivera dem automatiskt när den här funktionen blir allmänt tillgänglig. Om du inte uttryckligen aktiverar baslinje principer nu och inte väljer den **aktivera principen automatiskt i framtiden** alternativet principerna förblir inaktiverad när den här funktionen blir allmänt tillgänglig.


**För att aktivera en baslinjeprincip:**  

1. Logga in på den [Azure-portalen](https://portal.azure.com) som global administratör, säkerhetsadministratör eller administratör för villkorsstyrd åtkomst.

2. I den **Azure-portalen**, i det vänstra navigeringsfältet, klickar du på **Azure Active Directory**.

    ![Azure Active Directory](./media/baseline-protection/02.png)

3. På den **Azure Active Directory** sidan den **Security** klickar du på **villkorlig åtkomst**.

    ![Villkorlig åtkomst](./media/baseline-protection/05.png)

4. I listan med principer, klickar du på en princip som börjar med **baslinjeprincip:**. 

5. Om du vill aktivera principen, klickar du på **Använd principen omedelbart**.

6. Klicka på **Spara**. 
 
  
 

## <a name="what-you-should-know"></a>Det här bör du känna till 

Medan hantera principer för villkorlig åtkomst kräver en Azure AD Premium-licens, är baslinje principer tillgängliga i alla utgåvor av Azure AD.     

Katalogroller som ingår i baslinjeprincip är de mest Privilegierade rollerna för Azure AD. 

Om du har Privilegierade konton som används i dina skript, bör du ersätta dem med [hanterade identiteter för Azure-resurser](../managed-identities-azure-resources/overview.md) eller [tjänsthuvudnamn med certifikat](../develop/howto-authenticate-service-principal-powershell.md). Du kan utesluta specifika användarkonton från baslinje-principen som en tillfällig lösning. 

Grundläggande principer gäller för äldre autentiseringsflöden som POP, IMAP, äldre Office-klientversionen. 




## <a name="next-steps"></a>Nästa steg

Mer information finns i:

- [Fem steg för att skydda din infrastruktur för Identitetshantering](https://docs.microsoft.com/azure/security/azure-ad-secure-steps)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](overview.md) 

