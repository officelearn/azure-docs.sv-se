---
title: Hur du använder lösenord i Azure MFA? | Microsoft Docs
description: Den här sidan hjälper användarna att förstå vad applösenord är och hur de används med hänsyn till Azure MFA.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: lizross
ms.custom: end-user
ms.openlocfilehash: 290458e95aaed0cc85d83539d9d870c334df45df
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059433"
---
# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>Vad är Applösenord i Azure Multi-Factor Authentication?
Vissa icke-webbläsarbaserade appar, till exempel den interna e-Apple-klient som använder Exchange Active Sync, stöder för närvarande inte multifaktorautentisering. Multifaktorautentisering aktiveras per användare. Det innebär att om en användare har aktiverats för multifaktorautentisering och de försöker använda icke-webbläsarbaserade appar kan de inte att göra detta. Ett applösenord gör detta möjligt. Om du upprätthålla Multi-Factor Authentication via principer för villkorlig åtkomst och inte via MFA per användare, kan du inte skapa applösenord. Program som använder principer för villkorlig åtkomst för åtkomstkontroll behöver inte applösenord.

När du har ett applösenord kan använda du det i stället för det ursprungliga lösenordet med dessa icke-webbläsarbaserade appar. Det beror på att när du registrerar för tvåstegsverifiering kan du be Microsoft att inte låta alla logga in med ditt lösenord om de inte kan också utföra andra verifiering. Apple interna e-postklienten på din telefon kan inte logga in som du eftersom den inte kan uppmana för tvåstegsverifiering. För den här lösningen är att skapa en säkrare applösenord som du inte använder dagliga, men endast för de appar som inte stöder tvåstegsverifiering. Använda applösenordet så att appar kan kringgå multifaktorautentisering och fortsätta att fungera.

> [!NOTE]
> Office 2013-klienter (inklusive Outlook) har stöd för nya autentiseringsprotokoll och kan användas med tvåstegsverifiering.  Det innebär att när du har aktiverat, applösenord inte krävs för användning med Office 2013-klienter.  Mer information finns i [Office 2013 offentlig förhandsgranskning av modern autentisering](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).


## <a name="how-to-use-app-passwords"></a>Hur du använder applösenord
Här följer några saker att komma ihåg om hur du använder applösenord.

* Du skapa inte ditt eget lösenord. I stället genereras de automatiskt. Eftersom du behöver bara ange applösenordet en gång per app, är det säkrast att använda ett mer komplext lösenord som skapas automatiskt i stället för att göra en som du kan komma ihåg.
* Det finns för närvarande en gräns på 40 lösenord per användare. Om du försöker skapa ett lösenord när gränsen har uppnåtts uppmanas du att ta bort en av dina befintliga applösenord innan du skapar en ny.
* Du bör använda ett applösenord per enhet, inte per program. Du kan till exempel skapa ett applösenord för din bärbara dator och använda det applösenordet för alla dina program på den bärbara datorn. Skapa sedan en andra applösenordet för alla dina appar på skrivbordet.
* Du får ett applösenord första gången du registrerar dig för tvåstegsverifiering.  Om du behöver fler håller på att kan du skapa dem.



## <a name="creating-and-deleting-app-passwords"></a>Skapa och ta bort applösenord
Under din ursprungliga inloggning ges ett applösenord som du kan använda.  Dessutom kan du också skapa och ta bort applösenord senare.  Så här gör du beror på hur du använder multifaktorautentisering. Besvara följande frågor för att avgöra var du ska gå för att hantera applösenord:

1. Använder du tvåstegsverifiering för ditt personliga Microsoft-konto? Om Ja, du bör använda den [applösenord och tvåstegsverifiering](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification) artikeln om du behöver hjälp. Om Nej, Fortsätt att fråga två.

2. OK, så att du kan använda tvåstegsverifiering för arbets-eller skolkonto. Du använder den för att logga in på Office 365-appar? Om Ja, du bör använda [skapa ett applösenord för Office 365](https://support.office.com/article/Create-an-app-password-for-Office-365-3e7c860f-bda4-4441-a618-b53953ee1183) om du behöver hjälp. Om Nej, Fortsätt till fråga tre.

3. Använder du tvåstegsverifiering med Microsoft Azure? Om Ja, fortsätter du till den [hantera lösenord i Azure-portalen](#manage-app-passwords-in-the-Azure-portal) i den här artikeln. Om Nej, Fortsätt att fråga fyra.

4. Osäker på var du använda tvåstegsverifiering? Fortsätta att den [hantera applösenord med MyApps-portalen](#manage-app-passwords-with-the-myapps-portal) i den här artikeln.


## <a name="manage-app-passwords-in-the-azure-portal"></a>Hantera lösenord i Azure portal
Om du använder tvåstegsverifiering med Azure som du vill skapa applösenord via Azure portal.



## <a name="manage-app-passwords-with-the-myapps-portal"></a>Hantera applösenord med MyApps-portalen.
Om du inte är säker på hur du använder multifaktorautentisering kan sedan du alltid skapa och ta bort lösenord via myapps-portalen.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Skapa ett applösenord med hjälp av MyApps-portalen
1. Logga in på [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Klicka på namnet på din längst upp till höger och välj **profil**.
3. Välj **ytterligare säkerhetsverifiering**.
   ![Välj ytterligare säkerhetskontroll – skärmbild](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. Välj **applösenord**.
   ![Välj applösenord – skärmbild](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Klicka på **Skapa**.
6. Ange ett namn för applösenordet och klicka på **nästa**.
7. Kopiera applösenordet till Urklipp och klistra in den i din app.
   ![Skapa ett applösenord](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Att ta bort ett applösenord med hjälp av MyApps-portalen
1. Logga in på [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Välj profil överst på sidan.
3. Välj **ytterligare säkerhetsverifiering**.

   ![Välj ytterligare säkerhetskontroll – skärmbild](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. Välj **applösenord**.

   ![Välj applösenord – skärmbild](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Bredvid applösenordet som du vill ta bort, klickar du på **ta bort**.

   ![Ta bort ett applösenord](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

6. Bekräfta att du vill ta bort lösenordet genom att klicka på **Ja**.
7. När applösenordet har tagits bort, kan du klicka på **Stäng**.

## <a name="next-steps"></a>Nästa steg

- [Hantera dina inställningar för tvåstegsverifiering](multi-factor-authentication-end-user-manage-settings.md)

- Prova att använda den [Microsoft Authenticator-appen](microsoft-authenticator-app-how-to.md) att verifiera dina inloggningar med appmeddelanden, i stället för att ta emot skickar ett SMS eller samtal.
