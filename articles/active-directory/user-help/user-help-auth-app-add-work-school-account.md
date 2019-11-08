---
title: Lägg till ett arbets-eller skol konto i en Authenticator-app – Azure AD
description: Så här lägger du till ditt arbets-eller skol konto i Microsoft Authenticator-appen för tvåstegsverifiering.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7c9fa41a1278f0e1e54a06c3930e3bbc3d3d3ae
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73803737"
---
# <a name="add-your-work-or-school-account"></a>Lägg till ditt arbets-eller skol konto

Om din organisation använder tvåstegsverifiering kan du konfigurera ditt arbets-eller skol konto för att använda Microsoft Authenticator-appen som en av verifierings metoderna.

>[!Important]
>Innan du kan lägga till ditt konto måste du ladda ned och installera Microsoft Authenticator-appen. Om du inte har gjort det än följer du stegen i artikeln [Hämta och installera appen](user-help-auth-app-download-install.md) .

## <a name="add-your-work-or-school-account"></a>Lägg till ditt arbets-eller skol konto

1. På datorn går du till sidan [ytterligare säkerhets verifiering](https://aka.ms/mfasetup) .

    >[!Note]
    >Om du inte ser sidan **ytterligare säkerhets verifiering** är det möjligt att administratören har aktiverat säkerhets information (för hands versionen). Om så är fallet bör du följa anvisningarna i avsnittet [Konfigurera säkerhets information för att använda en Authenticator-app](security-info-setup-auth-app.md) . Om så inte är fallet måste du kontakta din organisations supportavdelning om du behöver hjälp. Mer information om säkerhets information finns i [Översikt över säkerhets information (för hands version)](user-help-security-info-overview.md).

2. Markera kryss rutan bredvid **Authenticator-app**och välj sedan **Konfigurera**.

    Sidan **Konfigurera mobilapp** visas.

    ![Skärm som tillhandahåller QR-koden](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Öppna appen Microsoft Authenticator, Välj **Lägg till konto** från ikonen **Anpassa och kontrol lera** längst upp till höger och välj sedan arbets- **eller skol konto**.

    >[!Note]
    >Om det här är första gången du konfigurerar Microsoft Authenticator-appen kan du få en fråga om du vill tillåta att appen får åtkomst till kameran (iOS) eller tillåter att appen tar bilder och spelar in video (Android). Du måste välja **Tillåt** så att Authenticator-appen kan komma åt kameran för att ta en bild av QR-koden i nästa steg. Om du inte tillåter kameran kan du fortfarande konfigurera Authenticator-appen, men du måste lägga till kod informationen manuellt. Information om hur du lägger till koden manuellt finns i se [lägga till ett konto manuellt i appen](user-help-auth-app-add-account-manual.md).

4. Använd enhetens kamera för att skanna QR-koden från skärmen **Konfigurera mobilappen** på datorn och välj sedan **Slutför**.

    >[!Note]
    >Om kameran inte kan avbilda QR-koden kan du manuellt lägga till din konto information i Microsoft Authenticator-appen för tvåstegsverifiering. Mer information och hur du gör det finns i [Lägg till ditt konto manuellt](user-help-auth-app-add-account-manual.md).

5. Granska skärmen **konton** i appen på din enhet för att se till att ditt konto är rätt och att det finns en tillhör ande sexsiffriga verifierings kod. För ytterligare säkerhet ändras verifierings koden var 30: e sekund som hindrar någon från att använda en kod flera gånger.

    ![Skärmen konton](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Nästa steg

- När du har lagt till dina konton i appen kan du logga in med Authenticator-appen på din enhet. Mer information finns i [Logga in med appen](user-help-auth-app-sign-in.md).

- För enheter som kör iOS kan du också säkerhetskopiera dina kontoautentiseringsuppgifter och relaterade appinställningar, till exempel ordningen på dina konton, i molnet. Mer information finns i [säkerhets kopiering och återställning med Microsoft Authenticator app](user-help-auth-app-backup-recovery.md).
