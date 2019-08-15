---
title: Konfigurera tvåstegsverifiering – Azure Active Directory | Microsoft Docs
description: När ditt företag konfigurerar Azure Multi-Factor Authentication uppmanas du att registrera dig för tvåstegsverifiering. Lär dig hur du konfigurerar det.
services: active-directory
keywords: använda Azure Directory, Active Directory i molnet, självstudier för Active Directory
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75bc067bfe8a98ef2337f368243b3221be1677d6
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949895"
---
# <a name="set-up-my-account-for-two-step-verification"></a>Konfigurera mitt konto för tvåstegsverifiering
Tvåstegsverifiering är ett extra säkerhets steg som hjälper dig att skydda ditt konto genom att göra det svårare för andra att avbryta i. Om du läser den här artikeln, fick du förmodligen ett e-postmeddelande från din arbets-eller skol administratör om multifaktorautentisering. Eller så kanske du försökte logga in och fått ett meddelande där du uppmanas att konfigurera ytterligare säkerhets verifiering. I så fall **kan du inte logga in förrän du har slutfört processen för automatisk registrering**.

Den här artikeln hjälper dig att konfigurera ditt **arbets-eller skol konto**. Om du vill aktivera tvåstegsverifiering för din egen, personliga Microsoft-konto, se [om](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)tvåstegsverifiering.

## <a name="set-up-your-account"></a>Konfigurera ditt konto

När företagets support kräver att du börjar använda tvåstegsverifiering, ser du en skärm som säger att **din administratör har begärt att du konfigurerar det här kontot för ytterligare säkerhets verifiering**:

![Konfiguration](./media/multi-factor-authentication-end-user-first-time/first.png)

Kom igång genom att välja **Konfigurera det nu.**

Om du inte ser en skärm som detta när du loggar in, följer du anvisningarna i [Hantera dina inställningar för](multi-factor-authentication-end-user-manage-settings.md#using-the-additional-security-verification-page) tvåstegsverifiering för att hitta inställnings sidan där du kan hantera dina verifierings alternativ.

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>Bestäm hur du vill verifiera inloggnings programmen

Den första frågan i registrerings processen är hur du vill att vi ska kontakta dig. Ta en titt på alternativen i tabellen och Använd länkarna för att gå till installations stegen för varje metod.

| Kontaktmetod | Beskrivning |
| --- | --- |
| [Mobilapp](#use-a-mobile-app-as-the-contact-method) |- **Ta emot meddelanden om verifiering.** Med det här alternativet skickas ett meddelande till Authenticator-appen på din smartphone eller surfplatta. Visa meddelandet och välj **autentisera** i appen om det är giltigt. Ditt arbete eller din skola kan kräva att du anger en PIN-kod innan du autentiserar.<br>- **Använd verifierings kod.** I det här läget genererar Authenticator-appen en verifierings kod som uppdateras var 30: e sekund. Ange den mest aktuella verifierings koden i inloggnings gränssnittet.<br>Microsoft Authenticator-appen finns för [Android](https://go.microsoft.com/fwlink/?linkid=866594) och [iOS](https://go.microsoft.com/fwlink/?linkid=866594).|
| [Mobil samtal eller SMS](#use-your-mobile-phone-as-the-contact-method) |- **Telefonsamtal** placerar ett automatiskt röst samtal till det telefonnummer som du anger. Besvara anropet och tryck på # i telefon tangent bordet för att autentisera.<br>- **SMS** avslutar ett textmeddelande som innehåller en verifierings kod. Följ anvisningarna i texten, antingen svara på textmeddelandet eller ange verifierings koden som anges i inloggnings gränssnittet. |
| [Telefonsamtal till kontor](#use-your-office-phone-as-the-contact-method) |Placerar ett automatiskt röst samtal till det telefonnummer som du anger. Besvara anropet och tryck på # i telefon tangent bordet för att autentisera. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Använd en mobilapp som kontakt metod
Om du använder den här metoden måste du installera en Authenticator-app på din telefon eller surfplatta. Stegen i den här artikeln baseras på Microsoft Authenticator-appen, som är tillgänglig för [Android](https://go.microsoft.com/fwlink/?Linkid=825072) och [iOS](https://go.microsoft.com/fwlink/?Linkid=825073).

>[!NOTE]
>Du behöver inte använda Microsoft Authenticator-appen. Om du redan använder en annan Authenticator-app kan du fortsätta att använda den.

1. Välj **mobilapp** i den nedrullningsbara listan.
2. Välj antingen **ta emot meddelanden för verifiering** eller **Använd verifierings koden**och välj sedan **Konfigurera**.

   ![Skärmen ytterligare säkerhets verifiering](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. Öppna appen på din telefon eller surfplatta och välj **+** att lägga till ett konto. (På Android-enheter väljer du de tre punkterna och sedan **Lägg till konto**.)
4. Ange att du vill lägga till ett arbets-eller skol konto. QR kod skannern på din telefon öppnas. Om kameran inte fungerar som den ska kan du välja att ange företagets information manuellt. Mer information finns i [lägga till ett konto manuellt](#add-an-account-manually).  
5. Skanna QR-kodfragmentet som visades med skärmen för att konfigurera mobilappen.  Välj **Done** för att stänga QR-kod skärmen.  

   ![Sidan QR-kod](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. När aktiveringen är klar på telefonen väljer du **kontakta mig**.  Det här steget skickar antingen ett meddelande eller en verifierings kod till din telefon. Välj **Verifiera**.  
7. Om ditt företag kräver en PIN-kod för att godkänna inloggnings verifieringen anger du det.

   ![Ruta för att ange en PIN-kod](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. När PIN-koden är klar väljer du **Stäng**. Nu bör verifieringen lyckas.
9. Vi rekommenderar att du anger ditt mobiltelefon nummer om du förlorar åtkomsten till mobilappen. Ange ditt land/din region i list rutan och ange ditt mobiltelefon nummer i rutan bredvid namnet på landet/regionen. Välj **Nästa**.
10. Nu uppmanas du att ställa in applösenord för icke-webbläsarbaserade appar, till exempel Outlook 2010 eller äldre, eller den interna e-postappen på Apple-enheter. Detta beror på att vissa appar inte stöder tvåstegsverifiering. Om du inte använder de här apparna klickar du på **färdig** och hoppar över resten av stegen.
11. Om du använder dessa appar kopierar du appens lösen ord och klistrar in det i programmet i stället för ditt vanliga lösen ord. Du kan använda samma applösenord för flera appar. Mer information finns i [hjälp med applösenord].
12. Klicka på **Klar**.

### <a name="add-an-account-manually"></a>Lägg till ett konto manuellt
Om du vill lägga till ett konto i mobilappen manuellt, i stället för att använda QR-läsaren, följer du de här stegen.

1. Välj knappen **Ange konto manuellt** .  
2. Ange koden och URL: en som finns på samma sida som visar streckkoden. Den här informationen visas i rutorna **kod** och **URL** i mobilappen.

    ![Konfiguration](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. När aktiveringen är färdig väljer du **kontakta mig**. Det här steget skickar antingen ett meddelande eller en verifierings kod till din telefon. Välj **Verifiera**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Använd din mobil telefon som kontakt metod
1. Välj **telefon för autentisering** i den nedrullningsbara listan.  

    ![Konfiguration](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. Välj land/region i list rutan och ange ditt mobiltelefon nummer.
3. Välj den metod som du föredrar att använda med mobil telefon – text eller samtal.
4. Välj **kontakta mig** för att verifiera ditt telefonnummer. Beroende på vilket läge du har valt skickar vi dig en text eller anropar dig. Följ anvisningarna på skärmen och välj **Verifiera**.
5. Nu uppmanas du att ställa in applösenord för icke-webbläsarbaserade appar, till exempel Outlook 2010 eller äldre, eller den interna e-postappen på Apple-enheter. Detta beror på att vissa appar inte stöder tvåstegsverifiering. Om du inte använder de här apparna klickar du på **färdig** och hoppar över resten av stegen.
6. Om du använder dessa appar kopierar du appens lösen ord och klistrar in det i programmet i stället för ditt vanliga lösen ord. Du kan använda samma applösenord för flera appar. Mer information finns i [hjälp med applösenord].
7. Klicka på **Klar**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Använd din arbets telefon som kontakt metod
1. Välj **arbets telefon** i list rutan  

    ![Konfiguration](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. Rutan telefonnummer fylls i automatiskt med företagets kontakt uppgifter. Om numret är fel eller saknas kan du be administratören att göra ändringar.
3. Välj **kontakta mig** för att verifiera ditt telefonnummer och vi ska ringa ditt nummer. Följ anvisningarna på skärmen och välj **Verifiera**.
4. Nu uppmanas du att ställa in applösenord för icke-webbläsarbaserade appar, till exempel Outlook 2010 eller äldre, eller den interna e-postappen på Apple-enheter. Detta beror på att vissa appar inte stöder tvåstegsverifiering. Om du inte använder de här apparna klickar du på **färdig** och hoppar över resten av stegen.
5. Om du använder dessa appar kopierar du appens lösen ord och klistrar in det i programmet i stället för ditt vanliga lösen ord. Du kan använda samma applösenord för flera appar. Mer information finns i [Vad är app-lösenord](multi-factor-authentication-end-user-app-passwords.md).
6. Klicka på **Klar**.

## <a name="next-steps"></a>Nästa steg
* Ändra önskade alternativ och [Hantera dina inställningar för](multi-factor-authentication-end-user-manage-settings.md) tvåstegsverifiering
* Konfigurera [applösenord](multi-factor-authentication-end-user-app-passwords.md) för appar för inbyggd enhet som inte stöder tvåstegsverifiering.
* Kolla in [Microsoft Authenticator-appen](user-help-auth-app-download-install.md) för snabb och säker autentisering även när du inte har någon cell tjänst.
