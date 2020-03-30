---
title: Villkor i principen villkorlig åtkomst - Azure Active Directory
description: Vad är villkor i en Azure AD-princip för villkorlig åtkomst
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
ms.openlocfilehash: bb14369275a111476867f2263766e1bb87b7c87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295338"
---
# <a name="conditional-access-conditions"></a>Villkorlig åtkomst: Villkor

I en princip för villkorlig åtkomst kan en administratör använda signaler från villkor som risk, enhetsplattform eller plats för att förbättra sina principbeslut. 

![Definiera en princip för villkorlig åtkomst och ange villkor](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

Flera villkor kan kombineras för att skapa detaljerade och specifika principer för villkorlig åtkomst.

Till exempel, när du öppnar ett känsligt program en administratör kan faktor inloggning riskinformation från Identitetsskydd och plats i deras åtkomstbeslut utöver andra kontroller som multifaktorautentisering.

## <a name="sign-in-risk"></a>Inanmälningsrisk

För kunder med åtkomst till [identitetsskydd](../identity-protection/overview-identity-protection.md)kan inloggningsrisken utvärderas som en del av en princip för villkorlig åtkomst. Inloggningsrisken representerar sannolikheten för att en viss autentiseringsbegäran inte godkänns av identitetsägaren. Mer information om inloggningsrisk finns i [artiklarna, Vad är risk](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) och [hur du: Konfigurera och aktivera riskprinciper](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="device-platforms"></a>Enhetsplattformar

Enhetsplattformen kännetecknas av operativsystemet som körs på en enhet. Azure AD identifierar plattformen med hjälp av information som tillhandahålls av enheten, till exempel användaragentsträngar. Eftersom användaragentsträngar kan ändras är den här informationen overifierad. Enhetsplattformen bör användas i samarbete med Microsoft Intune-principer för enhetsefterlevnad eller som en del av en blockeringssats. Standard är att gälla för alla enhetsplattformar.

Azure AD-villkorlig åtkomst stöder följande enhetsplattformar:

- Android
- iOS
- Windows Phone
- Windows
- macOS

Om du blockerar äldre autentisering med villkoret **Övriga klienter** kan du också ange enhetsplattformens villkor.

## <a name="locations"></a>Platser

När du konfigurerar plats som ett villkor kan organisationer välja att inkludera eller utesluta platser. Dessa namngivna platser kan omfatta den offentliga IPv4-nätverksinformationen, landet eller regionen eller till och med okända områden som inte mappas till specifika länder eller regioner. Endast IP-intervall kan markeras som en betrodd plats.

När du inkluderar **en plats**innehåller det här alternativet alla IP-adresser på internet som inte bara konfigurerats med namngivna platser. När du väljer **en plats**kan administratörer välja att utesluta **alla betrodda** eller **valda platser**.

Vissa organisationer kan till exempel välja att inte kräva multifaktorautentisering när användarna är anslutna till nätverket på en betrodd plats, till exempel deras fysiska huvudkontor. Administratörer kan skapa en princip som innehåller valfri plats men utesluter de valda platserna för huvudkontorets nätverk.

Mer information om platser finns i [artikeln, Vad är platsvillkoret i Azure Active Directory Villkorlig åtkomst](location-condition.md).

## <a name="client-apps-preview"></a>Klientappar (förhandsgranskning)

Principer för villkorlig åtkomst gäller som standard för webbläsarbaserade program och program som använder moderna autentiseringsprotokoll. Utöver dessa program kan administratörer välja att inkludera Exchange ActiveSync-klienter och andra klienter som använder äldre protokoll.

- Webbläsare
   - Dessa inkluderar webbaserade program som använder protokoll som SAML, WS-Federation, OpenID Connect eller tjänster som registrerats som en ocuth-konfidentiell klient.
- Mobilappar och skrivbordsklienter
   - Moderna autentiseringsklienter
      - Det här alternativet innehåller program som Office-skrivbords- och telefonprogram.
   - Exchange ActiveSync-klienter
      - Som standard inkluderar detta all användning av EAS-protokollet (Exchange ActiveSync). Om du bara väljer **Tillämpa princip på plattformar som stöds** begränsas till plattformar som stöds som iOS, Android och Windows.
      - När principen blockerar användningen av Exchange ActiveSync får den berörda användaren ett enda e-postmeddelande i karantän. Detta e-postmeddelande med information om varför de är blockerade och inkludera reparationsinstruktioner om det går.
   - Övriga klienter
      - Det här alternativet omfattar klienter som använder grundläggande/äldre autentiseringsprotokoll som inte stöder modern autentisering.
         - Autentiserade SMTP - Används av POP och IMAP-klientens för att skicka e-postmeddelanden.
         - Automatisk upptäckt - Används av Outlook- och EAS-klienter för att hitta och ansluta till postlådor i Exchange Online.
         - Exchange Online PowerShell - Används för att ansluta till Exchange Online med fjärr-PowerShell. Om du blockerar grundläggande autentisering för Exchange Online PowerShell måste du använda Exchange Online PowerShell-modulen för att ansluta. Instruktioner finns i [Anslut till Exchange Online PowerShell med multifaktorautentisering](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
         - Exchange Web Services (EWS) – Ett programmeringsgränssnitt som används av Outlook, Outlook för Mac och appar från tredje part.
         - IMAP4 - Används av IMAP-e-postklienter.
         - MAPI över HTTP (MAPI/HTTP) - Används av Outlook 2010 och senare.
         - OAB (Offline Address Book) – En kopia av adresslistsamlingar som hämtas och används av Outlook.
         - Outlook Anywhere (RPC över HTTP) – Används av Outlook 2016 och tidigare.
         - Outlook-tjänsten – används av appen E-post och kalender för Windows 10.
         - POP3 - Används av POP-e-postklienter.
         - Reporting Web Services - Används för att hämta rapportdata i Exchange Online.

Dessa villkor används ofta när du behöver en hanterad enhet, blockerar äldre autentisering och blockerar webbprogram men tillåter mobila eller stationära appar.

### <a name="supported-browsers"></a>Webbläsare som stöds

Den här inställningen fungerar med alla webbläsare. För att uppfylla en enhetsprincip, till exempel ett kompatibelt enhetskrav, stöds dock följande operativsystem och webbläsare:

| Operativsystem | Webbläsare |
| :-- | :-- |
| Windows 10 | Microsoft Edge, Internet Explorer, Chrome |
| Windows 8 / 8.1 | Internet Explorer, Chrome |
| Windows 7 | Internet Explorer, Chrome |
| iOS | Microsoft Edge, Intune hanterad webbläsare, Safari |
| Android | Microsoft Edge, Intune Hanterad webbläsare, Chrome |
| Windows Phone | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome, Safari |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Varför visas en certifikatfråga i webbläsaren

På Windows 7 identifierar iOS, Android och macOS Azure AD enheten med hjälp av ett klientcertifikat som etableras när enheten är registrerad med Azure AD.  När en användare först loggar in via webbläsaren uppmanas användaren att välja certifikatet. Användaren måste välja det här certifikatet innan du använder webbläsaren.

#### <a name="chrome-support"></a>Support för Chrome

För Chrome-stöd i **Windows 10 Creators Update (version 1703)** eller senare installerar du [tillägget Windows 10-konton](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Det här tillägget krävs när en princip för villkorlig åtkomst kräver enhetsspecifik information.

Om du automatiskt vill distribuera det här tillägget till webbläsaren Chrome skapar du följande registernyckel:

|    |    |
| --- | --- |
| Sökväg | HKEY_LOCAL_MACHINE\Programvara\Policy\Google\Chrome\ExtensionInstallForcelist |
| Namn | 1 |
| Typ | REG_SZ (sträng) |
| Data | ppnbnpeolgkicgegkbkbjmhlideopiji;https\://clients2.google.com/service/update2/crx |

För Chrome-stöd i **Windows 8.1 och 7**skapar du följande registernyckel:

|    |    |
| --- | --- |
| Sökväg | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Namn | 1 |
| Typ | REG_SZ (sträng) |
| Data | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

Dessa webbläsare stöder enhetsautentisering, vilket gör att enheten kan identifieras och valideras mot en princip. Enhetskontrollen misslyckas om webbläsaren körs i privat läge.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Mobila program och stationära klienter som stöds

Organisationer kan välja **Mobilappar och skrivbordsklienter** som klientapp.

Den här inställningen påverkar åtkomstförsök som görs från följande mobilappar och skrivbordsklienter:

| Klientappar | Måltjänst | Plattform |
| --- | --- | --- |
| Appen Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS och Android |
| Appen E-post/kalender/kontakter, Outlook 2016, Outlook 2013 (med modern autentisering)| Office 365 Exchange Online | Windows 10 |
| MFA och platsprincip för appar. Enhetsbaserade principer stöds inte.| Alla apptjänst för Mina appar | Android och iOS |
| Microsoft Teams Services – detta styr alla tjänster som stöder Microsoft Teams och alla dess klientappar – Windows Desktop, iOS, Android, WP och webbklient | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android och macOS |
| Office 2016-appar, Office 2013 (med modern autentisering), [OneDrive-synkroniseringsklient](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Office 2016-appar, Universella Office-program, Office 2013 (med modern autentisering), [OneDrive-synkroniseringsklient](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (endast Word, Excel, PowerPoint, OneNote). | Office 365 SharePoint Online | macOS |
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Office-mobilappar | Office 365 SharePoint Online | Android, iOS |
| Appen Office Yammer | Office 365 Yammer | Windows 10, iOS, Android |
| Utsikter 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office för macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (med modern autentisering), Skype för företag (med modern autentisering) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Outlook-mobilapp | Office 365 Exchange Online | Android, iOS |
| Power BI-app | Power BI-tjänst | Windows 10, Windows 8.1, Windows 7, Android och iOS |
| Skype för företag | Office 365 Exchange Online| Android, iOS |
| Appen Visual Studio Team Services | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS och Android |

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync-klienter

- Organisationer kan bara välja Exchange ActiveSync-klienter när de tilldelar principen till användare eller grupper. Om du väljer **Alla användare**, **Alla gäst- och externa användare**eller **Katalogroller** blockeras alla användare.
- När du skapar en princip som tilldelats Exchange ActiveSync-klienter bör **Office 365 Exchange Online** vara det enda molnprogram som tilldelats principen. 
- Organisationer kan begränsa omfattningen av den här principen till specifika plattformar med villkoret **Enhetsplattformar.**

Om åtkomstkontrollen som tilldelats principen använder **Kräv godkänd klientapp**uppmanas användaren att installera och använda Outlook-mobilklienten. Om **multifaktorautentisering** krävs blockeras berörda användare eftersom grundläggande autentisering inte stöder multifaktorautentisering.

Mer information finns i följande artiklar:

- [Blockera äldre autentisering med villkorlig åtkomst](block-legacy-authentication.md)
- [Kräver godkända klientappar med villkorlig åtkomst](app-based-conditional-access.md)

### <a name="other-clients"></a>Övriga klienter

Genom att välja **Andra klienter**kan du ange ett villkor som påverkar appar som använder grundläggande autentisering med e-postprotokoll som IMAP, MAPI, POP, SMTP och äldre Office-appar som inte använder modern autentisering.

## <a name="device-state-preview"></a>Enhetstillstånd (förhandsgranskning)

Enhetstillståndsvillkoret kan användas för att utesluta enheter som är hybrid-Azure AD-anslutna och/eller enheter som markerats som kompatibla med en Microsoft Intune-efterlevnadsprincip från en organisations principer för villkorlig åtkomst.

Till exempel *alla användare* som använder Microsoft Azure *Management* molnapp inklusive **alla enhetstillstånd** exklusive **Device Hybrid Azure AD-ansluten** och **Enhet markerad som kompatibel** och för *Access-kontroller*, **Blockera**. 
   - Det här exemplet skulle skapa en princip som bara tillåter åtkomst till Microsoft Azure Management från enheter som är hybrid Azure AD-anslutna och/eller enheter som markerats som kompatibla.

## <a name="next-steps"></a>Nästa steg

- [Villkorlig åtkomst: Bevilja](concept-conditional-access-grant.md)

- [Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)
