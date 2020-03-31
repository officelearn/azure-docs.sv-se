---
title: Säkerhetskopiera och återställa konton med Microsoft Authenticator-appen - Azure AD
description: Läs om hur du säkerhetskopierar och återställer dina säkerhetskopierade kontouppgifter med hjälp av Microsoft Authenticator-appen.
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 2ac761355fc528fe3e4b4b5f05ee0a6e317ff99a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298020"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Säkerhetskopiera och återställa kontouppgifter med hjälp av Microsoft Authenticator-appen

**Gäller:**

- iOS-enheter, som körs version 5.7.0 och senare

- Android-enheter, som kör version 6.6.0 och senare

Microsoft Authenticator-appen säkerhetskopierar dina kontouppgifter och relaterade appinställningar, till exempel ordningen på dina konton, till molnet. När du har säkerhetskopiering kan du också använda appen för att återställa informationen på en ny enhet, vilket kan undvika att bli utelåst eller behöva återskapa konton.

Varje lagringsplats för säkerhetskopiering kräver att du har ett personligt Microsoft-konto, medan iOS också kräver att du har ett iCloud-konto. Du kan ha flera konton lagrade på den enda platsen. Du kan till exempel ha ett personligt konto, ett arbets- eller skolkonto och ett personligt, icke-Microsoft-konto som för Facebook, Google och så vidare.

> [!IMPORTANT]
> Endast dina personliga kontouppgifter och 3:e parts kontouppgifter lagras, vilket inkluderar ditt användarnamn och den kontoverifieringskod som krävs för att bevisa din identitet. Vi lagrar ingen annan information som är kopplad till dina konton, inklusive e-post eller filer. Vi associerar inte heller eller delar dina konton på något sätt eller med någon annan produkt eller tjänst. Och slutligen får din IT-administratör ingen information om något av dessa konton.

## <a name="back-up-your-account-credentials"></a>Säkerhetskopiera dina kontouppgifter

Innan du kan säkerhetskopiera dina autentiseringsuppgifter måste du ha:

- Ett personligt [Microsoft-konto](https://account.microsoft.com/account) som ska fungera som ditt återställningskonto.

- **Endast för iOS** måste du ha ett [iCloud-konto](https://www.icloud.com/) för den faktiska lagringsplatsen.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>Så här aktiverar du säkerhetskopiering i molnet för iOS-enheter

- På din iOS-enhet väljer du **Inställningar,** väljer **Säkerhetskopiering**och aktiverar sedan **iCloud-säkerhetskopia**.

    Dina kontouppgifter säkerhetskopieras till ditt iCloud-konto.

    ![iOS-inställningsskärmen som visar platsen för iCloud-säkerhetskopieringsinställningarna](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Så här aktiverar du säkerhetskopiering i molnet för Android-enheter

- På din Android-enhet väljer du **Inställningar,** väljer **Säkerhetskopiering**och aktiverar sedan **säkerhetskopiering i molnet**.

    Dina kontouppgifter säkerhetskopieras till ditt molnkonto.

    ![Android-inställningsskärm, som visar platsen för säkerhetskopieringsinställningarna](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Återställa dina kontouppgifter på din nya enhet

Du kan återställa dina kontouppgifter från ditt molnkonto, men du måste först se till att kontot du återställer inte finns i Microsoft Authenticator-appen. Om du till exempel återställer ditt personliga Microsoft-konto måste du se till att du inte redan har konfigurerat ett personligt Microsoft-konto i autentiseringsappen. Den här kontrollen är viktig så att vi kan vara säkra på att vi inte skriver över eller raderar ett befintligt konto av misstag.

### <a name="to-recover-your-information"></a>Så här återställer du din information

1. Öppna Microsoft Authenticator-appen på din mobila enhet och välj **Börja återställas** längst ned på skärmen.

    ![Microsoft Authenticator-appen som visar var du ska klicka på Börja återställa](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Logga in på ditt återställningskonto med samma personliga Microsoft-konto som du använde under säkerhetskopieringsprocessen.

    Dina kontouppgifter återställs till den nya enheten.

När du är klar med återställningen kanske du märker att dina verifieringskoder för ditt personliga Microsoft-konto i Microsoft Authenticator-appen skiljer sig mellan dina gamla och nya telefoner. Koderna är olika eftersom varje enhet har sin egen unika autentiseringsuppgifter, men båda är giltiga och fungerar när de loggar in med den associerade telefonen.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Återställa ytterligare konton som kräver mer verifiering

Om du använder push-meddelanden med dina personliga konton eller arbets- eller skolkonton får du en avisering på skärmen som säger att du måste tillhandahålla ytterligare verifiering innan du kan återställa din information. Eftersom push-meddelanden kräver att du använder en autentiseringsidenti som är knuten till din specifika enhet och aldrig skickas över nätverket, måste du bevisa din identitet innan autentiseringsuppgifterna skapas på din enhet.

För personliga Microsoft-konton kan du bevisa din identitet genom att ange ditt lösenord tillsammans med ett alternativt e-postmeddelande eller telefonnummer. För arbets- eller skolkonton måste du skanna en QR-kod som du fått av din kontoleverantör.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Så här ger du ytterligare verifiering för personliga konton

1. Välj det konto som du vill återställa på skärmen **Konton** i Microsoft Authenticator. På en Android-enhet väljer du pilen bredvid det konto som du vill återställa.

    ![Microsoft Authenticator-appen som visar tillgängliga konton med tillhörande nedrullningsbar pilar](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

    På en iOS-enhet trycker du på det konto som du vill återställa för att öppna kontots fullskärmsvy.

    ![Microsoft Authenticator-appen som visar tillgängliga konton med tillhörande nedrullningsbar pilar](./media/user-help-auth-app-backup-recovery/ios-select-account-to-recover.png)

2. Logga in för att återställa kontot. På en Android-enhet väljer du **Logga in för att återställa**.

    ![Microsoft Authenticator-appen för att ange din inloggningsinformation på Android](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

    På en iOS-enhet trycker du på den kontopanel som du vill återställa och trycker sedan på alternativet för att logga in och återställa. Skriv sedan ditt lösenord och bekräfta sedan din e-postadress eller ditt telefonnummer som ytterligare verification.unt.

    ![Microsoft Authenticator-appen för att ange din inloggningsinformation på iOS](./media/user-help-auth-app-backup-recovery/ios-sign-in-to-recover.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Så här ger du ytterligare verifiering för arbets- eller skolkonton

1. Logga in för att återställa kontot. På en Android-enhet väljer du **Logga in för att återställa**.

    ![Microsoft Authenticator-appen återställer ett arbets- eller skolkonto på Android](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

    På en iOS-enhet trycker du på kontot som du vill återställa för att öppna kontots helskärmsläge.

    ![Microsoft Authenticator-appen återställer ett arbets- eller skolkonto på iOS](./media/user-help-auth-app-backup-recovery/ios-recover-work-or-school-account.png)

2. Du kan använda en QR-kod för att återställa ett konto. Välj **Skanna QR-kod för att återställa**och skanna sedan QR-koden.

    I Android:

    ![Microsoft Authenticator-appen på Android, så att du kan skanna din QR-kod](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    På iOS:

    ![Microsoft Authenticator-appen på iOS, så att du kan skanna din QR-kod](./media/user-help-auth-app-backup-recovery/ios-scan-qr-code.png)

    >[!NOTE]
    >Mer information om QR-koder och hur du skaffar en finns i [Komma igång med Microsoft Authenticator-appen](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) eller Konfigurera [säkerhetsinformation för att använda en autentiseringsapp](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app), baserat på om administratören har aktiverat säkerhetsinformation.
    >
    >Om det är första gången du konfigurerar Microsoft Authenticator-appen kan du få en uppmaning om du vill tillåta appen att komma åt kameran (iOS) eller låta appen ta bilder och spela in video (Android). Du måste välja **Tillåt** så att autentiseringsappen kan komma åt kameran för att ta en bild av QR-koden i nästa steg. Om du inte tillåter kameran kan du fortfarande konfigurera autentiseringsappen, men du måste lägga till kodinformationen manuellt. Information om hur du lägger till koden manuellt finns i [Lägg till ett konto manuellt i appen](user-help-auth-app-add-account-manual.md).

## <a name="troubleshoot-backup-and-recovery-problems"></a>Felsöka problem med säkerhetskopiering och återställning

Det finns några anledningar till varför säkerhetskopian kanske inte är tillgänglig

- **Ändra operativsystem:** Din säkerhetskopia lagras i iCloud för iOS och i Microsofts molnlagringsleverantör för Android. Det innebär att säkerhetskopian inte är tillgänglig om du växlar mellan Android- och iOS-enheter. Om du gör bytet måste du återskapa dina konton manuellt i Microsoft Authenticator-appen.

- **Nätverksproblem**: Om du har nätverksrelaterade problem kontrollerar du att du är ansluten till nätverket och är korrekt inloggad på ditt konto.

- **Kontoproblem**: Om du har problem med kontorelaterade konton kontrollerar du att du är korrekt inloggad på ditt konto. För iOS innebär det att du måste vara inloggad på iCloud med samma AppleID-konto som din iPhone.

- **Oavsiktlig borttagning**: Det är möjligt att du har tagit bort ditt säkerhetskopieringskonto från din tidigare enhet eller när du hanterar ditt molnlagringskonto. I det här fallet måste du manuellt återskapa ditt konto i appen.

- **Befintliga Microsoft Authenticator-konton**: Om du redan har konfigurerat konton i Microsoft Authenticator-appen kan appen inte återställa dina säkerhetskopierade konton. Om du förhindrar återställning kan du se till att dina kontouppgifter inte skrivs över med inaktuella uppgifter. I det här fallet måste du ta bort all befintlig kontoinformation från befintliga konton som konfigurerats i Autentiseringsappen innan du kan återställa säkerhetskopian.

- **Säkerhetskopiering är inaktuella:** Om säkerhetskopieringsinformationen är inaktuella kan du bli ombedd att uppdatera informationen genom att logga in på ditt Microsoft Recovery-konto igen. Ditt återställningskonto är det personliga Microsoft-konto som du använde från början för att lagra säkerhetskopian. Om det krävs en inloggning visas en röd punkt på menyn eller åtgärdsfältet, eller så visas ett utropstecken där du uppmanas att logga in för att slutföra återställningen från säkerhetskopian. När du har valt ikonen uppmanas du att logga in igen för att uppdatera informationen.

## <a name="next-steps"></a>Nästa steg

Nu när du har säkerhetskopierat och återställt dina kontouppgifter till den nya enheten kan du fortsätta att använda Microsoft Authenticator-appen för att verifiera din identitet. Mer information finns [i Logga in på dina konton med hjälp av Microsoft Authenticator-appen](user-help-sign-in.md).

## <a name="related-articles"></a>Relaterade artiklar

- [Vad är Microsoft Authenticator-appen?](user-help-auth-app-overview.md)

- [Vanliga frågor och svar om Microsoft Authenticator-appen](user-help-auth-app-faq.md)

- [Multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
