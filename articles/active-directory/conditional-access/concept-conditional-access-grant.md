---
title: Bevilja kontroller i principen villkorlig åtkomst - Azure Active Directory
description: Vad är bevilja kontroller i en Azure AD-princip för villkorlig åtkomst
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02ec8dace971cd4dc1407c9e8d20839504c9ecc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331847"
---
# <a name="conditional-access-grant"></a>Villkorlig åtkomst: Bevilja

I en princip för villkorlig åtkomst kan en administratör använda åtkomstkontroller för att antingen bevilja eller blockera åtkomst till resurser.

![Princip för villkorlig åtkomst med en bidragskontroll som kräver multifaktorautentisering](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Blockera åtkomst

Block tar hänsyn till eventuella tilldelningar och förhindrar åtkomst baserat på principkonfigurationen för villkorlig åtkomst.

Block är en kraftfull kontroll som bör utövas med lämplig kunskap. Det är något administratörer bör använda [rapport-bara läge](concept-conditional-access-report-only.md) för att testa innan du aktiverar.

## <a name="grant-access"></a>Bevilja åtkomst

Administratörer kan välja att tillämpa en eller flera kontroller när de beviljar åtkomst. Dessa kontroller innehåller följande alternativ: 

- [Kräv multifaktorautentisering (Azure Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Kräv att enheten ska markeras som kompatibel (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Kräv hybrid Azure AD-ansluten enhet](../devices/concept-azure-ad-join-hybrid.md)
- [Kräv godkänd klientapp](app-based-conditional-access.md)
- [Kräva appskyddsprincip](app-protection-based-conditional-access.md)

När administratörer väljer att kombinera dessa alternativ kan de välja följande metoder:

- Kräv alla markerade kontroller (kontroll **OCH** kontroll)
- Kräv en av de markerade kontrollerna (kontroll **ELLER** kontroll)

Som standard kräver villkorlig åtkomst alla markerade kontroller.

### <a name="require-multi-factor-authentication"></a>Kräv multifaktorautentisering

Om du markerar den här kryssrutan krävs att användarna utför Azure Multi-Factor Authentication. Mer information om hur du distribuerar Azure Multi-Factor Authentication finns i artikeln [Planera en molnbaserad Azure Multi-Factor Authentication-distribution](../authentication/howto-mfa-getstarted.md).

### <a name="require-device-to-be-marked-as-compliant"></a>Kräv att enheten ska markeras som kompatibel

Organisationer som har distribuerat Microsoft Intune kan använda informationen som returneras från sina enheter för att identifiera enheter som uppfyller specifika efterlevnadskrav. Den här principefterlevnadsinformationen vidarebefordras från Intune till Azure AD där villkorlig åtkomst kan fatta beslut om att bevilja eller blockera åtkomst till resurser. Mer information om efterlevnadsprinciper finns i artikeln [Ange regler på enheter som ger åtkomst till resurser i organisationen med Intune](/intune/protect/device-compliance-get-started).

En enhet kan markeras som kompatibel med Intune (för alla enhetsoperativsystem) eller av MDM-system från tredje part för Windows 10-enheter. Jamf pro är det enda MDM-systemet som stöds av tredje part. Mer information om integration finns i artikeln [Integrate Jamf Pro med Intune för efterlevnad](/intune/protect/conditional-access-integrate-jamf).

Enheter måste registreras i Azure AD innan de kan markeras som kompatibla. Mer information om enhetsregistrering finns i [artikeln, Vad är en enhetsidentitet](../devices/overview.md).

### <a name="require-hybrid-azure-ad-joined-device"></a>Kräv hybrid Azure AD-ansluten enhet

Organisationer kan välja att använda enhetsidentiteten som en del av principen villkorlig åtkomst. Organisationer kan kräva att enheter är hybrid Azure AD-anslutna med den här kryssrutan. Mer information om enhetsidentiteter finns i artikeln [Vad är en enhetsidentitet?](../devices/overview.md).

### <a name="require-approved-client-app"></a>Kräv godkänd klientapp

Organisationer kan kräva att ett åtkomstförsök till de valda molnapparna måste göras från en godkänd klientapp. Dessa godkända klientappar stöder [Intune-appskyddsprinciper](/intune/app-protection-policy) oberoende av mdm-lösning (Mobile Device Management).

För att utnyttja den här bidragskontrollen kräver villkorlig åtkomst att enheten registreras i Azure Active Directory som kräver användning av en mäklarapp. Koordinatorappen kan antingen vara Microsoft Authenticator för iOS eller Microsofts företagsportal för Android-enheter. Om en mäklarapp inte är installerad på enheten när användaren försöker autentisera omdirigeras användaren till App Store för att installera mäklarappen.

Den här inställningen gäller för följande iOS- och Android-appar:

- Informationsskydd för Microsoft Azure
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- Microsoft Office-hubb
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft-planerare
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
- Microsoft Whiteboard

**Anmärkningar**

- De godkända klientapparna stöder funktionen för hantering av Intune-mobilappar.
- **Kravet på godkända klientappar:**
   - Stöder endast iOS- och Android-enhetens plattformsvillkor.
   - En mäklarapp krävs för att registrera enheten. På iOS är mäklarappen Microsoft Authenticator och på Android är det Intune Company Portal-appen.
- Villkorlig åtkomst kan inte betrakta Microsoft Edge i InPrivate-läge som en godkänd klientapp.

Se artikeln [Så här: Kräv godkända klientappar för molnappåtkomst med villkorad åtkomst](app-based-conditional-access.md) för konfigurationsexempel.

### <a name="require-app-protection-policy"></a>Kräva appskyddsprincip

I principen villkorlig åtkomst kan du kräva att en [Intune-appskyddsprincip](/intune/app-protection-policy) finns i klientappen innan åtkomst är tillgänglig för de valda molnapparna. 

För att utnyttja den här bidragskontrollen kräver villkorlig åtkomst att enheten registreras i Azure Active Directory som kräver användning av en mäklarapp. Koordinatorappen kan antingen vara Microsoft Authenticator för iOS eller Microsofts företagsportal för Android-enheter. Om en mäklarapp inte är installerad på enheten när användaren försöker autentisera omdirigeras användaren till App Store för att installera mäklarappen.

Den här inställningen gäller för följande klientappar:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft-planerare

**Anmärkningar**

- Appar för appskyddsprincip stöder funktionen för hantering av Intune-mobilapplikationer med principskydd.
- Kraven **för appskyddskrav:**
    - Stöder endast iOS- och Android-enhetens plattformsvillkor.
    - En mäklarapp krävs för att registrera enheten. På iOS är mäklarappen Microsoft Authenticator och på Android är det Intune Company Portal-appen.

Se artikeln [Så här: Kräv appskyddsprincip och en godkänd klientapp för molnappåtkomst med villkorad åtkomst](app-protection-based-conditional-access.md) för konfigurationsexempel.

### <a name="terms-of-use"></a>Användningsvillkor

Om din organisation har skapat användningsvillkor kan ytterligare alternativ vara synliga under bidragskontroller. Dessa alternativ gör det möjligt för administratörer att kräva bekräftelse av användarvillkor som ett villkor för åtkomst till de resurser som skyddas av principen. Mer information om användarvillkor finns i artikeln, [Azure Active Directory användarvillkor](terms-of-use.md).

## <a name="next-steps"></a>Nästa steg

- [Villkorlig åtkomst: Sessionskontroller](concept-conditional-access-session.md)

- [Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

- [Läget Endast rapport](concept-conditional-access-report-only.md)
