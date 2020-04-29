---
title: Molnappar eller åtgärder i princip för villkorlig åtkomst – Azure Active Directory
description: Vad är molnappar eller åtgärder i en princip för villkorlig åtkomst för Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b15b022726c09ccbaf9674775d114c8dd1916e1d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457305"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Villkorlig åtkomst: molnappar eller åtgärder

Molnappar eller åtgärder är en nyckel signal i en princip för villkorlig åtkomst. Med principer för villkorlig åtkomst kan administratörer tilldela kontroller till vissa program eller åtgärder.

- Administratörer kan välja i listan över program som innehåller inbyggda Microsoft-program och alla [Azure AD-integrerade program](../manage-apps/what-is-application-management.md) , inklusive Galleri, icke-galleri och program som publicerats via [Application Proxy](../manage-apps/what-is-application-proxy.md).
- Administratörer kan välja att definiera principer som inte baseras på ett moln program, men på en användar åtgärd. Den enda åtgärd som stöds är att registrera säkerhets information (förhands granskning), så att villkorlig åtkomst kan användas för att genomdriva kontroller kring den [kombinerade säkerhets informations registreringen](../authentication/howto-registration-mfa-sspr-combined.md).

![Definiera en princip för villkorlig åtkomst och ange molnappar](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft Cloud-program

Många av de befintliga Microsoft Cloud-programmen ingår i listan över program som du kan välja bland. 

Administratörer kan tilldela en princip för villkorlig åtkomst till följande molnappar från Microsoft. Vissa appar som Office 365 (för hands version) och Microsoft Azure hantering inkluderar flera relaterade underordnade appar eller tjänster. Följande lista är inte fullständig och kan komma att ändras.

- [Office 365 (för hands version)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database och Data Warehouse](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure hantering](#microsoft-azure-management)
- Microsoft Azure prenumerations hantering
- Microsoft Cloud App Security
- Microsoft Commerce tools Access Control Portal
- Microsoft Commerce tools-autentiseringstjänst
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune registrering](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Search i Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office-Sway
- Outlook Groups
- Power BI-tjänst
- Project Online
- Skype för företag – Online
- Virtuellt privat nätverk (VPN)
- Windows Defender ATP

### <a name="office-365-preview"></a>Office 365 (för hands version)

Office 365 tillhandahåller molnbaserade produktivitets-och samarbets tjänster som Exchange, SharePoint och Microsoft Teams. Office 365 Cloud Services är djupt integrerade för att säkerställa smidiga och samarbets upplevelser. Den här integrationen kan orsaka förvirring när du skapar principer eftersom vissa appar, till exempel Microsoft Teams, har beroenden för andra som SharePoint eller Exchange.

Office 365-appen (för hands version) gör det möjligt att rikta dessa tjänster på samma gång. Vi rekommenderar att du använder den nya Office 365-appen (för hands version) i stället för att rikta in sig på enskilda molnappar. Att rikta in sig på den här gruppen med program bidrar till att undvika problem som kan uppstå på grund av inkonsekventa principer och beroenden.

Administratörer kan välja att undanta vissa appar från en princip om de vill genom att inkludera Office 365-appen (för hands version) och utesluta de enskilda appar som de väljer i principen.

Viktiga program som ingår i klient programmet för Office 365 (för hands version):

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - Office 365 Search Service
   - Office 365 Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype för företag – Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure hantering

Microsoft Azure hanterings programmet innehåller flera underliggande tjänster. 

   - Azure Portal
   - Azure Resource Manager Provider
   - API: er för klassisk distributions modell
   - Azure PowerShell
   - Administratörs Portal för Visual Studio-prenumerationer
   - Azure DevOps
   - Azure Data Factory Portal

> [!NOTE]
> Microsoft Azure hanterings programmet gäller Azure PowerShell, som anropar Azure Resource Manager-API: et. Den gäller inte för Azure AD PowerShell, som anropar Microsoft Graph.

## <a name="other-applications"></a>Andra program

Förutom Microsoft-appar kan administratörer lägga till alla registrerade Azure AD-program i principer för villkorlig åtkomst. Programmen kan innehålla: 

- Program som publicerats via [Azure AD-programproxy](../manage-apps/what-is-application-proxy.md)
- [Program som har lagts till från galleriet](../manage-apps/add-application-portal.md)
- [Anpassade program som inte är i galleriet](../manage-apps/add-non-gallery-app.md)
- [Äldre program som publicerats via app Delivery controllers och Networks](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>Användaråtgärder

Användar åtgärder är uppgifter som kan utföras av en användare. Den enda åtgärd som stöds för närvarande är att **Registrera säkerhets information**, vilket gör det möjligt för principen för villkorlig åtkomst att genomdriva när användare som har Aktiver ATS för kombinerad registrering försöker registrera sin säkerhets information. Mer information finns i artikeln [kombinerad säkerhets informations registrering](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>Nästa steg

- [Villkorlig åtkomst: villkor](concept-conditional-access-conditions.md)

- [Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)
- [Klient program beroenden](service-dependencies.md)
