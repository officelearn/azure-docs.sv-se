---
title: Återställning av lösen ord för självbetjäning – Azure Active Directory
description: Licens krav för återställning av lösen ord för Azure AD Self-service
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 598f3bd8500a59cd41cc4126915e6cccbd4fb2f3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848569"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Licensierings krav för återställning av lösen ord för självbetjäning i Azure AD

Azure Active Directory (Azure AD) ingår i flera versioner: kostnads fri, Premium P1 och Premium P2. Det finns flera olika funktioner som utgör självbetjäning för återställning av lösen ord, inklusive ändring, återställning, upplåsning och tillbakaskrivning, som är tillgängliga i de olika versionerna av Azure AD. Den här artikeln försöker förklara skillnaderna. Mer information om de funktioner som ingår i varje Azure AD-utgåva finns på [sidan Azure Active Directory priser](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Jämför versioner och funktioner

Återställning av lösen ord för självbetjäning i Azure AD licensieras per användare för att underhålla efterlevnadar krävs för att tilldela sina användare rätt licens.

* Självbetjäning för ändring av lösenord för molnanvändare
   * Jag är **endast molnbaserad användare** och känner till mitt lösen ord.
      * Jag vill **ändra** mitt lösen ord till något nytt.
   * Den här funktionen ingår i alla utgåvor av Azure AD.

* Självbetjäning för återställning av lösenord för molnanvändare
   * Jag är **endast en molnbaserad användare** och har glömt mitt lösen ord.
      * Jag vill **återställa** mitt lösen ord till något som jag känner till.
   * Den här funktionen ingår i Azure AD Premium P1 eller P2, Microsoft 365 Business eller Office 365.

* Lösen ords återställning via självbetjäning/ändra/Lås upp **med lokal tillbakaskrivning**
   * Jag är en **Hybrid användare** som mitt lokalt Active Directory användar kontot är synkroniserat med mitt Azure AD-konto med hjälp av Azure AD Connect. Jag vill ändra mitt lösen ord, ha glömt mitt lösen ord eller vara utelåst.
      * Jag vill ändra mitt lösen ord eller återställa det till något som jag känner till, eller låsa upp mitt konto **och** låta ändringen synkroniseras tillbaka till lokala Active Directory.
   * Den här funktionen ingår i Azure AD Premium P1 eller P2, eller Microsoft 365 Business.

> [!WARNING]
> Fristående Office 365-licens planer *stöder inte "självbetjäning för återställning av lösen ord/ändra/Lås upp med lokal tillbakaskrivning"* och kräver en plan som innehåller Azure AD Premium P1, Premium P2 eller Microsoft 365 Business för att den här funktionen ska fungera.
>

Ytterligare licens information, inklusive kostnader, finns på följande sidor:

* [Azure Active Directory prissättnings webbplats](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory funktioner och funktioner](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Beskrivning av Microsoft 365 Businesss tjänst](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Aktivera grupp-eller användar baserad licensiering

Azure AD stöder nu gruppbaserad licensiering. Administratörer kan tilldela licenser i bulk till en grupp användare i stället för att tilldela dem en i taget. Mer information finns i [tilldela, verifiera och lösa problem med licenser](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Vissa Microsoft-tjänster är inte tillgängliga på alla platser. Innan en licens kan tilldelas en användare måste administratören ange egenskapen för **användnings plats** för användaren. Tilldelning av licenser kan göras i avsnittet **användar** > **profil** > **Inställningar** i Azure Portal. *När du använder grupp licens tilldelning ärver alla användare som saknar en användnings plats platsen för katalogen.*

## <a name="next-steps"></a>Nästa steg

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord för självbetjäning](../user-help/active-directory-passwords-reset-register.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något är brutet. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)
