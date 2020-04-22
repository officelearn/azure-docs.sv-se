---
title: Begränsa Azure AD-appen till en uppsättning användare | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du begränsar åtkomsten till dina appar som är registrerade i Azure AD till en vald uppsättning användare.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8bdc7e6e3795719128a8ecfb1e8bc97c1a9a08c7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759023"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Så här begränsar du din Azure AD-app till en uppsättning användare i en Azure AD-klientorganisation

Program som registrerats i en Azure Active Directory-klientorganisation (Azure AD) är som standard tillgängliga för alla användare av klienten som autentiserar.

På samma sätt, i händelse av en app [med flera innehavare,](howto-convert-app-to-be-multi-tenant.md) kommer alla användare i Azure AD-klienten där den här appen har etablerats att kunna komma åt det här programmet när de har autentiserats i respektive klientorganisation.

Klientadministratörer och utvecklare har ofta krav där en app måste begränsas till en viss uppsättning användare. Utvecklare kan åstadkomma samma sak genom att använda populära auktoriseringsmönster som Rollbaserad åtkomstkontroll (RBAC), men den här metoden kräver en betydande mängd arbete på en del av utvecklaren.

Klientadministratörer och utvecklare kan begränsa en app till en viss uppsättning användare eller säkerhetsgrupper i klienten med hjälp av den här inbyggda funktionen i Azure AD.

## <a name="supported-app-configurations"></a>Appkonfigurationer som stöds

Alternativet att begränsa en app till en viss uppsättning användare eller säkerhetsgrupper i en klient fungerar med följande typer av program:

- Program som konfigurerats för federerad enkel inloggning med SAML-baserad autentisering
- Programproxyprogram som använder Azure AD-förautentisering
- Program som används direkt på Azure AD-programplattformen som använder OAuth 2.0/OpenID Connect-autentisering efter att en användare eller administratör har samtyckt till det programmet.

     > [!NOTE]
     > Den här funktionen är endast tillgänglig för webbapp/webb-API och företagsprogram. Appar som är registrerade som [inbyggda](quickstart-v1-integrate-apps-with-azure-ad.md) kan inte begränsas till en uppsättning användare eller säkerhetsgrupper i klienten.

## <a name="update-the-app-to-enable-user-assignment"></a>Uppdatera appen för att aktivera användartilldelning

Det finns två sätt att skapa ett program med aktiverad användartilldelning. En kräver den **globala administratörrollen,** den andra inte.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Företagsprogram (kräver rollen Global administratör)

1. Gå till [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör**.
1. Välj det inloggade kontot i det övre fältet. 
1. Under **Katalog**väljer du Den Azure AD-klient där appen ska registreras.
1. Välj **Azure Active Directory**i navigeringen till vänster . Om Azure Active Directory inte är tillgängligt i navigeringsfönstret gör du så här:

    1. Välj **Alla tjänster** högst upp på navigeringsmenyn för huvudmenvigering.
    1. Skriv i **Azure Active Directory** i sökrutan för filter och välj sedan Azure Active **Directory-objektet** från resultatet.

1. I fönstret **Azure Active Directory** väljer du Enterprise **Applications** på menyn Azure **Active Directory** till vänster.
1. Välj **Alla program om** du vill visa en lista över alla dina program.

     Om du inte ser det program du vill visa upp här använder du de olika filtren högst upp i listan **Alla program** för att begränsa listan eller rulla nedåt i listan för att hitta ditt program.

1. Välj det program som du vill tilldela en användare eller säkerhetsgrupp till i listan.
1. På programmets **översiktssida** väljer du **Egenskaper** på programmets navigeringsmeny till vänster.
1. Vill du söka efter inställningen **Yes** **Användartilldelning som krävs?** När det här alternativet är inställt på **Ja**måste användare i klienten först tilldelas det här programmet eller så kan de inte logga in på det här programmet.
1. Välj **Spara** om du vill spara den här konfigurationsändringen.

### <a name="app-registration"></a>Appregistrering

1. Gå till [**Azure-portalen**](https://portal.azure.com/).
1. Välj det inloggade kontot i det övre fältet. 
1. Under **Katalog**väljer du Den Azure AD-klient där appen ska registreras.
1. Välj **Azure Active Directory**i navigeringen till vänster .
1. I fönstret **Azure Active Directory** väljer du **Appregistreringar** på **menyn Azure Active Directory-navigering.**
1. Skapa eller välj den app som du vill hantera. Du måste vara **ägare** till den här appregistreringen.
1. På sidan **Översikt på** programmet följer du länken **Hanterat program i lokal katalog** under det väsentliga längst upp på sidan. Detta tar dig till den _hanterade Enterprise Application_ av din appregistrering.
1. Välj **Egenskaper**i navigeringsbladet till vänster .
1. Vill du söka efter inställningen **Yes** **Användartilldelning som krävs?** När det här alternativet är inställt på **Ja**måste användare i klienten först tilldelas det här programmet eller så kan de inte logga in på det här programmet.
1. Välj **Spara** om du vill spara den här konfigurationsändringen.

## <a name="assign-users-and-groups-to-the-app"></a>Tilldela användare och grupper till appen

När du har konfigurerat appen för att aktivera användartilldelning kan du gå vidare och tilldela användare och grupper till appen.

1. Välj fönstret **Användare och grupper** i programmets navigeringsmeny till vänster.
1. Högst upp i listan **Användare och grupper** väljer du knappen Lägg till **användare** för att öppna fönstret Lägg **till tilldelning.**
1. Välj **användareväljaren** i fönstret **Lägg till tilldelning.** 

     En lista över användare och säkerhetsgrupper visas tillsammans med en textruta för att söka efter och hitta en viss användare eller grupp. På den här skärmen kan du välja flera användare och grupper på en gång.

1. När du är klar med att välja användare och grupper trycker du på **välj-knappen** längst ned för att gå till nästa del.
1. (Valfritt) Om du har definierat approller i ditt program kan du använda alternativet **Välj roll** för att tilldela de valda användarna och grupperna till en av programmets roller. 
1. Tryck på knappen **Tilldela** längst ned för att slutföra tilldelningarna av användare och grupper till appen. 
1. Bekräfta att de användare och grupper som du har lagt till visas i listan Uppdaterade **användare och grupper.**

## <a name="more-information"></a>Mer information

- [Så här lägger du till approller i ditt program](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
- [Lägga till auktorisering med approller & roller anspråk på en ASP.NET Core webbapp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Använda säkerhetsgrupper och programroller i dina appar (video)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, nu med gruppanspråk och programroller](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory-appmanifest](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)