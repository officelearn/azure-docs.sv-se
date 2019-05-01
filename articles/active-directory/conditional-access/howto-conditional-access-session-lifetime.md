---
title: Konfigurera autentisering sessionshantering med villkorlig åtkomst i Azure Active Directory
description: Anpassa Azure AD authentication sessionskonfiguration inklusive användarinloggning i persistence för frekvens och webbläsare.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 884b3c2d3f4414427df87ee5f04138416f363b1c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577232"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Konfigurera autentisering sessionshantering med villkorlig åtkomst

I distributioner, kan organisationer har ett behov av att begränsa autentisering sessioner. Vissa scenarier kan innehålla:

* Åtkomst till resurser från en ohanterad eller delad enhet
* Åtkomst till känslig information från ett externt nätverk
* Stor effekt användare
* Verksamhetskritiska program

Kontrollerna för villkorlig åtkomst kan du skapa principer som är riktade mot specifika användningsfall i din organisation utan att påverka alla användare.

Innan du börjar information om hur du konfigurerar principen, låt oss nu undersöka standardkonfigurationen.

## <a name="user-sign-in-frequency"></a>Användaren loggar in frekvens

Logga in frekvensen definierar hur lång tid innan en användare uppmanas att logga in igen när du försöker komma åt en resurs.

Standardkonfigurationen för Azure Active Directory (Azure AD) för användarinloggning i frekvens är ett rullande fönster 90 dagar. Be användarens autentiseringsuppgifter ofta ser ut som en användningstyperna du ska göra, men det kan straffpoängen: användare som har tränats anger sina autentiseringsuppgifter utan att tänka kan oavsiktligt skicka till skadliga autentiseringsuppgifter.

Det kanske låter oroväckande inte be en användare kan logga in i 90 dagar i verkligheten varje överträdelse av IT-principer då återkallas sessionen. Några exempel omfattar (men är inte begränsad till) en lösenordsändring, över enheter eller inaktivera-konto. Du kan också uttryckligen [återkalla användarnas sessioner med hjälp av PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Standardkonfigurationen för Azure AD handlar om att ”fråga inte användare att ange sina autentiseringsuppgifter om säkerhetspositionen för sina sessioner inte har ändrats”.

Logga in frekvensen inställningen fungerar med appar som har implementerat OATH2 eller OIDC-protokoll enligt standarderna. De flesta Microsoft inbyggda appar för Windows, Mac och mobil kompatibel med inställningen.

## <a name="persistence-of-browsing-sessions"></a>Persistence för surfningssessioner

En beständig webbläsarsession kan användarna förbli inloggade när du stänger och sedan öppna sina webbläsarfönster.

Azure AD-standarden för persistence för webbläsare kan användare på personliga enheter för att välja om du vill spara sessionen genom att visa en ”Håll dig inloggad”? frågar efter en lyckad autentisering. Om webbläsaren persistence har konfigurerats i AD FS med hjälp av vägledningen i artikeln [AD FS Sign-On inställningar för enkel](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), kommer vi att följa principen och spara Azure AD-session. Du kan också konfigurera om användare i din klient finns i ”vilande inloggad”? fråga genom att ändra en inställning i fönstret i Azure-portalen genom att följa vägledningen i artikeln för företagsanpassning [anpassa din Azure AD-inloggningssida](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Konfigurera autentisering sessionskontroller

Villkorlig åtkomst är en Azure AD Premium-funktion som kräver en premiumlicens. Om du vill veta mer om villkorlig åtkomst, se [vad är villkorlig åtkomst i Azure Active Directory?](overview.md#license-requirements-for-using-conditional-access)

> [!WARNING]
> Om du använder den [konfigurerbara livslängd för uppdateringstoken](../develop/active-directory-configurable-token-lifetimes.md) funktion för närvarande i offentlig förhandsversion, Observera att vi inte stöder skapandet av två olika principer för samma kombination av användare eller app: en med den här funktionen och en annan med konfigurerbara livslängd för token-funktionen. Microsoft planerar att inaktivera funktionen konfigurerbara livslängd för token på 1 November och Ersätt den med funktionen för villkorlig åtkomst autentisering session hantering.  

### <a name="policy-1-sign-in-frequency-control"></a>Principen 1: Logga in frekvensen kontroll

1. Skapa ny princip
1. Välj alla nödvändiga villkor för kundens miljö, inklusive mål moln-appar.

   > [!NOTE]
   > Det rekommenderas att ställa in lika autentisering fråga frekvens för viktiga Microsoft Office-appar som Exchange Online och SharePoint Online för bästa användarupplevelsen.

1. Gå till **åtkomstkontroller** > **Session** och klicka på **inloggning frekvens**
1. Ange det obligatoriska värdet i dagar och tider i den första textrutan
1. Välj ett värde på **timmar** eller **dagar** listrutan
1. Spara princip

![Princip för villkorlig åtkomst som konfigurerats för inloggning i frekvens](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Registrerade Windows-enheter logga in på enheten anses vara en uppmaning i Azure AD. Om du har konfigurerat tecknet i säkerhetskopieringsfrekvens till 24 timmar för Office-appar, registrerad användare på Azure AD Windows enheter att uppfylla inloggningen frekvens princip genom att logga in på enheten och inte uppmanas igen när du öppnar Office-appar.

Om du har konfigurerat annan inloggning frekvens för olika web apps som körs i samma webbläsarsession tillämpas striktaste principen på båda apparna eftersom alla appar som körs i samma webbläsarsession delar en enda session-token.

### <a name="policy-2-persistent-browser-session"></a>Princip för 2: Beständig webbläsarsession

1. Skapa ny princip
1. Välj alla nödvändiga villkor.

   > [!NOTE]
   > Observera att den här kontrollen kräver för att välja ”alla Molnappar” som ett villkor

1. Gå till **åtkomstkontroller** > **Session** och klicka på **beständiga webbläsarsession**
1. Välj ett värde från listrutan
1. Spara princip

![Princip för villkorlig åtkomst som konfigurerats för beständiga webbläsare](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Beständiga webbläsarsession konfiguration i Azure AD villkorlig åtkomst kommer att skriva över den ”Håll dig inloggad”? Ange i fönstret i Azure-portalen för samma användare för företagsanpassning, om du har konfigurerat båda principerna.

## <a name="validation"></a>Validering

Verktyget hypotetiska används för att simulera en inloggning från användaren till målprogrammet och andra villkor baserat på hur du konfigurerat din princip. Sessionen management-kontroller för autentisering som visas i resultatet av verktyget.

![Vad händer om verktyget resulterar för villkorlig](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Distribution av princip

För att säkerställa att din princip fungerar som förväntat, är rutin att testa den innan du distribuerar det till produktion. Använd helst en test-klient för att kontrollera om den nya principen fungerar som avsett. Mer information finns i artikeln [bästa praxis för villkorlig åtkomst i Azure Active Directory](best-practices.md).

## <a name="next-steps"></a>Nästa steg

* Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst finns i artikeln [kräver MFA för specifika appar med villkorlig åtkomst i Azure Active Directory](app-based-mfa.md).
* Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö kan du läsa artikeln [bästa praxis för villkorlig åtkomst i Azure Active Directory](best-practices.md).
