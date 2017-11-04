---
title: "Azure Active Directory villkorlig åtkomst Teknisk referens | Microsoft Docs"
description: "Lär dig använda villkorlig åtkomstkontroll i Azure Active Directory. Ange villkor för att autentisera användare och kontrollera åtkomsten till ditt program. När de angivna villkoren uppfylls användare autentiseras och beviljas åtkomst till programmet."
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/01/2017
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 8cde8e10b6789e930ea1deda2f4aa0d4a5e7ea97
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Azure Active Directory villkorlig åtkomst Teknisk referens

Du kan använda [villkorlig åtkomst i Azure Active Directory (AD Azure)](active-directory-conditional-access-azure-portal.md) att finjustera hur behöriga användare kan komma åt dina resurser.  

Det här avsnittet innehåller information om stöd för följande konfigurationsalternativ för en princip för villkorlig åtkomst: 

- Molnet program tilldelningar

- Enhet plattform villkor 

- Klienten program villkor

- Godkända klienten programkrav



## <a name="cloud-apps-assignments"></a>Molnet appar tilldelningar

När du konfigurerar en princip för villkorlig åtkomst måste du [Välj molnappar som använder din princip](active-directory-conditional-access-azure-portal.md#who). 

![Välj molnappar för principen](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Microsoft molnprogram

Du kan tilldela en princip för villkorlig åtkomst till följande molnappar från Microsoft:

- Azure Information Protection - [Läs mer](https://docs.microsoft.com/information-protection/get-started/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Azure RemoteApp

- Microsoft Dynamics 365

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (inklusive OneDrive för företag)

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

Du kan konfigurera villkoret enhet plattform för att koppla principen till operativsystemet på en klient i en princip för villkorlig åtkomst.

![Koppla åtkomstprincipen till klientens operativsystem](./media/active-directory-conditional-access-technical-reference/41.png)

Azure AD villkorlig åtkomst har stöd för följande enhetsplattformar:

- Android

- iOS

- Windows Phone

- Windows

- macOS (förhandsgranskning)



## <a name="client-apps-condition"></a>Klienten appar villkor 

När du konfigurerar en princip för villkorlig åtkomst, kan du [Välj klientappar](active-directory-conditional-access-azure-portal.md#client-apps) för klienten app-villkor. Ange appar villkoret som beviljar eller blockerar åtkomst när en åtkomst görs från följande typer av klientprogram för klienten:

- Webbläsare
- Mobilappar och skrivbordsappar

![Kontrollera åtkomsten för klientprogram](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>Webbläsare som stöds 

Kontrollera webbläsaråtkomst genom att använda den **webbläsare** alternativ i principen för villkorlig åtkomst. Åtkomst beviljas endast när försöket görs av en webbläsare som stöds. När en åtkomst görs av en webbläsare som inte stöds blockeras försöket.

![Kontrollera åtkomst för webbläsare som stöds](./media/active-directory-conditional-access-technical-reference/05.png)

Följande webbläsare stöds i principen för villkorlig åtkomst: 


| Operativsystem                     | Webbläsare                    | Support     |
| :--                    | :--                         | :-:         |
| Windows 10             | Internet Explorer, kant     | ![Markera][1] |
| Windows 10             | Chrome                      | ![Markera][1] |
| Windows 8 / 8.1        | Internet Explorer, Chrome   | ![Markera][1] |
| Windows 7              | Internet Explorer, Chrome   | ![Markera][1] |
| iOS                    | Safari, Intune Managed Browser                      | ![Markera][1] |
| Android                | Chrome, Intune Managed Browser                      | ![Markera][1] |
| Windows Phone          | Internet Explorer, kant     | ![Markera][1] |
| Windows Server 2016    | Internet Explorer, kant     | ![Markera][1] |
| Windows Server 2016    | Chrome                      | Kommer snart |
| Windows Server 2012 R2 | Internet Explorer, Chrome   | ![Markera][1] |
| Windows Server 2008 R2 | Internet Explorer, Chrome   | ![Markera][1] |
| macOS                  | Safari                      | ![Markera][1] |
| macOS                  | Chrome                      | Kommer snart |

> [!NOTE]
> För Chrome-stöd, måste du använda Windows 10 skapare Update (version 1703) eller senare.<br>
> Du kan installera [tillägget](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="supported-mobile-applications-and-desktop-clients"></a>Stöd för mobila program och skrivbord klienter

Åtkomstkontroll appen och klienten med hjälp av den **mobilappar och skrivbordsklienter** alternativ i principen för villkorlig åtkomst. Åtkomst beviljas endast när försöket görs av en mobil app som stöds eller klientversionen. När en åtkomst görs av en app som inte stöds eller klienten är blockerad försöket.

![Kontrollera åtkomst för mobila appar som stöds eller skrivbordsklienter](./media/active-directory-conditional-access-technical-reference/06.png)

Följande mobila appar och skrivbord klienter stöder villkorlig åtkomst för Office 365 och andra Azure AD-anslutna tjänstprogram:


| Klientprogram| Måltjänsten| Plattform |
| --- | --- | --- |
| Azure RemoteApp| Azure RemoteApp-tjänsten| Windows 10, Windows 8.1, Windows 7, iOS, Android och Mac OS X|
| Dynamics CRM-app| Dynamics CRM| Windows 10, Windows 8.1, Windows 7, iOS och Android|
| Användare-e-post/kalender appen, Outlook 2016 Outlook 2013 (med modern autentisering)| Office 365 Exchange Online| Windows 10|
| Principen för MFA och plats för appar. Principer för enheter som är baserade stöds inte.| Alla Mina appar apptjänst| Android och iOS|
| Microsoft Team Services - detta styr alla tjänster som stöder Microsoft Teams och alla dess Klientappar - Windows-skrivbordet, iOS, Android, WP och Webbklient| Microsoft Teams| Windows 10, Windows 8.1, Windows 7, iOS och Android|
| Appar för Office 2016, Office 2013 (med modern autentisering), OneDrive synkronisera klienten (se [anteckningar](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))| Office 365 SharePoint Online| Windows 8.1, Windows 7|
| Appar för Office 2016, Universal Office-appar, Office 2013 (med modern autentisering), OneDrive synkroniseringsklient (se [anteckningar](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), stöd för Office-grupper är planerad för framtiden, stöd för SharePoint-appen är planerad i framtiden| Office 365 SharePoint Online| Windows 10|
| Office 2016 för macOS (Word, Excel, PowerPoint, OneNote endast). OneDrive för Business support planerad i framtiden| Office 365 SharePoint Online| Mac OS X|
| Office-mobilappar| Office 365 SharePoint Online| iOS, Android|
| Office Yammer-appen| Office 365 Yammer| Windows 10-, iOS, Android|
| Outlook 2016 (Office för macOS)| Office 365 Exchange Online| Mac OS X|
| Outlook 2016 Outlook 2013 (med modern autentisering), Skype för företag (med modern autentisering)| Office 365 Exchange Online| Windows 8.1, Windows 7|
| Mobila Outlook-appen| Office 365 Exchange Online| iOS|
| PowerBI-appen. Power BI-appen för Android stöder för närvarande inte enhetsbaserad villkorlig åtkomst.| PowerBI service| Windows 10, Windows 8.1, Windows 7 och iOS|
| Skype för företag| Office 365 Exchange Online| Android IOS|
| Visual Studio Team Services app| Visual Studio Team Services| Windows 10, Windows 8.1, Windows 7, iOS och Android|



## <a name="approved-client-app-requirement"></a>Godkända klienten app-krav 

Kontrollera klientanslutningar genom att använda den **kräver godkända klientappen** alternativ i principen för villkorlig åtkomst. Åtkomst beviljas endast när ett anslutningsförsök görs av en godkänd klientapp.

![Kontrollera åtkomsten för godkända klientprogram](./media/active-directory-conditional-access-technical-reference/21.png)

Följande klientappar kan användas med programkrav godkända klienten:

- Microsoft Excel

- Microsoft OneDrive

- Microsoft Outlook

- Microsoft OneNote

- Microsoft PowerPoint

- Microsoft SharePoint

- Microsoft Skype för företag

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


