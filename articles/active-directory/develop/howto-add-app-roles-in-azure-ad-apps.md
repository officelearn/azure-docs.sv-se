---
title: Lägga till roller i Azure Active Directory-registrerade programmet och tar emot dem i token
description: Lär dig hur du lägger till roller i ett program i Azure Active Directory, tilldela användare och grupper till de här rollerna och tar emot dem i den `roles` anspråk i token.
services: active-directory
documentationcenter: ''
author: kkrishna
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
ms.openlocfilehash: df0d0b02efe7e99253b64ba02a5d9e77bb968993
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138362"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Anvisningar: Lägg till roller i ditt program och tar emot dem i token

Rollbaserad åtkomstkontroll (RBAC) är en populär mekanism auktorisering i program. När du använder RBAC kan beviljar en administratör behörigheter till roller och inte till enskilda användare eller grupper. Administratören kan sedan tilldela roller till olika användare och grupper för att kontrollera vem som har åtkomst till vilka innehåll och funktioner.

Med RBAC med programroller och Rollanspråk, kan utvecklare på ett säkert sätt använda auktorisering i sina appar utan besvär själva.

En annan metod är att använda Azure AD-grupper och gruppanspråk, enligt [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). Azure AD-grupper och roller för programmet är inte menat ömsesidigt uteslutande; de kan användas tillsammans för att ge ännu finare detaljerade åtkomstkontroll.

## <a name="declare-roles-for-an-application"></a>Deklarera roller för ett program

Dessa programroller definieras i den [Azure-portalen](https://portal.azure.com) i applikationsmanifestet registrering.  När en användare loggar in i programmet, Azure AD skickar en `roles` anspråk för varje roll som användaren har beviljats individuellt till användaren och från deras gruppmedlemskap.  Tilldelningen av användare och grupper till roller kan vara klar via portalens Användargränssnittet eller programmässigt med [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Deklarera roller med hjälp av Azure portal

1. Logga in på [Azure Portal](https://portal.azure.com).
1. I det översta fältet väljer du ditt konto, och sedan **växla katalog**.
1. När den **katalog + prenumeration** öppnas, Välj den Active Directory-klient som du vill registrera ditt program från den **Favoriter** eller **alla kataloger**lista.
1. Välj **alla tjänster** i det vänstra navigeringsfältet och välj **Azure Active Directory**.
1. I den **Azure Active Directory** väljer **appregistreringar** att visa en lista över alla dina program.

     Om du inte ser programmet som du vill att visas här använder du olika filtren högst upp på den **appregistreringar** för att begränsa listan eller bläddra nedåt i listan för att hitta ditt program.

1. Välj det program som du vill definiera roller i.
1. I bladet för ditt program, väljer **Manifest**.
1. Redigera appmanifestet genom att leta upp den `appRoles` inställning och lägga till alla programroller.

     > [!NOTE]
     > Varje approlldefinitionen i manifestet måste ha ett annat giltigt GUID för den `id` egenskapen. 
     > 
     > Den `value` egenskapen för varje approlldefinitionen bör exakt matcha de strängar som används i koden i programmet. Den `value` egenskapen får inte innehålla blanksteg. I annat fall får ett fel när du sparar manifestet.
     
1. Spara manifestet.

### <a name="examples"></a>Exempel

I följande exempel visas den `appRoles` som du kan tilldela `users`.

> [!NOTE]
>  Den `id` måste vara ett unikt GUID.

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

Du kan definiera roller till målet `users`, `applications`, eller båda. När det är tillgängligt för `applications`, visas de roller som programbehörigheter i den **nödvändiga behörigheter** bladet. I följande exempel visas en app-roll som är riktade mot en `Application`.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "Consumer Apps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

### <a name="assign-users-and-groups-to-roles"></a>Tilldela roller användare och grupper

När du har lagt till roller i ditt program, kan du tilldela användare och grupper till dessa roller.

1. I den **Azure Active Directory** väljer **företagsprogram** från den **Azure Active Directory** vänstra navigeringsmenyn.
1. Välj **alla program** att visa en lista över alla dina program.

     Om du inte ser programmet som du vill att visas här använder du olika filtren högst upp på den **alla program** för att begränsa listan eller bläddra nedåt i listan för att hitta ditt program.

1. Välj det program som du vill tilldela roller användare eller säkerhetsgrupp.
1. Välj den **användare och grupper** fönstret på programmets vänstra navigeringsmenyn.
1. Överst på den **användare och grupper** väljer den **Lägg till användare** knappen för att öppna den **Lägg till tilldelning** fönstret.
1. Välj den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

     En lista över användare och säkerhetsgrupper kommer att visas tillsammans med en textruta för att söka efter och hitta en viss användare eller grupp. Den här skärmen kan du välja flera användare och grupper i en går.

1. När du har valt de användare och grupper, tryck på den **Välj** knappen längst ned för att flytta till nästa del.
1. Välj den **Välj roll** selector från den **Lägg till tilldelning** fönstret. Alla roller som deklarerats tidigare i appmanifestet visas.
1. Välj en roll och tryck på den **Välj** knappen.
1. Tryck på den **tilldela** knappen längst ned att slutföra tilldelningar av användare och grupper till appen.
1. Bekräfta att användare och grupper som du har lagt till visas i den uppdaterade **användare och grupper** lista.

## <a name="more-information"></a>Mer information

- [Auktorisering i en webbapp med Azure AD-programroller &amp; rollanspråk (exempel)](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapp-roleclaims/)
- [Använda säkerhetsgrupper och programroller i dina appar (Video)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, nu med gruppanspråk och programroller](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles)
- [Azure Active Directory-appmanifestet](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [AAD-åtkomsttoken](access-tokens.md)
- [AAD `id_tokens`](id-tokens.md)
