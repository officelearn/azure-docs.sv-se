---
title: Lägg till approller och hämta dem från en token | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du lägger till approller i ett program som är `roles` registrerat i Azure Active Directory, tilldelar användare och grupper till dessa roller och tar emot dem i anspråket i token.
services: active-directory
author: kkrishna
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 144fad249011d547ac6a8cf2d404cb3f8fe74f96
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884264"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Så här lägger du till approller i ditt program och får dem i token

Rollbaserad åtkomstkontroll (RBAC) är en populär mekanism för att genomdriva auktorisering i program. När du använder RBAC ger en administratör behörighet till roller och inte till enskilda användare eller grupper. Administratören kan sedan tilldela roller till olika användare och grupper för att styra vem som har åtkomst till vilket innehåll och vilka funktioner.

Med hjälp av RBAC med programroller och rollanspråk kan utvecklare på ett säkert sätt genomdriva auktorisering i sina appar med liten ansträngning från deras sida.

En annan metod är att använda Azure AD-grupper och gruppanspråk, som visas i [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). Azure AD-grupper och programroller utesluter inte på något sätt varandra. de kan användas tillsammans för att ge ännu finare kornig åtkomstkontroll.

## <a name="declare-roles-for-an-application"></a>Deklarera roller för ett program

Dessa programroller definieras i [Azure-portalen](https://portal.azure.com) i programmets registreringsmanifest.  När en användare loggar in på programmet `roles` avger Azure AD ett anspråk för varje roll som användaren har beviljats individuellt till användaren och från deras gruppmedlemskap.  Tilldelning av användare och grupper till roller kan göras via portalens användargränssnitt eller programmässigt med Hjälp av [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Deklarera approller med Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **Katalog + Prenumeration** i portalverktygsfältet.
1. I listan **Favoriter** eller **Alla kataloger** väljer du den Active Directory-klient där du vill registrera ditt program.
1. Sök efter och välj **Azure Active Directory**i Azure-portalen .
1. I fönstret **Azure Active Directory** väljer du **Appregistreringar** för att visa en lista över alla dina program.
1. Välj det program som du vill definiera approller i. Välj sedan **Manifest**.
1. Redigera appmanifestet `appRoles` genom att hitta inställningen och lägga till alla programroller.

     > [!NOTE]
     > Varje approlldefinition i det här manifestet måste ha ett annat `id` giltigt GUID inom ramen för manifestet för egenskapen.
     >
     > Egenskapen `value` för varje approlldefinition bör exakt matcha de strängar som används i koden i programmet. Egenskapen `value` får inte innehålla blanksteg. Om den gör det visas ett felmeddelande när du sparar manifestet.

1. Spara manifestet.

### <a name="examples"></a>Exempel

I följande exempel `appRoles` visas det `users`som du kan tilldela .

> [!NOTE]
>Måste `id` vara en unik GUID.

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
>Den `displayName` kan inte innehålla blanksteg.

Du kan definiera approller som mål, `users` `applications`eller båda. När approllerna är tillgängliga `applications`visas de som programbehörigheter i bladet Obligatoriska **behörigheter.** I följande exempel visas en `Application`approll som är inriktad på en .

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

Antalet definierade roller påverkar de gränser som programmanifestet har. De har diskuterats i detalj på [manifestet gränser](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits) sidan.

### <a name="assign-users-and-groups-to-roles"></a>Tilldela användare och grupper till roller

När du har lagt till approller i ditt program kan du tilldela användare och grupper till dessa roller.

1. I fönstret **Azure Active Directory** väljer du **Enterprise-program** på **menyn Azure Active Directory** till vänster.
1. Välj **Alla program** om du vill visa en lista över alla dina program.

     Om du inte ser det program du vill visa upp här använder du de olika filtren högst upp i listan **Alla program** för att begränsa listan eller rulla nedåt i listan för att hitta ditt program.

1. Välj det program där du vill tilldela användare eller säkerhetsgrupp till roller.
1. Välj fönstret **Användare och grupper** i programmets navigeringsmeny till vänster.
1. Högst upp i listan **Användare och grupper** väljer du knappen Lägg till **användare** för att öppna fönstret Lägg **till tilldelning.**
1. Välj **väljaren Användare och grupper** i fönstret **Lägg till tilldelning.**

     En lista över användare och säkerhetsgrupper visas tillsammans med en textruta för att söka efter och hitta en viss användare eller grupp. På den här skärmen kan du välja flera användare och grupper på en gång.

1. När du är klar med att välja användare och grupper trycker du på **välj-knappen** längst ned för att gå till nästa del.
1. Välj **selector för Välj roll** i fönstret **Lägg till tilldelning.** Alla roller som deklarerats tidigare i appmanifestet visas.
1. Välj en roll och tryck på **knappen Välj.**
1. Tryck på knappen **Tilldela** längst ned för att slutföra tilldelningarna av användare och grupper till appen.
1. Bekräfta att de användare och grupper som du har lagt till visas i listan Uppdaterade **användare och grupper.**

## <a name="more-information"></a>Mer information

- [Lägga till auktorisering med approller & roller anspråk på en ASP.NET Core webbapp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Använda säkerhetsgrupper och programroller i dina appar (video)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, nu med gruppanspråk och programroller](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory-appmanifest](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [AAD Access-token](access-tokens.md)
- [Aad`id_tokens`](id-tokens.md)
