---
title: Vad är Azure AD-registrerade enheter?
description: Lär dig hur enheten identity management kan hjälpa dig att hantera enheter som har åtkomst till resurser i din miljö.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e2a8cad7cd4410a95a6ebd60ada22de456737bf
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462764"
---
# <a name="azure-ad-registered-devices"></a>Azure AD-registrerade enheter

Målet med Azure AD-registrerade enheter är att ge dina användare med stöd för Bring Your Own Device (BYOD) eller scenarier med mobila enheter. I dessa scenarier kan en användare kan komma åt din organisations Azure Active Directory kontrolleras resurser med hjälp av en personlig enhet.

|   | Azure AD-registrerad |
| --- | --- |
| **Definition** | Registrerad till Azure AD utan organisationskonto för att logga in på enheten |
| **Primära målgruppen** | Gäller för alla användare med följande kriterier: |
|   | Ta med din egen enhet (BYOD) |
|   | Mobila enheter |
| **Ägarskap för enhet** | Användaren eller organisationen |
| **Operativsystem** | Windows 10, iOS, Android och MacOS |
| **Etablering** | Windows 10 – inställningar |
|   | iOS/Android – företagets Portal eller Microsoft Authenticator-appen |
|   | MacOS-Företagsportalen |
| **Enheten inloggningsalternativ** | Slutanvändaren lokala autentiseringsuppgifter |
|   | Lösenord |
|   | Windows Hello |
|   | PIN |
|   | Biometrik eller ett mönster för andra enheter |
| **Enhetshantering** | Hantering av mobila enheter (exempel: Microsoft Intune) |
|   | Hantering av mobilprogram |
| **Viktiga funktioner** | Enkel inloggning till molnresurser |
|   | Villkorlig åtkomst efter att ha registrerats i Intune |
|   | Villkorlig åtkomst via appskyddsprincip |
|   | Aktiverar Phone logga in med Microsoft Authenticator-appen |

![Azure AD-registrerade enheter](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD-registrerade enheter har loggat in med ett lokalt konto som ett Microsoft-konto på en Windows 10-enhet, men har även en Azure AD-konto som är anslutna för åtkomst till företagsresurser. Åtkomst till resurser i organisationen kan vara mer begränsad baserat på det Azure AD-kontot och principer för villkorlig åtkomst tillämpas på enhetens identitet.

Administratörer kan skydda och ytterligare Kontrollera enheterna Azure AD-registrerad med Mobile Device Management (MDM)-verktyg som Microsoft Intune. MDM innebär att tillämpa organisation krävs konfigurationer som kräver lagring krypteras, lösenordens komplexitet och säkerhetsprogram hålls uppdaterade. 

Azure AD-registrering kan utföras vid åtkomst till ett program för arbete för första gången eller manuellt med hjälp av menyn Inställningar för Windows 10. 

## <a name="scenarios"></a>Scenarier

En användare i din organisation vill komma åt verktyg för e-post, rapportering semester och fördelar registreringen från sina home PC-datorer. Din organisation har dessa verktyg bakom en villkorlig åtkomstprincip som kräver åtkomst från en kompatibel enhet i Intune. Användaren lägger till deras organisation och registrerar sina home PC-datorer med Azure AD och de nödvändiga Intune-principerna tillämpas ger användaråtkomst till sina resurser.

En annan användare vill få åtkomst till sin organisations e-post på sina personliga Android-telefon som har blivit rotad. Ditt företag kräver en kompatibel enhet och har skapat en efterlevnadsprincip i Intune för att blockera alla rotade enheter. Medarbetaren har stoppats från att komma åt företagsresurser på den här enheten.

## <a name="next-steps"></a>Nästa steg

- [Hantera enhetsidentiteter med Azure portal](device-management-azure-portal.md)
- [Hantera inaktuella enheter i Azure AD](manage-stale-devices.md)
