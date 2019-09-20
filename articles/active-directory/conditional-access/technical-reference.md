---
title: Referens för Azure Active Directory inställningar för villkorlig åtkomst | Microsoft Docs
description: Få en översikt över de inställningar som stöds i en Azure Active Directory princip för villkorlig åtkomst.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d56b1f800c71a5bbef7ffb1155d05e096113e2c
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162418"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Referens för Azure Active Directory villkorlig åtkomst inställningar

Du kan använda [villkorlig åtkomst för Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) för att styra hur auktoriserade användare kan komma åt dina resurser.

Den här artikeln innehåller supportinformation för följande konfigurations alternativ i en princip för villkorlig åtkomst:

- Tilldelningar av moln program
- Enhets plattforms villkor
- Klient program villkor
- Godkänt klient program krav

Lämna en kommentar i slutet av den här artikeln om detta inte är den information du söker.

## <a name="cloud-apps-assignments"></a>Cloud Apps-tilldelningar

Med principer för villkorlig åtkomst styr du hur dina användare får åtkomst till dina [molnappar](conditions.md#cloud-apps-and-actions). När du konfigurerar en princip för villkorlig åtkomst måste du välja minst en molnbaserad app. 

![Välj molnappar för principen](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Microsoft Cloud-program

Du kan tilldela en princip för villkorlig åtkomst till följande molnappar från Microsoft:

- Azure Analysis Services
- Azure DevOps
- Azure SQL Database och informations lager – [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- Microsoft Azure Information Protection – [Läs mer](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Microsoft Azure hantering – [Läs mer](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Microsoft Azure prenumerations hantering
- Microsoft Cloud App Security
- Microsoft Commerce tools Access Control Portal
- Microsoft Commerce tools-autentiseringstjänst
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Microsoft Intune-registrering
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
- Office Sway
- Outlook Groups
- Power BI tjänst
- Project Online
- Skype för företag – Online
- Virtuellt privat nätverk (VPN)
- Visual Studio App Center
- Windows Defender ATP

### <a name="other-applications"></a>Andra program

Förutom Microsoft Cloud Apps kan du tilldela en princip för villkorlig åtkomst till följande typer av molnappar:

- Azure AD-anslutna program
- Förintegrerad program vara för SaaS (federerat program vara som en tjänst)
- Program som använder enkel inloggning (SSO) för lösen ord
- Line-of-business-program
- Program som använder Azure AD-programproxy

## <a name="device-platform-condition"></a>Enhets plattforms villkor

I en princip för villkorlig åtkomst kan du konfigurera enhetens plattforms villkor så att principen anknyter till operativ systemet på en klient. Villkorlig åtkomst för Azure AD stöder följande enhetsspecifika plattformar:

- Android
- iOS
- Windows Phone
- Windows
- macOS

![Knyt åtkomst principen till klientens operativ system](./media/technical-reference/41.png)

Om du blockerar äldre autentisering med hjälp av **andra klient** villkor kan du även ange enhetens plattforms villkor.

## <a name="client-apps-condition"></a>Villkor för klient program

I din princip för villkorlig åtkomst kan du konfigurera villkor för [klient program](conditions.md#client-apps) så att de binder till den klient app som har initierat ett åtkomst försök. Ange villkor för klient program för att bevilja eller blockera åtkomst när ett åtkomst försök görs från följande typer av klient program:

- Browser
- Mobilappar och skrivbordsappar

![Kontrol lera åtkomst för klient program](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Webbläsare som stöds

I din princip för villkorlig åtkomst kan du välja **webbläsare** som klient program.

![Kontrol lera åtkomst för webbläsare som stöds](./media/technical-reference/05.png)

Den här inställningen fungerar med alla webbläsare. Men för att uppfylla en enhets princip, som ett krav för en kompatibel enhet, stöds följande operativ system och webbläsare:

| OS                     | Webbläsare                                        |
| :--                    | :--                                             |
| Windows 10             | Microsoft Edge, Internet Explorer, Chrome       |
| Windows 8/8,1        | Internet Explorer, Chrome                       |
| Windows 7              | Internet Explorer, Chrome                       |
| iOS                    | Microsoft Edge, Intune Managed Browser, Safari  |
| Android                | Microsoft Edge, Intune Managed Browser, Chrome  |
| Windows Phone          | Microsoft Edge, Internet Explorer               |
| Windows Server 2019    | Microsoft Edge, Internet Explorer, Chrome       |
| Windows Server 2016    | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer                       |
| Windows Server 2008 R2 | Internet Explorer                       |
| macOS                  | Chrome, Safari                                  |


#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Varför visas en certifikat varning i webbläsaren

På Windows 7, iOS, Android och macOS Azure AD identifierar enheten med ett klient certifikat som är etablerad när enheten har registrerats med Azure AD.  När en användare först loggar in via webbläsaren uppmanas användaren att välja certifikatet. Användaren måste välja det här certifikatet innan du använder webbläsaren.

#### <a name="chrome-support"></a>Chrome-support

För Chrome-stöd i **Windows 10 Creators Update (version 1703)** eller senare installerar du [tillägget Windows 10-konton](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Det här tillägget krävs när en princip för villkorlig åtkomst kräver enhets information.

Skapa följande register nyckel för att automatiskt distribuera tillägget till Chrome-webbläsare:

|    |    |
| --- | --- |
| `Path` | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Name | 1 |
| Type | REG_SZ (sträng) |
| Data | ppnbnpeolgkicgegkbkbjmhlideopiji; https\://clients2.Google.com/service/update2/CRX |

Skapa följande register nyckel för Chrome-stöd i **Windows 8,1 och 7**:

|    |    |
| --- | --- |
| `Path` | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Name | 1 |
| Type | REG_SZ (sträng) |
| Data | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

Dessa webbläsare har stöd för enhetsautentisering, så att enheten kan identifieras och verifieras mot en princip. Enhets kontrollen Miss lyckas om webbläsaren körs i privat läge.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Mobila program och skriv bords klienter som stöds

I din princip för villkorlig åtkomst kan du välja **mobilappar och skriv bords klienter** som klient program.

![Kontrol lera åtkomst för mobila appar eller Skriv bords klienter som stöds](./media/technical-reference/06.png)

Den här inställningen påverkar åtkomst försök som görs från följande mobilappar och skriv bords klienter:

| Klientappar | Mål tjänst | Plattform |
| --- | --- | --- |
| Dynamics CRM-App | Dynamics CRM | Windows 10, Windows 8,1, iOS och Android |
| E-post/kalender/Peoples-app, Outlook 2016, Outlook 2013 (med modern autentisering)| Office 365 Exchange Online | Windows 10 |
| MFA och plats princip för appar. Enhets principer stöds inte.| App Service för Mina appar| Android och iOS |
| Microsoft Teams Services – detta styr alla tjänster som stöder Microsoft-team och alla dess klient program – Windows-skrivbord, iOS, Android, WP och webb klient | Microsoft Teams | Windows 10, Windows 8,1, Windows 7, iOS, Android och macOS |
| Office 2016-appar, Office 2013 (med modern autentisering), OneDrive sync-klient (se [kommentarer](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) | Office 365 SharePoint Online | Windows 8,1, Windows 7 |
| Office 2016-appar, universella Office-appar, Office 2013 (med modern autentisering), OneDrive sync-klient (se [anteckningar](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), support för Office-grupper planeras för framtiden. support för SharePoint-appar planeras för framtiden | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (Word, Excel, PowerPoint, endast OneNote). OneDrive för företag-support planerat för framtiden| Office 365 SharePoint Online| macOS|
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Office Mobile-appar | Office 365 SharePoint Online | Android, iOS |
| Office Yammer-app | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office för macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (med modern autentisering), Skype för företag (med modern autentisering) | Office 365 Exchange Online | Windows 8,1, Windows 7 |
| Outlook Mobile-App | Office 365 Exchange Online | Android, iOS |
| Power BI app | Power BI-tjänst | Windows 10, Windows 8,1, Windows 7, Android och iOS |
| Skype för företag | Office 365 Exchange Online| Android, IOS |
| Visual Studio Team Services-app | Visual Studio Team Services | Windows 10, Windows 8,1, Windows 7, iOS och Android |

## <a name="support-for-legacy-authentication"></a>Stöd för äldre autentisering

Genom att välja **andra klienter**kan du ange ett villkor som påverkar appar som använder grundläggande autentisering med e-postprotokoll som IMAP, MAPI, pop, SMTP och äldre Office-appar som inte använder modern autentisering.  

![Övriga klienter](./media/technical-reference/11.png)

Mer information finns i [klient program](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Godkänt klient program krav

I din princip för villkorlig åtkomst kan du kräva att ett åtkomst försök till de valda molnappar måste göras från en godkänd klient app. 

![Kontrol lera åtkomst för godkända klient program](./media/technical-reference/21.png)

Den här inställningen gäller för följande klient program:

- Microsoft Azure Information Protection
- Microsoft Bookings
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
- Microsoft Skype for Business
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
   - Stöder endast iOS och Android för [enhets plattforms villkor](#device-platform-condition).

## <a name="app-protection-policy-requirement"></a>Krav för skydds princip för app 

I din princip för villkorlig åtkomst kan du kräva att en app Protection-princip finns i klient programmet innan åtkomst är tillgänglig för de valda molnappar. 

![Kontrol lera åtkomst med appens skydds princip](./media/technical-reference/22.png)

Den här inställningen gäller för följande klient program:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Kommentarer**

- Appar för skydds principer för appar stöder funktionen för hantering av mobil program i Intune med princip skydd.
- **Kräv skydds princip** krav för appar:
    - Stöder endast iOS och Android för [enhets plattforms villkor](#device-platform-condition).

## <a name="next-steps"></a>Nästa steg

- En översikt över villkorlig åtkomst finns i [Vad är villkorlig åtkomst i Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
- Om du är redo att konfigurera principer för villkorlig åtkomst i din miljö, se [rekommenderade metoder för villkorlig åtkomst i Azure Active Directory](best-practices.md).

<!--Image references-->
[1]: ./media/technical-reference/01.png
