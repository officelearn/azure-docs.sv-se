---
title: Referens för roaminginställningar i Windows 10 - Azure Active Directory
description: Inställningar som kommer att flyttas eller säkerhetskopieras i Windows 10 med ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a7abc402f1fc2e449e7aac5effdb01b6b941100
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672630"
---
# <a name="windows-10-roaming-settings-reference"></a>Referens för Windows 10-växlingsinställningar

Följande är en lista över de inställningar som kommer att strövas eller säkerhetskopieras i Windows 10. 

## <a name="devices-and-endpoints"></a>Enheter och slutpunkter

Se följande tabell för en sammanfattning av de enheter och kontotyper som stöds av ramverket för synkronisering, säkerhetskopiering och återställning i Windows 10.

| Kontotyp och åtgärd | Skrivbord | Mobil |
| --- | --- | --- |
| Azure Active Directory: synkronisering |Ja |Inga |
| Azure Active Directory: säkerhetskopiering/återställning |Inga |Inga |
| Microsoft-konto: synkronisera |Ja |Ja |
| Microsoft-konto: säkerhetskopiering/återställning |Inga |Ja |

## <a name="what-is-backup"></a>Vad är backup?

Windows-inställningarna synkroniseras vanligtvis som standard, men vissa inställningar säkerhetskopieras bara, till exempel listan över installerade program på en enhet. Säkerhetskopiering är endast för mobila enheter och är för närvarande inte tillgängligt för Roaminganvändare i företagtillstånd. Säkerhetskopiering använder ett Microsoft-konto och lagrar inställningar och programdata i OneDrive. Om en användare inaktiverar synkronisering på enheten med hjälp av appen Inställningar blir programdata som normalt synkroniseras endast säkerhetskopiering. Säkerhetskopierade data kan endast nås via återställningen under den första körningen av en ny enhet. Säkerhetskopior kan inaktiveras via enhetsinställningarna och kan hanteras och tas bort via användarens OneDrive-konto.

## <a name="windows-settings-overview"></a>Översikt över Inställningar i Windows

Följande inställningsgrupper är tillgängliga för slutanvändare för att aktivera/inaktivera synkronisering av inställningar på Windows 10-enheter.

* Tema: skrivbordsbakgrund, användarpanel, aktivitetsfältets position, etc. 
* Inställningar för Internet Explorer: webbhistorik, maskinskrivna webbadresser, favoriter osv. 
* Lösenord: Windows-autentiseringshanteraren, inklusive Wi-Fi-profiler 
* Språkinställningar: stavningsordlista, inställningar för systemspråk 
* Hjälpmedel: skärmläsare, skärmtangentbord, förstoringsglas 
* Andra Windows-inställningar: se Information om Windows-inställningar
* Webbläsarinställning för Microsoft Edge: Microsoft Edge-favoriter, läslista och andra inställningar

![Synkronisera dina inställningar](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-syncyoursettings.png)

> [!NOTE]
> Den här artikeln gäller den HTML-baserade webbläsaren Microsoft Edge Legacy som lanserades med Windows 10 i juli 2015. Artikeln gäller inte den nya Microsoft Edge Chromium-baserade webbläsaren som släpptes den 15 januari 2020. Mer information om synkroniseringsbeteendet för den nya Microsoft Edge finns i artikeln [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

Microsoft Edge-webbläsarinställningsgrupp (favoriter, läslista) synkronisering kan aktiveras eller inaktiveras av slutanvändare via menyalternativet Inställningar för Microsoft Edge.

![Konto](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-edge.png)

För Windows 10 version 1803 eller senare kan internet explorer-inställningsgrupp (favoriter, maskinskrivna webbadresser) aktiveras eller inaktiveras av slutanvändare via menyalternativet Inställningar i Internet Explorer. 

![Inställningar](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Information om Windows-inställningar

I följande tabell refererar Andra poster i kolumnen Inställningar grupp till inställningar som kan inaktiveras genom att gå till Inställningar > konton > Synkronisera inställningarna > Andra Windows-inställningar. 

Interna poster i kolumnen Inställningar grupp refererar till inställningar och appar som bara kan inaktiveras från synkronisering i själva appen eller genom att inaktivera synkronisering för hela enheten med hjälp av mdm-inställningar (Mobile Device Management) eller Grupprincip.
Inställningar som inte rör sig eller synkroniseras tillhör inte en grupp.

| Inställningar | Skrivbord | Mobil | Grupp |
| --- | --- | --- | --- |
| **Konton**: kontobild |sync |X |Tema |
| **Konton**: andra kontoinställningar |X |X | |
| **Avancerat mobilt bredband:** Nätverksnamn för internetanslutning (möjliggör automatisk upptäckt av mobila Wi-Fi-hotspots via Bluetooth) |X |X |Lösenord |
| **Appdata**: enskilda appar kan synkronisera data |synkronisera säkerhetskopiering |synkronisera säkerhetskopiering |Interna |
| **Applista**: lista över installerade appar |X |säkerhetskopiering |Annat |
| **Bluetooth**: alla Bluetooth-inställningar |X |X | |
| **Kommandotolken**: Inställningar för kommandotolken "Standardinställningar" |sync |X |Interna |
| **Autentiseringsuppgifter**: Skåp för autentiseringsuppgifter |sync |sync |password |
| **Datum, tid och region**: automatisk tid (internettidssynkronisering) |sync |sync |language |
| **Datum, tid och region:** 24-timmarsklocka |sync |X |language |
| **Datum, tid och region**: datum och tid |sync |X |language |
| **Datum, tid och region**: tidszon | |X |language |
| **Datum, tid och region**: sommartid |sync |X |language |
| **Datum, tid och region**: land/region |sync |X |language |
| **Datum, tid och region:** första veckodagen |sync |X |language |
| **Datum, tid och region**: regionformat (språk) |sync |X |language |
| **Datum, tid och region:** kort datum |sync |X |language |
| **Datum, tid och region**: långt datum |sync |X |language |
| **Datum, tid och region:** kort tid |sync |X |language |
| **Datum, tid och region:** lång tid |sync |X |language |
| **Desktop personalisering:** skrivbord tema (bakgrund, systemfärg, standardsystem ljud, skärmsläckare) |sync |X |Tema |
| **Desktop personalisering:** bildspel tapeter |sync |X |Tema |
| **Desktop personalisering:** Aktivitetsfältet inställningar (position, auto-hide, etc.) |sync |X |Tema |
| **Anpassning av stationära**datorer: startskärmslayout |X |säkerhetskopiering | |
| **Enheter**: delade skrivare som du har anslutit till |X |X |övrigt |
| **Microsoft Edge webbläsare:** läslista |sync |sync |Interna |
| **Microsoft Edge webbläsare:** favoriter |sync |sync |Interna |
| **Microsoft Edge webbläsare:** topp webbplatser <sup> [[1]](#footnote-1)</sup> |sync |sync |Interna |
| **Microsoft Edge webbläsare:** skrivit webbadresser <sup> [[1]](#footnote-1)</sup> |sync |sync |Interna |
| **Microsoft Edge webbläsare:** favoriter bar inställningar <sup> [[1]](#footnote-1)</sup> |sync |sync |Interna |
| **Microsoft Edge webbläsare**: visa hemknappen <sup> [[1]](#footnote-1)</sup> |sync |sync |Interna |
| **Microsoft Edge webbläsare**: blockera popup-fönster <sup> [[1]](#footnote-1)</sup> |sync |sync |Interna |
| **Microsoft Edge webbläsare:** fråga mig vad jag ska göra med varje nedladdning <sup> [[1]](#footnote-1)</sup> |sync |sync |Interna |
| **Microsoft Edge webbläsare**: erbjuda sig att spara lösenord <sup> [[1]](#footnote-1)</sup> |sync |sync |Interna |
| **Microsoft Edge webbläsare:** skicka inte spåra förfrågningar <sup> [[1]](#footnote-1)</sup> |sync |sync |Interna |
| **Microsoft Edge webbläsare**: spara formulärposter <sup> [[1]](#footnote-1)</sup> |sync |sync |Interna |
| **Microsoft Edge webbläsare:** visa sök-och webbplatsförslag som jag skriver <sup> [[1]](#footnote-1)</sup> |sync |sync |Interna |
| **Microsoft Edge webbläsare**: cookies inställning <sup> [[1]](#footnote-1)</sup> |sync |sync |Interna |
| **Microsoft Edge webbläsare:** låt webbplatser spara skyddade medielicenser på min enhet <sup> [[1]](#footnote-1)</sup> |sync |sync |Interna |
| **Microsoft Edge webbläsare**: skärmläsare inställning <sup> [[1]](#footnote-1)</sup> |sync |sync |Interna |
| **Hög kontrast**: På eller av |sync |X |hjälpmedel |
| **Hög kontrast**: Temainställningar |sync |X |hjälpmedel |
| **Internet Explorer**: öppna flikar (URL och titel) |sync |sync |Internet Explorer |
| **Internet Explorer**: läslista |sync |sync |Internet Explorer |
| **Internet Explorer**: skrivna webbadresser |sync |sync |Internet Explorer |
| **Internet Explorer**: webbhistorik |sync |sync |Internet Explorer |
| **Internet Explorer**: favoriter |sync |sync |Internet Explorer |
| **Internet Explorer:** uteslutna webbadresser |sync |sync |Internet Explorer |
| **Internet Explorer**: hemsidor |sync |sync |Internet Explorer |
| **Internet Explorer**: domänförslag |sync |sync |Internet Explorer |
| **Tangentbord:** användare kan slå på/av skärmtangentbordet |sync |X |hjälpmedel |
| **Tangentbord:** slå på klibbigt ja (av som standard) |sync |X |hjälpmedel |
| **Tangentbord:** aktivera filtertangenter (av som standard) |sync |X |hjälpmedel |
| **Tangentbord:** aktivera växlingsknappar (av som standard) |sync |X |hjälpmedel |
| **Internet Explorer**: domänspråk: Kinesiska (CHS) QWERTY - aktivera självlärande |sync |X |Språk |
| **Språk**: CHS QWERTY - aktivera dynamisk kandidatrankning |sync |X |Språk |
| **Språk**: CHS QWERTY - char-set förenklad kinesiska |sync |X |Språk |
| **Språk**: CHS QWERTY - char-set traditionell kinesiska |sync |X |Språk |
| **Språk**: CHS QWERTY - fuzzy pinyin |sync |säkerhetskopiering |Språk |
| **Språk**: CHS QWERTY - luddiga par |sync |säkerhetskopiering |Språk |
| **Språk**: CHS QWERTY - full pinyin |sync |X |Språk |
| **Språk**: CHS QWERTY - dubbel pinyin |sync |X |Språk |
| **Språk**: CHS QWERTY - läsa automatisk korrigering |sync |X |Språk |
| **Språk**: CHS QWERTY - C/E-brytare, skift |sync |X |Språk |
| **Språk**: CHS QWERTY - C/E-knapp, Ctrl |sync |X |Språk |
| **Språk**: CHS WUBI - inmatningsläge med en tecken |sync |X |Språk |
| **Språk**: CHS WUBI - visa den återstående kodningen av kandidaten |sync |X |Språk |
| **Språk**: CHS WUBI - pip när 4-kodning är ogiltig |sync |X |Språk |
| **Språk**: CHT Bopomofo - inkludera CJK Ext-A |sync |X |Språk |
| **Språk**: Japansk IME - prediktiv skriva och anpassade ord |sync |sync |Språk |
| **Språk**: Koreanska (KOR) IME |X |X |Språk |
| **Språk**: handskriftsigenkänning |X |X |Språk |
| **Språk**: språkprofil |sync |säkerhetskopiering |Språk |
| **Språk**: stavningskontroll - autokorrigering och markera felstavningar |sync |säkerhetskopiering |Språk |
| **Språk**: lista över tangentbord |sync |säkerhetskopiering |Språk |
| **Låsskärm:** alla inställningar för låsskärmen |X |X | |
| **Förstoringsglas**: på eller av (master växla) |X |X |Enkel åtkomst |
| **Förstoringsglas:** aktivera eller inaktivera inverteringsfärg (av som standard) |sync |X |Enkel åtkomst |
| **Förstoringsglas:** spårning - följ tangentbordet fokus |sync |X |Enkel åtkomst |
| **Förstoringsglas:** spårning - följ muspekaren |sync |X |Enkel åtkomst |
| **Förstoringsglas:** starta när användare loggar in (av som standard) |sync |X |Enkel åtkomst |
| **mus:** ändra storleken på muspekaren |sync |X |övrigt |
| **mus:** ändra färg på muspekaren |sync |X |övrigt |
| **Mus:** alla andra inställningar |X |X | |
| **Skärmläsaren**: snabbstart |sync |X |Enkel åtkomst |
| **Skärmläsaren**: användare kan ändra Berättarens talhöjd |sync |X |Enkel åtkomst |
| **Skärmläsaren**: användare kan aktivera eller inaktivera läsartips för Skärmläsaren för vanliga objekt (aktiverat som standard) |sync |X |Enkel åtkomst |
| **Skärmläsaren**: användare kan aktivera eller inaktivera om de kan höra maskinskrivna tecken (på som standard) |sync |X |Enkel åtkomst |
| **Skärmläsaren**: användare kan aktivera eller inaktivera om de kan höra maskinskrivna ord (på som standard) |sync |X |Enkel åtkomst |
| **Skärmläsaren**: har infogat markören efter Skärmläsaren (på som standard) |sync |X |Enkel åtkomst |
| **Skärmläsare**: aktivera visuell markering av Skärmläsarens markör (aktivera som standard) |sync |X |Enkel åtkomst |
| **Skärmläsare**: spela upp ljudsignaler (på som standard) |sync |X |Enkel åtkomst |
| **Skärmläsaren**: aktivera tangenterna på pektangentbordet när du lyfter fingret (av som standard) |sync |X |Enkel åtkomst |
| **Enkel åtkomst:** ställ in tjockleken på den blinkande markören |sync |X |Enkel åtkomst |
| **Enkel åtkomst:** ta bort bakgrundsbilder (av som standard) |sync |X |Enkel åtkomst |
| **Ström och strömsparläge**: alla inställningar |X |X | |
| **Anpassning av Startskärmen:** accentfärg (endast telefon) |X |sync |Tema |
| **Skriva**: stavningsordlista |sync |säkerhetskopiering |Språk |
| **Skriva:** autokorrigering felstavat ord |sync |säkerhetskopiering |Språk |
| **Skriva:** markera felstavade ord |sync |säkerhetskopiering |Språk |
| **Skriva:** visa textförslag medan jag skriver |sync |säkerhetskopiering |Språk |
| **Skriva:** lägg till ett blanksteg när jag har valt ett textförslag |sync |säkerhetskopiering |Språk |
| **Skriva:** lägg till en punkt efter att jag dubbeltrycker på mellanslagstangenten |sync |säkerhetskopiering |Språk |
| **Skriva:** versal den första bokstaven i varje mening |sync |säkerhetskopiering |Språk |
| **Skriva:** använd alla versaler när jag dubbeltrycker skifttangent |sync |säkerhetskopiering |Språk |
| **Skriva:** spela upp viktiga ljud som jag skriver |sync |säkerhetskopiering |Språk |
| **Skriva:** anpassningsdata för pektangentbord |sync |säkerhetskopiering |Språk |
| **Wi-Fi:** Wi-Fi-profiler (endast WPA) |sync |sync |Lösenord |

###### <a name="footnote-1"></a>Fotnot 1

Lägsta OS-version som stöds av Windows Creators Update (Build 15063). 

## <a name="next-steps"></a>Nästa steg

En översikt finns i [roaming översikt över företagstillstånd](enterprise-state-roaming-overview.md).
