---
title: Lägg till app-roller och hämta dem från en token | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du lägger till app-roller i ett program som är registrerat i Azure Active Directory, tilldela användare och grupper till dessa roller och ta emot dem i `roles` anspråket i token.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 07/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: be5cb1c1e6ff428b3c4d4305c915e07d3880839c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91258395"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Gör så här: Lägg till app-roller i ditt program och ta emot dem i token

Rollbaserad åtkomst kontroll (RBAC) är en populär mekanism för att genomdriva auktorisering i program. När du använder RBAC beviljar en administratör behörigheter till roller och inte till enskilda användare eller grupper. Administratören kan sedan tilldela roller till olika användare och grupper för att kontrol lera vem som har åtkomst till det innehåll och de funktioner som finns.

Med hjälp av RBAC med program roller och roll anspråk kan utvecklare på ett säkert sätt framtvinga auktorisering i sina appar med lite ansträngning på sin sida.

En annan metod är att använda Azure AD-grupper och grupp anspråk, som du ser i [webapp-GroupClaims-dotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). Azure AD-grupper och program roller är inte heller ömsesidigt uteslutande; de kan användas i tandem för att ge ännu bättre kornig åtkomst kontroll.

## <a name="declare-roles-for-an-application"></a>Deklarera roller för ett program

Dessa program roller definieras i [Azure Portal](https://portal.azure.com) i programmets registrerings manifest.  När en användare loggar in i programmet, genererar Azure AD ett `roles` anspråk för varje roll som användaren har beviljats individuellt för användaren och från grupp medlemskapet.  Tilldelning av användare och grupper till roller kan göras via portalens användar gränssnitt eller program mässigt med hjälp av [Microsoft Graph](/graph/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Deklarera rollbaserade appar med hjälp av Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält.
1. I listan **Favoriter** eller **alla kataloger** väljer du den Active Directory klient organisation där du vill registrera programmet.
1. I Azure Portal söker du efter och väljer **Azure Active Directory**.
1. I fönstret  **Azure Active Directory** väljer du **Appregistreringar** för att visa en lista över alla dina program.
1. Välj det program som du vill definiera app-roller i. Välj sedan **manifest**.
1. Redigera appens manifest genom att hitta `appRoles` inställningen och lägga till alla dina program roller.

     > [!NOTE]
     > Varje app Role-definition i det här manifestet måste ha ett annat giltigt GUID inom kontexten för `id` egenskapens manifest.
     >
     > `value`Egenskapen för varje app Role-definition ska exakt matcha de strängar som används i koden i programmet. `value`Egenskapen får inte innehålla blank steg. Om det gör det får du ett fel meddelande när du sparar manifestet.

1. Spara manifestet.

### <a name="examples"></a>Exempel

I följande exempel visas den `appRoles` som du kan tilldela till `users` .

> [!NOTE]
>`id`Måste vara ett unikt GUID.

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
>`displayName`Får innehålla blank steg.

Du kan definiera app-roller som mål `users` , `applications` eller båda. När det är tillgängligt för `applications` visas app-roller som program behörigheter under **hantera** avsnitt > **API-behörigheter > lägg till en behörighet > mina API: er > Välj en API > program behörigheter**. I följande exempel visas en app-roll som är riktad mot en `Application` .

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

Antalet definierade roller påverkar de gränser som applikations manifestet har. De har diskuterats i detalj på sidan [manifest gränser](./reference-app-manifest.md#manifest-limits) .

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

### <a name="receive-roles-in-tokens"></a>Ta emot roller i token

När de användare som är tilldelade till de olika app-rollerna loggar in i programmet kommer deras token att ha sina tilldelade roller i `roles` anspråket.

## <a name="next-steps"></a>Nästa steg

- [Lägg till auktorisering med hjälp av app-roller & roller anspråk till en ASP.NET Core webbapp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Implementera auktorisering i dina program med Microsoft Identity Platform (video)](https://www.youtube.com/watch?v=LRoc-na27l0)
- [Azure Active Directory, nu med grupp anspråk och program roller](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory-appmanifest](./reference-app-manifest.md)
- [Azure AD-åtkomsttoken](access-tokens.md)
- [Azure AD `id_tokens`](id-tokens.md)
