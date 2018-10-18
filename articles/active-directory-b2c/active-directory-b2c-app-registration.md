---
title: Programregistrering i Azure Active Directory B2C | Microsoft Docs
description: Registrera ditt program med Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 6/13/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4f8a5b1ceda77ef254ad0c2afb7d2316581d778e
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376404"
---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: Registrera ditt program

Med hjälp av den här snabbstarten registrerar du ett program i en Microsoft Azure Active Directory (Azure AD) B2C-klientorganisation på några få minuter. När du är färdig är programmet klart att användas i Azure AD B2C-klientorganisationen.

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa ett program som accepterar registrering och inloggning av konsumenter måste du först registrera programmet med en Azure Active Directory B2C-klient. Skaffa en egen klient genom att följa stegen i [Skapa en Azure AD B2C-klient](active-directory-b2c-get-started.md).

Program som har skapats i Azure-portalen måste hanteras från samma plats. Om du redigerar Azure AD B2C-program med hjälp av PowerShell eller någon annan portal, stöds de inte och fungerar inte med Azure AD B2C. Mer information finns i avsnittet [felaktiga appar](#faulted-apps). 

Informationen i den här artikeln hjälper dig att komma igång med våra exempel. Du lär dig mer om dessa exempel i efterföljande artiklar.

## <a name="navigate-to-b2c-settings"></a>Gå till B2C-inställningar

Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för B2C-klientorganisationen. 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

## <a name="choose-next-steps-based-on-your-application-type"></a>Välj nästa steg beroende på din programtyp

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

## <a name="limitations"></a>Begränsningar

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

Nu när du har registrerat ett program med Azure AD B2C kan du göra [en av snabbstartsguiderna](active-directory-b2c-overview.md) för att komma igång.

> [!div class="nextstepaction"]
> [Skapa en ASP.NET-webbapp med registrering, inloggning och lösenordsåterställning](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
