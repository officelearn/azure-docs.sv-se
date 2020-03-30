---
title: Vad är enhetsidentitet i Azure Active Directory?
description: Lär dig hur enhetsidentitetshantering kan hjälpa dig att hantera de enheter som har åtkomst till resurser i din miljö.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 03/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c895a13eb9b2bed9e3a8a5a250c4e925dfa834c5
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80331814"
---
# <a name="what-is-a-device-identity"></a>Vad är en enhetsidentitet?

Med spridningen av enheter av alla former och storlekar och Bring Your Own Device (BYOD) koncept, IT-proffs står inför två något motsatta mål:

- Låt slutanvändarna vara produktiva var och när
- Skydda organisationens tillgångar

För att skydda dessa tillgångar måste IT-personal först hantera enhetsidentiteterna. IT-personal kan bygga vidare på enhetens identitet med verktyg som Microsoft Intune för att säkerställa att standarder för säkerhet och efterlevnad uppfylls. Azure Active Directory (Azure AD) möjliggör enkel inloggning på enheter, appar och tjänster var som helst via dessa enheter.

- Användarna får åtkomst till organisationens tillgångar de behöver. 
- IT-personalen får de kontroller de behöver för att skydda din organisation.

Enhetsidentitetshantering är grunden för [enhetsbaserad villkorlig åtkomst](../conditional-access/require-managed-devices.md). Med enhetsbaserade principer för villkorlig åtkomst kan du se till att åtkomst till resurser i din miljö endast är möjlig med hanterade enheter.

## <a name="getting-devices-in-azure-ad"></a>Hämta enheter i Azure AD

För att få en enhet i Azure AD har du flera alternativ:

- **Azure AD-registrerad**
   - Enheter som är Azure AD-registrerade ägs vanligtvis personligen eller mobila enheter och är inloggade med ett personligt Microsoft-konto eller ett annat lokalt konto.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Azure AD-ansluten**
   - Enheter som är Azure AD-anslutna ägs av en organisation och är inloggade med ett Azure AD-konto som tillhör den organisationen. De finns bara i molnet.
      - Windows 10 
      - Windows Server 2019 (Serverkärna stöds inte)
- **Hybrid Azure AD-ansluten**
   - Enheter som är hybrid Azure AD-anslutna ägs av en organisation och är inloggade med ett Azure AD-konto som tillhör den organisationen. De finns i molnet och lokalt.
      - Windows 7, 8.1 eller 10
      - Windows Server 2008 eller nyare

![Enheter som visas i azure AD-enheter](./media/overview/azure-active-directory-devices-all-devices.png)

## <a name="device-management"></a>Enhetshantering

Enheter i Azure AD kan hanteras med hjälp av MDM-verktyg (Mobile Device Management) som Microsoft Intune, Microsoft Endpoint Configuration Manager, Group Policy (hybrid Azure AD join), MAM-verktyg (Mobile Application Management) eller andra verktyg från tredje part.

## <a name="resource-access"></a>Tillgång till resurser

Om du registrerar och ansluter till enheter till Azure AD får användarna sömlös inloggning (SSO) till molnresurser. Den här processen ger också administratörer möjlighet att tillämpa principer för villkorlig åtkomst på resurser baserat på den enhet de används från. 

> [!NOTE]
> Enhetsbaserade principer för villkorlig åtkomst kräver antingen hybrid Azure AD-anslutna enheter eller kompatibla Azure AD-anslutna eller Azure AD-registrerade enheter.

Enheter som är Azure AD-anslutna eller hybrid Azure AD-anslutna dra nytta av SSO till organisationens lokala resurser samt molnresurser. Mer information finns i artikeln, [Hur SSO till lokala resurser fungerar på Azure AD-anslutna enheter](azuread-join-sso.md).

## <a name="device-security"></a>Enhetssäkerhet

- **Azure AD-registrerade enheter** använder ett konto som hanteras av slutanvändaren, det här kontot är antingen ett Microsoft-konto eller en annan lokalt hanterad autentiseringsuppgifter som skyddas med ett eller flera av följande.
   - lösenord
   - PIN-KOD
   - Mönster
   - Windows Hello
- **Azure AD-anslutna eller hybrid-Azure AD-anslutna enheter** använder ett organisationskonto i Azure AD som skyddas med ett eller flera av följande.
   - lösenord
   - Windows Hello för företag

## <a name="provisioning"></a>Etablering

Att hämta enheter till Azure AD kan göras på ett självbetjäningssätt eller en kontrollerad etableringsprocess av administratörer.

## <a name="summary"></a>Sammanfattning

Med enhetsidentitetshantering i Azure AD kan du:

- Förenkla processen för att ta med och hantera enheter i Azure AD
- Ge användarna enkel åtkomst till organisationens molnbaserade resurser

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure AD-registrerade enheter](concept-azure-ad-register.md)
- Läs mer om [Azure AD-anslutna enheter](concept-azure-ad-join.md)
- Läs mer om [hybrid-Azure AD-anslutna enheter](concept-azure-ad-join-hybrid.md)
- Information om hur du hanterar enhetsidentiteter i Azure-portalen finns i [Hantera enhetsidentiteter med Azure-portalen](device-management-azure-portal.md).
- Mer information om enhetsbaserad villkorlig åtkomst finns i [Konfigurera Azure Active Directory-enhetsbaserade principer för villkorlig åtkomst](../conditional-access/require-managed-devices.md).
