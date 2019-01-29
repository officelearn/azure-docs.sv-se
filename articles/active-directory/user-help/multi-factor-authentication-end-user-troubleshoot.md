---
title: Felsöka tvåstegsverifiering - Azure Active Directory | Microsoft Docs
description: Innehåller instruktioner för användarna vad du kan göra om de stöter på ett problem med Azure Multi-Factor Authentication och tvåstegsverifiering.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: lizross
ms.reviewer: kexia
ms.openlocfilehash: 2b8a0db21ab4beb2128ae0a1cebbf9fb42691f3c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55177415"
---
# <a name="get-help-with-two-step-verification"></a>Få hjälp med tvåstegsverifiering

Tvåstegsverifiering är en säkerhetsfunktion som din organisation använder för att skydda dina konton. Tvåstegsverifiering är säkrare än bara ett lösenord eftersom den består av två typer av autentisering: något du känner till och något du har med dig. Något du känner är lösenordet, medan något du har med dig är din telefon eller en enhet. Använda tvåstegsverifiering kan hjälpa till att hindra illasinnade hackare att logga in som du, även om de komma åt ditt lösenord.

Microsoft erbjuder tvåstegsverifiering, men det är din organisation som avgör om du använder funktionen. Du kan inte välja om din organisation kräver det, precis som du inte kan välja bort med ett lösenord för att skydda ditt konto.

>[!Note]
>Om du vill ha mer information om hur du använder tvåstegsverifiering med ditt personliga Microsoft-konto, se den [om tvåstegsverifiering](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) artikeln.

## <a name="why-do-i-need-to-use-another-verification-method"></a>Varför behöver jag använda en annan verifieringsmetod?

Det finns flera orsaker till varför du kan behöva använda en annan verifieringsmetod med ditt konto. Exempel:

- **Du har glömt din telefon eller enhet.** Vissa dagar som du lämnar din telefon i hemmet, men ändå måste du logga in på arbetet. Först bör du försöka logga in med hjälp av en annan metod som inte behöver din telefon.

- **Du förlorat din telefon eller har fått ett nytt telefonnummer.** Om du har förlorat din telefon eller tagit emot en ny kod, du kan logga in med en annan metod eller be administratören att rensa dina inställningar. Vi rekommenderar starkt att låta din administratör veta om telefonen har tappats bort eller blir stulen, så att de lämpliga uppdateringarna kan göras till ditt konto. När dina inställningar är avmarkerad försvinner uppmanas du att [registrera för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md) nästa gång du loggar in.

- **Få inte autentisering text eller telefonsamtal.** Det finns flera orsaker till varför du inte kanske att hämta text eller telefonsamtal. Om du har lyckats få skickar ett SMS eller telefonsamtal tidigare, men detta är troligen ett problem med provider för telefon, inte ditt konto. Om du har ofta fördröjningar på grund av en felaktig signal kan vi rekommenderar att du använder den [Microsoft Authenticator-appen](microsoft-authenticator-app-how-to.md) på din mobila enhet. Den här appen kan generera slumpmässiga säkerhetskoder för att logga in, utan att kräva något cell-signal eller Internet-anslutning.<br><br>Om du försöker få ett textmeddelande be en vän till text du som ett test för att kontrollera att du kan skaffa ett och om du har tagit emot flera meddelanden, se till att du använder koden från den mest aktuella.

- **Dina applösenord fungerar inte.** Lösenord ersätter dina normala lösenordet för äldre program som inte stöder tvåstegsverifiering. Kontrollera först att du har angett lösenordet korrekt. Om detta inte löser den, försök logga in på [skapa ett nytt applösenord](multi-factor-authentication-end-user-app-passwords.md) eller kontakta administratören om du vill [ta bort dina befintliga applösenord](../authentication/howto-mfa-userdevicesettings.md) så att du kan skapa en ny.

## <a name="sign-in-using-another-verification-method"></a>Logga in med en annan verifieringsmetod

1. logga in på ditt konto normalt och välj den **loggar du in ett annat sätt** länkar på den **tvåstegsverifiering** sidan.

    ![Ändra inloggning verifieringsmetod](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Om du inte ser den **loggar du in ett annat sätt** länken, betyder det att du inte har konfigurerat några andra verifieringsmetoder. Du måste kontakta administratören för hjälp med att logga in på ditt konto. När du loggar in kan du kontrollera att du lägger till ytterligare verifieringsmetoder. Mer information om att lägga till verifieringsmetoder finns i den [hantera dina inställningar för tvåstegsverifiering](multi-factor-authentication-end-user-manage-settings.md) artikeln.<br><br>Om du ser länken, men fortfarande inte ser några andra verifieringsmetoder, måste du kontakta administratören för hjälp med att logga in på ditt konto.

2. Välj din alternativa verifieringsmetod och fortsätta med tvåstegsprocessen för verifiering.

3. När du är tillbaka i ditt konto kan du uppdatera din verifieringsmetoder (vid behov). Mer information om Lägg till eller ändra din metod, se den [hantera dina inställningar för tvåstegsverifiering](multi-factor-authentication-end-user-manage-settings.md) artikeln.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Jag gick inte att hitta svar på mitt problem

Om du har gjort de här stegen, men är fortfarande körs i problem, kan du kontakta administratören för mer hjälp.

## <a name="related-topics"></a>Relaterade ämnen

* [Hantera dina inställningar för tvåstegsverifiering](multi-factor-authentication-end-user-manage-settings.md)

* [Microsoft Authenticator-program vanliga frågor och svar](microsoft-authenticator-app-faq.md)
