---
title: Skapa applösenord från sidan Säkerhetsinformation (förhandsversion) - Azure AD
description: Skapa automatiskt genererade lösenord (applösenord) som du kan använda med en app som inte är webbläsarappar eller appar som inte stöder tvåfaktorsverifiering i organisationen. Detta applösenord är skilt från ett vanligt lösenord och kan ställas in från sidan Säkerhetsinformation.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: curtand
ms.openlocfilehash: 787fa67ee77997fd1f9967db3abdbfc83d4ffad2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064078"
---
# <a name="create-app-passwords-from-the-security-info-preview-page"></a>Skapa applösenord från sidan Säkerhetsinformation (förhandsgranskning)

Vissa appar, till exempel Outlook 2010, stöder inte tvåstegsverifiering. Den här bristen på stöd innebär att om du använder tvåstegsverifiering i din organisation fungerar inte appen. För att komma runt det här problemet kan du skapa ett automatiskt genererat lösenord som ska användas med varje app som inte är en webbläsare, separat från ditt vanliga lösenord.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>Administratören kanske inte tillåter att du använder applösenord. Om du inte ser **Applösenord** som ett alternativ är de inte tillgängliga i organisationen.

När du använder applösenord är det viktigt att komma ihåg:

- Applösenord genereras automatiskt och ska skapas och anges en gång per app.

- Det finns en gräns på 40 lösenord per användare. Om du försöker skapa ett efter den gränsen uppmanas du att ta bort ett befintligt lösenord innan du får skapa det nya.

    >[!Note]
    >Office 2013-klienter (inklusive Outlook) stöder nya autentiseringsprotokoll och kan användas med tvåstegsverifiering. Det här stödet innebär att när tvåstegsverifiering har aktiverats behöver du inte längre applösenord för Office 2013-klienter. Mer information finns i artikeln [Hur modern autentisering fungerar för Office 2013 och Office 2016-klientappar.](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)

## <a name="create-new-app-passwords"></a>Skapa nya applösenord

Om du använder tvåstegsverifiering med ditt arbets- eller skolkonto och administratören har aktiverat säkerhetsinformationsupplevelsen kan du skapa och ta bort applösenorden med sidan **Säkerhetsinformation.**

>[!Note]
>Om administratören inte har aktiverat säkerhetsinformationsupplevelsen måste du följa instruktionerna och informationen i avsnittet [Hantera applösenord för tvåstegsverifiering.](multi-factor-authentication-end-user-app-passwords.md)

### <a name="to-create-a-new-app-password"></a>Så här skapar du ett nytt applösenord

1. Logga in på ditt arbets- eller https://myprofile.microsoft.com/ skolkonto och gå sedan till din sida.

    ![Sidan Min profil, med markerade länkar för säkerhetsinformation](media/security-info/securityinfo-myprofile-page.png)

2. Välj **Säkerhetsinformation** i det vänstra navigeringsfönstret eller från länken i blocket **Säkerhetsinformation** och välj sedan **Lägg till metod** på sidan **Säkerhetsinformation.**

    ![Sidan Säkerhetsinformation med markerat alternativ för Lägg till metod](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. På sidan **Lägg till en metod** väljer du **Applösenord** i listrutan och väljer sedan **Lägg till**.

    ![Rutan Lägg till metod med applösenordet markerat](media/security-info/securityinfo-myprofile-addpassword.png)

4. Skriv namnet på appen som kräver applösenordet och välj sedan **Nästa**.

    ![Sidan Applösenord, med namnet på appen](media/security-info/securityinfo-myprofile-password-appname.png)

5. Kopiera texten från rutan **Lösenord,** klistra in lösenordet i lösenordsområdet i appen (i det här exemplet Outlook 2010) och välj sedan **Klar**.

    ![Sidan Applösenord, med namnet på appen](media/security-info/securityinfo-myprofile-password-copytext.png)

    Lösenordet läggs till och du kan logga in på din app framöver.

## <a name="delete-your-app-passwords"></a>Ta bort dina applösenord

Om du inte längre behöver använda en app som kräver ett applösenord kan du ta bort det associerade applösenordet. Om du tar bort applösenordet frigörs en av de tillgängliga applösenordsplatserna för användning i framtiden.

>[!Important]
>Om du tar bort ett applösenord av misstag kan du inte ångra det. Du måste skapa ett nytt applösenord och skriva in det i appen igen, i följande steg i avsnittet [Skapa nya applösenord](#create-new-app-passwords) i den här artikeln.

### <a name="to-delete-an-app-password"></a>Så här tar du bort ett applösenord

1. På sidan **Säkerhetsinformation** väljer du länken **Ta bort bredvid** alternativet **Applösenord** för den specifika appen.

    ![Länk för att ta bort metoden med applösenord från säkerhetsinformation](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Välj **Ja** i bekräftelserutan om du vill ta bort **applösenordet**. När applösenordet har tagits bort tas det bort från din säkerhetsinformation och försvinner från sidan **Säkerhetsinformation.**

## <a name="for-more-information"></a>Mer information

- Mer information om sidan **Säkerhetsinformation** och hur du konfigurerar den finns i [Översikt över säkerhetsinformation](user-help-security-info-overview.md)
