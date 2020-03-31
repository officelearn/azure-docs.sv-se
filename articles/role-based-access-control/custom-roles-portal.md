---
title: Skapa eller uppdatera anpassade Azure-roller med Azure-portalen (förhandsversion) - Azure RBAC
description: Lär dig hur du skapar anpassade Azure-roller för Azure-rollbaserad åtkomstkontroll (Azure RBAC) med hjälp av Azure-portalen. Detta inkluderar hur du listar, skapar, uppdaterar och tar bort anpassade roller.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: rolyon
ms.openlocfilehash: 3204cdf51f3f37588f684f801a811f569b337d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77674875"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal-preview"></a>Skapa eller uppdatera anpassade Azure-roller med Azure-portalen (förhandsversion)

> [!IMPORTANT]
> Azure-anpassade roller med Azure-portalen är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om [de inbyggda Azure-rollerna](built-in-roles.md) inte uppfyller organisationens specifika behov kan du skapa dina egna Azure-anpassade roller. Precis som inbyggda roller kan du tilldela anpassade roller till användare, grupper och tjänsthuvudnamn vid prenumerations- och resursgruppomfång. Anpassade roller lagras i en Azure Active Directory -katalog (Azure AD) och kan delas mellan prenumerationer. Varje katalog kan ha upp till 5000 anpassade roller. Anpassade roller kan skapas med Azure-portalen, Azure PowerShell, Azure CLI eller REST API. I den här artikeln beskrivs hur du skapar anpassade roller med Azure-portalen (för närvarande i förhandsversion).

## <a name="prerequisites"></a>Krav

Om du vill skapa anpassade roller behöver du:

- Behörigheter att skapa anpassade roller som [Owner](built-in-roles.md#owner) (Ägare) eller [User Access Administrator](built-in-roles.md#user-access-administrator) (Administratör för användaråtkomst)

## <a name="step-1-determine-the-permissions-you-need"></a>Steg 1: Bestäm vilka behörigheter du behöver

Azure har tusentals behörigheter som du kan eventuellt inkludera i din anpassade roll. Här är fyra sätt som du kan bestämma vilka behörigheter du vill lägga till i din anpassade roll:

| Metod | Beskrivning |
| --- | --- |
| Titta på befintliga roller | Du kan titta på befintliga roller för att se vilka behörigheter som används. Mer information finns [i Azure-inbyggda roller](built-in-roles.md). |
| Sök efter behörigheter efter nyckelord | När du skapar en anpassad roll med hjälp av Azure-portalen kan du söka efter behörigheter efter nyckelord. Du kan till exempel söka efter *virtuella datorer* eller faktureringsbehörigheter. *billing* Den här sökfunktionen beskrivs mer senare i [steg 4: Behörigheter](#step-4-permissions). |
| Ladda ner alla behörigheter | När du skapar en anpassad roll med Azure-portalen kan du hämta alla behörigheter som en CSV-fil och sedan söka i den här filen. Klicka på knappen **Hämta alla behörigheter** i fönstret Lägg till **behörigheter** om du vill hämta alla behörigheter. Mer information om fönstret Lägg till behörigheter finns i [Steg 4: Behörigheter](#step-4-permissions). |
| Visa behörigheterna i dokumenten | Du kan visa tillgängliga behörigheter i [Azure Resource Manager-resursprovideråtgärder](resource-provider-operations.md). |

## <a name="step-2-choose-how-to-start"></a>Steg 2: Välj hur du startar

Det finns tre sätt att börja skapa en anpassad roll. Du kan klona en befintlig roll, börja om från början eller börja med en JSON-fil. Det enklaste sättet är att hitta en befintlig roll som har de flesta av de behörigheter du behöver och sedan klona och ändra den för ditt scenario. 

### <a name="clone-a-role"></a>Klona en roll

Om en befintlig roll inte riktigt har de behörigheter du behöver kan du klona den och sedan ändra behörigheterna. Följ dessa steg för att börja klona en roll.

1. Öppna en prenumeration eller resursgrupp i Azure-portalen där du vill att den anpassade rollen ska tilldelas och sedan öppna **Åtkomstkontroll (IAM)**.

    Följande skärmbild visar sidan Access control (IAM) som öppnats för en prenumeration.

    ![Sidan Åtkomstkontroll (IAM) för en prenumeration](./media/custom-roles-portal/access-control-subscription.png)

1. Klicka på fliken **Roller** om du vill visa en lista över alla inbyggda och anpassade roller.

1. Sök efter en roll som du vill klona, till exempel rollen Faktureringsläsare.

1. Klicka på ellipsen (**...**) i slutet av raden och klicka sedan på **Klon .**

    ![På snabbmenyn Klona](./media/custom-roles-portal/clone-menu.png)

    Då öppnas den anpassade roller-redigeraren med alternativet **Klona ett rollalternativ** markerat.

1. Gå vidare till [steg 3: Grunderna](#step-3-basics).

### <a name="start-from-scratch"></a>Börja från början

Om du vill kan du följa dessa steg för att starta en anpassad roll från grunden.

1. Öppna en prenumeration eller resursgrupp i Azure-portalen där du vill att den anpassade rollen ska tilldelas och sedan öppna **Åtkomstkontroll (IAM)**.

1. Klicka på **Lägg till** och sedan på Lägg till anpassad **roll (förhandsgranskning)**.

    ![Lägg till anpassad rollmeny](./media/custom-roles-portal/add-custom-role-menu.png)

    Då öppnas redigeraren för anpassade roller med alternativet **Start från början** markerat.

1. Gå vidare till [steg 3: Grunderna](#step-3-basics).

### <a name="start-from-json"></a>Börja från JSON

Om du vill kan du ange de flesta av dina anpassade rollvärden i en JSON-fil. Du kan öppna filen i redigeraren för anpassade roller, göra ytterligare ändringar och sedan skapa den anpassade rollen. Följ dessa steg för att börja med en JSON-fil.

1. Skapa en JSON-fil med följande format:

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. Ange värden för de olika egenskaperna i JSON-filen. Här är ett exempel med några värden tillagda. Information om de olika egenskaperna finns i [Förstå rolldefinitioner](role-definitions.md).

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. Öppna sidan **IAM (Access Control) (IAM)** på Azure-portalen.

1. Klicka på **Lägg till** och sedan på Lägg till anpassad **roll (förhandsgranskning)**.

    ![Lägg till anpassad rollmeny](./media/custom-roles-portal/add-custom-role-menu.png)

    Detta öppnar den anpassade roller editor.

1. Välj **Start från JSON**i **Baslinjebehörigheter**på fliken Grunderna .

1. Klicka på mappknappen bredvid rutan Välj en fil för att öppna dialogrutan Öppna.

1. Markera filen JSON och klicka sedan på **Öppna**.

1. Gå vidare till [steg 3: Grunderna](#step-3-basics).

## <a name="step-3-basics"></a>Steg 3: Grunderna

På fliken **Grunderna** anger du namn, beskrivning och baslinjebehörigheter för din anpassade roll.

1. Ange ett namn för den anpassade rollen i rutan **Anpassat rollnamn.** Namnet måste vara unikt för Azure AD-katalogen. Namnet kan innehålla bokstäver, siffror, blanksteg och specialtecken.

1. Ange en valfri beskrivning för den anpassade rollen i rutan **Beskrivning.** Detta blir verktygstipset för den anpassade rollen.

    Alternativet **Originalbehörigheter** bör redan anges baserat på föregående steg, men du kan ändra.

    ![Fliken Grunderna med angivna värden](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>Steg 4: Behörigheter

På fliken **Behörigheter** anger du behörigheterna för din anpassade roll. Beroende på om du har klonat en roll eller om du började med JSON kan fliken Behörigheter redan ha en lista över vissa behörigheter.

![Fliken Behörigheter för den anpassade rollen skapa](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>Lägga till eller ta bort behörigheter

Följ dessa steg för att lägga till eller ta bort behörigheter för din anpassade roll.

1. Om du vill lägga till behörigheter klickar du på **Lägg till behörigheter** för att öppna fönstret Lägg till behörigheter.

    I den här rutan visas alla tillgängliga behörigheter grupperade i olika kategorier i ett kortformat. Varje kategori representerar en *resursprovider*, som är en tjänst som tillhandahåller Azure-resurser.

1. Skriv **en** sträng i rutan Sök efter en behörighet för att söka efter behörigheter. Sök till exempel efter *faktura* för att hitta behörigheter relaterade till faktura.

    En lista över resursleverantörskort visas baserat på söksträngen. En lista över hur resursleverantörer mappar till Azure-tjänster finns i [Resursleverantörer för Azure-tjänster](../azure-resource-manager/management/azure-services-resource-providers.md).

    ![Fönstret Lägg till behörigheter med resursprovidern](./media/custom-roles-portal/add-permissions-provider.png)

1. Klicka på ett resursleverantörskort som kan ha de behörigheter du vill lägga till i din anpassade roll, till exempel **Microsoft Billing**.

    En lista över hanteringsbehörigheterna för den resursleverantören visas baserat på söksträngen.

    ![Lägga till behörighetslista](./media/custom-roles-portal/add-permissions-list.png)

1. Om du letar efter behörigheter som gäller för dataplanet klickar du på **Dataåtgärder**. Annars lämnar du åtgärderna växlingsknapp inställd **på Åtgärder** för att lista behörigheter som gäller för hanteringsplanet. Mer information om skillnaderna mellan hanteringsplanet och dataplanet finns i [Hanterings- och dataåtgärder](role-definitions.md#management-and-data-operations).

1. Om det behövs uppdaterar du söksträngen för att förfina sökningen ytterligare.

1. När du har hittat en eller flera behörigheter som du vill lägga till i din anpassade roll lägger du till en bock bredvid behörigheterna. Lägg till exempel till en bock bredvid **Annan: Hämta faktura** för att lägga till behörigheten att hämta fakturor.

1. Klicka på **Lägg till** om du vill lägga till behörigheten i din behörighetslista.

    Behörigheten läggs till `Actions` som `DataActions`en eller en .

    ![Behörighet har lagts till](./media/custom-roles-portal/permissions-list-add.png)

1. Om du vill ta bort behörigheter klickar du på borttagningsikonen i slutet av raden. I det här exemplet, eftersom en användare inte behöver `Microsoft.Support/*` möjlighet att skapa supportärenden, kan behörigheten tas bort.

### <a name="add-wildcard-permissions"></a>Lägga till jokerteckenbehörigheter

Beroende på hur du valde att starta kan du\*ha behörigheter med jokertecken ( ) i listan med behörigheter. Ett jokertecken\*( ) utökar behörigheten till allt som matchar strängen du anger. Anta till exempel att du vill lägga till alla behörigheter som är relaterade till Azure Cost Management och export. Du kan lägga till alla dessa behörigheter:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

I stället för att lägga till alla dessa behörigheter kan du bara lägga till en jokerteckenbehörighet. Följande jokerteckenbehörighet motsvarar till exempel de tidigare fem behörigheterna. Detta skulle också omfatta eventuella framtida exportbehörigheter som kan läggas till.

```
Microsoft.CostManagement/exports/*
```

Om du vill lägga till en ny jokerteckenbehörighet kan du inte lägga till den med hjälp av fönstret **Lägg till behörigheter.** Om du vill lägga till en jokerteckenbehörighet måste du lägga till den manuellt med fliken **JSON.** Mer information finns i [steg 6: JSON](#step-6-json).

### <a name="exclude-permissions"></a>Uteslut behörigheter

Om din roll har\*behörighet för jokertecken ( ) och du vill utesluta eller subtrahera specifika behörigheter från jokerteckenbehörigheten kan du utesluta dem. Anta till exempel att du har följande jokerteckenbehörighet:

```
Microsoft.CostManagement/exports/*
```

Om du inte vill tillåta att en export tas bort kan du utesluta följande behörighet:

```
Microsoft.CostManagement/exports/delete
```

När du utesluter en behörighet läggs `NotActions` `NotDataActions`den till som en eller . De effektiva hanteringsbehörigheterna beräknas genom `Actions` att lägga till alla `NotActions`och sedan subtrahera alla . De gällande databehörigheterna beräknas genom `DataActions` att lägga till alla `NotDataActions`och sedan subtrahera alla .

> [!NOTE]
> Att utesluta ett tillstånd är inte samma sak som en neka. Att utesluta behörigheter är helt enkelt ett bekvämt sätt att subtrahera behörigheter från ett jokerteckenbehörighet.

1. Om du vill utesluta eller subtrahera en behörighet från en tillåten jokerteckenbehörighet klickar du på **Uteslut behörigheter** för att öppna fönstret Uteslut behörigheter.

    I det här fönstret anger du vilka hanterings- eller databehörigheter som är undantagna eller subtraheras.

1. När du har hittat en eller flera behörigheter som du vill utesluta lägger du till en bock bredvid behörigheterna och klickar sedan på knappen **Lägg till.**

    ![Fönstret Uteslut behörigheter - behörigheten markerad](./media/custom-roles-portal/exclude-permissions-select.png)

    Behörigheten läggs till `NotActions` `NotDataActions`som en eller .

    ![Utesluten behörighet](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>Steg 5: Tilldelningsbara scope

På fliken **Tilldelarr scope** anger du var din anpassade roll är tillgänglig för tilldelning, till exempel prenumeration eller resursgrupp. Beroende på hur du valde att starta kan den här fliken visa hur långtfånget där du öppnade sidan IAM (Access Control). Det går inte att ange tillsättbart scope till rotomfattning ("/"). För den här förhandsversionen kan du inte lägga till en hanteringsgrupp som ett tillsättbart scope.

1. Klicka på **Lägg till tilldelade scope** om du vill öppna fönstret Lägg till tilldelade scope.

    ![Fliken Tillåtna scope](./media/custom-roles-portal/assignable-scopes.png)

1. Klicka på ett eller flera scope som du vill använda, vanligtvis din prenumeration.

    ![Lägga till tilldelade scope](./media/custom-roles-portal/add-assignable-scopes.png)

1. Klicka på knappen **Lägg** till om du vill lägga till ditt tilldelade scope.

## <a name="step-6-json"></a>Steg 6: JSON

På fliken **JSON** visas din anpassade roll formaterad i JSON. Om du vill kan du redigera JSON direkt. Om du vill lägga till\*en behörighet för jokertecken ( ) måste du använda den här fliken.

1. Om du vill redigera JSON klickar du på **Redigera**.

    ![Fliken JSON som visar anpassad roll](./media/custom-roles-portal/json.png)

1. Gör ändringar i JSON.

    Om JSON inte är korrekt formaterad visas en röd ojämn linje och en indikator i den vertikala rännstenen.

1. När du är klar med redigeringen klickar du på **Spara**.

## <a name="step-7-review--create"></a>Steg 7: Granska + skapa

På fliken **Granska + skapa** kan du granska dina anpassade rollinställningar.

1. Granska dina anpassade rollinställningar.

    ![Fliken Granska + skapa](./media/custom-roles-portal/review-create.png)

1. Klicka på **Skapa** om du vill skapa din anpassade roll.

    Efter en liten stund visas en meddelanderuta som anger att din anpassade roll har skapats.

    ![Skapa anpassat rollmeddelande](./media/custom-roles-portal/custom-role-success.png)

    Om några fel upptäcks visas ett meddelande.

    ![Granska + skapa fel](./media/custom-roles-portal/review-create-error.png)

1. Visa din nya anpassade roll i listan **Roller.** Om du inte ser din anpassade roll klickar du på **Uppdatera**.

     Det kan ta några minuter innan din anpassade roll visas överallt.

## <a name="list-custom-roles"></a>Lista anpassade roller

Följ dessa steg för att visa dina anpassade roller.

1. Öppna en prenumeration eller resursgrupp och öppna sedan **Access-kontrollen (IAM)**.

1. Klicka på fliken **Roller** om du vill visa en lista över alla inbyggda och anpassade roller.

1. Välj **CustomRole** i listan **Typ** om du bara vill se dina egna roller.

    Om du just har skapat din anpassade roll och inte ser den i listan klickar du på **Uppdatera**.

    ![Anpassad rolllista](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>Uppdatera en anpassad roll

1. Som beskrivs tidigare i den här artikeln öppnar du listan över anpassade roller.

1. Klicka på ellipsen (**...**) för den anpassade roll som du vill uppdatera och klicka sedan på **Redigera**. Observera att du inte kan uppdatera inbyggda roller.

    Den anpassade rollen öppnas i redigeraren.

    ![Anpassad rollmeny](./media/custom-roles-portal/edit-menu.png)

1. Använd de olika flikarna för att uppdatera den anpassade rollen.

1. När du är klar med ändringarna klickar du på fliken **Granska + skapa** för att granska ändringarna.

1. Klicka på knappen **Uppdatera** om du vill uppdatera din anpassade roll.

## <a name="delete-a-custom-role"></a>Ta bort en anpassad roll

1. Som beskrivs tidigare i den här artikeln öppnar du listan över anpassade roller.

1. Ta bort alla rolltilldelningar som använder den anpassade rollen.

1. Klicka på ellipsen (**...**) för den anpassade roll som du vill ta bort och klicka sedan på **Ta bort**.

    ![Anpassad rollmeny](./media/custom-roles-portal/delete-menu.png)

    Det kan ta några minuter innan din anpassade roll tas bort helt.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Skapa en anpassad roll med Azure PowerShell](tutorial-custom-role-powershell.md)
- [Anpassade roller i Azure](custom-roles.md)
- [Azure Resource Manager-resursprovideråtgärder](resource-provider-operations.md)
