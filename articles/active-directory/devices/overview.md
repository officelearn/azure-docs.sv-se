---
title: Vad är enhetens identitet i Azure Active Directory?
description: Lär dig hur enheten identity management kan hjälpa dig att hantera enheter som har åtkomst till resurser i din miljö.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d38ca8bdf93ff201b3f5842f4cb0e8409dcd0c3
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481663"
---
# <a name="what-is-a-device-identity"></a>Vad är en enhetsidentitet?

Med den ökande mängden av enheter i alla former och storlekar och Bring Your Own Device (BYOD)-konceptet inför IT-proffs två något helt motsatt mål:

- Tillåt användare att vara produktiva oavsett tid och plats
- Skydda organisationens tillgångar

För att skydda dessa tillgångar, måste IT-personal att först hantera enhetsidentiteter. IT-personal kan skapa på enhetens identitet med verktyg som Microsoft Intune för att se till att standarder för säkerhet och efterlevnad är uppfyllda. Azure Active Directory (Azure AD) möjliggör enkel inloggning till enheter, appar och tjänster från var som helst via dessa enheter.

- Användarna får åtkomst till organisationens resurser som de behöver. 
- IT-personalen får de kontroller som de behöver för att skydda din organisation.

Identitetshantering för enheten är grunden för [enhetsbaserad villkorlig åtkomst](../conditional-access/require-managed-devices.md). Med principer för enhetsbaserad villkorlig åtkomst kan du se till att åtkomsten till resurser i din miljö är endast möjlig med hanterade enheter.

## <a name="getting-devices-in-azure-ad"></a>Hämta enheter i Azure AD

Om du vill ha en enhet i Azure AD, har du flera alternativ:

- **Azure AD-registrerad**
   - Enheter som är Azure AD-registrerad är vanligtvis personligt eller mobila enheter och är inloggad med ett personligt microsoftkonto eller en annan lokalt konto.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Azure AD-ansluten**
   - Enheter som är Azure AD-anslutna ägs av en organisation och är inloggad på med ett Azure AD-konto som hör till organisationen. De finns bara i molnet.
      - Windows 10 
- **Hybrid Azure AD-ansluten**
   - Enheter som är hybrid Azure AD-anslutna ägs av en organisation och är inloggad på med ett Azure AD-konto som hör till organisationen. De finns i molnet och 
      - Windows 7, 8.1 och 10
      - Windows Server 2008 eller senare

![Enheter som visas i bladet för Azure AD-enheter](./media/overview/azure-ad-devices-all-devices-overview.png)

## <a name="device-management"></a>Enhetshantering

Enheter i Azure AD kan hanteras med hjälp av Mobile Device Management (MDM)-verktyg som Microsoft Intune, System Center Configuration Manager, en Grupprincip (hybrid Azure AD-anslutning), Mobile Application Management (MAM) verktyg eller andra verktyg från tredje part.

## <a name="resource-access"></a>Resursåtkomst

Registrera och ansluta till ge användarna sömlös inloggning (SSO) till molnet resurser och administratörer möjligheten att använda principer för villkorlig åtkomst för dessa resurser. 

Enheter som är ansluten till Azure AD eller hybrid Azure AD ansluten förmånen från enkel inloggning till organisationens lokala resurser och molnresurser. Mer information finns i artikeln [hur SSO till lokala resurser fungerar i Azure AD-anslutna enheter](azuread-join-sso.md).

## <a name="device-security"></a>Enhetssäkerhet

- **Azure AD-registrerade enheter** använda ett konto som hanteras av användaren, det här kontot är ett Microsoft-konto eller en annan lokalt hanterade autentiseringsuppgifter som skyddas med en eller flera av följande.
   - Lösenord
   - PIN
   - Mönster
   - Windows Hello
- **Azure AD-anslutna eller Azure AD-anslutna hybridenheter** använda ett organisationskonto i Azure AD som skyddas med en eller flera av följande.
   - Lösenord
   - Windows Hello för företag

## <a name="provisioning"></a>Etablering

Skaffa enheter i Azure AD kan göras på ett sätt som självbetjäning eller en kontrollerad etableringen av administratörer.

## <a name="summary"></a>Sammanfattning

Med identity enhetshantering i Azure AD kan du:

- Förenkla processen att föra och hantering av enheter i Azure AD
- Ge användarna enkel åtkomst till organisationens molnbaserade resurser

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure AD-registrerade enheter](concept-azure-ad-register.md)
- Läs mer om [Azure AD-anslutna enheter](concept-azure-ad-join.md)
- Läs mer om [hybrid Azure AD-anslutna enheter](concept-azure-ad-join-hybrid.md)
- Om du vill få en översikt över hur du hanterar enhetsidentiteter i Azure-portalen, se [hantera enhetsidentiteter med Azure portal](device-management-azure-portal.md).
- Läs mer om enhetsbaserad villkorlig åtkomst i [principer för Konfigurera Azure Active Directory-enhetsbaserad villkorlig åtkomst](../conditional-access/require-managed-devices.md).
