---
title: Inställningsreferens för villkorlig åtkomst till Azure Active Directory | Microsoft Docs
description: Få en översikt över inställningar som stöds i en villkorlig åtkomstprincip i Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e87a4c7ebafd8ddcfa54c87b189316b0ce98b0f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301316"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Inställningsreferens för villkorlig åtkomst till Azure Active Directory

Du kan använda [villkorlig åtkomst i Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) att styra hur behöriga användare kan komma åt dina resurser.

Den här artikeln ger dig information om följande konfigurationsalternativ i en princip för villkorlig åtkomst:

- Molnet program tilldelningar
- Enheten plattform villkor
- Klienten program villkor
- Godkända programkrav

Om det inte är den information du söker efter, lämna en kommentar i slutet av den här artikeln.

## <a name="cloud-apps-assignments"></a>Tilldelningar för moln-appar

Med principer för villkorlig åtkomst kan du styra hur dina användare kommer åt din [molnappar](conditions.md#cloud-apps). När du konfigurerar en princip för villkorlig åtkomst, måste du välja minst en molnapp. 

![Välj vilka molnappar för principen](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Microsoft-molnprogram

Du kan tilldela en princip för villkorlig åtkomst till följande moln-appar från Microsoft:

- Azure Analysis Services
- Azure DevOps
- Azure SQL Database och Data Warehouse – [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- Microsoft Azure Information Protection - [Läs mer](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Microsoft Azure Management - [Läs mer](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Microsoft Azure RemoteApp
- Microsoft Azure-prenumerationshantering
- Microsoft Cloud App Security
- Microsoft Commerce Access Control Portal-verktyg
- Microsoft Commerce verktyg autentiseringstjänst
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Microsoft Intune-registrering
- Microsoft Planner
- Microsoft Power BI
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
- Project Online
- Skype för företag – Online
- Virtuellt privat nätverk (VPN)
- Visual Studio App Center
- Windows Defender ATP

### <a name="other-applications"></a>Andra program

Du kan tilldela en princip för villkorlig åtkomst till följande typer av molnappar förutom molnappar Microsoft:

- Azure AD-anslutna program
- Förintegrerade federerad programvara som en tjänst (SaaS)
- Program som använder lösenord för enkel inloggning (SSO)
- Line-of-business-program
- Program som använder Azure AD Application Proxy

## <a name="device-platform-condition"></a>Enheten plattform villkor

Du kan konfigurera enheten plattform villkoret att koppla principen till operativsystemet på en klient i en princip för villkorlig åtkomst. Azure AD villkorsstyrd åtkomst stöder följande enhetsplattformar:

- Android
- iOS
- Windows Phone
- Windows
- macOS

![Koppla åtkomstprincipen för klient-OS](./media/technical-reference/41.png)

## <a name="client-apps-condition"></a>Klient apps villkor

Du kan konfigurera i principen för villkorlig åtkomst i [klientappar](conditions.md#client-apps) villkor för att koppla principen klientappar som har initierat en åtkomstförsök. Ange klienten appar villkor för att bevilja eller blockera åtkomst när en åtkomstförsök görs från följande typer av klientprogram:

- Webbläsare
- Mobilappar och skrivbordsappar

![Kontrollera åtkomsten för klientprogram](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Webbläsare som stöds

I princip för villkorlig åtkomst kan du välja **webbläsare** som klientapp.

![Kontrollera åtkomsten för webbläsare som stöds](./media/technical-reference/05.png)

Den här inställningen fungerar med alla webbläsare. Dock för att uppfylla en princip, t.ex. krav kompatibel enhet, som följande operativsystem och webbläsare stöds:

| Operativsystem                     | Webbläsare                                      |
| :--                    | :--                                           |
| Windows 10             | Internet Explorer, Microsoft Edge, Chrome     |
| Windows 8 / 8.1        | Internet Explorer, Chrome                     |
| Windows 7              | Internet Explorer, Chrome                     |
| iOS                    | Safari, Microsoft Edge, Intune Managed Browser |
| Android                | Chrome, Microsoft Edge Intunes hanterade webbläsare |
| Windows Phone          | Internet Explorer, Microsoft Edge             |
| Windows Server 2016    | Internet Explorer, Microsoft Edge             |
| Windows Server 2016    | Chrome                                        |
| Windows Server 2012 R2 | Internet Explorer, Chrome                     |
| Windows Server 2008 R2 | Internet Explorer, Chrome                     |
| macOS                  | Chrome, Safari                                |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Varför ser jag ett certifikat som frågar i webbläsaren

Azure AD identifierar enheten med ett klientcertifikat som har etablerats när enheten är registrerad med Azure AD på Windows 7, iOS, Android och macOS.  När en användare loggar in via webbläsaren först uppmanas användaren att välja certifikatet. Användaren måste välja det här certifikatet innan du kan använda webbläsaren.

#### <a name="chrome-support"></a>Chrome-stöd

Chrome stöd i **Windows 10 Creators Update (version 1703)** eller senare, installera [det här tillägget](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

För att automatiskt distribuera det här tillägget till Chrome webbläsare, skapar du följande registernyckel:

|    |    |
| --- | --- |
| Sökväg | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Namn | 1 |
| Typ | REG_SZ (String) |
| Data | ppnbnpeolgkicgegkbkbjmhlideopiji; https://clients2.google.com/service/update2/crx |

Chrome stöd i **Windows 8.1 och 7**, skapa följande registernyckel:

|    |    |
| --- | --- |
| Sökväg | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Namn | 1 |
| Typ | REG_SZ (String) |
| Data | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

Dessa webbläsare har stöd för autentisering, vilket gör att enheten har identifierats och verifieras mot en princip. Enhet-kontrollen misslyckas om webbläsaren körs i privat läge.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Mobilappar och skrivbordsklienter som stöds

I princip för villkorlig åtkomst kan du välja **mobilappar och skrivbordsklienter** som klientapp.

![Kontrollera åtkomsten för mobila appar som stöds eller skrivbordsklienter](./media/technical-reference/06.png)

Den här inställningen påverkar åtkomstförsök från följande mobilappar och skrivbordsklienter:

| Klientappar | Måltjänsten | Plattform |
| --- | --- | --- |
| Azure RemoteApp | Azure RemoteApp-tjänsten | Windows 10, Windows 8.1, Windows 7, iOS, Android och macOS |
| Dynamics CRM-app | Dynamics CRM | Windows 10, Windows 8.1, iOS och Android |
| E-post/kalender/personer app, Outlook 2016 Outlook 2013 (med modern autentisering)| Office 365 Exchange Online | Windows 10 |
| MFA- och platspolicy för appar. Enhetsbaserade principer stöds inte.| Alla Mina appar apptjänst| Android och iOS |
| Microsoft Teams-tjänster – detta styr alla tjänster som stöder Microsoft Teams och alla dess klient Apps - Windows-skrivbordet och iOS, Android, WP och Webbklient | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android och macOS |
| Office 2016-appar, Office 2013 (med modern autentisering), OneDrive synkronisera klient (se [anteckningar](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Office 2016-appar, universella Office-appar, Office 2013 (med modern autentisering), OneDrive-Synkroniseringsklienten (se [anteckningar](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), Office-grupper stöd planeras i framtiden, stöd för SharePoint-appar planeras i framtiden | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (Word, Excel, PowerPoint och OneNote endast). OneDrive för företag-stöd planeras för framtiden| Office 365 SharePoint Online| macOS|
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Office-mobilappar | Office 365 SharePoint Online | Android, iOS |
| Office Yammer-appen | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office för Mac OS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (med modern autentisering), Skype för företag (med modern autentisering) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Outlook-mobilappen | Office 365 Exchange Online | Android, iOS |
| Power BI-appen | Power BI-tjänsten | Windows 10, Windows 8.1, Windows 7, Android och iOS |
| Skype för företag | Office 365 Exchange Online| Android, IOS |
| Visual Studio Team Services app | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS och Android |

## <a name="support-for-legacy-authentication"></a>Stöd för äldre autentisering

Genom att välja **andra klienter**, du kan ange ett villkor som påverkar appar som använder grundläggande autentisering med e-postprotokoll som IMAP-, MAPI-, POP-, SMTP- och äldre Office-program som inte använder modern autentisering.  

![Övriga klienter](./media/technical-reference/11.png)

Mer information finns i [klientappar](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Krav för godkända appar

Du kan kräva att en försöka valda moln-appar måste göras från en godkänd klientapp i principen för villkorlig åtkomst. 

![Kontrollera åtkomsten för godkända klientappar](./media/technical-reference/21.png)

Den här inställningen gäller för klientappar som följande:

- Microsoft Azure Information Protection
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
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

- Godkända klientappar stöder funktionen Intune mobile application management.
- Den **Kräv godkänd klientapp** krav:
   - Stöder endast iOS och Android för [enheten plattform villkor](#device-platform-condition).

## <a name="app-protection-policy-requirement"></a>Kravet för App protection 

Du kan kräva en appskyddsprincip finnas i klientappen innan har du tillgång till de valda molnapparna i din princip för villkorlig åtkomst. 

![Kontrollera åtkomst med appskyddsprinciper](./media/technical-reference/22.png)

Den här inställningen gäller för klientappar som följande:

- Microsoft OneDrive
- Microsoft Outlook

**Kommentarer**

- Appar för appskyddsprinciper stöder funktionen Intune mobilprogram management med princip för skydd.
- Den **kräver appskyddsprincip** krav:
    - Stöder endast iOS och Android för [enheten plattform villkor](#device-platform-condition).

## <a name="next-steps"></a>Nästa steg

- En översikt av villkorlig åtkomst finns i [vad är villkorlig åtkomst i Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
- Om du är redo att konfigurera principer för villkorlig åtkomst i din miljö kan du läsa den [rekommenderade metoder för villkorlig åtkomst i Azure Active Directory](best-practices.md).

<!--Image references-->
[1]: ./media/technical-reference/01.png
