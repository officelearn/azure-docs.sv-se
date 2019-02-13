---
title: Hur du avblockerar användare med Azure Active Directory Identity Protection | Microsoft Docs
description: Lär dig hur avblockera användare som har blockerats av en Azure Active Directory Identity Protection-princip.
services: active-directory
keywords: Azure active directory identity protection kan avblockera användare
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: a953d425-a3ef-41f8-a55d-0202c3f250a7
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8dd27e022524e3dd6211591104020f81124245c3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209565"
---
# <a name="how-to-unblock-users"></a>Instruktioner: Avblockera användare

Med Azure Active Directory Identity Protection kan konfigurera du principer för att blockera användare om de angivna villkoren är uppfyllda. Vanligtvis en blockerad användare kontakter supportavdelningen att bli avblockerad. Den här artikeln beskrivs de steg som du kan utföra för att låsa upp en blockerad användare.

## <a name="determine-the-reason-for-blocking"></a>Ta reda på varför för blockering
Som ett första steg för att avblockera en användare, måste du bestämmer vilken typ av princip som har blockerats användaren eftersom nästa steg är beroende av den.
Med Azure Active Directory Identity Protection, kan en användare antingen blockeras av en princip för inloggninsrisk- eller en princip för användarrisk.

Du kan få typ av princip som har blockerat en användare från rubriken i den dialogruta som angavs för användaren under en inloggningsförsök:

| Princip | Användardialogrutan |
| --- | --- |
| Inloggningsrisk |![Blockerade inloggning](./media/howto-unblock-user/02.png) |
| Användarrisk |![Blockerade konto](./media/howto-unblock-user/104.png) |

En användare som har blockerats av:

* En princip för inloggningsrisk är även känd som misstänkt inloggning
* En princip för användarrisk kallas även för ett konto i fara

## <a name="unblocking-suspicious-sign-ins"></a>Avblockera misstänkta inloggningar
För att låsa upp en misstänkt inloggning, har du följande alternativ:

1. **Logga in från en bekant plats eller enhet** – en vanlig orsak till blockerade misstänkta inloggningar är inloggningsförsök från okända platser eller enheter. Användarna kan snabbt se om detta är orsaken till blockerar genom att logga in från en bekant plats eller enhet.
2. **Undanta från principen** – om du tror att den aktuella konfigurationen av din inloggningsprincip orsakar problem för specifika användare, du kan undanta användare från den. Mer information finns i [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
3. **Inaktivera principen** – om du tycker att din principkonfiguration orsakar problem för alla användare kan du inaktivera principen. Mer information finns i [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="unblocking-accounts-at-risk"></a>Avblockera konton i risk
För att låsa upp ett konto i fara, har du följande alternativ:

1. **Återställ lösenord** – du kan återställa användarens lösenord. 
2. **Stäng alla riskhändelser** – användaren risk principen blockerar en användare om den konfigurerade användaren risknivå för blockering av åtkomst har uppnåtts. Du kan minska en användare är risknivå manuellt stänger rapporterade riskhändelser. 
3. **Undanta från principen** – om du tror att den aktuella konfigurationen av din inloggningsprincip orsakar problem för specifika användare, du kan undanta användare från den. Mer information finns i [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
4. **Inaktivera principen** – om du tycker att din principkonfiguration orsakar problem för alla användare kan du inaktivera principen. Mer information finns i [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="next-steps"></a>Nästa steg
 
Vill du veta mer om Azure AD Identity Protection? Kolla in [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
