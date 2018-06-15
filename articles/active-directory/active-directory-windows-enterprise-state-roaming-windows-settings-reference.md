---
title: Windows 10 roaming-inställningar | Microsoft Docs
description: En fullständig lista över alla inställningar som kommer att flyttade eller säkerhetskopierade i Windows 10.
services: active-directory
keywords: företagsroaming, windows-moln
documentationcenter: ''
author: tanning
manager: mtillman
editor: curtand
ms.assetid: 17cffc3e-2928-4235-91f7-a685bd6bdcbf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 741b76935b5a6d9b2a6869ef57caa0ac0dc6351a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32159085"
---
# <a name="windows-10-roaming-settings-reference"></a>Referens för Windows 10-växlingsinställningar
Följande är en fullständig lista över alla inställningar som kommer att flyttade eller säkerhetskopierade i Windows 10. 

## <a name="devices-and-endpoints"></a>Enheter och slutpunkter
Se följande tabell för en sammanfattning av de enheter och typer som stöds av sync, säkerhetskopiering, och Återställ ramverk i Windows 10.

| Kontotyp och åtgärd | Skrivbord | Mobil |
| --- | --- | --- |
| Azure Active Directory: synkronisering |Ja |Nej |
| Azure Active Directory: säkerhetskopiering/återställning |Nej |Nej |
| Microsoft-konto: synkronisering |Ja |Ja |
| Microsoft-konto: säkerhetskopiering/återställning |Nej |Ja |

## <a name="what-is-backup"></a>Vad är säkerhetskopiering?
Windows-inställningar synkroniseras vanligtvis som standard, men vissa inställningar endast säkerhetskopieras, till exempel listan över installerade program på en enhet. Säkerhetskopiering är för mobila enheter endast och för närvarande inte tillgänglig för Enterprise tillstånd centrala användare. Säkerhetskopiering använder ett Microsoft-konto och lagrar inställningar och data i OneDrive. Om en användare inaktiverar synkronisering på enheten med appen inställningar, blir programdata som normalt synkroniseras säkerhetskopiering endast. Säkerhetskopierade data kan endast nås via återställningen under välkomstprogrammet en ny enhet. Säkerhetskopieringar kan inaktiveras via inställningar för enheter och kan hanteras och tas bort via användarens OneDrive-konto.

## <a name="windows-settings-overview"></a>Översikt över Windows-inställningar
Följande inställningsgrupper med är tillgängliga för slutanvändarna att aktivera/inaktivera inställningar för synkronisering av Windows 10-enheter.

* Tema: skrivbordsbakgrund, Användarikon, Aktivitetsfältet plats och så vidare. 
* Internet Explorer-inställningar: webbhistorik angav URL: er, Favoriter och så vidare. 
* Lösenord: [säker plats för Windows-autentiseringsuppgifter](https://technet.microsoft.com/library/jj554668.aspx), inklusive Wi-Fi-profiler 
* Språkinställningar: stavning ordlista system språkinställningar 
* Hjälpmedel: Skärmläsaren, Skärmtangentbordet Skärmförstoraren 
* Andra Windows-inställningar: information om Windows-inställningar finns

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-syncyoursettings.png)

Edge webbläsare inställningen group (Favoriter, läsning lista) synkroniserar kan aktiveras eller inaktiveras av slutanvändare via Edge-webbläsaren menyalternativet för inställningar.

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-edge.png)

För Windows 10 version 1803 eller senare, Internet Explorer i gruppen (Favoriter, angav URL: er) synkroniserar kan aktiveras eller inaktiveras av slutanvändare via Internet Explorer-inställningar. 

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Information om Windows-inställningar
I följande tabell andra poster i kolumnen grupp med inställningar som refererar till inställningar som kan inaktiveras genom att gå till Inställningar > konton > Synkronisera dina inställningar > andra Windows-inställningar. 

Interna poster i kolumnen Enhetsinställningsgruppen refererar till inställningar och appar som kan endast inaktiveras synkroniseras i själva appen eller genom att inaktivera synkronisering för hela enheten med hjälp av hantering av mobila enheter (MDM) eller inställningarna för Grupprincip.
Inställningar som inte flyttas eller synkronisering kommer inte tillhör en grupp.

| Inställningar | Skrivbord | Mobil | Grupp |
| --- | --- | --- | --- |
| **Konton**: kontobild |Synkronisering |X |Tema |
| **Konton**: andra kontoinställningar |X |X | |
| **Avancerade mobilt bredband**: Internetanslutningsdelning nätverksnamn (aktiverar automatisk identifiering av mobila Wi-Fi-anslutningar via Bluetooth) |X |X |Lösenord |
| **AppData**: enskilda appar kan synkronisera data |Synkronisera säkerhetskopiering |Synkronisera säkerhetskopiering |internt |
| **Listan över appar**: listan över installerade appar |X |säkerhetskopiering |Annat |
| **Bluetooth**: alla Bluetooth-inställningar |X |X | |
| **Kommandotolk**: kommandotolk ”” standardvärden |Synkronisering |X | |
| **Autentiseringsuppgifter**: säker plats för autentiseringsuppgifter |Synkronisering |Synkronisering |lösenord |
| **Datum, tid och Region**: automatisk tid (Internet tidssynkronisering) |Synkronisering |Synkronisering |språk |
| **Datum, tid och Region**: 24-timmarsklocka |Synkronisering |X |språk |
| **Datum, tid och Region**: datum och tid |Synkronisering |X |språk |
| **Datum, tid och Region**: tidszon | |X |språk |
| **Datum, tid och Region**: sommartid |Synkronisering |X |språk |
| **Datum, tid och Region**: land/region |Synkronisering |X |språk |
| **Datum, tid och Region**: första dagen i veckan |Synkronisering |X |språk |
| **Datum, tid och Region**: regionsformat (språk) |Synkronisering |X |språk |
| **Datum, tid och Region**: kort datum |Synkronisering |X |språk |
| **Datum, tid och Region**: långt datum |Synkronisering |X |språk |
| **Datum, tid och Region**: kort tid |Synkronisering |X |språk |
| **Datum, tid och Region**: lång tid |Synkronisering |X |språk |
| **Skrivbordsanpassning**: tema (bakgrund, systemfärg, standard systemljud, skärmsläckare) |Synkronisering |X |Tema |
| **Skrivbordsanpassning**: bildspel skrivbordsunderlägg |Synkronisering |X |Tema |
| **Skrivbordsanpassning**: inställningar för Aktivitetsfältet (position, Dölj automatiskt osv.) |Synkronisering |X |Tema |
| **Skrivbordsanpassning**: starta skärmlayout |X |säkerhetskopiering | |
| **Enheter**: delade skrivare som du har anslutit till |X |X |övrigt |
| **Edge-webbläsaren**: läsa listan |Synkronisering |Synkronisering |internt |
| **Edge-webbläsaren**: Favoriter |Synkronisering |Synkronisering |internt |
| **Edge-webbläsaren**: de främsta platser <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |internt |
| **Edge-webbläsaren**: angav URL: er <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |internt |
| **Edge-webbläsaren**: inställningar för Favoriter <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |internt |
| **Edge-webbläsaren**: Visa knappen Startsida <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |internt |
| **Edge-webbläsaren**: blockera popup-fönster <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |internt |
| **Edge-webbläsaren**: Fråga mig vad du gör med varje version <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |internt |
| **Edge-webbläsaren**: erbjuda för att spara lösenord <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |internt |
| **Edge-webbläsaren**: skicka do not track-begäranden <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |internt |
| **Edge-webbläsaren**: spara formulärposter <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |internt |
| **Edge-webbläsaren**: Visa sökning och förslag medan du skriver <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |internt |
| **Edge-webbläsaren**: cookies inställningar <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |internt |
| **Edge-webbläsaren**: låta platser spara skyddade media licenser på min enhet <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |internt |
| **Edge-webbläsaren**: Skärmläsaren inställningen <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |internt |
| **Hög kontrast**: aktivera eller inaktivera |Synkronisering |X |hjälpmedel |
| **Hög kontrast**: Temainställningar |Synkronisering |X |hjälpmedel |
| **Internet Explorer**: öppna flikar (URL: en och rubrik) |Synkronisering |Synkronisering |Internet Explorer |
| **Internet Explorer**: läsa listan |Synkronisering |Synkronisering |Internet Explorer |
| **Internet Explorer**: angav URL: er |Synkronisering |Synkronisering |Internet Explorer |
| **Internet Explorer**: webbhistorik |Synkronisering |Synkronisering |Internet Explorer |
| **Internet Explorer**: Favoriter |Synkronisering |Synkronisering |Internet Explorer |
| **Internet Explorer**: undantagna URL: er |Synkronisering |Synkronisering |Internet Explorer |
| **Internet Explorer**: hemsidor |Synkronisering |Synkronisering |Internet Explorer |
| **Internet Explorer**: domän förslag |Synkronisering |Synkronisering |Internet Explorer |
| **Tangentbord**: användare kan aktivera/ut Skärmtangentbordet |Synkronisering |X |hjälpmedel |
| **Tangentbord**: aktivera Fäst Ja (inaktiverat som standard) |Synkronisering |X |hjälpmedel |
| **Tangentbord**: aktivera filter nycklar (inaktiverat som standard) |Synkronisering |X |hjälpmedel |
| **Tangentbord**: aktivera växla nycklar (inaktiverat som standard) |Synkronisering |X |hjälpmedel |
| **Internet Explorer**: domän språk: kinesiska (CHS) QWERTY – aktivera automatisk learning |Synkronisering |X |Språk |
| **Språk**: CHS QWERTY - aktivera dynamisk kandidat rangordning |Synkronisering |X |Språk |
| **Språk**: QWERTY CHS --teckenuppsättningen förenklad kinesiska |Synkronisering |X |Språk |
| **Språk**: QWERTY CHS --teckenuppsättningen traditionell kinesiska |Synkronisering |X |Språk |
| **Språk**: CHS QWERTY - fuzzy pinyin |Synkronisering |säkerhetskopiering |Språk |
| **Språk**: CHS QWERTY - fuzzy par |Synkronisering |säkerhetskopiering |Språk |
| **Språk**: CHS QWERTY - fullständig pinyin |Synkronisering |X |Språk |
| **Språk**: CHS QWERTY - dubbla pinyin |Synkronisering |X |Språk |
| **Språk**: CHS QWERTY - läsning automatisk korrigering |Synkronisering |X |Språk |
| **Språk**: CHS QWERTY - nyckel för C/E-växeln, SKIFT |Synkronisering |X |Språk |
| **Språk**: CHS QWERTY - nyckel för C/E-växeln, Ctrl |Synkronisering |X |Språk |
| **Språk**: CHS WUBI - tecken inkommande läge |Synkronisering |X |Språk |
| **Språk**: CHS WUBI - visa de återstående kodning av kandidaten |Synkronisering |X |Språk |
| **Språk**: CHS WUBI - signal när 4-kodning är ogiltigt |Synkronisering |X |Språk |
| **Språk**: CHT Bopomofo - inkludera CJK Ext-A |Synkronisering |X |Språk |
| **Språk**: japanska IME - förutsägande skrivfel och anpassade ord |Synkronisering |Synkronisering |Språk |
| **Språk**: koreanska (KOR) IME |X |X |Språk |
| **Språk**: handskriftsigenkänning |X |X |Språk |
| **Språk**: språk profil |Synkronisering |säkerhetskopiering |Språk |
| **Språk**: stavningskontroll - Autokorrigering och markera felstavade |Synkronisering |säkerhetskopiering |Språk |
| **Språk**: listan över tangentbord |Synkronisering |säkerhetskopiering |Språk |
| **Lås skärmen**: alla Lås skärminställningar |X |X | |
| **Skärmförstoraren**: aktivera eller inaktivera (master växla) |X |X |Hjälpmedel |
| **Skärmförstoraren**: aktivera och inaktivera inverteras färg (inaktiverat som standard) |Synkronisering |X |Hjälpmedel |
| **Skärmförstoraren**: spåra - Följ tangentbordsfokus |Synkronisering |X |Hjälpmedel |
| **Skärmförstoraren**: spåra - Följ markören |Synkronisering |X |Hjälpmedel |
| **Skärmförstoraren**: starta när användare loggar in (inaktiverat som standard) |Synkronisering |X |Hjälpmedel |
| **Musen**: ändra storlek på markören |Synkronisering |X |övrigt |
| **Musen**: ändra färg på markören |Synkronisering |X |övrigt |
| **Musen**: alla inställningar |X |X | |
| **Skärmläsaren**: Snabbstart |Synkronisering |X |Hjälpmedel |
| **Skärmläsaren**: användarna kan ändra Skärmläsaren tala breddsteg |Synkronisering |X |Hjälpmedel |
| **Skärmläsaren**: användare kan aktivera eller inaktivera Skärmläsaren läsa tips för vanliga objekt (på som standard) |Synkronisering |X |Hjälpmedel |
| **Skärmläsaren**: användare kan aktivera eller inaktivera om de kan höra tecken (på som standard) |Synkronisering |X |Hjälpmedel |
| **Skärmläsaren**: användare kan aktivera eller inaktivera om de kan höra skrivna ord (på som standard) |Synkronisering |X |Hjälpmedel |
| **Skärmläsaren**: har Infoga markören efter Skärmläsaren (på som standard) |Synkronisering |X |Hjälpmedel |
| **Skärmläsaren**: aktivera visuella markeringar Skärmläsaren markören (på som standard) |Synkronisering |X |Hjälpmedel |
| **Skärmläsaren**: spela upp ljud (på som standard) |Synkronisering |X |Hjälpmedel |
| **Skärmläsaren**: aktivera nycklar på skärmtangentbordet när du lyfter din fingeravtrycksläsare (inaktiverat som standard) |Synkronisering |X |Hjälpmedel |
| **Hjälpmedel**: Ange tjocklek blinkande markör |Synkronisering |X |Hjälpmedel |
| **Hjälpmedel**: ta bort bakgrundsbilder (inaktiverat som standard) |Synkronisering |X |Hjälpmedel |
| **Ström och strömsparläge**: alla inställningar |X |X | |
| **Starta skärmen anpassning**: dekorfärg färg (endast mobil) |X |Synkronisering |Tema |
| **Att skriva**: stavningsordlista |Synkronisering |säkerhetskopiering |Språk |
| **Att skriva**: Autokorrigering felstavat ord |Synkronisering |säkerhetskopiering |Språk |
| **Att skriva**: Markera felstavade ord |Synkronisering |säkerhetskopiering |Språk |
| **Att skriva**: Visa textförslag medan du skriver |Synkronisering |säkerhetskopiering |Språk |
| **Att skriva**: Lägg till ett blanksteg när du har valt ett förslag på en text |Synkronisering |säkerhetskopiering |Språk |
| **Att skriva**: lägga till en period när jag double-tryck på blanksteg |Synkronisering |säkerhetskopiering |Språk |
| **Att skriva**: inledande versal i varje mening |Synkronisering |säkerhetskopiering |Språk |
| **Att skriva**: använda versaler när jag double-tryck SKIFT-tangenten |Synkronisering |säkerhetskopiering |Språk |
| **Att skriva**: spela upp nyckeln ljud medan du skriver |Synkronisering |säkerhetskopiering |Språk |
| **Att skriva**: anpassningsdata för Skärmtangentbordet |Synkronisering |säkerhetskopiering |Språk |
| **Wi-Fi**: Wi-Fi-profiler (endast WPA) |Synkronisering |Synkronisering |Lösenord |

###### <a name="footnote-1"></a>Fotnot 1
Minsta OS-versionen av Windows skapare Update (skapa 15063) som stöds. 

## <a name="related-topics"></a>Relaterade ämnen
* [Övergripande översikt över Enterprise tillstånd](active-directory-windows-enterprise-state-roaming-overview.md)
* [Aktivera företagsroaming i Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [Inställningar och dataroaming vanliga frågor och svar](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Gruppen principer och MDM-inställningar för synkronisering av inställningar](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Felsökning](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
