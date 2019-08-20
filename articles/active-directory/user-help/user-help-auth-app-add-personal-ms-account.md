---
title: Lägg till dina personliga Microsoft-konton i Microsoft Authenticator app-Azure Active Directory | Microsoft Docs
description: Hur du lägger till dina personliga Microsoft-konton, till exempel för Outlook.com eller Xbox LIVE till Microsoft Authenticator-appen för tvåstegsverifiering.
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
ms.openlocfilehash: 7cf4629ca97abc5d4ce508f7078f4691467ba249
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616087"
---
# <a name="add-your-personal-microsoft-accounts"></a>Lägg till dina personliga Microsoft-konton

Lägg till dina personliga Microsoft-konton, till exempel för Outlook.com och Xbox LIVE i Microsoft Authenticator-appen för både standard processen med två faktorer och lösen ords lös inloggnings metod.

- **Standard verifierings metod med två faktorer.** Ange ditt användar namn och lösen ord i den enhet som du loggar in på och välj sedan om Microsoft Authenticator appen skickar ett meddelande eller om du hellre vill kopiera den tillhör ande verifierings koden från skärmen **konton** i Microsoft Authenticator-app.

- **Lösen ords registrerings metod.** Ange ditt användar namn i enheten som du loggar in på för din personliga Microsoft-konto och Använd sedan din mobila enhet för att kontrol lera att det är du genom att använda finger avtryck, ansikte eller PIN-kod. För den här metoden behöver du inte ange ditt lösenord.

>[!Important]
>Innan du kan lägga till ditt konto måste du ladda ned och installera Microsoft Authenticator-appen. Om du inte har gjort det än följer du stegen i artikeln [Hämta och installera appen](user-help-auth-app-download-install.md) .

## <a name="add-your-personal-microsoft-account"></a>Lägg till din personliga Microsoft-konto

Du kan lägga till din personliga Microsoft-konto genom att först aktivera tvåstegsverifiering och sedan lägga till kontot i appen.

>[!Note]
>Om du endast planerar att använda lösen ords lös inloggning för din personliga Microsoft-konto behöver du inte aktivera tvåstegsverifiering. För ytterligare konto säkerhet rekommenderar vi dock att du aktiverar tvåstegsverifiering.

### <a name="turn-on-two-factor-verification"></a>Aktivera tvåstegsverifiering

1. På din dator går du till sidan [grundläggande om säkerhet](https://account.microsoft.com/security) och loggar in med din personliga Microsoft-konto. Till exempel alain@outlook.com.

2. Längst ned på sidan **grundläggande säkerhets** inställningar väljer du länken **fler säkerhets alternativ** .

    ![Sidan grundläggande säkerhet med länken "fler säkerhets alternativ" markerad](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Gå till avsnittet **tvåstegsverifiering** och välj att aktivera funktionen. Du kan också inaktivera det här om du inte längre vill använda det med ditt personliga konto.

### <a name="add-your-microsoft-account-to-the-app"></a>Lägg till din Microsoft-konto i appen

1. Öppna appen Microsoft Authenticator på den mobila enheten.

2. Välj **Lägg till konto** från ikonen **Anpassa och kontrol lera** längst upp till höger.

    ![Sidan konton, där ikonen anpassa och kontroll är markerad](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. På sidan **Lägg till konto** väljer du **personligt konto**.

4. Logga in på ditt personliga konto med lämplig e-postadress (till exempel alain@outlook.com) och välj sedan **Nästa**.

    >[!Note]
    >Om du inte har någon personlig Microsoft-konto kan du skapa en här.

5. Ange ditt lösen ord och välj sedan **Logga**in.

    Ditt personliga konto läggs till i Microsoft Authenticator-appen.

## <a name="next-steps"></a>Nästa steg

- När du har lagt till dina konton i appen kan du logga in med Authenticator-appen på din enhet. Mer information finns i [Logga in med appen](user-help-auth-app-sign-in.md).

- Om du har problem med att hämta din verifierings kod för din personliga Microsoft-konto kan du läsa avsnittet **fel sökning av verifierings kod problem** i artikeln [Microsoft-konto säkerhets information & verifierings koder](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- För enheter som kör iOS kan du också säkerhetskopiera dina kontoautentiseringsuppgifter och relaterade appinställningar, till exempel ordningen på dina konton, i molnet. Mer information finns i [säkerhets kopiering och återställning med Microsoft Authenticator app](user-help-auth-app-backup-recovery.md).
