---
title: Så här avblockerar du användare med Azure Active Directory Identity Protection | Microsoft Docs
description: Lär dig hur du avblockerar användare som blockerats av en Azure Active Directory Identity Protection-princip.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c28e30b8e44b6888cdb7416b9c7b563b955a68ce
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335373"
---
# <a name="how-to-unblock-users"></a>Instruktioner: Avblockera användare

Med Azure Active Directory Identity Protection kan du konfigurera principer för att blockera användare om de konfigurerade villkoren är uppfyllda. Vanligt vis kan en blockerad användare Kontakta supportavdelningen för att bli avblockerad. I den här artikeln beskrivs de steg du kan utföra för att avblockera en blockerad användare.

## <a name="determine-the-reason-for-blocking"></a>Ta reda på orsaken till blockeringen

Som ett första steg för att avblockera en användare måste du bestämma vilken typ av princip som har blockerat användaren, eftersom nästa steg är beroende av den.
Med Azure Active Directory Identity Protection kan en användare antingen blockeras av en inloggnings risk princip eller en användar risk princip.

Du kan hämta den typ av princip som har blockerat en användare från rubriken i dialog rutan som visas för användaren under ett inloggnings försök:

| Princip | Användar dialog ruta |
| --- | --- |
| Inloggningsrisk |![Blockerad inloggning](./media/howto-unblock-user/02.png) |
| Användarrisk |![Blockerat konto](./media/howto-unblock-user/104.png) |

En användare som blockeras av:

* En inloggnings risk princip kallas även misstänkt inloggning
* En användar risk princip kallas även ett konto vid risk

## <a name="unblocking-suspicious-sign-ins"></a>Avblockera misstänkta inloggningar

Om du vill avblockera en misstänkt inloggning har du följande alternativ:

1. **Logga in från en välbekant plats eller enhet** – en vanlig orsak till blockerade misstänkta inloggningar är inloggnings försök från okända platser eller enheter. Användarna kan snabbt avgöra om detta är den spärrnings orsaken genom att försöka logga in från en välbekant plats eller enhet.
2. **Exkludera från princip** – om du tror att den aktuella konfigurationen av din inloggnings princip orsakar problem för specifika användare kan du undanta användarna från den. Mer information finns i [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
3. **Inaktivera princip** – om du tror att princip konfigurationen orsakar problem för alla dina användare kan du inaktivera principen. Mer information finns i [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="unblocking-accounts-at-risk"></a>Avblockera konton i risk zonen

Om du vill avblockera ett konto med risk har du följande alternativ:

1. **Återställ lösen ord** – du kan återställa användarens lösen ord. 
2. **Ignorera alla risk händelser** – användar risk principen blockerar en användare om den konfigurerade användar risk nivån för blockerad åtkomst har nåtts. Du kan minska risk nivån för en användare genom att manuellt stänga rapporterade risk händelser. 
3. **Exkludera från princip** – om du tror att den aktuella konfigurationen av din inloggnings princip orsakar problem för specifika användare kan du undanta användarna från den. Mer information finns i [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
4. **Inaktivera princip** – om du tror att princip konfigurationen orsakar problem för alla dina användare kan du inaktivera principen. Mer information finns i [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="next-steps"></a>Nästa steg
 
Vill du veta mer om Azure AD Identity Protection? Kolla [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
