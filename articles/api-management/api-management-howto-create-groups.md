---
title: "Hantera konton för utvecklare med hjälp av grupper i Azure API Management | Microsoft Docs"
description: "Lär dig att hantera developer konton med hjälp av grupper i Azure API Management"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 1587243bcd5f2b9af98b8b529c152ba49ef676be
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Hur du skapar och använda grupper för att hantera developer konton i Azure API Management
I API Management används grupper för att hantera hur produkter visas för utvecklare. Produkter görs först synliga för grupper och sedan utvecklare i dessa grupper kan visa och prenumerera på de produkter som är kopplade till grupperna. 

API Management har följande systemgrupper som inte kan ändras.

* **Administratörer** – Administratörer av Azure-prenumerationer är medlemmar i den här gruppen. Administratörer hanterar API Management-tjänstinstanser genom att skapa API:er, åtgärder och produkter som används av utvecklare.
* **Utvecklare** – Autentiserade användare av utvecklarportalen hör till den här gruppen. Utvecklare är de kunder som utvecklar program med hjälp av dina API:er. Utvecklare beviljas åtkomst till utvecklarportalen och bygger program som anropar åtgärderna i ett API.
* **Gäster** – Oautentiserade användare av utvecklarportalen. Potentiella kunder som besöker utvecklarportalen för en API Management-instans hör t.ex. till den här gruppen. De kan beviljas viss skrivskyddad åtkomst, t.ex. möjligheten att visa API:er men inte anropa dem.

Förutom grupperna system kan administratörer skapa anpassade grupper eller [utnyttja externa grupper i associerade Azure Active Directory-klienter][leverage external groups in associated Azure Active Directory tenants]. Anpassade och externa grupper kan användas tillsammans med systemgrupper för att välja vilka utvecklare som kan se och komma åt API-produkter. Du kan till exempel skapa en anpassad grupp för utvecklare som hör till en specifik partnerorganisation och ge dem åtkomst till API:erna från en produkt som endast innehåller relevanta API:er. En användare kan tillhöra mer än en grupp.

Den här guiden visar hur administratörer av en instans för API-hantering kan lägga till nya grupper och koppla dem till produkter och utvecklare.

> [!NOTE]
> Förutom att skapa och hantera grupper i publisher portal, du kan skapa och hantera grupper med hjälp av API Management REST API [grupp](https://msdn.microsoft.com/library/azure/dn776329.aspx) entitet.
> 
> 

## <a name="create-group"></a>Skapar du en grupp
Klicka för att skapa en ny grupp **Publisher portal** i Azure Portal för API Management-tjänsten. När du gör det kommer du till utgivarportalen för API Management.

![Utgivarportalen][api-management-management-console]

> Om du inte har skapat en instans för API Management-tjänsten finns [skapa en instans för API Management-tjänsten][Create an API Management service instance].
> 
> 

Klicka på **grupper** från den **API Management** menyn till vänster och klicka sedan på **Lägg till grupp**.

![Lägg till ny grupp][api-management-add-group]

Ange ett unikt namn för gruppen och en valfri beskrivning och klicka på **spara**.

![Lägg till ny grupp][api-management-add-group-window]

Den nya gruppen visas i fliken grupper. Så här redigerar du den **namn** eller **beskrivning** i gruppen, klicka på namnet på gruppen i listan. Ta bort gruppen, klicka på **ta bort**.

![Grupp som har lagts till][api-management-new-group]

Nu när gruppen skapas kan det vara associerat med produkter och utvecklare.

## <a name="associate-group-product"></a>Associera en grupp med en produkt
Koppla en grupp med en produkt genom att klicka på **produkter** från den **API Management** menyn till vänster och klicka sedan på namnet på den önskade produkten.

![Ange synlighet][api-management-add-group-to-product]

Välj den **synlighet** fliken att lägga till och ta bort grupper och för att visa de aktuella grupperna för produkten. Om du vill lägga till eller ta bort grupper, markera eller avmarkera kryssrutorna för de önskade grupperna och klicka på **spara**.

![Ange synlighet][api-management-add-group-to-product-visibility]

> [!NOTE]
> Om du vill lägga till Azure Active Directory-grupper, se [så att auktorisera developer konton med hjälp av Azure Active Directory i Azure API Management](api-management-howto-aad.md).
> 
> Så här konfigurerar du grupper från den **synlighet** för en produkt klickar du på **hantera grupper**.
> 
> 

När en produkt är associerad med en grupp kan kan utvecklare i gruppen visa och prenumerera på produkten.

## <a name="associate-group-developer"></a>Associera grupper med utvecklare
Om du vill associera grupper med utvecklare, klickar du på **användare** från den **API Management** menyn till vänster och sedan markera kryssrutan bredvid de utvecklare som du vill associera med en grupp.

![Lägga till utvecklaren i grupp][api-management-add-group-to-developer]

När önskade utvecklare kontrolleras klickar du på önskad grupp i den **lägga till i gruppen** listrutan. Utvecklare kan tas bort från grupper med hjälp av den **ta bort från gruppen** listrutan. 

![Utvecklare][api-management-add-group-to-developer-saved]

När kopplingen har lagts till mellan utvecklare och gruppen, kan du visa den i den **användare** fliken.

## <a name="next-steps"> </a>Nästa steg
* När en utvecklare har lagts till i en grupp, kan de visa och prenumerera på de produkter som är kopplade till den gruppen. Mer information finns i [hur skapa och publicera en produkt i Azure API Management][How create and publish a product in Azure API Management],
* Förutom att skapa och hantera grupper i publisher portal, du kan skapa och hantera grupper med hjälp av API Management REST API [grupp](https://msdn.microsoft.com/library/azure/dn776329.aspx) entitet.

[api-management-management-console]: ./media/api-management-howto-create-groups/api-management-management-console.png
[api-management-add-group]: ./media/api-management-howto-create-groups/api-management-add-group.png
[api-management-add-group-window]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
[api-management-new-group]: ./media/api-management-howto-create-groups/api-management-new-group.png
[api-management-add-group-to-product]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
[api-management-add-group-to-product-visibility]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
[api-management-add-group-to-developer]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
[api-management-add-group-to-developer-saved]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png

[api-management-]: ./media/api-management-howto-create-groups/api-management-.png

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group
