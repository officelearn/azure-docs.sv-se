---
title: Vad är Azure AD-registrerade enheter?
description: Lär dig hur enhetsidentitetshantering kan hjälpa dig att hantera enheter som har åtkomst till resurser i din miljö.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67462764"
---
# <a name="azure-ad-registered-devices"></a>Azure AD-registrerade enheter

Målet med Azure AD-registrerade enheter är att ge användarna stöd för BYOD -scenarierna (Bring Your Own Device) eller mobila enheter. I dessa scenarier kan en användare komma åt organisationens Azure Active Directory-kontrollerade resurser med hjälp av en personlig enhet.

|   | Azure AD-registrerad |
| --- | --- |
| **Definition** | Registrerad på Azure AD utan att organisationskontot behöver logga in på enheten |
| **Primär målgrupp** | Gäller för alla användare med följande kriterier: |
|   | BYOD (Bring Your Own Device) |
|   | Mobila enheter |
| **Äganderätt till enhet** | Användare eller organisation |
| **Operativsystem** | Windows 10, iOS, Android och MacOS |
| **Etablering** | Windows 10 – Inställningar |
|   | iOS/Android – Företagsportal eller Microsoft Authenticator-app |
|   | MacOS – Företagsportal |
| **Alternativ för inloggning på enheten** | Lokala autentiseringsuppgifter för slutanvändare |
|   | lösenord |
|   | Windows Hello |
|   | PIN-KOD |
|   | Biometri eller mönster för andra enheter |
| **Enhetshantering** | Hantering av mobila enheter (exempel: Microsoft Intune) |
|   | Hantering av mobilprogram |
| **De viktigaste funktionerna** | SSO till molnresurser |
|   | Villkorlig åtkomst vid inskrivning i Intune |
|   | Villkorad åtkomst via appskyddsprincip |
|   | Aktiverar telefon inloggning med Microsoft Authenticator app |

![Azure AD-registrerade enheter](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD-registrerade enheter är inloggade på att använda ett lokalt konto som ett Microsoft-konto på en Windows 10-enhet, men dessutom har ett Azure AD-konto kopplat för åtkomst till organisationsresurser. Åtkomsten till resurser i organisationen kan begränsas ytterligare baserat på de Azure AD-konto- och villkorlig åtkomstprinciper som tillämpas på enhetsidentiteten.

Administratörer kan skydda och ytterligare styra dessa Azure AD-registrerade enheter med hjälp av MDM-verktyg (Mobile Device Management) som Microsoft Intune. MDM är ett sätt att framtvinga konfigurationer som kräver att lagring krypteras, lösenordskomplexitet och säkerhetsprogram som hålls uppdaterade. 

Azure AD-registrering kan utföras när du öppnar ett arbetsprogram för första gången eller manuellt med hjälp av menyn Inställningar i Windows 10. 

## <a name="scenarios"></a>Scenarier

En användare i organisationen vill komma åt verktyg för e-post, rapportera ledighet och dra nytta av registrering från sin hemdator. Din organisation har dessa verktyg bakom en policy för villkorlig åtkomst som kräver åtkomst från en Intune-kompatibel enhet. Användaren lägger till sitt organisationskonto och registrerar sin hemdator med Azure AD och de Intune-principer som krävs tillämpas som ger användaren åtkomst till sina resurser.

En annan användare vill komma åt sin organisatoriska e-post på sin personliga Android-telefon som har sina rötter. Ditt företag kräver en kompatibel enhet och har skapat en Intune-efterlevnadsprincip för att blockera alla rotade enheter. Medarbetaren stoppas från att komma åt organisationsresurser på den här enheten.

## <a name="next-steps"></a>Nästa steg

- [Hantera enhetsidentiteter med Azure-portalen](device-management-azure-portal.md)
- [Hantera inaktuella enheter i Azure AD](manage-stale-devices.md)
