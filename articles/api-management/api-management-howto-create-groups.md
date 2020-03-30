---
title: Hantera utvecklarkonton med hjälp av grupper i Azure API Management
titleSuffix: Azure API Management
description: Lär dig hur du hanterar utvecklarkonton med hjälp av grupper i Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 182f0ea93ddfb2ab64de1c15b36958537fa5431f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430716"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Så skapar och använder du grupper för att hantera utvecklarkonton i Azure API Management

I API Management används grupper för att hantera hur produkter visas för utvecklare. Produkter görs först synliga för grupper och sedan kan utvecklare i dessa grupper visa och prenumerera på de produkter som är associerade med grupperna. 

API Management har följande systemgrupper som inte kan ändras:

* **Administratörer** – Administratörer av Azure-prenumerationer är medlemmar i den här gruppen. Administratörer hanterar API Management-tjänstinstanser genom att skapa API:er, åtgärder och produkter som används av utvecklare.
* **Utvecklare** – Autentiserade användare av utvecklarportalen hör till den här gruppen. Utvecklare är de kunder som utvecklar program med hjälp av dina API:er. Utvecklare beviljas åtkomst till utvecklarportalen och bygger program som anropar åtgärderna i ett API.
* **Gäster** – Oautentiserade användare av utvecklarportalen. Potentiella kunder som besöker utvecklarportalen för en API Management-instans hör t.ex. till den här gruppen. De kan beviljas viss skrivskyddad åtkomst, t.ex. möjligheten att visa API:er men inte anropa dem.

Utöver dessa systemgrupper kan administratörer skapa anpassade grupper eller [använda externa grupper i tillhörande Azure Active Directory-klienter][leverage external groups in associated Azure Active Directory tenants]. Anpassade och externa grupper kan användas tillsammans med systemgrupper för att välja vilka utvecklare som kan se och komma åt API-produkter. Du kan till exempel skapa en anpassad grupp för utvecklare som hör till en specifik partnerorganisation och ge dem åtkomst till API:erna från en produkt som endast innehåller relevanta API:er. En användare kan tillhöra mer än en grupp.

Den här guiden visar hur administratörer av en API Management-instans kan lägga till nya grupper och associera dem med produkter och utvecklare.

Förutom att skapa och hantera grupper i utgivarportalen kan du skapa [Group](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) och hantera dina grupper med hjälp av API Management REST API Group-entiteten.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Krav

Slutför uppgifter i den här artikeln: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-group"></a><a name="create-group"> </a>Skapa en grupp

I det här avsnittet visas hur du lägger till en ny grupp i ditt API Management-konto.

1. Välj fliken **Grupper** till vänster om skärmen.
2. Klicka på **+Lägg till**.
3. Ange ett unikt namn för gruppen och en valfri beskrivning.
4. Tryck på **Skapa**.

    ![Lägga till en ny grupp](./media/api-management-howto-create-groups/groups001.png)

När gruppen har skapats läggs den till i listan **Grupper.** <br/>Om du vill redigera **gruppens namn** eller **beskrivning** klickar du på namnet på gruppen och **Inställningar**.<br/>Om du vill ta bort gruppen klickar du på gruppens namn och trycker på **Ta bort**.

Nu när gruppen har skapats kan den associeras med produkter och utvecklare.

## <a name="associate-a-group-with-a-product"></a><a name="associate-group-product"> </a>Associera en grupp med en produkt

1. Välj fliken **Produkter** till vänster.
2. Klicka på namnet på önskad produkt.
3. Tryck på **Åtkomstkontroll**.
4. Klicka på **+ Lägg till grupp**.

    ![Associera en grupp med en produkt](./media/api-management-howto-create-groups/groups002.png)
5. Markera den grupp som du vill lägga till.

    ![Associera en grupp med en produkt](./media/api-management-howto-create-groups/groups003.png)

    Om du vill ta bort en grupp från produkten klickar du på **Ta bort**.

    ![Ta bort en grupp](./media/api-management-howto-create-groups/groups004.png)

När en produkt är associerad med en grupp kan utvecklare i den gruppen visa och prenumerera på produkten.

> [!NOTE]
> Information om hur du lägger till Azure Active Directory-grupper finns i [Så här godkänner du utvecklarkonton med Azure Active Directory i Azure API Management](api-management-howto-aad.md).

## <a name="associate-groups-with-developers"></a><a name="associate-group-developer"> </a>Associera grupper med utvecklare

I det här avsnittet visas hur du associerar grupper med medlemmar.

1. Välj fliken **Grupper** till vänster om skärmen.
2. Välj **medlemmar**.

    ![Lägga till en medlem](./media/api-management-howto-create-groups/groups005.png)
3. Tryck på **+Lägg till** och välj en medlem.

    ![Lägga till en medlem](./media/api-management-howto-create-groups/groups006.png)
4. Tryck **på Markera**.

När associationen har lagts till mellan utvecklaren och gruppen kan du visa den på fliken **Användare.**

## <a name="next-steps"></a><a name="next-steps"> </a>Nästa steg

* När en utvecklare har lagts till i en grupp kan de visa och prenumerera på de produkter som är associerade med den gruppen. Mer information finns i [Hur du skapar och publicerar en produkt i Azure API Management][How create and publish a product in Azure API Management],
* Förutom att skapa och hantera grupper i utgivarportalen kan du skapa [Group](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) och hantera dina grupper med hjälp av API Management REST API Group-entiteten.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
