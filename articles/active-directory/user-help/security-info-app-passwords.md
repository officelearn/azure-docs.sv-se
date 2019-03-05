---
title: Ställa in applösenord från sidan säkerhetsinformation (förhandsversion) – Azure Active Directory | Microsoft Docs
description: Konfigurera automatiskt genererade lösenord (lösenord) ska användas med alla icke-webbläsarbaserade appar eller appar som inte stöder två faktorer verifiering i din organisation. Det här applösenordet skiljer sig från ett normalt lösenord och kan ställas in från sidan säkerhetsinformation.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55dfab0c60e77b86157a005db34c37917a5e08d2
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341111"
---
# <a name="manage-app-passwords-from-your-security-info-preview-page"></a>Hantera lösenord från sidan Security info (förhandsversion)
Vissa appar, till exempel Outlook 2010 stöder inte tvåstegsverifiering. Den här avsaknaden av stöd innebär att appen inte fungerar om du använder tvåstegsverifiering i din organisation. För att undvika det här problemet, kan du skapa ett automatiskt genererat lösenord ska användas med varje icke-Webbläsarprogrammet, separat från normala lösenordet.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>Administratören kan inte använda applösenord. Om du inte ser **applösenord** som ett alternativ kan de inte är tillgängliga i din organisation.

När du använder lösenord, är det viktigt att komma ihåg:

- Applösenord är automatiskt genererade och endast anges en gång per app.

- Det finns en gräns på 40 lösenord per användare. Om du försöker skapa en efter att den gränsen, uppmanas du att ta bort ett befintligt lösenord innan de kan skapa en ny.

- Använda ett applösenord per enhet, inte per app. Till exempel skapa ett enda lösenord för alla appar på din bärbara dator och sedan ett annat lösenord till alla appar på ditt skrivbord.

    >[!Note]
    >Office 2013-klienter (inklusive Outlook) har stöd för nya autentiseringsprotokoll och kan användas med tvåstegsverifiering. Det här stödet innebär att när tvåstegsverifiering är aktiverad, du behöver inte längre applösenord för Office 2013-klienter. Mer information finns i den [hur modern autentisering fungerar för Office 2013 och Office 2016 klientappar](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) artikeln.

## <a name="create-new-app-passwords"></a>Skapa nya applösenord
Om du använder tvåstegsverifiering med ditt arbets- eller skolkonto konto och din administratör har aktiverat info säkerhetsupplevelse, du kan skapa och ta bort ditt lösenord med hjälp av den **säkerhetsinformation** sidan.

>[!Note]
>Om administratören inte har aktiverat info säkerhetsupplevelse, du måste följa anvisningar och information i den [hantera applösenord för tvåstegsverifiering](multi-factor-authentication-end-user-app-passwords.md) avsnittet.

### <a name="to-create-a-new-app-password"></a>Skapa ett nytt applösenord
1. Logga in på ditt arbets- eller skolkonto konto och gå sedan till din https://myprofile.microsoft.com/ sidan.

    ![Min profil-sidan som visar markerad Security info länkar](media/security-info/securityinfo-myprofile-page.png)

2. Välj **säkerhetsinformation** från det vänstra navigeringsfönstret eller från länken i den **säkerhetsinformation** blockera och sedan välja **tilläggsmetod** från den **säkerhetsinformation**  sidan.

    ![Sidan för Security information med markerade Lägg till metodalternativet](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. På den **Lägg till en metod** väljer **applösenord** från listrutan och välj sedan **Lägg till**.

    ![Lägg till metoden rutan med applösenord som valts](media/security-info/securityinfo-myprofile-addpassword.png)

4. Skriv namnet på appen som kräver applösenordet och välj sedan **nästa**.

    ![Applösenordssidan med namnet på appen](media/security-info/securityinfo-myprofile-password-appname.png)

5. Kopiera text från den **lösenord** , klistra in lösenordet i området lösenord i appen (i det här exemplet Outlook 2010) och väljer sedan **klar**.

    ![Applösenordssidan med namnet på appen](media/security-info/securityinfo-myprofile-password-copytext.png)
    
    Lösenordet har lagts till och du kan logga in till appen framöver.

## <a name="delete-your-app-passwords"></a>Ta bort dina applösenord
Om du inte längre behöver använda en app som kräver ett applösenord kan du ta bort det associerade applösenordet. Ta bort applösenordet frigör en tillgänglig app lösenord vinklar för användning i framtiden.

>[!Important]
>Om du tar bort ett applösenord av misstag, går det inte att ångra åtgärden. Du måste skapa ett nytt applösenord och ange den igen i appen, följa stegen i den [skapa nya applösenord](#create-new-app-passwords) i den här artikeln.

### <a name="to-delete-an-app-password"></a>Att ta bort ett applösenord

1. På den **säkerhetsinformation** väljer den **ta bort** länka bredvid den **applösenord** alternativ för den specifika appen.

    ![Länk för att ta bort app lösenordsmetoden från säkerhetsinformation](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Välj **Ja** från dialogrutan Bekräfta att ta bort den **applösenord**. När applösenordet har tagits bort, tas den bort från din säkerhetsinformation och försvinner den från den **säkerhetsinformation** sidan.

## <a name="for-more-information"></a>Mer information
- Mer information om den **säkerhetsinformation** sidan och hur du konfigurerar den finns i [info Säkerhetsöversikt](user-help-security-info-overview.md)
