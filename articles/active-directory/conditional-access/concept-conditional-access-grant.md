---
title: Bevilja kontroller i princip för villkorlig åtkomst – Azure Active Directory
description: Vad är beviljande av kontroller i en princip för villkorlig åtkomst för Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5df7eedcd92d338d3f741f7092ff6ef73f3442d
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585891"
---
# <a name="conditional-access-grant"></a>Villkorlig åtkomst: bevilja

I en princip för villkorlig åtkomst kan en administratör använda åtkomst kontroller för att antingen bevilja eller blockera åtkomst till resurser.

![Princip för villkorlig åtkomst med en beviljande kontroll som kräver Multi-Factor Authentication](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Blockera åtkomst

Block tar hänsyn till eventuella tilldelningar och förhindrar åtkomst baserat på konfiguration av villkorlig åtkomst princip.

Block är en kraftfull kontroll som bör vara wielded med lämplig kunskap. Det är något som administratörer kan [använda för](concept-conditional-access-report-only.md) att testa innan de aktive ras.

## <a name="grant-access"></a>Bevilja åtkomst

Administratörer kan välja att tillämpa en eller flera kontroller när de beviljar åtkomst. Dessa kontroller innehåller följande alternativ: 

- [Kräv Multi-Factor Authentication (Azure Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Kräv att enheten är markerad som kompatibel (Microsoft Intune)](https://docs.microsoft.com/intune/protect/device-compliance-get-started)
- [Kräv hybrid Azure AD-ansluten enhet](../devices/concept-azure-ad-join-hybrid.md)
- [Kräv godkänd klient app](app-based-conditional-access.md)
- [Kräv app Protection-princip](app-protection-based-conditional-access.md)

När administratörer väljer att kombinera de här alternativen kan de välja följande metoder:

- Kräv alla markerade kontroller (kontroll **och** kontroll)
- Kräv en av de valda kontrollerna (kontroll **eller** kontroll)

Som standard kräver villkorlig åtkomst alla valda kontroller.

### <a name="require-multi-factor-authentication"></a>Kräv Multi-Factor Authentication

Om du markerar den här kryss rutan krävs det att användarna utför Azure-Multi-Factor Authentication. Mer information om hur du distribuerar Azure Multi-Factor Authentication finns i artikeln [Planera en molnbaserad Azure-Multi-Factor Authentication distribution](../authentication/howto-mfa-getstarted.md).

### <a name="require-device-to-be-marked-as-compliant"></a>Kräv att enheten ska markeras som kompatibel

Organisationer som har distribuerat Microsoft Intune kan använda den information som returneras från sina enheter för att identifiera enheter som uppfyller särskilda krav för efterlevnad. Den här policyn för efterlevnad vidarebefordras från Intune till Azure AD där villkorlig åtkomst kan fatta beslut om att bevilja eller blockera åtkomst till resurser. Mer information om efterlevnadsprinciper finns i artikeln [Ange regler på enheter för att tillåta åtkomst till resurser i din organisation med hjälp av Intune](https://docs.microsoft.com/intune/protect/device-compliance-get-started).

En enhet kan markeras som kompatibel av Intune (för alla enhetens operativ system) eller MDM-system från tredje part för Windows 10-enheter. MDM-system från tredje part för andra typer av enhets operativ system än Windows 10 stöds inte.

Enheter måste vara registrerade i Azure AD innan de kan markeras som kompatibla. Mer information om enhets registrering finns i artikeln, [Vad är en enhets identitet](../devices/overview.md).

### <a name="require-hybrid-azure-ad-joined-device"></a>Kräv hybrid Azure AD-ansluten enhet

Organisationer kan välja att använda enhets identiteten som en del av den villkorliga åtkomst principen. Organisationer kan kräva att enheterna är hybrid Azure AD-anslutna med den här kryss rutan. Mer information om enhets identiteter finns i artikeln [Vad är en enhets identitet?](../devices/overview.md).

### <a name="require-approved-client-app"></a>Kräv godkänd klientapp

Organisationer kan kräva att ett åtkomst försök till de valda molnappar måste göras från en godkänd klient app. Dessa godkända klient program har stöd för [Intune App Protection-principer](/intune/app-protection-policy) oberoende av alla lösningar för hantering av mobila enheter (MDM).

Den här inställningen gäller för följande klient program:

- Microsoft Azure Information Protection
- Microsoft-bokningar
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft fakturering
- Microsoft Kaizala
- Startprogram för Microsoft
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype för företag
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer

**Kommentarer**

- Godkända klient program har stöd för funktionen för hantering av mobil program i Intune.
- **Kräv godkänd klient program** krav:
   - Stöder endast iOS och Android för enhets plattforms villkor.
- Villkorlig åtkomst kan inte beakta Microsoft Edge i InPrivate-läge en godkänd klient app.

### <a name="require-app-protection-policy"></a>Kräva appskyddsprincip

I din princip för villkorlig åtkomst kan du kräva att en [Intune App Protection-princip](/intune/app-protection-policy) finns i klient programmet innan åtkomst är tillgänglig för de valda molnappar. 

Den här inställningen gäller för följande klient program:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Kommentarer**

- Appar för skydds principer för appar stöder funktionen för hantering av mobil program i Intune med princip skydd.
- **Kräv skydds princip** krav för appar:
    - Stöder endast iOS och Android för enhets plattforms villkor.

### <a name="terms-of-use"></a>Användningsvillkor

Om din organisation har skapat användnings villkor kan ytterligare alternativ visas under bevilja kontroller. Med de här alternativen kan administratörer kräva bekräftelse av användnings villkoren som ett villkor för att komma åt de resurser som skyddas av principen. Mer information om användnings villkoren finns i artikeln Azure Active Directory användnings [villkor](terms-of-use.md).

## <a name="next-steps"></a>Nästa steg

- [Villkorlig åtkomst: kontrol lera sessioner](concept-conditional-access-session.md)

- [Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

- [Endast rapport läge](concept-conditional-access-report-only.md)
