---
title: Ställ in tvåstegsverifiering för mitt konto för arbetet eller skolan | Microsoft Docs
description: 'När ditt företag konfigurerar Azure Multi-Factor Authentication, uppmanas du att registrera sig för tvåstegsverifiering. Lär dig hur du ställer in. '
services: multi-factor-authentication
keywords: hur du använder azure-katalogen active directory i molnet, active directory-självstudier
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.reviewer: richagi
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 04b8d2b8d7d84bd4c6b46507be5d597c03d9dbb0
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
ms.locfileid: "28198363"
---
# <a name="set-up-my-account-for-two-step-verification"></a>Konfigurera mitt konto för tvåstegsverifiering
Tvåstegsverifiering är en ytterligare säkerhetssteg som hjälper dig att skydda ditt konto genom att göra det svårare för andra att logga in. Om du läser den här artikeln har du förmodligen ett e-postmeddelande från administratören arbets- eller skolkonto om Multifaktorautentisering. Eller kanske du försökte logga in och fick ett meddelande där du vill ställa in ytterligare säkerhetsverifiering. Om så är fallet, **kan inte logga in förrän du har slutfört den automatiska registreringen**.

Den här artikeln kan du ställa in din **arbets- eller skolkonto**. Om du vill aktivera tvåstegsverifiering för ditt eget, personligt Microsoft-konto finns [om tvåstegsverifiering](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="set-up-your-account"></a>Konfigurera ditt konto

När ditt företag stöd kräver att du vill börja använda tvåstegsverifiering, visas en skärm som säger **din administratör har begärt att du ställer in det här kontot för ytterligare säkerhetsverifiering**:

![Konfiguration](./media/multi-factor-authentication-end-user-first-time/first.png)

Om du vill komma igång, Välj **ställa in den nu.**

Om du inte ser en skärm som liknar detta när du loggar in, följer du anvisningarna i [hantera inställningar för tvåstegsverifiering](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) att hitta inställningssidan där du kan hantera dina alternativ för verifiering.

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>Bestäm hur du vill kontrollera din inloggningar

Den första frågan i registreringen är hur du vill att vi ska kontakta dig. Ta en titt på alternativen i tabellen och Använd länkarna för att gå till konfigurationsstegen för varje metod.

| Kontaktmetod | Beskrivning |
| --- | --- |
| [Mobilapp](#use-a-mobile-app-as-the-contact-method) |- **Ta emot meddelanden för verifiering.** Det här alternativet om ett meddelande skickas till authenticator-appen på din smartphone eller surfplatta. Visa meddelandet och, om det är tillförlitligt **autentisera** i appen. Ditt arbete eller skola kan kräva att du anger en PIN-kod innan du autentisera.<br>- **Använd verifieringskoden.** I detta läge gäller genererar autentiseringsappen en Verifieringskod som uppdateras var 30: e sekund. Ange den mest aktuella verifieringskoden i gränssnittet för inloggning.<br>Microsoft Authenticator-appen är tillgänglig för [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594), och [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071). |
| [Mobiltelefon samtal eller textmeddelande](#use-your-mobile-phone-as-the-contact-method) |- **Telefonsamtal** placerar en automatiserad röstsamtal till det telefonnummer som du anger. Besvara samtalet och tryck på # tangenten för att autentisera.<br>- **Textmeddelande** slutar ett SMS med verifieringskoden. Följande fråga i texten, svara på textmeddelandet eller ange verifieringskoden som visas i gränssnittet för inloggning. |
| [Telefonsamtal till arbete](#use-your-office-phone-as-the-contact-method) |Placerar en automatiserad röstsamtal till det telefonnummer som du anger. Besvara samtalet och trycka på #-tangenten för att autentisera. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Använda en mobilapp som kontaktmetod
Med den här metoden kräver att du installerar en autentiseringsapp på din telefon eller surfplatta. Stegen i den här artikeln är baserade på Microsoft Authenticator-appen som är tillgänglig för [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), och [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

1. Välj **mobilappen** från den nedrullningsbara listan.
2. Välj antingen **ta emot meddelanden för verifiering** eller **Använd verifieringskoden**och välj **konfigurera**.

   ![Ytterligare säkerhet verifiering skärmen](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. Öppna appen på din telefon eller surfplatta, och välj  **+**  att lägga till ett konto. (På Android-enheter, väljer du de tre punkterna sedan **Lägg till konto**.)
4. Ange att du vill lägga till ett konto för arbetet eller skolan. Skanner för QR-kod på din telefon öppnas. Om kameran inte fungerar kan du välja för att ange företagets information manuellt. Mer information finns i [manuellt lägga till ett konto](#add-an-account-manually).  
5. Skanna QR-kod bilden som visades med skärmen för att konfigurera mobilappen.  Välj **klar** att stänga skärmen QR-kod.  

   ![Skärmen för QR-kod](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. När aktiveringen är klar på telefonen, Välj **kontakta mig**.  Det här steget skickar ett meddelande eller en Verifieringskod till din telefon. Välj **Kontrollera**.  
7. Om ditt företag kräver en PIN-kod för att godkänna inloggningsverifiering, anger du den.

   ![Kryssrutan för att ange en PIN-kod](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. När PIN-koden är klar, Välj **Stäng**. Nu ska verifieringen lyckades.
9. Vi rekommenderar att du anger ditt mobiltelefonnummer ifall du skulle förlora åtkomsten till din mobilapp. Välj land i listrutan och ange ditt mobiltelefonnummer i rutan bredvid namnet på land. Välj **Nästa**.
10. Nu uppmanas du att ställa in applösenord för icke-webbläsarappar, till exempel Outlook 2010 eller senare, eller den interna e-app på Apple-enheter. Det beror på att vissa appar inte stöder tvåstegsverifiering. Om du inte använder de här apparna klickar du på **klar** och hoppa över resten av stegen.
11. Om du använder de här apparna kopiera applösenordet tillhandahålls och klistra in den i ditt program i stället för vanliga lösenord. Du kan använda samma applösenord för flera appar. Mer information [hjälp med applösenord].
12. Klicka på **Klar**.

### <a name="add-an-account-manually"></a>Lägg till ett konto manuellt
Följ dessa steg om du vill lägga till ett konto till mobilappen manuellt, i stället för QR-läsaren.

1. Välj den **ange kontot manuellt** knappen.  
2. Ange koden och Webbadressen som tillhandahålls på samma sida som visar streckkoden. Den här informationen är med i den **kod** och **URL** rutor på mobilappen.

    ![Konfiguration](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. När aktiveringen är klar, Välj **kontakta mig**. Det här steget skickar ett meddelande eller en Verifieringskod till din telefon. Välj **Kontrollera**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Använda din mobiltelefon som kontaktmetod
1. Välj **telefon för autentisering** från den nedrullningsbara listan.  

    ![Konfiguration](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. Välj ditt land i listrutan och ange ditt mobiltelefonnummer.
3. Välj den metod du föredrar att använda med mobiltelefonen - textmeddelande eller samtal.
4. Välj **kontakta mig** att verifiera ditt telefonnummer. Beroende på det läge som du har valt vi skickar ett SMS eller ringa dig. Följ anvisningarna på skärmen och väljer sedan **Kontrollera**.
5. Nu uppmanas du att ställa in applösenord för icke-webbläsarappar, till exempel Outlook 2010 eller senare, eller den interna e-app på Apple-enheter. Det beror på att vissa appar inte stöder tvåstegsverifiering. Om du inte använder de här apparna klickar du på **klar** och hoppa över resten av stegen.
6. Om du använder de här apparna kopiera applösenordet tillhandahålls och klistra in den i ditt program i stället för vanliga lösenord. Du kan använda samma applösenord för flera appar. Mer information [hjälp med applösenord].
7. Klicka på **Klar**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Använd din Arbetstelefon som kontaktmetod
1. Välj **Arbetstelefon** från listrutan  

    ![Konfiguration](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. Rutan telefonnummer fylls automatiskt med din kontaktinformation för företaget. Om talet är saknas eller är felaktig, be administratören att göra ändringar.
3. Välj **kontakta mig** för att verifiera telefonen antal och vi ringer ditt nummer. Följ anvisningarna på skärmen och väljer sedan **Kontrollera**.
4. Nu uppmanas du att ställa in applösenord för icke-webbläsarappar, till exempel Outlook 2010 eller senare, eller den interna e-app på Apple-enheter. Det beror på att vissa appar inte stöder tvåstegsverifiering. Om du inte använder de här apparna klickar du på **klar** och hoppa över resten av stegen.
5. Om du använder de här apparna kopiera applösenordet tillhandahålls och klistra in den i ditt program i stället för vanliga lösenord. Du kan använda samma applösenord för flera appar. Mer information finns i [vad är Applösenord](multi-factor-authentication-end-user-app-passwords.md).
6. Klicka på **Klar**.

## <a name="next-steps"></a>Nästa steg
* Ändra önskade alternativ och [hantera inställningar för tvåstegsverifiering](multi-factor-authentication-end-user-manage-settings.md)
* Ställ in [applösenord](multi-factor-authentication-end-user-app-passwords.md) för ursprunglig enhetsappar som inte stöder tvåstegsverifiering.
* Kolla in den [Microsoft Authenticator-appen](microsoft-authenticator-app-how-to.md) säker autentisering för snabb, även om du inte har cell service.
