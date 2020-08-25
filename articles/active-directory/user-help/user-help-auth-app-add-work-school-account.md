---
title: Lägg till ett arbets-eller skol konto i Microsoft Authenticator app – Azure AD
description: Lägg till ditt arbets-eller skol konto i Microsoft Authenticator-appen för att verifiera din identitet när du använder tvåstegsverifiering.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: e003c45aa1e7d75b709b7fbf99532fb1302fcbb9
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797657"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Lägg till ditt arbets-eller skol konto i Microsoft Authenticator-appen

Om din organisation använder tvåstegsverifiering kan du konfigurera ditt arbets-eller skol konto för att använda Microsoft Authenticator-appen som en av verifierings metoderna.

>[!Important]
>Innan du kan lägga till ditt konto måste du ladda ned och installera Microsoft Authenticator-appen. Om du inte har gjort det än följer du stegen i artikeln [Hämta och installera appen](user-help-auth-app-download-install.md) .

## <a name="add-your-work-or-school-account"></a>Lägga till ditt arbets- eller skolkonto

1. På datorn går du till sidan [ytterligare säkerhets verifiering](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) .

    >[!Note]
    >Om du inte ser sidan **ytterligare säkerhets verifiering** är det möjligt att administratören har aktiverat säkerhets information (för hands versionen). Om så är fallet bör du följa anvisningarna i avsnittet [Konfigurera säkerhets information för att använda en Authenticator-app](security-info-setup-auth-app.md) . Om så inte är fallet måste du kontakta din organisations supportavdelning om du behöver hjälp. Mer information om säkerhets information finns i [Översikt över säkerhets information (för hands version)](./security-info-setup-signin.md).

2. Markera kryss rutan bredvid **Authenticator-app**och välj sedan **Konfigurera**.

    Sidan **Konfigurera mobilapp** visas.

    ![Skärm som tillhandahåller QR-koden](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Öppna appen Microsoft Authenticator, Välj **Lägg till konto** från ikonen **Anpassa och kontrol lera** längst upp till höger och välj sedan arbets- **eller skol konto**.

    >[!Note]
    >Om det här är första gången du konfigurerar Microsoft Authenticator-appen kan du få en fråga om du vill tillåta att appen får åtkomst till kameran (iOS) eller tillåter att appen tar bilder och spelar in video (Android). Du måste välja **Tillåt** så att Authenticator-appen kan komma åt kameran för att ta en bild av QR-koden i nästa steg. Om du inte tillåter kameran kan du fortfarande konfigurera Authenticator-appen, men du måste lägga till kod informationen manuellt. Information om hur du lägger till koden manuellt finns i [Lägg till ett konto manuellt i appen](user-help-auth-app-add-account-manual.md).

4. Använd enhetens kamera för att skanna QR-koden från skärmen **Konfigurera mobilappen** på datorn och välj sedan **Slutför**.

    >[!Note]
    >Om kameran inte kan avbilda QR-koden kan du manuellt lägga till din konto information i Microsoft Authenticator-appen för tvåstegsverifiering. Mer information och hur du gör det finns i [Lägg till ditt konto manuellt](user-help-auth-app-add-account-manual.md).

5. Granska skärmen **konton** i appen på din enhet för att se till att ditt konto är rätt och att det finns en tillhör ande sexsiffriga verifierings kod. För ytterligare säkerhet ändras verifierings koden var 30: e sekund som hindrar någon från att använda en kod flera gånger.

    ![Skärmen konton](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Nästa steg

- När du har lagt till dina konton i appen kan du logga in med Authenticator-appen på din enhet. Mer information finns i [Logga in med appen](user-help-auth-app-sign-in.md).

- För enheter som kör iOS kan du också säkerhetskopiera dina kontoautentiseringsuppgifter och relaterade appinställningar, till exempel ordningen på dina konton, i molnet. Mer information finns i [säkerhets kopiering och återställning med Microsoft Authenticator app](user-help-auth-app-backup-recovery.md).