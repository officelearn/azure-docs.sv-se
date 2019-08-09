---
title: Säkerhetskopiera och återställa med Microsoft Authenticator app-Azure Active Directory | Microsoft Docs
description: Lär dig hur du säkerhetskopierar och återställer autentiseringsuppgifter för kontot med hjälp av Microsoft Authenticator-appen.
services: active-directory
author: eross-msft
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba99001159277d9d221910cafa4f5165ae2bd812
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880761"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Säkerhetskopiera och återställa kontoautentiseringsuppgifter med Microsoft Authenticator-appen

**Gäller för:**

- iOS-enheter

Microsoft Authenticator-appen säkerhetskopierar dina kontoautentiseringsuppgifter och relaterade appinställningar, till exempel ordningen på dina konton, till molnet. När du har säkerhetskopierat kan du också använda appen för att återställa din information på en ny enhet, vilket kan undvika att bli utelåst eller behöver återskapa konton.

> [!IMPORTANT]
> Du behöver en personlig Microsoft-konto och ett iCloud-konto för varje lagrings plats för säkerhets kopior. Men inom den lagrings platsen kan du säkerhetskopiera flera konton. Du kan till exempel ha ett personligt konto, ett skol konto och ett konto från tredje part som Facebook, Google och så vidare.
>
> Endast dina personliga och tredje parts konto uppgifter lagras, som innehåller ditt användar namn och den konto verifierings kod som krävs för att bevisa din identitet. Vi lagrar inte övrig information som är kopplad till dina konton, inklusive e-post eller filer. Vi associerar inte heller eller delar dina konton på något sätt eller med någon annan produkt eller tjänst. Slutligen får IT-administratören ingen information om något av dessa konton.

## <a name="back-up-your-account-credentials"></a>Säkerhetskopiera dina kontoautentiseringsuppgifter

Innan du kan säkerhetskopiera dina autentiseringsuppgifter måste du ha båda:

- En personlig [Microsoft-konto](https://account.microsoft.com/account) som fungerar som ditt återställnings konto.

- Ett [iCloud-konto](https://www.icloud.com/) för den faktiska lagrings platsen.

Att kräva att du loggar in på båda kontona ger starkare säkerhet för din säkerhets kopierings information.

### <a name="to-turn-on-cloud-backup"></a>Aktivera moln säkerhets kopiering

- På din iOS-enhet väljer du **Inställningar**, **säkerhets kopiering**och aktiverar sedan **iCloud-säkerhetskopiering**.

    Autentiseringsuppgifterna för ditt konto säkerhets kopie ras till ditt iCloud-konto.

    ![skärmen iOS-inställningar, som visar platsen för säkerhets kopierings inställningarna i iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Återställa autentiseringsuppgifterna för ditt konto på din nya enhet

Du kan återställa dina kontoautentiseringsuppgifter från ditt iCloud-konto med samma återställnings konto för Microsoft som du ställer in när du säkerhetskopierade informationen.

### <a name="to-recover-your-information"></a>Så här återställer du din information

1. Öppna appen Microsoft Authenticator på iOS-enheten och välj **Starta återställning** längst ned på skärmen.

    ![Microsoft Authenticator app, som visar var du klickar på Starta återställning](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Logga in på ditt återställnings konto med samma personliga Microsoft-konto som du använde under säkerhets kopieringen.

    Dina kontoautentiseringsuppgifter återställs till den nya enheten.

När du har slutfört återställningen kan du märka att dina personliga Microsoft-konto verifierings koder i Microsoft Authenticator-appen skiljer sig mellan dina gamla och nya telefoner. Koderna är olika eftersom varje enhet har sina egna unika autentiseringsuppgifter, men båda är giltiga och fungerar samtidigt som du loggar in med den associerade telefonen.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Återställa ytterligare konton som kräver mer verifiering

Om du använder push-meddelanden med dina personliga konton, arbets-eller skol konton får du en avisering på skärmen som säger att du måste ange ytterligare verifiering innan du kan återställa din information. Eftersom push-meddelanden kräver användning av autentiseringsuppgifter som är knutna till din enhet och aldrig skickas via nätverket, måste du bevisa din identitet innan autentiseringsuppgiften skapas på enheten.

För personliga Microsoft-konton kan du bevisa din identitet genom att ange ditt lösen ord tillsammans med ett alternativt e-postadress eller telefonnummer. För arbets-eller skol konton måste du skanna en QR-kod som du fått av din konto leverantör.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>För att tillhandahålla ytterligare verifiering för personliga konton

1. På skärmen **konton** i Microsoft Authenticator-appen väljer du den nedrullningsbara pilen bredvid det konto som du vill återställa.

    ![Microsoft Authenticator app, som visar tillgängliga konton med tillhör ande nedrullningsbara pilar](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

2. Välj **Logga in för att återställa**, skriv ditt lösen ord och bekräfta sedan din e-postadress eller ditt telefonnummer som ytterligare verifiering.

    ![Microsoft Authenticator app, så att du kan ange din inloggnings information](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>För att ge ytterligare verifiering för arbets-eller skol konton

1. På skärmen **konton** i Microsoft Authenticator-appen väljer du den nedrullningsbara pilen bredvid det konto som du vill återställa.

    ![Microsoft Authenticator app, som visar tillgängliga konton med tillhör ande nedrullningsbara pilar](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

2. Välj **Skanna QR-kod för att återställa**och skanna QR-koden.

    ![Microsoft Authenticator app, så att du kan skanna din QR-kod](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >Om det här är första gången du konfigurerar Microsoft Authenticator-appen kan du få en fråga om du vill tillåta att appen får åtkomst till kameran (iOS) eller tillåter att appen tar bilder och spelar in video (Android). Du måste välja **Tillåt** så att Authenticator-appen kan komma åt kameran för att ta en bild av QR-koden i nästa steg. Om du inte tillåter kameran kan du fortfarande konfigurera Authenticator-appen, men du måste lägga till kod informationen manuellt. Information om hur du lägger till koden manuellt finns i se [lägga till ett konto manuellt i appen](user-help-auth-app-add-account-manual.md).
    >
    >Mer information om hur du hämtar en QR-kod finns i [Kom igång med Microsoft Authenticator-appen](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) eller [Konfigurera säkerhets information för att använda en Authenticator-app](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app), baserat på om din administratör har aktiverat säkerhets information.

## <a name="troubleshooting-backup-and-recovery-problems"></a>Fel sökning av problem med säkerhets kopiering och återställning

Det finns några skäl till varför säkerhets kopieringen kanske inte är tillgänglig:

- **Ändra operativ system.** Säkerhets kopian lagras i moln lagrings alternativet som tillhandahålls av din telefons operativ system, vilket innebär att säkerhets kopian inte är tillgänglig om du växlar mellan Android och iOS. I så fall måste du manuellt återskapa ditt konto i appen.

- **Problem med nätverks-eller lösen ord.** Kontrol lera att du är ansluten till ett nätverk och har loggat in på ditt iCloud-konto med samma AppleID som du använde för din senaste iPhone.

- **Oavsiktlig borttagning.** Det är möjligt att du har tagit bort ditt säkerhets kopierings konto från din tidigare enhet eller när du har hanterat ditt moln lagrings konto. I så fall måste du manuellt återskapa ditt konto i appen.

- **Befintliga Microsoft Authenticator-konton.** Om du redan har konfigurerat konton i Microsoft Authenticator-appen kan appen inte återställa dina säkerhetskopierade konton. Förhindra återställning hjälper till att se till att konto informationen inte skrivs över med den inaktuella informationen. I så fall måste du ta bort eventuell befintlig konto information från de befintliga konton som kon figurer ATS i din Authenticator-app innan du kan återställa säkerhets kopian.

## <a name="next-steps"></a>Nästa steg

Nu när du har säkerhetskopierat och återställt dina konto uppgifter till din nya enhet kan du fortsätta att använda appen Microsoft Authenticator för att verifiera din identitet. Mer information finns i [Logga in på dina konton med hjälp av Microsoft Authenticator-appen](user-help-sign-in.md).

## <a name="related-topics"></a>Relaterade ämnen

- [Vad är Microsoft Authenticator-appen?](user-help-auth-app-overview.md)

- [Vanliga frågor och svar om Microsoft Authenticator-appen](user-help-auth-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
