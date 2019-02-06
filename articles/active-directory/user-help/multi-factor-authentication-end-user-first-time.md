---
title: Konfigurera tvåstegsverifiering - Azure Active Directory | Microsoft Docs
description: När ditt företag konfigurerar Azure Multi-Factor Authentication, uppmanas du att registrera dig för tvåstegsverifiering. Lär dig hur du ställer in.
services: active-directory
keywords: hur du använder azure-katalogen, active directory i molnet, active directory-självstudien
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: lizross
ms.openlocfilehash: 6a5a2fee11d785c7513b6c1d1e66bd8b80d80292
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749720"
---
# <a name="set-up-my-account-for-two-step-verification"></a>Konfigurera mitt konto för tvåstegsverifiering
Tvåstegsverifiering är ett ytterligare steg som hjälper dig att skydda ditt konto genom att göra det svårare för andra att logga in. Om du läser den här artikeln fick du förmodligen ett e-postmeddelande från din arbets- eller skolkonto administratör om Multi-Factor Authentication. Eller kanske du försökte logga in och fick ett meddelande som ber dig att ställa in ytterligare säkerhetsverifiering. Om så är fallet, **du inte logga in förrän du har slutfört den automatiska registreringen**.

Den här artikeln visar hur du konfigurerar din **arbets- eller skolkonto**. Om du vill aktivera tvåstegsverifiering för dina egna, personligt Microsoft-konto, se [om tvåstegsverifiering](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="set-up-your-account"></a>Konfigurera ditt konto

När företagets support kräver att du börjar använda tvåstegsverifiering, visas en skärm som anger **din administratör har begärt att du ställer in det här kontot för ytterligare säkerhetsverifiering**:

![Konfiguration](./media/multi-factor-authentication-end-user-first-time/first.png)

För att komma igång, Välj **konfigurera nu.**

Om du inte ser en sådan här skärm när du loggar in, följer du anvisningarna i [hantera dina inställningar för tvåstegsverifiering](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) att hitta sidan där du kan hantera din verifieringsalternativ.

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>Bestäm hur du vill verifiera dina inloggningar

Den första frågan i registreringen är hur du vill att vi ska kontakta dig. Ta en titt på alternativen i tabellen och Använd länkar för att gå till inställningsstegen för varje metod.

| Kontaktmetod | Beskrivning |
| --- | --- |
| [Mobilapp](#use-a-mobile-app-as-the-contact-method) |- **Få meddelanden för verifiering.** Det här alternativet skickar ett meddelande till autentiseringsappen på din smartphone eller surfplatta. Visa meddelandet och, om det är tillförlitligt väljer **autentisera** i appen. Ditt arbete eller din skola kan kräva att du anger en PIN-kod innan du autentiserar dig.<br>- **Använd Verifieringskod.** I det här läget genererar authenticator-appen en Verifieringskod som uppdaterar med 30 sekunders mellanrum. Ange den mest aktuella autentiseringskoden i i inloggningsgränssnittet.<br>Microsoft Authenticator-appen är tillgänglig för [Android](https://go.microsoft.com/fwlink/?linkid=866594) och [iOS](https://go.microsoft.com/fwlink/?linkid=866594).|
| [Mobiltelefonsamtal eller textmeddelande](#use-your-mobile-phone-as-the-contact-method) |- **Telefonsamtal** placerar ett automatiserat röstsamtal till telefonnumret som du anger. Besvara samtalet och tryck på # tangenten för att autentisera.<br>- **SMS: et** slutar ett SMS med verifieringskoden. Följande fråga i texten, svara på SMS: et eller ange verifieringskoden som visas i i inloggningsgränssnittet. |
| [Telefonsamtal till arbete](#use-your-office-phone-as-the-contact-method) |Placerar ett automatiserat röstsamtal till telefonnumret som du anger. Besvara samtalet och trycka på #-tangenten för att autentisera. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Använda en mobilapp som kontaktmetod
Med den här metoden kräver att du installerar en authenticator-appen på din telefon eller surfplatta. Stegen i den här artikeln baseras på Microsoft Authenticator-appen, som är tillgänglig för [Android](https://go.microsoft.com/fwlink/?Linkid=825072) och [iOS](https://go.microsoft.com/fwlink/?Linkid=825073).

>[!NOTE]
>Du behöver inte använda Microsoft Authenticator-appen. Om du redan använder en annan autentiseringsapp kan fortsätta du att använda den.

1. Välj **mobilappen** från den nedrullningsbara listan.
2. Välj antingen **ta emot meddelanden för verifiering** eller **Använd Verifieringskod**och välj sedan **konfigurera**.

   ![Ytterligare verifiering skärmen](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. Öppna appen på din telefon eller surfplatta, och välj **+** att lägga till ett konto. (På Android-enheter, Välj de tre punkterna sedan **Lägg till konto**.)
4. Ange att du vill lägga till ett arbets- eller skolkonto konto. QR-kodsskannern på din telefon öppnas. Om din kamera inte fungerar kan du välja för att ange företagets information manuellt. Mer information finns i [Lägg till ett konto manuellt](#add-an-account-manually).  
5. Skanna QR-kod bilden som visas med skärmen för att konfigurera mobilappen.  Välj **klar** att Stäng skärmen QR-kod.  

   ![Skärmen för QR-kod](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. När aktiveringen är klar på telefonen Välj **kontakta mig**.  Det här steget skickar en avisering eller en Verifieringskod till din telefon. Välj **Kontrollera**.  
7. Om ditt företag kräver en PIN-kod för att godkänna inloggningsverifieringssystem, anger du den.

   ![Där du anger en PIN-kod](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. När PIN-koden är avslutad, Välj **Stäng**. Nu ska verifieringen lyckas.
9. Vi rekommenderar att du anger ditt mobiltelefonnummer ifall du skulle förlora åtkomsten till din mobilapp. Välj land i listrutan och ange mobiltelefonnumret i rutan bredvid namnet på land. Välj **Nästa**.
10. Nu uppmanas du att ställa in applösenord för icke-webbläsarappar, till exempel Outlook 2010 eller äldre eller interna e-postappen på Apple-enheter. Det beror på att vissa appar inte stöder tvåstegsverifiering. Om du inte använder de här apparna klickar du på **klar** och hoppa över resten av stegen.
11. Om du använder de här apparna kan kopiera applösenordet tillhandahålls och klistra in den i ditt program istället för ditt vanliga lösenord. Du kan använda samma applösenord för flera appar. Mer information [hjälp med applösenord].
12. Klicka på **Klar**.

### <a name="add-an-account-manually"></a>Lägg till ett konto manuellt
Följ dessa steg om du vill lägga till ett konto i mobilappen manuellt, istället för att använda QR-läsaren.

1. Välj den **ange konto manuellt** knappen.  
2. Ange koden och URL: en som tillhandahålls på samma sida som visar streckkoden. Den här informationen används i den **kod** och **URL** rutor i mobilappen.

    ![Konfiguration](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. När aktiveringen är klar, Välj **kontakta mig**. Det här steget skickar en avisering eller en Verifieringskod till din telefon. Välj **Kontrollera**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Använda din mobiltelefon som kontaktmetod
1. Välj **Autentiseringstelefon** från den nedrullningsbara listan.  

    ![Konfiguration](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. Välj ditt land i listrutan och ange ditt mobiltelefonnummer.
3. Välj den metod som du föredrar att använda med mobiltelefonen - textmeddelande eller samtal.
4. Välj **kontakta mig** att verifiera ditt telefonnummer. Beroende på läge du har valt, vi skickar ett SMS eller ringer upp dig. Följ anvisningarna på skärmen och välj sedan **Kontrollera**.
5. Nu uppmanas du att ställa in applösenord för icke-webbläsarappar, till exempel Outlook 2010 eller äldre eller interna e-postappen på Apple-enheter. Det beror på att vissa appar inte stöder tvåstegsverifiering. Om du inte använder de här apparna klickar du på **klar** och hoppa över resten av stegen.
6. Om du använder de här apparna kan kopiera applösenordet tillhandahålls och klistra in den i ditt program istället för ditt vanliga lösenord. Du kan använda samma applösenord för flera appar. Mer information [hjälp med applösenord].
7. Klicka på **Klar**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Använda din Arbetstelefon som kontaktmetod
1. Välj **Arbetstelefon** från listrutan  

    ![Konfiguration](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. Rutan telefonnummer fylls automatiskt med din kontaktinformation för företag. Om talet är felaktig eller saknas, be administratören att göra ändringar.
3. Välj **kontakta mig** för att verifiera telefonen tal, och vi ska ringa ditt nummer. Följ anvisningarna på skärmen och välj sedan **Kontrollera**.
4. Nu uppmanas du att ställa in applösenord för icke-webbläsarappar, till exempel Outlook 2010 eller äldre eller interna e-postappen på Apple-enheter. Det beror på att vissa appar inte stöder tvåstegsverifiering. Om du inte använder de här apparna klickar du på **klar** och hoppa över resten av stegen.
5. Om du använder de här apparna kan kopiera applösenordet tillhandahålls och klistra in den i ditt program istället för ditt vanliga lösenord. Du kan använda samma applösenord för flera appar. Mer information finns i [vad är Applösenord](multi-factor-authentication-end-user-app-passwords.md).
6. Klicka på **Klar**.

## <a name="next-steps"></a>Nästa steg
* Ändra din önskade alternativ och [hantera dina inställningar för tvåstegsverifiering](multi-factor-authentication-end-user-manage-settings.md)
* Konfigurera [applösenord](multi-factor-authentication-end-user-app-passwords.md) för ursprunglig enhetsappar som inte stöder tvåstegsverifiering.
* Kolla in den [Microsoft Authenticator-appen](microsoft-authenticator-app-how-to.md) för snabb, säker autentisering även om du inte har cell-tjänsten.
