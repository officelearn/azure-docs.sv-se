---
title: Lägg till app-roller i din Azure Active Directory-registrerade app och ta emot dem i token
titleSuffix: Microsoft identity platform
description: Lär dig hur du lägger till app-roller i ett program som är registrerat i Azure Active Directory, tilldela användare och grupper till dessa roller och ta emot dem i `roles`-anspråk i token.
services: active-directory
documentationcenter: ''
author: kkrishna
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
ms.openlocfilehash: 84275be5c85570a44d968dca8887b448fce09979
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546845"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Gör så här: Lägg till app-roller i ditt program och ta emot dem i token

Rollbaserad åtkomst kontroll (RBAC) är en populär mekanism för att genomdriva auktorisering i program. När du använder RBAC beviljar en administratör behörigheter till roller och inte till enskilda användare eller grupper. Administratören kan sedan tilldela roller till olika användare och grupper för att kontrol lera vem som har åtkomst till det innehåll och de funktioner som finns.

Med hjälp av RBAC med program roller och roll anspråk kan utvecklare på ett säkert sätt framtvinga auktorisering i sina appar med lite ansträngning på sin sida.

En annan metod är att använda Azure AD-grupper och grupp anspråk, som du ser i [webapp-GroupClaims-dotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). Azure AD-grupper och program roller är inte heller ömsesidigt uteslutande; de kan användas i tandem för att ge ännu bättre kornig åtkomst kontroll.

## <a name="declare-roles-for-an-application"></a>Deklarera roller för ett program

Dessa program roller definieras i [Azure Portal](https://portal.azure.com) i programmets registrerings manifest.  När en användare loggar in i programmet, genererar Azure AD ett `roles`-anspråk för varje roll som användaren har beviljats individuellt för användaren och från grupp medlemskapet.  Tilldelning av användare och grupper till roller kan göras via portalens användar gränssnitt eller program mässigt med hjälp av [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Deklarera rollbaserade appar med hjälp av Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I det översta fältet väljer du ditt konto och byter sedan **katalog**.
1. När fönstret **katalog + prenumeration** öppnas väljer du den Active Directory klient organisation där du vill registrera ditt program, från listan **Favoriter** eller **alla kataloger** .
1. Välj **alla tjänster** i det vänstra navigerings fältet och välj **Azure Active Directory**.
1. I fönstret **Azure Active Directory** väljer du **Appregistreringar** för att visa en lista över alla dina program.
1. Välj det program som du vill definiera app-roller i. Välj sedan **manifest**.
1. Redigera appens manifest genom att leta upp `appRoles`s inställningen och lägga till alla dina program roller.

     > [!NOTE]
     > Varje app Role-definition i det här manifestet måste ha ett annat giltigt GUID för egenskapen `id`. 
     > 
     > Egenskapen `value` för varje app Role-definition bör exakt matcha de strängar som används i koden i programmet. Egenskapen `value` får inte innehålla blank steg. Om det gör det får du ett fel meddelande när du sparar manifestet.
     
1. Spara manifestet.

### <a name="examples"></a>Exempel

I följande exempel visas de `appRoles` som du kan tilldela till `users`.

> [!NOTE]
>`id` måste vara ett unikt GUID.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-98f8-45fd-aa4a-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

> [!NOTE]
>`displayName` får inte innehålla blank steg.

Du kan definiera app-roller för att ange mål `users`, `applications`eller båda. När det är tillgängligt för att `applications`visas app-roller som program behörigheter på bladet **nödvändiga behörigheter** . I följande exempel visas en app-roll som riktar sig mot en `Application`.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

Antalet definierade roller påverkar de gränser som applikations manifestet har. De har diskuterats i detalj på sidan [manifest gränser](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits) .

### <a name="assign-users-and-groups-to-roles"></a>Tilldela roller till användare och grupper

När du har lagt till app-roller i ditt program kan du tilldela de här rollerna användare och grupper.

1. I fönstret **Azure Active Directory** väljer du **företags program** från **Azure Active Directory** vänster navigerings meny.
1. Välj **alla program** om du vill visa en lista över alla dina program.

     Om du inte ser det program som du vill visa här, använder du de olika filtren överst i listan **alla program** för att begränsa listan eller bläddra nedåt i listan för att hitta programmet.

1. Välj det program som du vill tilldela användare eller säkerhets grupp till roller.
1. Välj fönstret **användare och grupper** i programmets vänstra navigerings meny.
1. Klicka på knappen **Lägg till användare** längst upp i listan **användare och grupper** för att öppna fönstret **Lägg till tilldelning** .
1. Välj Välj **användare och grupper** i fönstret **Lägg till tilldelning** .

     En lista över användare och säkerhets grupper visas tillsammans med en text ruta där du kan söka efter och hitta en viss användare eller grupp. På den här skärmen kan du välja flera användare och grupper i en enda go.

1. När du är färdig med att välja användare och grupper trycker du på knappen **Välj** längst ned för att gå vidare till nästa del.
1. Välj **Välj roll** väljare i fönstret **Lägg till tilldelning** . Alla roller som har deklarerats tidigare i app-manifestet visas.
1. Välj en roll och klicka på knappen **Välj** .
1. Tryck på knappen **tilldela** längst ned för att slutföra tilldelningen av användare och grupper till appen.
1. Bekräfta att de användare och grupper som du har lagt till visas i listan uppdaterade **användare och grupper** .

## <a name="more-information"></a>Mer information

- [Auktorisering i en webbapp med hjälp av Azure AD-programroller &amp; roll anspråk (exempel)](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims)
- [Använda säkerhets grupper och program roller i dina appar (video)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, nu med grupp anspråk och program roller](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles)
- [Azure Active Directory app-manifest](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [AAD-åtkomsttoken](access-tokens.md)
- [AAD-`id_tokens`](id-tokens.md)
