---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med mobil program för arbets dagar | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och arbets dagarnas mobil program.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: 256da169761da486d8ac064a2f58a59be43bb5df
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754724"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med mobil program för arbets dagar

I den här självstudien får du lära dig hur du integrerar Azure Active Directory (Azure AD), villkorlig åtkomst och Intune med Workday-Mobile Apps. När du integrerar Workday Mobile Apps med Microsoft kan du:

* Se till att enheterna är kompatibla med dina principer innan du loggar in.
* Lägg till kontroller i Workday-appen för att se till att användarna har åtkomst till företagets data på ett säkert sätt. 
* Kontroll i Azure AD som har åtkomst till Workday.
* Gör det möjligt för användarna att logga in automatiskt till Workday med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* Integrera Workday med Azure AD
* Självstudie: [Azure Active Directory integration med enkel inloggning (SSO) med Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Microsofts principer för villkorlig åtkomst och Intune med arbets dagens mobila program.

* Det federerade programmet för arbets dagar kan nu konfigureras med Azure AD för att aktivera SSO. Om du vill ha mer information om hur du konfigurerar ska du följa [den här](workday-tutorial.md) länken.

> [!NOTE] 
> Workday har inte stöd för Intunes skydds principer för appar. Du måste använda hantering av mobila enheter för att använda villkorlig åtkomst.


## <a name="ensure-users-have-access-to-the-workday-mobile-app"></a>Se till att användarna har åtkomst till Workday-mobilappen:

Konfigurera arbets dagar för att tillåta åtkomst till deras erbjudanden för mobilappar. Du måste konfigurera följande principer för mobila enheter:

Du kan konfigurera dessa genom att följa dessa anvisningar:

1. Gå till säkerhets principer för domäner för funktionellt Area-rapport.
2. Välj en säkerhets princip.
    * Mobil användning – Android
    * Mobil användning – iPad
    * Mobil användning – iPhone
3. Klicka på Redigera behörigheter.
4. Markera kryss rutan Visa eller ändra om du vill ge säkerhets grupperna åtkomst till rapporten eller uppgiften skydds bara objekt.
5. Markera kryss rutan hämta eller placera för att ge säkerhets grupper åtkomst till integrering och rapport-eller aktivitets skydds bara åtgärder.

Aktivera väntande säkerhets princip ändringar genom att köra **Aktivera väntande säkerhets princip ändringar** .

## <a name="open-workday-login-page-in-mobile-browser"></a>Öppna inloggnings sidan för arbets dagar i mobil webbläsare:

Om du vill tillämpa villkorlig åtkomst till Workday-mobilappen krävs det att appen öppnas i en extern webbläsare. Detta kan göras genom att markera kryss rutan **Aktivera mobil webbläsare SSO för inbyggda appar** i **Redigera klient organisations konfiguration-säkerhet.** Det kräver att en Intune-godkänd webbläsare installeras på enheten för iOS och i arbets profilen för Android

![Inloggning för mobil webbläsare](./media/workday-tutorial/mobile-browser.png)

## <a name="setup-conditional-access-policy"></a>Konfigurera princip för villkorlig åtkomst:

Den här principen påverkar bara inloggningen på en iOS-eller Android-enhet. Om du vill utöka den till alla plattformar väljer du bara **en enhet.** Den här principen kräver att enheten är kompatibel med principen och kommer att verifiera denna via Microsoft Intune. På grund av Android med arbets profiler, ska detta blockera alla användare från att logga in på Workday (webb eller app) om de inte loggar in via sin arbets profil och har installerat appen via Intune-företagsportal. Det finns ett ytterligare steg för iOS för att se till att samma situation gäller. Här följer några skärm bilder av installations programmet för villkorlig åtkomst.

**Workday stöder följande åtkomst kontroller:**
* Kräv Multi-Factor Authentication
* Kräv att enheten ska markeras som kompatibel

**Workday-appen stöder inte följande:**
* Kräv godkänd klientapp
* Kräv app Protection-princip (förhands granskning)

Utför följande steg för att konfigurera **arbets dagar** som **hanterad enhet** :

![Konfigurera princip för villkorlig åtkomst](./media/workday-tutorial/managed-devices-only.png)

1. Klicka på **start > Microsoft Intune > villkorliga Access-Policies > endast hanterade enheter** 

1. På sidan **endast hanterade enheter** ger du **namn** fältet värde som `Managed Devices Only` och klickar på **molnappar eller åtgärder** .

1. Utför följande steg i **molnappar eller-åtgärder** .

    a. Växla **Välj vad den här principen gäller för** **molnappar** .

    b. I inkludera, klickar du på **Välj appar** .

    c. Välj **arbets dag** i listan Välj.

    d. Klicka på **Klar** .

1. Aktivera principen för **att aktivera** .

1. Klicka på **Spara** .

Utför följande steg för att **bevilja** åtkomst:

![Princip för villkorlig åtkomst för Workday-installation](./media/workday-tutorial/managed-devices-only-2.png)

1. Klicka på **start > Microsoft Intune > villkorliga Access-Policies > endast hanterade enheter** 

1. På sidan **endast hanterade enheter** ger du **namn** fältet värde som `Managed Devices Only` och klickar på **åtkomst kontroller > bevilja tilldelning** .

1. Utför följande steg på sidan **bevilja** .

    a. Välj de kontroller som ska tillämpas som **beviljad åtkomst** .

    b. Markera kryss rutan **Kräv att enheten är markerad som kompatibel** .

    c. Välj **Kräv en av de valda kontrollerna** .

    d. Klicka på **Välj** .

1. Aktivera principen för **att aktivera** .

1. Klicka på **Spara**

## <a name="set-up-device-compliance-policy"></a>Konfigurera efterlevnadsprinciper för enheter:

För att säkerställa att iOS-enheter bara kan logga in via en MDM-hanterad Workday-app måste du blockera appen App Store genom att lägga till **com. workday. workdayapp** i listan över begränsade appar. Detta säkerställer att endast enheter som har Workday-appen installerat via företags portalen kan komma åt Workday. För webbläsare kommer de endast att kunna komma åt Workday om enheten hanteras av Intune och de använder en hanterad webbläsare.

![Efterlevnadsprincip för installation av workday-enhet](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-microsoft-intune-app-configuration-policies"></a>Konfigurera Microsoft Intune konfigurations principer för appar:

| Scenario | Nyckel värdes par |
|----------------------------------------------------------------------------------------   |-----------|
| Fyll i fälten för klient och webb adress automatiskt för:<br>● Arbets dag på Android när du aktiverar Android for Work-profiler.<br>● Arbets dag på iPad och iPhone.     | Använd de här värdena för att konfigurera din klient: <br>● Konfigurations nyckel = UserGroupCode<br>● Värde typ = sträng <br>● Konfigurations värde = ditt klient namn. Exempel: GMS<br>Använd följande värden för att konfigurera webb adressen:<br>● Konfigurations nyckel = AppServiceHost<br>● Värde typ = sträng<br>● Konfigurations värde = den grundläggande URL: en för din klient. Exempel: https://www.myworkday.com                              |   |
| Inaktivera de här åtgärderna för Workday på iPad och iPhone:<br>● Klipp ut, kopiera och klistra in<br>● Utskrift                       | Ange värdet (Boolean) till false för de här nycklarna för att inaktivera funktionen:<br>● AllowCutCopyPaste<br>● AllowPrint  |
| Inaktivera skärm bilder för arbets dagar på Android. |Ange värdet (Boolean) till false i AllowScreenshots-nyckeln för att inaktivera funktionen.|
| Inaktivera föreslagna uppdateringar för dina användare.|Ange värdet (Boolean) till false i AllowSuggestedUpdates-nyckeln för att inaktivera funktionen.|
|Anpassa App Store-URL: en för att dirigera mobila användare till valfri App Store.|Använd de här värdena för att ändra App Store-URL:<br>● Konfigurations nyckel = AppUpdateURL<br>● Värde typ = sträng<br> ● Konfigurations värde = App Store-URL|
|       |


## <a name="ios-configuration-policies"></a>Konfigurations principer för iOS:

1. Gå till https://portal.azure.com/ och logga in
2. Sök efter **Intune** eller klicka på widgeten i listan.
3. Gå till **klient program – > appar – > konfigurations principer för appar-> + Lägg till > hanterade enheter**
4. Ange ett namn.
5. Under **plattform** väljer du **iOS/iPad**
6. Under **associerad app** väljer du arbets dag för iOS-app som du har lagt till
7. Klicka på **konfigurations inställningar** och under **konfigurations inställnings format** väljer du **Ange XML-data**
8. Här är en exempel-XML-fil. Lägg till de konfigurationer som du vill använda. Ersätt **STRING_VALUE** med strängen som du vill använda. Ersätt `<true />` eller `<false />` med `<true />` eller  `<false />` . Om du inte lägger till en konfiguration fungerar den som den skulle vara inställd på True.

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
9. Klicka på Lägg till
10. Uppdatera sidan och klicka på den nyligen skapade principen.
11. Klicka på tilldelningar och välj vem du vill att appen ska gälla för.
12. Klicka på Spara.

## <a name="android-configuration-policies"></a>Konfigurations principer för Android:

1. Gå till `https://portal.azure.com/` och logga in.
2. Sök efter **Intune** eller klicka på widgeten i listan.
3. Gå till **klient program – > appar – > konfigurations principer för appar-> + Lägg till > hanterade enheter**
5. Ange ett namn. 
6. Under **plattform** väljer du **Android**
7. Under **associerad app** väljer du arbets dag för den Android-app som du har lagt till
8. Klicka på **konfigurations inställningar** och under **konfigurations inställnings format** väljer du **Ange JSON-data**

