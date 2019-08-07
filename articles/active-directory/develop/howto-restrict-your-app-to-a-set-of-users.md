---
title: Begränsa din Azure Active Directory-registrerade app till en uppsättning användare
description: Lär dig hur du begränsar åtkomsten till dina appar som är registrerade i Azure AD till en vald uppsättning användare.
services: active-directory
documentationcenter: ''
author: kalyankrishna1
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a51c49633e68fdc5f9afd4bf0205adaa625940ff
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812969"
---
# <a name="how-to-restrict-your-app-to-a-set-of-users"></a>Anvisningar: Begränsa din app till en uppsättning användare

Program som är registrerade i en Azure Active Directory-klient (Azure AD) är som standard tillgängliga för alla användare av klienten som autentiseras korrekt.

På samma sätt kommer alla användare i Azure AD-klienten där den här appen är etablerad att kunna komma åt det här programmet när de har autentiserat sig i deras respektive klient organisationer, om det finns en app [med flera klienter](howto-convert-app-to-be-multi-tenant.md) .

Klient organisations administratörer och utvecklare har ofta krav på när en app måste begränsas till en viss uppsättning användare. Utvecklare kan utföra samma med hjälp av populära behörighets mönster som rollbaserad Access Control (RBAC), men den här metoden kräver en stor mängd arbete på en del av utvecklaren.

Med Azure AD kan klient organisations administratörer och utvecklare begränsa en app till en särskild uppsättning användare eller säkerhets grupper i klienten.

## <a name="supported-app-configurations"></a>App-konfigurationer som stöds

Alternativet att begränsa en app till en speciell uppsättning användare eller säkerhets grupper i en klient organisation fungerar med följande typer av program:

- Program som kon figurer ATS för federerad enkel inloggning med SAML-baserad autentisering
- Application Proxy-program som använder Azure AD pre-Authentication
- Program som byggts direkt på den Azure AD-programplattform som använder OAuth 2.0/OpenID Connect-autentisering när en användare eller administratör har samtyckt till det programmet.

     > [!NOTE]
     > Den här funktionen är endast tillgänglig för webbapp/webb-API och företags program. Appar som är registrerade som [interna](quickstart-v1-integrate-apps-with-azure-ad.md) kan inte begränsas till en uppsättning användare eller säkerhets grupper i klienten.

## <a name="update-the-app-to-enable-user-assignment"></a>Uppdatera appen för att aktivera användar tilldelning

Det finns två sätt att skapa ett program med aktive rad användar tilldelning. En kräver rollen **Global administratör** , den andra inte.

### <a name="enterprise-applications-requires-the-global-adminstrator-role"></a>Företags program (kräver rollen global administratör)

1. Gå till [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör**.
1. I det översta fältet väljer du det inloggade kontot. 
1. Under **katalog**väljer du den Azure AD-klient där appen ska registreras.
1. I navigeringen till vänster väljer du **Azure Active Directory**. Följ dessa steg om Azure Active Directory inte är tillgänglig i navigerings fönstret:

    1. Välj **alla tjänster** överst i den huvudsakliga navigerings menyn i den vänstra menyn.
    1. Skriv **Azure Active Directory** i rutan filtrera sökning och välj sedan **Azure Active Directory** objektet från resultatet.

1. I fönstret **Azure Active Directory** väljer du **företags program** från **Azure Active Directory** vänster navigerings meny.
1. Välj **alla program** om du vill visa en lista över alla dina program.

     Om du inte ser det program som du vill visa här, använder du de olika filtren överst i listan **alla program** för att begränsa listan eller bläddra nedåt i listan för att hitta programmet.

1. Välj det program som du vill tilldela en användare eller säkerhets grupp till i listan.
1. På sidan **Översikt** för program väljer du **Egenskaper** från programmets vänstra navigerings meny.
1. Leta upp inställningen **användar tilldelning krävs?** och ange den till **Ja**. När det här alternativet är inställt på **Ja**måste användarna först tilldelas till det här programmet innan de kan komma åt det.
1. Välj **Spara** för att spara den här konfigurations ändringen.

### <a name="app-registration"></a>Appregistrering

1. Gå till [**Azure Portal**](https://portal.azure.com/).
1. I det översta fältet väljer du det inloggade kontot. 
1. Under **katalog**väljer du den Azure AD-klient där appen ska registreras.
1. I navigeringen till vänster väljer du **Azure Active Directory**.
1. I fönstret **Azure Active Directory** väljer du **app** -registreringar från **Azure Active Directory** vänster navigerings meny.
1. Skapa eller Välj den app som du vill hantera. Du måste vara **ägare** till den här appens registrering.
1. På sidan **Översikt** för program följer du länken **hanterat program i lokal katalog** under Essentials överst på sidan. Detta tar dig till det _hanterade företags programmet_ för din app-registrering.
1. Från navigerings bladet till vänster väljer du **Egenskaper**.
1. Leta upp inställningen **användar tilldelning krävs?** och ange den till **Ja**. När det här alternativet är inställt på **Ja**måste användarna först tilldelas till det här programmet innan de kan komma åt det.
1. Välj **Spara** för att spara den här konfigurations ändringen.

## <a name="assign-users-and-groups-to-the-app"></a>Tilldela användare och grupper till appen

När du har konfigurerat din app för att aktivera användar tilldelning kan du gå vidare och tilldela användare och grupper till appen.

1. Välj fönstret **användare och grupper** i programmets vänstra navigerings meny.
1. Klicka på knappen **Lägg till användare** längst upp i listan **användare och grupper** för att öppna fönstret **Lägg till tilldelning** .
1. Välj **användarens** väljare i fönstret **Lägg till tilldelning** . 

     En lista över användare och säkerhets grupper visas tillsammans med en text ruta där du kan söka efter och hitta en viss användare eller grupp. På den här skärmen kan du välja flera användare och grupper i en enda go.

1. När du är färdig med att välja användare och grupper trycker du på knappen **Välj** längst ned för att gå vidare till nästa del.
1. Tryck på knappen **tilldela** längst ned för att slutföra tilldelningen av användare och grupper till appen. 
1. Bekräfta att de användare och grupper som du har lagt till visas i listan uppdaterade **användare och grupper** .

