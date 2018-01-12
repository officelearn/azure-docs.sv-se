---
title: "Licens Självbetjäning för återställning av lösenord – Azure Active Directory"
description: "Azure AD Självbetjäning för lösenordsåterställning licenskrav"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 2ff9f80cfe9991000fdee45421fc74e662a4dfd1
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Återställ Licensieringskrav för självbetjäning Azure AD-lösenord

För lösenordsåterställning till funktionen, Azure Active Directory (AD Azure) du *måste ha minst en licens för i din organisation*. Vi framtvingar inte per användare-licensiering om återställning av lösenord. Du måste tilldela licenser till användare som använder premiumfunktioner för att bibehålla kompatibilitet med ditt Microsoft-licensavtalet.

* **Endast molnbaserad användare**: Office 365 någon betald SKU eller Azure AD Basic
* **Molnet** eller **lokala användare**: Azure AD Premium P1 eller P2, Enterprise Mobility + Security (EMS) eller Microsoft 365

## <a name="licenses-required-for-password-writeback"></a>Licenser som krävs för tillbakaskrivning av lösenord

Om du vill använda tillbakaskrivning av lösenord, måste du ha en av de följande licenser som har tilldelats din klient:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 (Plan E3)
* Microsoft 365 (Plan E5)

> [!WARNING]
> Fristående Office 365-licensiering planer *tillbakaskrivning av lösenord stöder inte* och kräver att du har något av föregående planer för den här funktionen ska fungera.
>

Ytterligare licensinformation, inklusive kostnader, kan hittas på följande sidor:

* [Azure Active Directory priser för platsen](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory-funktioner och funktioner](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Aktivera grupp eller användarbaserade licensiering

Azure AD nu stöder gruppbaserade licensiering. Administratörer kan tilldela licenser gruppvis till en grupp med användare i stället för att tilldela dem en i taget. Mer information finns i [tilldela kontrollerar och lösa problem med licenser](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses).

Vissa Microsoft-tjänster är inte tillgängliga på alla platser. Innan en användare kan tilldelas en licens, administratören måste ange den **användningsplats** egenskapen för användaren. Tilldelning av licenser kan göras den **användare** > **profil** > **inställningar** avsnitt i Azure-portalen. *När du använder gruppen licenstilldelning ärver alla användare utan en användningsplats anges platsen för katalogen.*

## <a name="next-steps"></a>Nästa steg

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](active-directory-passwords-best-practices.md)
* [Återställ eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord för självbetjäning](active-directory-passwords-reset-register.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](active-directory-passwords-data.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](active-directory-passwords-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](active-directory-passwords-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](active-directory-passwords-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](active-directory-passwords-how-it-works.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)
