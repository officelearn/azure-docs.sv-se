---
title: Självstudiekurs – registrera ett program för att aktivera registrering och inloggning med Azure Active Directory B2C | Microsoft Docs
description: Använda Azure portal för att skapa en Azure AD B2C-klient och registrera ett program med den.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/08/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ebfe4a2d8c6a5b1d5334034e406131fac81f0c19
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713906"
---
# <a name="tutorial-register-an-application-to-enable-sign-up-and-sign-in-using-azure-active-directory-b2c"></a>Självstudier: Registrera ett program för att aktivera registrering och inloggning med Azure Active Directory B2C

Den här kursen hjälper dig att skapa en Microsoft Azure Active Directory (AD Azure) B2C-klient och registrera ett program med ett par minuter.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en Azure AD B2C-klient
> * Länka din klient till din prenumeration
> * Registrera ditt program

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Skapa en Azure AD B2C-klient

B2C-funktioner kan inte aktiveras i din befintliga klienter. Du måste skapa en Azure AD B2C-klient.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

Grattis, du har skapat en Azure Active Directory B2C-klient. Du är en Global administratör för klienten. Du kan lägga till andra globala administratörer om det behövs. Växla till den nya innehavaren, klicka på den *hantera nya klient länken*.

![Hantera din ny klient-länk](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> Om du planerar att använda en B2C-klient för en produktionsapp artikeln om [produktionsskala kontra preview B2C hyresgäster](active-directory-b2c-reference-tenant-type.md). Det finns kända problem när du tar bort en befintlig B2C-klient och skapa den igen med samma domännamn. Du måste skapa en B2C-klient med ett annat domännamn.
>
>

## <a name="link-your-tenant-to-your-subscription"></a>Länka din klient till din prenumeration

Du måste länka din Azure AD B2C-klient till din Azure-prenumeration att aktivera alla B2C-funktioner och betalar avgifter för användning. Mer information, [i den här artikeln](active-directory-b2c-how-to-enable-billing.md). Om du inte länka din Azure AD B2C-klient till din Azure-prenumeration, vissa funktioner är blockerad och visas ett varningsmeddelande (”ingen prenumeration länkad till den här B2C-klient eller prenumeration måste kontrolleras”.) i B2C-inställningar. Det är viktigt att du vidtar åtgärden innan du levererar dina appar i produktionen.


[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

Du kan också komma åt bladet genom att ange `Azure AD B2C` i **söka resurser** överst i portalen. Välj i resultatlistan **Azure AD B2C** att komma åt bladet B2C-inställningar.

## <a name="register-your-application"></a>Registrera ditt program

Om du vill skapa ett program som accepterar registrering och inloggning av konsumenter måste du först registrera programmet med en Azure Active Directory B2C-klient. Skaffa en egen klient genom att följa stegen i [Skapa en Azure AD B2C-klient](active-directory-b2c-get-started.md).

Program som har skapats i Azure-portalen måste hanteras från samma plats. Om du redigerar Azure AD B2C-program med hjälp av PowerShell eller någon annan portal, stöds de inte och fungerar inte med Azure AD B2C. Mer information finns i avsnittet [felaktiga appar](#faulted-apps). 

Informationen i den här artikeln hjälper dig att komma igång med våra exempel. Du lär dig mer om dessa exempel i efterföljande artiklar.

### <a name="navigate-to-b2c-settings"></a>Gå till B2C-inställningar

Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för B2C-klientorganisationen. 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

### <a name="choose-next-steps-based-on-your-application-type"></a>Välj nästa steg beroende på din programtyp

* [Registrera ett webbprogram](#register-a-web-app)
* [Registrera ett webb-API](#register-a-web-api)
* [Registrera ett mobilt eller inbyggt program](#register-a-mobile-or-native-app)
 
### <a name="register-a-web-app"></a>Registrera en webbapp

[!INCLUDE [active-directory-b2c-register-web-app](../../includes/active-directory-b2c-register-web-app.md)]

### <a name="create-a-web-app-client-secret"></a>Skapa en klienthemlighet för webbappar

Gör så här om webbprogrammet anropar ett webb-API som skyddas av Azure AD B2C:
   1. Skapa en programhemlighet genom att gå till bladet **Nycklar** och klicka på **Generera nyckel**. Anteckna **appnyckel**-värdet. Du använder värdet som programhemlighet i programkoden.
   2. Klicka på **API-åtkomst**, **Lägg till** och välj webb-API och områden (behörigheter).

> [!NOTE]
> En **programhemlighet** är en viktig autentiseringsuppgift och bör skyddas på lämpligt sätt.
> 

[Gå vidare till **nästa steg**](#next-steps)

### <a name="register-a-web-api"></a>Registrera ett webb-API

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

Klicka på **Publicerade områden** om du behöver lägga till fler områden. Som standard definieras området ”user_impersonation”. Området user_impersonation ger andra program möjlighet att komma åt det här API:et för den inloggade användarens räkning. Om du vill kan området user_impersonation tas bort.

[Gå vidare till **nästa steg**](#next-steps)

### <a name="register-a-mobile-or-native-app"></a>Registrera en mobil eller inbyggd app

[!INCLUDE [active-directory-b2c-register-mobile-native-app](../../includes/active-directory-b2c-register-mobile-native-app.md)]

[Gå vidare till **nästa steg**](#next-steps)

### <a name="choosing-a-web-app-or-api-reply-url"></a>Om du väljer en webbapp eller en api-svarswebbadress

Appar som har registrerats med Azure AD B2C är för närvarande begränsade till en begränsad uppsättning svars-URL-värden. Svars-URL för webbprogram och tjänster måste börja med schemat `https` och alla svars-URL-värden måste dela en enda DNS-domän. Exempelvis kan du registrera ett webbprogram som har en av dessa svars-URL: er:

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Registreringssystemet jämför hela DNS-namnet på den befintliga svars-URL:en med DNS-namnet på den svars-URL som du lägger till. Begäran om att lägga till DNS-namnet misslyckas om något av följande villkor föreligger:

* Hela DNS-namnet på den nya svars-URL:en motsvarar inte DNS-namnet på den befintliga svars-URL:en.
* Hela DNS-namnet på den nya svars-URL:en är inte en underdomän till den befintliga svars-URL:en.

Till exempel om appen har svars-URL:

`https://login.contoso.com`

Du kan lägga till data så här:

`https://login.contoso.com/new`

I det här fallet matchar DNS-namnet exakt. Du kan också göra detta:

`https://new.login.contoso.com`

I så fall måste du referera till DNS-underdomänen login.contoso.com. Om du vill ha en app som har login-east.contoso.com och login-west.contoso.com som svars-URL: er måste du lägga till dessa svars-URL: er i den här ordningen:

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Du kan lägga till två senare eftersom de är underdomäner i den första reply-URL:en, contoso.com.

### <a name="choosing-a-native-app-redirect-uri"></a>Om du väljer en omdirigerings-URI för en inbyggd app

Det finns två viktiga överväganden när du väljer en omdirigerings-URI för mobila/interna program:

* **Unik**: Schemat för omdirigerings-URI måste vara unikt för varje program. I exemplet (com.onmicrosoft.contoso.appname://redirect/path), är com.onmicrosoft.contoso.appname schemat. Vi rekommenderar att detta mönster följs. Om två program delar samma schema visas en dialogruta för att ”välja app”. Inloggningen misslyckas om användaren gör ett felaktigt val.
* **Fullständig**: Omdirigerings-URI måste ha ett schema och en sökväg. Sökvägen måste innehålla minst ett snedstreck efter domänen (till exempel fungerar //contoso/ medan //contoso misslyckas).

Se till att det inte finns några specialtecken som understreck i omdirigerings-uri.

### <a name="faulted-apps"></a>Felaktig appar

B2C program bör INTE redigeras:

* På andra programhanteringsportaler, som [Programregistreringsportalen](https://apps.dev.microsoft.com/).
* Med Graph API eller PowerShell

Om du redigerar Azure AD B2C-programmet enligt beskrivning och försöker att redigera det i Azure AD B2C-funktionerna på Azure Portal, blir det en felaktig app och programmet kommer inte längre att kunna användas med Azure AD B2C. Du måste ta bort programmet och skapa det igen.

Ta bort appen genom att gå till den [Appregistreringsportalen](https://apps.dev.microsoft.com/) och ta bort det appen. Du måste vara ägare till appen (och inte bara en administratör för klienten) för att appen ska vara synlig.

## <a name="next-steps"></a>Nästa steg

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Skapa en Azure AD B2C-klient
> * Länka din klient till din prenumeration
> * Registrera ditt program

> [!div class="nextstepaction"]
> [Aktivera ett webbprogram att autentisera med konton](active-directory-b2c-tutorials-web-app.md)