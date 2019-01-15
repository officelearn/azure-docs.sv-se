---
title: Licens för Azure Active Directory självåterställning av lösenord
description: Licensieringskrav för återställning av lösenord för självbetjäning av Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 12c75d67d9c58dd80796caac04a6a57303675cba
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54303355"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Återställning av Licensieringskrav för lösenord för självbetjäning i Azure AD

Azure Active Directory (Azure AD) finns i fyra versioner: Kostnadsfri, Basic, Premium P1 och Premium P2. Det finns flera olika funktioner som gör dig lösenordsåterställning via självbetjäning, inklusive ändringen, återställa, låsa upp och tillbakaskrivning av som är tillgängliga i de olika versionerna av Azure AD. Den här artikeln försöker beskriver skillnaderna. Mer information om de funktioner som ingår i varje Azure AD-version kan hittas på den [sidan med priser för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Jämför versioner och funktioner

Lösenord för självbetjäning av Azure AD lösenordsåterställning licensieras per användare, för att underhålla kompatibiliteten organisationer som krävs för att tilldela rätt licens till sina användare.

* Självbetjäning för ändring av lösenord för molnanvändare
   * Jag är en **endast molnbaserad** och vet mitt lösenord.
      * Jag skulle vilja **ändra** mitt lösenord till något nytt.
   * Den här funktionen ingår i alla utgåvor av Azure AD.

* Självbetjäning för återställning av lösenord för molnanvändare
   * Jag är en **endast molnbaserad** och har glömt mitt lösenord.
      * Jag skulle vilja **återställa** mitt lösenord till något som jag vet.
   * Den här funktionen ingår i Azure AD Basic, Premium P1 eller P2 eller Microsoft 365 Business.

* Självbetjäning lösenord återställning/ändring/upplåsning **med lokal tillbakaskrivning**
   * Jag är en **hybrid användaren** mitt lokala Active Directory-användarkonto är synkroniserad med min Azure AD-konto med Azure AD Connect. Jag skulle vilja ändra mitt lösenord, har glömt mitt lösenord eller låsts ute.
      * Jag skulle vilja ändra mitt lösenord eller återställa den till något som jag vet eller låsa upp mitt konto, **och** har att ändra synkroniseras tillbaka till en lokal Active Directory.
   * Den här funktionen ingår i Azure AD Premium P1 eller P2 eller Microsoft 365 Business.

> [!WARNING]
> Fristående Office 365 licensiering planer *stöder inte ”Self Service lösenord återställning/ändring/upplåsning med lokal tillbakaskrivning”* och kräver en plan som innehåller Azure AD Premium P1, Premium P2 eller Microsoft 365 Business för detta funktionen ska fungera.
>

Ytterligare information om licenser går inklusive kostnader, kan hittas på följande sidor:

* [Priser platsen för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory-funktioner och egenskaper](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Tjänstbeskrivning för Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Aktivera grupp eller användarbaserade licensiering

Azure AD nu stöder gruppbaserad licensiering. Administratörer kan tilldela licenser till en grupp med användare i stället för att tilldela dem en i taget. Mer information finns i [tilldela kontrollerar och lösa problem med licenser](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Vissa Microsoft-tjänster är inte tillgängliga på alla platser. Innan en användare kan tilldelas en licens, administratören måste ange den **användningsplats** egenskapen på användaren. Tilldelning av licenser kan göras den **användaren** > **profil** > **inställningar** avsnitt i Azure-portalen. *När du använder gruppen licenstilldelning ärver alla användare utan att användningsplats angetts platsen för katalogen.*

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
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)
