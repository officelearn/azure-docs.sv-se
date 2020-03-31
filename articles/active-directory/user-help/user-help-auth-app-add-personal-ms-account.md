---
title: Lägga till ett personligt Microsoft-konto i Microsoft Authenticator-appen - Azure AD
description: Lägg till personliga Microsoft-konton, till exempel för Outlook.com eller Xbox LIVE i Microsoft Authenticator-appen för att verifiera din identitet när du använder tvåfaktorsverifiering.
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
ms.openlocfilehash: 289c00e28fd82b78b321732628ff85d22c64cabf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062310"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Lägga till personliga Microsoft-konton i Microsoft Authenticator-appen

Lägg till dina personliga Microsoft-konton, till exempel för Outlook.com och Xbox LIVE i Microsoft Authenticator-appen för både standardverifieringsprocessen för tvåfaktorsverifiering och den lösenordslösa inloggningsmetoden för telefoner.

- **Standard verifieringsmetod med två faktorer.** Skriv in ditt användarnamn och lösenord i enheten du loggar in på och välj sedan om Microsoft Authenticator-appen ska skicka ett meddelande eller om du föredrar att kopiera den associerade verifieringskoden från skärmen **Konton** i Microsoft Authenticator-appen.

- **Lösenordslös inloggningsmetod.** Skriv in ditt användarnamn på den enhet som du loggar in på för ditt personliga Microsoft-konto och använd sedan din mobila enhet för att verifiera att det är du med hjälp av fingeravtryck, ansikte eller PIN-kod. För den här metoden behöver du inte ange ditt lösenord.

>[!Important]
>Innan du kan lägga till ditt konto måste du hämta och installera Microsoft Authenticator-appen. Om du inte har gjort det ännu följer du stegen i [hämtnings- och installationsartikeln.](user-help-auth-app-download-install.md)

## <a name="add-your-personal-microsoft-account"></a>Lägg till ditt personliga Microsoft-konto

Du kan lägga till ditt personliga Microsoft-konto genom att först aktivera tvåfaktorsverifiering och sedan genom att lägga till kontot i appen.

>[!Note]
>Om du planerar att bara använda lösenordslös telefon inloggning för ditt personliga Microsoft-konto, behöver du inte aktivera tvåfaktorsverifiering. För ytterligare kontosäkerhet rekommenderar vi dock att du aktiverar tvåfaktorsverifiering.

### <a name="turn-on-two-factor-verification"></a>Aktivera tvåfaktorsverifiering

1. På datorn går du till sidan [Grundläggande säkerhet](https://account.microsoft.com/security) och loggar in med ditt personliga Microsoft-konto. Till exempel alain@outlook.com.

2. Välj länken **fler säkerhetsalternativ** längst ned på sidan **Grunderna i Säkerhet.**

    ![Sidan Grundläggande säkerhetsinställningar med länken "fler säkerhetsalternativ" markerad](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Gå till avsnittet **Tvåstegsverifiering** och välj att aktivera **funktionen**. Du kan också stänga av den här om du inte längre vill använda den med ditt personliga konto.

### <a name="add-your-microsoft-account-to-the-app"></a>Lägga till ditt Microsoft-konto i appen

1. Öppna Microsoft Authenticator-appen på din mobila enhet.

2. Välj **Lägg till konto** i ikonen Anpassa och **kontrollera** längst upp till höger.

    ![Sidan Konton, med ikonen Anpassa och kontrollera markerad](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. På sidan **Lägg till konto** väljer du Personligt **konto**.

4. Logga in på ditt personliga konto med rätt alain@outlook.come-postadress (till exempel ) och välj sedan **Nästa**.

    >[!Note]
    >Om du inte har ett personligt Microsoft-konto kan du skapa ett här.

5. Ange ditt lösenord och välj sedan **Logga in**.

    Ditt personliga konto läggs till i Microsoft Authenticator-appen.

## <a name="next-steps"></a>Nästa steg

- När du har lagt till dina konton i appen kan du logga in med appen Authenticator på enheten. Mer information finns [i Logga in med appen](user-help-auth-app-sign-in.md).

- Om du har problem med att få din verifieringskod för ditt personliga Microsoft-konto läser du avsnittet **Felsöka verifieringskodproblem** i [säkerhetsinformationen för Microsoft-kontot & verifieringskoder.](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)

- För enheter som kör iOS kan du också säkerhetskopiera dina kontouppgifter och relaterade appinställningar, till exempel ordningen på dina konton, till molnet. Mer information finns i [Säkerhetskopiering och återställning med Microsoft Authenticator-appen](user-help-auth-app-backup-recovery.md).
