---
title: Hur du begränsar din Azure Active Directory-registrerad app till en uppsättning användare
description: Lär dig hur du begränsar åtkomsten till dina appar som är registrerade i Azure AD för en vald användare.
services: active-directory
documentationcenter: ''
author: kalyankrishna1
manager: mtillman
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
ms.openlocfilehash: 17b76a25b3c5c2c3ce4dc4217389706a4b24d837
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60410067"
---
# <a name="how-to-restrict-your-app-to-a-set-of-users"></a>Anvisningar: Begränsa din app till en uppsättning användare

Program som är registrerade i en Azure Active Directory (Azure AD)-klient är som standard för alla användare på klienten som autentiseras.

På samma sätt i händelse av en [flera innehavare](howto-convert-app-to-be-multi-tenant.md) app, alla användare i Azure AD-klient där den här appen har etablerats kommer att kunna komma åt det här programmet när de autentisera i sina respektive klient.

Innehavaradministratörer och utvecklare har ofta krav där en app måste vara begränsad till en viss uppsättning användare. Utvecklare kan göra samma med hjälp av populära auktorisering mönster som rollbaserad åtkomstkontroll (RBAC), men den här metoden kräver en betydande mängd arbete på en del av utvecklaren.

Azure AD kan klienten administratörer och utvecklare att begränsa en app till en specifik uppsättning användare eller säkerhetsgrupper i klienten.

## <a name="supported-app-configurations"></a>Stöds appkonfigurationer

Alternativet för att begränsa en app till en specifik uppsättning användare eller säkerhetsgrupper i en klient fungerar med följande typer av program:

- Program som har konfigurerats för federerad enkel inloggning med SAML-baserad autentisering
- Application proxy-program som använder Azure AD-förautentisering
- Program som skapats direkt på Azure AD-plattformen för program som använder OAuth 2.0/OpenID Connect-autentisering när en användare eller administratör har godkänt att programmet.

     > [!NOTE]
     > Den här funktionen är tillgänglig för webb-/ ett webb-API och enterprise endast. Appar som är registrerade som [interna](quickstart-v1-integrate-apps-with-azure-ad.md) kan inte begränsas till en uppsättning användare eller säkerhetsgrupper i klienten.

## <a name="update-the-app-to-enable-user-assignment"></a>Uppdatera appen om du vill aktivera Användartilldelning

1. Gå till den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**
1. Välj det inloggade kontot i det översta fältet. 
1. Under **Directory**, Välj den Azure AD-klient där appen ska registreras.
1. I navigeringsfönstret till vänster, Välj **Azure Active Directory**. Om Azure Active Directory inte är tillgängligt i navigeringsfönstret, följer du dessa steg:

    1. Välj **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.
    1. Skriv i **Azure Active Directory** i filtret sökrutan och välj sedan den **Azure Active Directory** objekt från resultatet.

1. I den **Azure Active Directory** väljer **företagsprogram** från den **Azure Active Directory** vänstra navigeringsmenyn.
1. Välj **alla program** att visa en lista över alla dina program.

     Om du inte ser programmet som du vill att visas här använder du olika filtren högst upp på den **alla program** för att begränsa listan eller bläddra nedåt i listan för att hitta ditt program.

1. Välj det program som du vill tilldela en användare eller säkerhetsgrupp från listan.
1. I programmets **översikt** väljer **egenskaper** från programmets vänstra navigeringsmenyn.
1. Leta reda på inställningen **Användartilldelning krävs?** och ge den värdet **Ja**. Om det här alternativet är inställt på **Ja**, och användarna måste först tilldelas det här programmet innan du kan komma åt den.
1. Välj **spara** att spara konfigurationsändringen.

## <a name="assign-users-and-groups-to-the-app"></a>Tilldela användare och grupper till appen

När du har konfigurerat din app om du vill aktivera Användartilldelning kan du gå vidare och tilldela användare och grupper till appen.

1. Välj den **användare och grupper** fönstret på programmets vänstra navigeringsmenyn.
1. Överst på den **användare och grupper** väljer den **Lägg till användare** knappen för att öppna den **Lägg till tilldelning** fönstret.
1. Välj den **användare** selector från den **Lägg till tilldelning** fönstret. 

     En lista över användare och säkerhetsgrupper kommer att visas tillsammans med en textruta för att söka efter och hitta en viss användare eller grupp. Den här skärmen kan du välja flera användare och grupper i en går.

1. När du har valt de användare och grupper, tryck på den **Välj** knappen längst ned för att flytta till nästa del.
1. Tryck på den **tilldela** knappen längst ned att slutföra tilldelningar av användare och grupper till appen. 
1. Bekräfta att användare och grupper som du har lagt till visas i den uppdaterade **användare och grupper** lista.

