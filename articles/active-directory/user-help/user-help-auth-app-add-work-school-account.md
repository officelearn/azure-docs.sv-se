---
title: Lägga till ett arbets- eller skolkonto i Microsoft Authenticator-appen - Azure AD
description: Lägg till ditt arbets- eller skolkonto i Microsoft Authenticator-appen för att verifiera din identitet när du använder tvåfaktorsverifiering.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: f0cc14a53f7ead7f0a496728d477d7d30857a0fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063925"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Lägga till ditt arbets- eller skolkonto i Microsoft Authenticator-appen

Om din organisation använder tvåfaktorsverifiering kan du konfigurera ditt arbets- eller skolkonto så att microsoft authenticator-appen används som en av verifieringsmetoderna.

>[!Important]
>Innan du kan lägga till ditt konto måste du hämta och installera Microsoft Authenticator-appen. Om du inte har gjort det ännu följer du stegen i [hämtnings- och installationsartikeln.](user-help-auth-app-download-install.md)

## <a name="add-your-work-or-school-account"></a>Lägga till ditt arbets- eller skolkonto

1. Gå till sidan [Ytterligare säkerhetsverifiering](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) på datorn.

    >[!Note]
    >Om sidan **Ytterligare säkerhetsverifiering** inte visas är det möjligt att administratören har aktiverat säkerhetsinformationen (förhandsversionen). Om så är fallet bör du följa instruktionerna i [avsnittet Konfigurera säkerhetsinformation om du vill använda en autentiseringsapp.](security-info-setup-auth-app.md) Om så inte är fallet måste du kontakta organisationens supportavdelning för att få hjälp. Mer information om säkerhetsinformation finns i [Översikt över säkerhetsinformation (förhandsversion).](user-help-security-info-overview.md)

2. Markera rutan **bredvid Appen Autentiserare**och välj sedan **Konfigurera**.

    Sidan **Konfigurera mobilapp** visas.

    ![Skärm som tillhandahåller QR-koden](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Öppna Microsoft Authenticator-appen, välj **Lägg till konto** i ikonen Anpassa och **kontrollera** längst upp till höger och välj sedan **Arbets- eller skolkonto**.

    >[!Note]
    >Om det är första gången du konfigurerar Microsoft Authenticator-appen kan du få en uppmaning om du vill tillåta appen att komma åt kameran (iOS) eller låta appen ta bilder och spela in video (Android). Du måste välja **Tillåt** så att autentiseringsappen kan komma åt kameran för att ta en bild av QR-koden i nästa steg. Om du inte tillåter kameran kan du fortfarande konfigurera autentiseringsappen, men du måste lägga till kodinformationen manuellt. Information om hur du lägger till koden manuellt finns i [Lägg till ett konto manuellt i appen](user-help-auth-app-add-account-manual.md).

4. Använd enhetens kamera för att skanna QR-koden från skärmen **Konfigurera mobilapp** på datorn och välj sedan **Klar**.

    >[!Note]
    >Om kameran inte kan fånga QR-koden kan du manuellt lägga till kontoinformationen i Microsoft Authenticator-appen för tvåfaktorsverifiering. Mer information och hur du gör det finns i [Lägg till ditt konto manuellt](user-help-auth-app-add-account-manual.md).

5. Granska skärmen **Konton** för appen på enheten för att se till att ditt konto har rätt och att det finns en tillhörande sexsiffrig verifieringskod. För ytterligare säkerhet ändras verifieringskoden var 30:e sekund så att någon inte använder en kod flera gånger.

    ![Skärmen Konton](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Nästa steg

- När du har lagt till dina konton i appen kan du logga in med appen Authenticator på enheten. Mer information finns [i Logga in med appen](user-help-auth-app-sign-in.md).

- För enheter som kör iOS kan du också säkerhetskopiera dina kontouppgifter och relaterade appinställningar, till exempel ordningen på dina konton, till molnet. Mer information finns i [Säkerhetskopiering och återställning med Microsoft Authenticator-appen](user-help-auth-app-backup-recovery.md).
