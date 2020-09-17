---
title: Lägg till app-roller och hämta dem från en token | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du lägger till app-roller till ett program som är registrerat i Azure Active Directory, tilldelar användare och grupper till dessa roller och tar emot dem i "roles"-anspråk i token.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 02fe3c1ebc893dea259abcda3ea1d13ab96d68d5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706021"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Gör så här: Lägg till app-roller i ditt program och ta emot dem i token

Rollbaserad åtkomst kontroll (RBAC) är en populär mekanism för att genomdriva auktorisering i program. När du använder RBAC beviljar en administratör behörigheter till roller och inte till enskilda användare eller grupper. Administratören kan sedan tilldela roller till olika användare och grupper för att kontrol lera vem som har åtkomst till det innehåll och de funktioner som finns.

Med hjälp av RBAC med program roller och roll anspråk kan utvecklare på ett säkert sätt framtvinga auktorisering i sina appar med mindre ansträngning för deras del.

En annan metod är att använda Azure AD-grupper och grupp anspråk, som du ser i [GroupClaims-koden webapp--dotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet) på GitHub. Azure AD-grupper och program roller är inte ömsesidigt uteslutande. de kan användas i tandem för att ge ännu bättre kornig åtkomst kontroll.

## <a name="declare-roles-for-an-application"></a>Deklarera roller för ett program

Program roller definieras i [Azure Portal](https://portal.azure.com).  När en användare loggar in i programmet, genererar Azure AD ett `roles` anspråk för varje roll som användaren har beviljats individuellt för användaren och från grupp medlemskapet.  Tilldelning av användare och grupper till roller kan göras via portalens användar gränssnitt eller program mässigt med hjälp av [Microsoft Graph](/graph/azuread-identity-access-management-concept-overview).

Så här skapar du en app-roll:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** i den översta menyn och välj sedan den Azure Active Directory klient som innehåller den app-registrering som du vill lägga till en app-roll för.
1. Sök efter och välj **Azure Active Directory**.
1. Under **Hantera**väljer du **Appregistreringar**och väljer sedan det program som du vill definiera app-roller i.
1. Välj **app-roller | Förhandsgranska**och välj sedan **skapa app-roll**.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="En app-registreringens fönster för app-roller i Azure Portal":::
1. I fönstret **skapa app-roll** anger du inställningarna för rollen. I tabellen som följer bilden beskrivs varje inställning och deras parametrar.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="En app-registrerings app-roller skapa kontext fönster i Azure Portal":::

    | Fält | Beskrivning | Exempel |
    |-------|-------------|---------|
    | **Visningsnamn** | Visnings namn för den app-roll som visas i upplevelserna för administratörs medgivande och app-tilldelning. Det här värdet kan innehålla blank steg.  | `Survey Writer` |
    | **Tillåtna medlems typer** | Anger om den här program rollen kan tilldelas till användare, program eller både och. | `Users/Groups` |
    | **Värde** | Anger värdet för det roll anspråk som programmet ska förvänta sig i token. Värdet ska exakt matcha den sträng som refereras i programmets kod. Värdet får inte innehålla blank steg. | `Survey.Create` |
    | **Beskrivning** | En mer detaljerad beskrivning av den app-roll som visas under admin-appens tilldelning och medgivande upplevelser. | `Writers can create surveys.` |
    | **Vill du aktivera den här program rollen?** | Om app-rollen är aktive rad. Avmarkera den här kryss rutan om du vill ta bort en app-roll och tillämpa ändringen innan du försöker ta bort åtgärden. | *Markerad* |

1. Tryck på **Apply** (Verkställ) för att spara ändringarna.

> [!NOTE]
> Antalet roller som du lägger till räknas mot de gränser som har definierats för ett applikations manifest. Mer information om dessa begränsningar finns i avsnittet  [begränsningar i manifestet](./reference-app-manifest.md#manifest-limits) för [Azure Active Directory app manifest referens](reference-app-manifest.md).

## <a name="assign-users-and-groups-to-roles"></a>Tilldela roller till användare och grupper

När du har lagt till app-roller i ditt program kan du tilldela roller till användare och grupper.

1. I **Azure Active Directory** i Azure Portal väljer du **företags program** i den vänstra navigerings menyn.
1. Välj **alla program** om du vill visa en lista över alla dina program.

     Om ditt program inte visas i listan använder du filtren högst upp i listan **alla program** för att begränsa listan eller bläddra nedåt i listan för att hitta programmet.

1. Välj det program som du vill tilldela användare eller säkerhets grupp till roller.
1. Under **Hantera** väljer du **Användare och grupper**.
1. Öppna fönstret **Lägg till tilldelning** genom att välja **Lägg till användare** .
1. Välj Välj **användare och grupper** i fönstret **Lägg till tilldelning** .

     En lista över användare och säkerhets grupper visas. Du kan söka efter en viss användare eller grupp och välja flera användare och grupper som visas i listan.

1. När du har valt användare och grupper väljer du knappen **Välj** för att gå vidare.
1. Välj **Välj roll** i fönstret **Lägg till tilldelning** . Alla roller som du har definierat för programmet visas.
1. Välj en roll och välj knappen **Välj** .
1. Välj knappen **tilldela** för att slutföra tilldelningen av användare och grupper till appen.
1. Bekräfta att de användare och grupper som du har lagt till visas i listan **användare och grupper** .

## <a name="receive-roles-in-tokens"></a>Ta emot roller i token

När de användare som är tilldelade till de olika app-rollerna loggar in i programmet kommer deras token att ha sina tilldelade roller i `roles` anspråket.

## <a name="web-api-application-permissions"></a>Behörigheter för webb-API-program

Du kan definiera app-roller som riktar sig till **användare/grupper**, **program**eller **båda**. När du väljer **program** eller **båda**visas app-rollen som en program behörighet i klient appens API- **behörigheter**.

För att välja program behörighet i en klient program registrering när du har definierat en roll som är riktad mot **program** eller **båda**:

1. I **Azure Active Directory** i Azure Portal väljer du **Appregistreringar**och väljer sedan klient appens registrering.
1. Under **Hantera**, Välj **API-behörigheter**.
1. Välj **Lägg till en behörighet**  >  **Mina API: er**.
1. Välj den app som du har lagt till i program rollen och välj sedan **program behörigheter**.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om app-roller med följande resurser:

- Kod exempel: [Lägg till auktorisering med hjälp av app-roller & roll anspråk till en ASP.net Core webbapp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles) (GitHub)
- Video: [implementera auktorisering i dina program med Microsoft Identity Platform ](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
- [Azure Active Directory-appmanifest](./reference-app-manifest.md)
- [Azure AD-åtkomsttoken](access-tokens.md)
- [Azure AD `id_tokens`](id-tokens.md)
