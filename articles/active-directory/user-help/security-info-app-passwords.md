---
title: Konfigurera applösenord med hjälp av säkerhetsinformation - Azure Active Directory | Microsoft Docs
description: Konfigurera automatiskt genererade lösenord (lösenord) ska användas med varje icke-Webbläsarprogrammet, avgränsa från ett normalt lösenord, med hjälp av säkerhetsinformation.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 185c6e18a84369702de3bd0c398a9cc0c64b8217
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55191388"
---
# <a name="manage-app-passwords-using-security-info-preview"></a>Hantera lösenord med hjälp av säkerhetsinformation (förhandsversion)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Vissa icke-webbläsarappar, till exempel Outlook 2010 inte stöder tvåstegsverifiering. Den här avsaknaden av stöd innebär att appen inte fungerar om du använder tvåstegsverifiering. För att undvika det här problemet, kan du skapa ett automatiskt genererat lösenord ska användas med varje icke-Webbläsarprogrammet, separat från normala lösenordet.

När du använder lösenord, är det viktigt att komma ihåg:

- Applösenord är automatiskt genererade och endast anges en gång per app.

- Det finns en gräns på 40 lösenord per användare. Om du försöker skapa en efter att den gränsen, uppmanas du att ta bort ett befintligt lösenord innan de kan skapa en ny.

- Använda ett applösenord per enhet, inte per app. Till exempel skapa ett enda lösenord för alla appar på din bärbara dator och sedan ett annat lösenord till alla appar på ditt skrivbord.

    >[!Note]
    >Office 2013-klienter (inklusive Outlook) har stöd för nya autentiseringsprotokoll och kan användas med tvåstegsverifiering. Det här stödet innebär att när tvåstegsverifiering är aktiverad, du behöver inte längre applösenord för Office 2013-klienter. Mer information finns i den [hur modern autentisering fungerar för Office 2013 och Office 2016 klientappar](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) artikeln.

## <a name="create-and-delete-app-passwords-using-security-info"></a>Skapa och ta bort lösenord med hjälp av säkerhetsinformation

Om du använder tvåstegsverifiering med företagets eller skolans konto och din administratör har aktiverat info säkerhetsupplevelse, kan du skapa och ta bort ditt lösenord med hjälp av Mina appar-portalen.

Om administratören inte har aktiverat info säkerhetsupplevelse, du måste följa anvisningar och information i den [hantera applösenord för tvåstegsverifiering](multi-factor-authentication-end-user-app-passwords.md) avsnittet.

### <a name="to-create-app-passwords-using-the-my-apps-portal"></a>Att skapa applösenord med hjälp av Mina appar-portalen

1. Logga in på ditt arbets- eller skolkonto konto.

2. Gå till myapps.microsoft.com, väljer du ditt namn i övre högra hörnet på sidan och välj sedan **profil**.

3. I den **Hantera konto** Välj **redigera säkerhetsinformation**.

    ![Profil för skärmen med Redigera security info med länken markerad](media/security-info/security-info-profile.png)

4. I den **skydda ditt konto** väljer **lägga till säkerhetsinformation**.

    ![Security info skärmen med befintliga, redigerbara information](media/security-info/security-info-edit-add-info.png)

5. I den **lägga till säkerhetsinformation** väljer **applösenord**.

6. I den **skapa ditt applösenord** skärmen, Skriv ett namn för ditt applösenord och välj sedan **nästa**.

    ![Skärm där du namnger ditt applösenord](media/security-info/security-info-name-app-password.png)

7. Välj **kopia** att kopiera lösenord till Urklipp och välj sedan **nästa**.

    ![Skärmen med applösenord för att kopiera](media/security-info/security-info-create-app-password.png)
    
8. Kontrollera att applösenordet visas på den **skydda ditt konto** skärmen.

    ![Behåll säker skärmen med applösenord](media/security-info/security-info-keep-secure-app-password.png)

### <a name="to-delete-app-passwords-using-the-my-apps-portal"></a>Ta bort lösenord med hjälp av Mina appar-portalen

1. På den **skydda ditt konto** väljer den **X** bredvid applösenord för att ta bort.

    ![Håll säker skärm, ta bort applösenordet](media/security-info/security-info-keep-secure-delete-app-password.png)

2. I den **ta bort programlösenord** väljer **ta bort**.

    ![Ta bort skärmen](media/security-info/security-info-keep-secure-delete-app-password2.png)

## <a name="next-steps"></a>Nästa steg

- Om du behöver uppdatera din säkerhetsinformation, följ instruktionerna i den [hantera din säkerhetsinformation](security-info-manage-settings.md) artikeln.

- Mer allmän information om säkerhetsinformation och vad du kan göra finns i [info Säkerhetsöversikt](user-help-security-info-overview.md) 
