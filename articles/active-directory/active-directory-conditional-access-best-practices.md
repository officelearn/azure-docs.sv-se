---
title: Bästa praxis för villkorlig åtkomst i Azure Active Directory | Microsoft Docs
description: Lär dig mer om saker du bör känna till och vad det är bör du undvika detta när du konfigurerar principer för villkorlig åtkomst.
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
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 88e6b1a64d4ceff64c1ee74a297e7af0c5a27379
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232597"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Metodtips för villkorlig åtkomst i Azure Active Directory

Med [villkorlig åtkomst i Azure Active Directory (AD Azure)](active-directory-conditional-access-azure-portal.md), du kan styra hur behöriga användare åtkomst till dina molnappar. Den här artikeln innehåller information om:

- Saker du bör känna till 
- Vad bör du inte göra när du konfigurerar principer för villkorlig åtkomst. 

Den här artikeln förutsätter att du känner koncept och termer som beskrivs i [vad är villkorlig åtkomst i Azure Active Directory?](active-directory-conditional-access-azure-portal.md)



## <a name="whats-required-to-make-a-policy-work"></a>Vad har krävs för att skapa en princip för att fungera?

När du skapar en ny princip, finns det inga användare, grupper, appar eller åtkomstkontroller som valts.

![Molnappar](./media/active-directory-conditional-access-best-practices/02.png)


Om du vill att din princip för att fungera, måste du konfigurera:


|Vad           | Hur                                  | Varför|
|:--            | :--                                  | :-- |
|**Molnappar** |Du måste välja en eller flera appar.  | Målet med en princip för villkorlig åtkomst är så att du kan styra hur behöriga användare har åtkomst till molnappar.|
| **Användare och grupper** | Du måste välja minst en användare eller grupp som har behörighet att komma åt dina valda molnappar. | En villkorlig åtkomstprincip som har ingen användare och grupper som har tilldelats, utlöses aldrig. |
| **Åtkomstkontroll** | Du måste välja minst en åtkomstkontroll. | Om villkoren uppfylls måste princip processorn vet vad du gör.|




## <a name="what-you-should-know"></a>Vad du bör känna till

### <a name="how-are-assignments-evaluated"></a>Hur utvärderas tilldelningar?

Alla tilldelningar som är logiskt **and**. Om du har mer än en tilldelning konfigurerats måste alla tilldelningar uppfyllas för att utlösa en princip.  

Om du behöver konfigurera en plats-villkor som gäller för alla anslutningar från utanför företagets nätverk:

- Inkludera **alla platser**
- Undanta **alla betrodda IP-adresser**


### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Vad du gör om du har låsts ute från Azure AD administrationsportal?

Om du har låsts ute från Azure AD-portalen på grund av en felaktig inställning i en princip för villkorlig åtkomst:

- Kontrollera om det finns andra administratörer i din organisation som inte är spärrade ännu. En administratör med åtkomst till Azure-portalen kan inaktivera den princip som viktig för inloggning. 

- Om ingen av administratörer i din organisation kan uppdatera principen, måste du skicka en supportförfrågan. Microsoft support kan granska och uppdatera principer för villkorlig åtkomst som hindrar åtkomst.


### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Vad händer om du har principer i den klassiska Azure-portalen och Azure-portalen konfigurerad?  

Båda principerna tillämpas av Azure Active Directory och användaren får åtkomst till endast när alla krav är uppfyllda.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Vad händer om du har principer i Intune Silverlight-portalen och Azure portal?

Båda principerna tillämpas av Azure Active Directory och användaren får åtkomst till endast när alla krav är uppfyllda.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Vad händer om jag har flera principer för samma användare konfigurerade?  

För varje inloggning, Azure Active Directory utvärderar alla principer och garanterar att alla krav är uppfyllda innan du beviljar åtkomst till användaren.


### <a name="does-conditional-access-work-with-exchange-activesync"></a>Fungerar villkorlig åtkomst med Exchange ActiveSync

Ja, du kan använda Exchange ActiveSync i en princip för villkorlig åtkomst.






## <a name="what-you-should-avoid-doing"></a>Vad du bör undvika att göra

Villkorlig åtkomst framework ger dig en utmärkt konfigurationsflexibilitet. Stor flexibilitet innebär dock också bör du noggrant granska varje princip för konfiguration innan du släpper den för att undvika oönskade resultat. I den här kontexten bör du vara särskilt uppmärksam på tilldelningar som påverkar fullständiga **alla användare / grupper / molnappar**.

I din miljö bör du undvika följande konfigurationer:


**För alla användare alla molnappar:**

- **Blockera åtkomst** -den här konfigurationen blockerar hela organisationen, som definitivt inte är en bra idé.

- **Kräver en kompatibel enhet** – för användare som inte har registrerat sina enheter än den här principen blockerar all åtkomst, inklusive åtkomst till Intune-portalen. Om du är administratör saknar en registrerad enhet blockerar du från att få tillbaka till Azure portal för att ändra principen i den här principen.

- **Kräva domänanslutning** – den här principen blockera åtkomst har också möjlighet att blockera åtkomst för alla användare i organisationen om du inte har en domänansluten enhet ännu.


**För alla användare, alla molnappar, alla enhetsplattformar:**

- **Blockera åtkomst** -den här konfigurationen blockerar hela organisationen, som definitivt inte är en bra idé.


## <a name="how-should-you-deploy-a-new-policy"></a>Hur ska du distribuera en ny princip?

Som ett första steg bör du utvärdera principen med hjälp av den [vad händer om verktyget](active-directory-conditional-access-whatif.md).

När du är redo att distribuera en ny princip i din miljö, bör du göra detta i faser:

1. Tillämpa en princip till en liten uppsättning användare och kontrollera att den fungerar som förväntat. 

2.  När du har expanderat en princip för att inkludera flera användare kan fortsätta att undanta alla administratörer från principen. Detta säkerställer att administratörer fortfarande ha åtkomst och kan uppdatera en princip om en ändring krävs.

3. Tillämpa en princip för alla användare bara om det verkligen behövs. 

Skapa ett användarkonto som är ett bra tips:

- Dedikerad till administration av principer 
- Exkluderad från alla dina principer


## <a name="policy-migration"></a>Migrering av princip

Bör du migrera de principer som du inte har skapat i Azure-portalen eftersom:

- Du kan nu hantera scenarier som du inte kan hantera innan.

- Du kan minska antalet principer som du måste hantera genom att konsolidera dem.   

- Du kan hantera alla dina principer för villkorlig åtkomst på en central plats.

- Den klassiska Azure-portalen har tagits bort.   


Mer information finns i [migrera klassiska principer i Azure portal](active-directory-conditional-access-migration.md).


## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst finns [kräver MFA för specifika appar med Azure Active Directory för villkorlig åtkomst](active-directory-conditional-access-app-based-mfa.md).
