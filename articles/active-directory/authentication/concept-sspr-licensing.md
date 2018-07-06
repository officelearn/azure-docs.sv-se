---
title: Licens lösenordsåterställning via självbetjäning – Azure Active Directory
description: Licensieringskrav för återställning av lösenord för självbetjäning av Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 64c12177e5cf6c82018731b493c0da22e1895b7f
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855532"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Återställning av Licensieringskrav för lösenord för självbetjäning i Azure AD

För Azure Active Directory (Azure AD)-lösenordsåterställning ska fungera du *måste ha minst en engångslicens tilldelad inom organisationen* för användaren. En riktig licens krävs om en användare direkt eller indirekt utnyttjar någon funktion som omfattas av den licensen.

* **Molnexklusiva användare**: Office 365 någon betalda SKU: N eller Azure AD Basic
* **Molnet** eller **lokala användare**: Azure AD Premium P1 eller P2, Enterprise Mobility + Security (EMS) eller Microsoft 365

## <a name="licensing-requirements-for-password-writeback"></a>Licensieringskrav för tillbakaskrivning av lösenord

**Självbetjäning lösenord återställning/ändring/upplåsning med lokal tillbakaskrivning är en premiumfunktion i Azure AD**. Mer information om licensiering finns i den [priser platsen för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Om du vill använda tillbakaskrivning av lösenord, måste du ha en av de följande licenser som tilldelats på din klient:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 eller A3
* Enterprise Mobility + Security E5 eller A5
* Microsoft 365 E3 eller A3
* Microsoft 365 E5 eller A5
* Microsoft 365 F1

> [!WARNING]
> Fristående Office 365 licensiering planer *inte har stöd för tillbakaskrivning av lösenord* och kräver att du har en av de föregående prenumerationerna för den här funktionen ska fungera.
>

Ytterligare information om licenser går inklusive kostnader, kan hittas på följande sidor:

* [Priser platsen för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory-funktioner och egenskaper](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Aktivera grupp eller användarbaserade licensiering

Azure AD nu stöder gruppbaserad licensiering. Administratörer kan tilldela licenser till en grupp med användare i stället för att tilldela dem en i taget. Mer information finns i [tilldela kontrollerar och lösa problem med licenser](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Vissa Microsoft-tjänster är inte tillgängliga på alla platser. Innan en användare kan tilldelas en licens, administratören måste ange den **användningsplats** egenskapen på användaren. Tilldelning av licenser kan göras den **användaren** > **profil** > **inställningar** avsnitt i Azure-portalen. *När du använder gruppen licenstilldelning ärver alla användare utan att användningsplats angetts platsen för katalogen.*

## <a name="next-steps"></a>Nästa steg

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösenord](../active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord för självbetjäning](../active-directory-passwords-reset-register.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)
