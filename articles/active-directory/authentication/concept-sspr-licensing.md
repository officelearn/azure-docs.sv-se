---
title: Återställning av lösenord för licens - Azure Active Directory
description: Licenskrav för azure AD-licensiering för återställning av lösenordsåterbeställning
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848569"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Licenskrav för azure ad-återställning av lösenord för självbetjäning

Azure Active Directory (Azure AD) finns i flera versioner: Gratis, Premium P1 och Premium P2. Det finns flera olika funktioner som utgör återställning av lösenord för självbetjäning, inklusive ändring, återställning, upplåsning och tillbakaskrivning, som är tillgängliga i de olika utgåvorna av Azure AD. Denna artikel försöker förklara skillnaderna. Mer information om de funktioner som ingår i varje Azure AD-utgåva finns på [azure Active Directory-prissidan](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Jämför utgåvor och funktioner

Azure AD-självbetjäningslösenordsåterställning licensieras per användare för att upprätthålla efterlevnadsorganisationer krävs för att tilldela rätt licens till sina användare.

* Lösenordsändring för självbetjäning för molnanvändare
   * Jag är en **användare som bara är molnet** och känner till mitt lösenord.
      * Jag skulle vilja **ändra** mitt lösenord till något nytt.
   * Den här funktionen ingår i alla versioner av Azure AD.

* Återställning av lösenord för självbetjäning för molnanvändare
   * Jag är en **användare som endast är molnet** och har glömt mitt lösenord.
      * Jag skulle vilja **återställa** mitt lösenord till något jag vet.
   * Den här funktionen ingår i Azure AD Premium P1 eller P2, Microsoft 365 Business eller Office 365.

* Återställning/ändring/låsning/låsning/upplåsning **med lokal tillbakaskrivning**
   * Jag är en **hybridanvändare** mitt lokala Active Directory-användarkonto synkroniseras med mitt Azure AD-konto med Azure AD Connect. Jag skulle vilja ändra mitt lösenord, har glömt mitt lösenord eller blivit utelåst.
      * Jag skulle vilja ändra mitt lösenord eller återställa det till något jag vet, eller låsa upp mitt konto, **och** har den ändringen synkroniseras tillbaka till lokala Active Directory.
   * Den här funktionen ingår i Azure AD Premium P1 eller P2 eller Microsoft 365 Business.

> [!WARNING]
> Fristående Office 365-licensieringsplaner *stöder inte "Självbetjäningslösenordsåterställning/ändring/låsning med lokal tillbakaskrivning"* och kräver ett abonnemang som innehåller Azure AD Premium P1, Premium P2 eller Microsoft 365 Business för att den här funktionen ska fungera.
>

Ytterligare licensinformation, inklusive kostnader, finns på följande sidor:

* [Prisplats för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Funktioner och funktioner i Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Företag](https://www.microsoft.com/microsoft-365/enterprise)
* [Beskrivning av Microsoft 365 Business-tjänsten](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Aktivera grupp- eller användarbaserad licensiering

Azure AD stöder nu gruppbaserad licensiering. Administratörer kan tilldela licenser i grupp av användare i grupp i stället för att tilldela dem en i taget. Mer information finns i [Tilldela, verifiera och lösa problem med licenser](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Vissa Microsoft-tjänster är inte tillgängliga på alla platser. Innan en licens kan tilldelas en användare måste administratören ange egenskapen **Användningsplats** för användaren. Tilldelning av licenser kan göras under avsnittet > **Användarprofilinställningar** > **Settings** i Azure-portalen. **User** *När du använder grupplicenstilldelning ärver alla användare utan angiven användningsplats platsen för katalogen.*

## <a name="next-steps"></a>Nästa steg

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställa eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord med självbetjäning](../user-help/active-directory-passwords-reset-register.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något är trasigt. Hur felsöker jag SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)
