---
title: Molnappar eller -åtgärder i principen villkorlig åtkomst – Azure Active Directory
description: Vad är molnappar eller -åtgärder i en Azure AD-princip för villkorlig åtkomst
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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457305"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Villkorlig åtkomst: Molnappar eller -åtgärder

Molnappar eller -åtgärder är en nyckelsignal i en princip för villkorlig åtkomst. Principer för villkorlig åtkomst gör det möjligt för administratörer att tilldela kontroller till specifika program eller åtgärder.

- Administratörer kan välja från listan över program som innehåller inbyggda Microsoft-program och alla [Azure AD-integrerade program,](../manage-apps/what-is-application-management.md) inklusive galleri, icke-galleri och program som publiceras via [Application Proxy](../manage-apps/what-is-application-proxy.md).
- Administratörer kan välja att definiera principen som inte baseras på ett molnprogram utan på en användaråtgärd. Den enda åtgärd som stöds är Registrera säkerhetsinformation (förhandsversion), vilket gör att villkorlig åtkomst kan tillämpa kontroller kring den [kombinerade säkerhetsinformationsregistreringsupplevelsen](../authentication/howto-registration-mfa-sspr-combined.md).

![Definiera en princip för villkorlig åtkomst och ange molnappar](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft-molnprogram

Många av de befintliga Microsoft-molnprogrammen finns med i listan över program som du kan välja mellan. 

Administratörer kan tilldela en princip för villkorlig åtkomst till följande molnappar från Microsoft. Vissa appar som Office 365 (förhandsversion) och Microsoft Azure Management innehåller flera relaterade underordnade appar eller tjänster. Följande lista är inte uttömmande och kan komma att ändras.

- [Office 365 (förhandsgranskning)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database och Data Warehouse](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- [Informationsskydd för Microsoft Azure](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure-hantering](#microsoft-azure-management)
- Microsoft Azure-prenumerationshantering
- Microsoft Cloud App Security
- Åtkomstkontrollportal för Microsoft Commerce Tools
- Autentiseringstjänst för Microsoft Commerce Tools
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune-registrering](/intune/enrollment/multi-factor-authentication)
- Microsoft-planerare
- Microsoft PowerApps
- Microsoft-sökning i Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Power BI-tjänst
- Project Online
- Skype för företag – Online
- Virtuellt privat nätverk (VPN)
- Windows Defender ATP

### <a name="office-365-preview"></a>Office 365 (förhandsgranskning)

Office 365 tillhandahåller molnbaserade produktivitets- och samarbetstjänster som Exchange, SharePoint och Microsoft Teams. Office 365-molntjänster är djupt integrerade för att säkerställa smidiga och samarbetsinriktade upplevelser. Den här integreringen kan orsaka förvirring när du skapar principer som vissa appar som Microsoft Teams har beroenden av andra, till exempel SharePoint eller Exchange.

Office 365-appen (förhandsversion) gör det möjligt att rikta dessa tjänster på en gång. Vi rekommenderar att du använder den nya Office 365-appen (förhandsversion) i stället för att rikta in dig på enskilda molnappar. Inriktning på den här gruppen av program hjälper till att undvika problem som kan uppstå på grund av inkonsekventa principer och beroenden.

Administratörer kan välja att utesluta specifika appar från principen om de vill genom att inkludera Office 365-appen (förhandsversion) och utesluta de specifika appar som de själva väljer i principen.

Viktiga program som ingår i office 365-klientprogrammet (förhandsversion):

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - Söktjänst för Office 365
   - Office 365 Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype för företag – Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure-hantering

Microsoft Azure Management-programmet innehåller flera underliggande tjänster. 

   - Azure Portal
   - Azure Resource Manager-provider
   - Api:er för klassiska distributionsmodell
   - Azure PowerShell
   - Visual Studio-prenumerationsadministratörsportal
   - Azure DevOps
   - Azure Data Factory-portal

> [!NOTE]
> Microsoft Azure Management-programmet gäller för Azure PowerShell, som anropar Azure Resource Manager API. Det gäller inte Azure AD PowerShell, som anropar Microsoft Graph.

## <a name="other-applications"></a>Andra applikationer

Förutom Microsoft-apparna kan administratörer lägga till alla Azure AD-registrerade program i principer för villkorlig åtkomst. Dessa tillämpningar kan omfatta: 

- Program som publiceras via [Azure AD Application Proxy](../manage-apps/what-is-application-proxy.md)
- [Program tillagda från galleriet](../manage-apps/add-application-portal.md)
- [Anpassade program som inte finns i galleriet](../manage-apps/add-non-gallery-app.md)
- [Äldre program som publiceras via appleveransstyrenheter och nätverk](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>Användaråtgärder

Användaråtgärder är uppgifter som kan utföras av en användare. Den enda åtgärd som för närvarande stöds är **Registrera säkerhetsinformation**, vilket gör att principen villkorlig åtkomst kan tillämpas när användare som är aktiverade för kombinerad registrering försöker registrera sin säkerhetsinformation. Mer information finns i artikeln, [Kombinerad registrering](../authentication/concept-registration-mfa-sspr-combined.md)av säkerhetsinformation .

## <a name="next-steps"></a>Nästa steg

- [Villkorlig åtkomst: Villkor](concept-conditional-access-conditions.md)

- [Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)
- [Beroenden för klientprogram](service-dependencies.md)
