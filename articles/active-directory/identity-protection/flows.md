---
title: Inloggnings upplevelser med Azure AD Identity Protection | Microsoft Docs
description: Ger en översikt över användar upplevelsen när identitets skydd har minimerat eller reparerat en användare eller när Multi-Factor Authentication krävs av en princip.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e513027eed44ec7649f41f8786882aed8511bc6
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335491"
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Inloggnings upplevelser med Azure AD Identity Protection

Med Azure Active Directory Identity Protection kan du:

* Kräv att användare registrerar sig för Multi-Factor Authentication
* hantera riskfyllda inloggningar och komprometterade användare

Svar på systemet för dessa problem har en inverkan på användarens inloggnings upplevelse, eftersom inloggningen av ett användar namn och lösen ord inte längre är möjlig. Ytterligare steg krävs för att få en användare att vara säker på verksamheten.

Den här artikeln ger en översikt över en användares inloggnings upplevelse för alla fall som kan uppstå.

**Multi-Factor Authentication**

* Multi-Factor Authentication-registrering

**Inloggning i risk**

* Återställning av riskfyllda inloggningar
* Riskfylld inloggning blockerad
* Registrering av Multi-Factor Authentication under en riskfylld inloggning

**Användare i risk**

* Komprometterad konto återställning
* Komprometterat konto blockerat

## <a name="multi-factor-authentication-registration"></a>Multi-Factor Authentication-registrering
Den bästa användar upplevelsen för båda, det komprometterade konto återställnings flödet och det riskfyllda inloggnings flödet, är när användaren kan återställa sig själv. Om användare har registrerats för Multi-Factor Authentication har de redan ett telefonnummer som är kopplat till sitt konto som kan användas för att klara säkerhets utmaningar. Ingen support-eller administratörs inblandning krävs för att återställa mot konto kompromisser. Därför rekommenderar vi starkt att du får dina användare registrerade för Multi-Factor Authentication. 

Administratörer kan ange en princip som kräver att användare konfigurerar sina konton för ytterligare säkerhets verifiering. Med den här principen kan användarna hoppa över Multi-Factor Authentication-registreringen i upp till 14 dagar. Det går inte att konfigurera den 14 dagariga Grace-perioden.

**Multi-Factor Authentication-registreringen har tre steg:**

1. I det första steget får användaren ett meddelande om kravet att ställa in kontot för Multi-Factor Authentication. 
   
    ![Reparation](./media/flows/140.png "Reparation")
2. Om du vill konfigurera Multi-Factor Authentication måste du låta systemet veta hur du vill kontaktas.
   
    ![Reparation](./media/flows/141.png "Reparation")
3. Systemet skickar en utmaning till dig och du måste svara.
   
    ![Reparation](./media/flows/142.png "Reparation")

## <a name="risky-sign-in-recovery"></a>Återställning av riskfyllda inloggningar
När en administratör har konfigurerat en princip för inloggnings risker, meddelas de berörda användarna när de försöker logga in. 

**Det riskfyllda inloggnings flödet har två steg:** 

1. Användaren informeras om att något ovanlig upptäcktes om deras inloggning, till exempel att logga in från en ny plats, enhet eller app. 
   
    ![Reparation](./media/flows/120.png "Reparation")
2. Användaren måste bevisa sin identitet genom att lösa en säkerhets utmaning. Om användaren är registrerad för Multi-Factor Authentication måste de avrunda en säkerhets kod till sitt telefonnummer. Eftersom detta bara är en riskfylld inloggning och inte ett komprometterat konto behöver användaren inte ändra lösen ordet i det här flödet. 
   
    ![Reparation](./media/flows/121.png "Reparation")

## <a name="risky-sign-in-blocked"></a>Riskfylld inloggning blockerad
Administratörer kan också välja att ange en inloggnings risk princip för att blockera användare vid inloggning, beroende på risk nivån. Slutanvändare måste kontakta en administratör eller supportavdelningen, annars kan de försöka logga in från en välbekant plats eller enhet. Att lösa genom att lösa Multi-Factor Authentication är inte ett alternativ i det här fallet.

![Reparation](./media/flows/200.png "Reparation")

## <a name="compromised-account-recovery"></a>Komprometterad konto återställning
När en säkerhets princip för en användar risk har kon figurer ATS måste användare som uppfyller användar risk nivån som anges i principen (och därför anses komprometterade) gå igenom användarens komprometterade återställnings flöde innan de kan logga in. 

**Användarens komprometterade återställnings flöde har tre steg:**

1. Användaren informeras om att deras konto säkerhet är utsatt för risk på grund av misstänkt aktivitet eller läcka autentiseringsuppgifter.
   
    ![Reparation](./media/flows/101.png "Reparation")
2. Användaren måste bevisa sin identitet genom att lösa en säkerhets utmaning. Om användaren är registrerad för Multi-Factor Authentication kan de själv återställas. De måste avrunda en säkerhets kod till sitt telefonnummer. 
   
   ![Reparation](./media/flows/110.png "Reparation")
3. Användaren måste slutligen ändra sitt lösen ord eftersom någon annan kan ha haft åtkomst till sitt konto. 
   Skärm dum par av den här upplevelsen finns nedan.
   
   ![Reparation](./media/flows/111.png "Reparation")

## <a name="compromised-account-blocked"></a>Komprometterat konto blockerat
Användaren måste kontakta en administratör eller supportavdelningen för att få en användare som blockerats av en säkerhets princip för användare som har avblockerats. Att lösa genom att lösa Multi-Factor Authentication är inte ett alternativ i det här fallet.

![Reparation](./media/flows/104.png "Reparation")

## <a name="reset-password"></a>Återställ lösenord
Om komprometterade användare blockeras från att logga in kan en administratör generera ett tillfälligt lösen ord för dem. Användarna måste ändra sina lösen ord vid nästa inloggning.

![Reparation](./media/flows/160.png "Reparation")

## <a name="see-also"></a>Se också

* [Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md) 
