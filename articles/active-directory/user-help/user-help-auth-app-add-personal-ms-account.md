---
title: Lägga till ditt personliga Microsoft-konton i Microsoft Authenticator-appen – Azure Active Directory | Microsoft Docs
description: Hur du lägger till ditt personliga Microsoft-konton som för Outlook.com eller Xbox LIVE till Microsoft Authenticator-appen för verifiering av två faktorer.
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
ms.openlocfilehash: eba77f79ad4625319a9b6e3eae46bf8470f43607
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203785"
---
# <a name="add-your-personal-microsoft-accounts"></a>Lägg till ditt personliga Microsoft-konton
Lägga till ditt personliga Microsoft-konton som för Outlook.com, Xbox LIVE och på Microsoft Authenticator-appen för både standard tvåfaktors-verifieringen och konfiguration av lösenordsfri phone inloggningsmetod.

- **Standard tvåfaktorsautentisering verifieringsmetod.** Ange ditt användarnamn och lösenord i enheten du har loggat in och välj sedan om Microsoft Authenticator-appen skickar ett meddelande eller om du vill kopiera den associerade verifieringskoden från den **konton** skärmen i Microsoft Authenticator-appen.

- **Konfiguration av lösenordsfri inloggning metod.** Ange ditt användarnamn i enheten du loggar in för ditt personliga Microsoft-konto och sedan använda din mobila enhet för att kontrollera att det är du med hjälp av ditt fingeravtryck, ett ansikte eller en PIN-kod. För den här metoden behöver du inte ange ditt lösenord.

>[!Important]
>Innan du kan lägga till ditt konto, måste du hämta och installera Microsoft Authenticator-appen. Om du inte har gjort det ännu, följer du stegen i den [ladda ned och installera appen](user-help-auth-app-download-install.md) artikeln.

## <a name="add-your-personal-microsoft-account"></a>Lägg till ditt personliga Microsoft-konto
Du kan lägga till ditt personliga Microsoft-konto genom att aktivera tvåfaktorsautentisering verifiering och sedan genom att lägga till kontot i appen.

>[!Note]
>Om du planerar att endast använda lösenordslös telefoninloggning för ditt personliga Microsoft-konto har du inte aktivera tvåfaktorsautentisering verifiering. För ytterligare kontosäkerhet rekommenderar vi dock att du aktiverar tvåfaktorsautentisering verifiering.

### <a name="turn-on-two-factor-verification"></a>Aktivera tvåfaktorsautentisering verifiering

1. På din dator går du till din [Säkerhetsgrunder](https://account.microsoft.com/security) sidan och logga in med ditt personliga Microsoft-konto. Till exempel alain@outlook.com.

2. Längst ned på den **säkerhetskoncepten** väljer du den **mer säkerhetsalternativ** länk.

    ![Grunderna säkerhetssidan med ”mer säkerhetsalternativ”-länken markerad](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Gå till den **tvåstegsverifiering** avsnittet och väljer att inaktivera funktionen **på**. Du kan också stänga av den här om du inte längre vill använda den med ditt personliga konto.

### <a name="add-your-microsoft-account-to-the-app"></a>Lägga till ditt Microsoft-konto i appen

1. Öppna Microsoft Authenticator-appen på din mobila enhet.

2. Välj **Lägg till konto** från den **anpassa och styra** ikonen i det övre högra hörnet.

    ![På kontosidan, med ikonen anpassa och styra markerat](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. I den **Lägg till konto** väljer **personligt konto**.

4. Logga in på ditt personliga konto, med lämplig e-postadress (till exempel alain@outlook.com), och välj sedan **nästa**.

    >[!Note]
    >Om du inte har ett personligt microsoftkonto, kan du skapa en här.

5. Ange ditt lösenord och väljer sedan **logga in**.

    Ditt personliga konto läggs till Microsoft Authenticator-appen.

## <a name="next-steps"></a>Nästa steg

- När du har lagt till dina konton till appen, kan du logga in med Authenticator-appen på din enhet. Mer information finns i [logga in med appen](user-help-auth-app-sign-in.md).

- För enheter som kör iOS, du kan även säkerhetskopiera dina autentiseringsuppgifter och appinställningar, till exempel ordningen på dina konton, som rör molnet. Mer information finns i [säkerhetskopiering och återställning med Microsoft Authenticator-appen](user-help-auth-app-backup-recovery.md).
