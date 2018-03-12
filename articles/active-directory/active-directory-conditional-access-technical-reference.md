---
title: "Inställningsreferens för villkorlig åtkomst till Azure Active Directory | Microsoft Docs"
description: "Få en översikt över inställningar som stöds i en villkorlig åtkomstprincip för Azure Active Directory."
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/07/2018
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: ba93e1b597bb6bdb752563f2873b8e0c5f7a8181
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Inställningsreferens för villkorlig åtkomst till Azure Active Directory

Du kan använda [villkorlig åtkomst i Azure Active Directory (AD Azure)](active-directory-conditional-access-azure-portal.md) att styra hur behöriga användare kan komma åt dina resurser.   

Den här artikeln innehåller information om stöd för följande konfigurationsalternativ i en princip för villkorlig åtkomst: 

- Molnet program tilldelningar

- Enhet plattform villkor 

- Klienten program villkor

- Godkända klienten programkrav


Om detta inte är den information du söker efter, lämna en kommentar i slutet av den här artikeln.

## <a name="cloud-apps-assignments"></a>Molnet appar tilldelningar

Med principer för villkorlig åtkomst kan du styra hur användarna kommer åt din [molnappar](active-directory-conditional-access-conditions.md#cloud-apps). Du måste välja minst en molnapp för när du konfigurerar en princip för villkorlig åtkomst. 

![Välj molnappar för principen](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Microsoft molnprogram

Du kan tilldela en princip för villkorlig åtkomst till följande molnappar från Microsoft:

- Azure Information Protection - [Läs mer](https://docs.microsoft.com/information-protection/get-started/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)

- Azure RemoteApp

- Microsoft Dynamics 365

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (inklusive OneDrive för företag och Project Online)

- Microsoft Power BI 

- Microsoft Visual Studio Team Services

- Microsoft Teams


### <a name="other-applications"></a>Andra program 

Du kan tilldela en princip för villkorlig åtkomst till följande typer av molnappar förutom molnappar Microsoft:

- Azure AD-anslutna program

- Förintegrerade federerade programvara som en tjänst (SaaS)

- Program som använder lösenord enkel inloggning (SSO)

- Line-of-business-program

- Program som använder Azure AD Application Proxy


## <a name="device-platform-condition"></a>Enhet plattform villkor

Du kan konfigurera villkoret enhet plattform för att koppla principen till operativsystemet på en klient i en princip för villkorlig åtkomst. Azure AD villkorlig åtkomst har stöd för följande enhetsplattformar:

- Android

- iOS

- Windows Phone

- Windows

- macOS


![Koppla åtkomstprincipen till klientens operativsystem](./media/active-directory-conditional-access-technical-reference/41.png)





## <a name="client-apps-condition"></a>Klienten appar villkor 

I principen för villkorlig åtkomst konfigurerar du den [klientappar](active-directory-conditional-access-conditions.md#client-apps) villkoret för att koppla principen till klientappen som har initierat en anslutningsförsöket. Ange appar villkoret som beviljar eller blockerar åtkomst när en åtkomst görs från följande typer av klientprogram för klienten:

- Webbläsare
- Mobilappar och skrivbordsappar

![Kontrollera åtkomsten för klientprogram](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>Webbläsare som stöds 

Du kan välja i principen för villkorlig åtkomst **webbläsare** som klientapp.

![Kontrollera åtkomst för webbläsare som stöds](./media/active-directory-conditional-access-technical-reference/05.png)

Den här inställningen fungerar med alla webbläsare. Dock för att uppfylla en princip, t.ex. en kompatibel enhet krav som följande operativsystem och webbläsare stöds:


| Operativsystem                     | Webbläsare                            | Support     |
| :--                    | :--                                 | :-:         |
| Windows 10             | Internet Explorer, Edge, Chrome     | ![Markera][1] |
| Windows 8 / 8.1        | Internet Explorer, Chrome           | ![Markera][1] |
| Windows 7              | Internet Explorer, Chrome           | ![Markera][1] |
| iOS                    | Safari, Intune Managed Browser      | ![Markera][1] |
| Android                | Chrome, Intune Managed Browser      | ![Markera][1] |
| Windows Phone          | Internet Explorer, Edge             | ![Markera][1] |
| Windows Server 2016    | Internet Explorer, Edge             | ![Markera][1] |
| Windows Server 2016    | Chrome                              | Kommer snart |
| Windows Server 2012 R2 | Internet Explorer, Chrome           | ![Markera][1] |
| Windows Server 2008 R2 | Internet Explorer, Chrome           | ![Markera][1] |
| macOS                  | Chrome, Safari                      | ![Markera][1] |



#### <a name="chrome-support"></a>Chrome-support

Chrome stöd i **Windows 10 skapare Update (version 1703)** eller senare, installera [tillägget](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

Chrome stöd i **Windows 8.1 och 7**, skapa följande registernyckel:

|    |    |
|--- | ---|
|Sökväg | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
|Namn | 1 |
|Typ | REG_SZ (String) |
|Data | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}}|

Dessa webbläsare stöder enhetsautentisering att enheten kan identifieras och verifieras mot en princip. Det går inte att enheten kontrollen om webbläsaren körs i privat läge. 


### <a name="supported-mobile-applications-and-desktop-clients"></a>Stöd för mobila program och skrivbord klienter

Du kan välja i principen för villkorlig åtkomst **mobilappar och skrivbordsklienter** som klientapp.


![Kontrollera åtkomst för mobila appar som stöds eller skrivbordsklienter](./media/active-directory-conditional-access-technical-reference/06.png)


Den här inställningen påverkar åtkomstförsök från följande mobila appar och skrivbord klienter: 


|Klientappar|Måltjänsten|Plattform|
|---|---|---|
|Azure RemoteApp|Azure RemoteApp-tjänsten|Windows 10, Windows 8.1, Windows 7, iOS, Android och Mac OS X|
|Dynamics CRM-app|Dynamics CRM|Windows 10, Windows 8.1, Windows 7, iOS och Android|
|Användare-e-post/kalender appen, Outlook 2016 Outlook 2013 (med modern autentisering)|Office 365 Exchange Online|Windows 10|
|Principen för MFA och plats för appar. Principer för enheter som är baserade stöds inte. |Alla Mina appar apptjänst|Android och iOS|
|Microsoft Team Services - detta styr alla tjänster som stöder Microsoft Teams och alla dess Klientappar - Windows-skrivbordet, iOS, Android, WP och Webbklient|Microsoft Teams|Windows 10, Windows 8.1, Windows 7, iOS, Android och macOS |
|Appar för Office 2016, Office 2013 (med modern autentisering), OneDrive synkronisera klienten (se [anteckningar](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))|Office 365 SharePoint Online|Windows 8.1, Windows 7|
|Appar för Office 2016, Universal Office-appar, Office 2013 (med modern autentisering), OneDrive synkroniseringsklient (se [anteckningar](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), stöd för Office-grupper är planerad för framtiden, stöd för SharePoint-appen är planerad i framtiden|Office 365 SharePoint Online|Windows 10|
|Office 2016 för macOS (Word, Excel, PowerPoint, OneNote endast). OneDrive för Business support planerad i framtiden|Office 365 SharePoint Online|Mac OS X|
|Office-mobilappar|Office 365 SharePoint Online|Android, iOS|
|Office Yammer-appen|Office 365 Yammer|Windows 10, iOS, Android|
|Outlook 2016 (Office för macOS)|Office 365 Exchange Online|Mac OS X|
|Outlook 2016 Outlook 2013 (med modern autentisering), Skype för företag (med modern autentisering)|Office 365 Exchange Online|Windows 8.1, Windows 7|
|Mobila Outlook-appen|Office 365 Exchange Online|Android, iOS|
|PowerBI-appen|PowerBI service|Windows 10, Windows 8.1, Windows 7 och iOS|
|Skype för företag|Office 365 Exchange Online|Android, IOS |
|Visual Studio Team Services app|Visual Studio Team Services|Windows 10, Windows 8.1, Windows 7, iOS och Android|


## <a name="approved-client-app-requirement"></a>Godkända klienten app-krav 

Du kan kräva att ett åtkomst försöker valda molnappar måste göras från en godkänd klientappen i principen för villkorlig åtkomst. 

![Kontrollera åtkomsten för godkända klientprogram](./media/active-directory-conditional-access-technical-reference/21.png)

Den här inställningen gäller för följande klientappar:


- Microsoft Intune Managed Browser
- Microsoft PowerBI
- Microsoft fakturering
- Microsoft Launcher
- Microsoft Azure Information Protection
- Microsoft Excel
- Microsoft Kaizala 
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype för företag
- Microsoft StaffHub
- Microsoft Teams
- Microsoft Visio
- Microsoft Word



**Kommentarer**

- Godkända klientappar stöder hanteringsfunktionen för mobilprogram i Intune.

- Den **kräver godkända klientappen** krav:

    - Stöder endast iOS och Android för [enhet plattform villkoret](#device-platforms-condition).

    - Stöder inte den **webbläsare** för den [klienten appar villkoret](#supported-browsers).
    
    - Ersätter den **mobilappar och skrivbordsklienter** för den [klienten appar villkoret](#supported-mobile-apps-and-desktop-clients) när det här alternativet är markerat.


## <a name="next-steps"></a>Nästa steg

- En översikt av villkorlig åtkomst finns [villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md).
- Om du är redo att konfigurera principer för villkorlig åtkomst i din miljö finns i [rekommenderade metoder för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-best-practices.md).



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png


