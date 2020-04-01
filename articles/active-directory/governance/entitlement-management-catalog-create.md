---
title: Skapa & hantera en katalog med resurser i rättighetshantering - Azure AD
description: Lär dig hur du skapar en ny behållare med resurser och åtkomstpaket i Azure Active Directory-berättigandehantering.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffa6f2fbdcde41d81e268779c3a0586bd8310792
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437560"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Skapa och hantera en katalog med resurser i Azure AD-berättigandehantering

## <a name="create-a-catalog"></a>Skapa en katalog

En katalog är en behållare med resurser och åtkomstpaket. Du skapar en katalog när du vill gruppera relaterade resurser och åtkomstpaket. Den som skapar katalogen blir den första katalogägaren. En katalogägare kan lägga till ytterligare katalogägare.

**Viktig roll:** Global administratör, användaradministratör eller katalogskapare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Kataloger**på den vänstra menyn.

    ![Hanteringskataloger för berättigande i Azure-portalen](./media/entitlement-management-catalog-create/catalogs.png)

1. Klicka på **Ny katalog**.

1. Ange ett unikt namn för katalogen och ge en beskrivning.

    Användarna ser den här informationen i information om ett åtkomstpaket.

1. Om du vill att åtkomstpaketen i den här katalogen ska vara tillgängliga för användare att begära så snart de har skapats anger du **Aktiverad** till **Ja**.

1. Om du vill tillåta användare i valda externa kataloger att kunna begära åtkomstpaket i den här katalogen anger du **Aktiverat för externa användare** till **Ja**.

    ![Nytt katalogfönster](./media/entitlement-management-shared/new-catalog.png)

1. Klicka på **Skapa** om du vill skapa katalogen.

### <a name="creating-a-catalog-programmatically"></a>Skapa en katalog programmässigt

Du kan också skapa en katalog med Microsoft Graph.  En användare i en lämplig roll med `EntitlementManagement.ReadWrite.All` ett program som har delegerad behörighet kan anropa API:et för att [skapa en accessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-post?view=graph-rest-beta).

## <a name="add-resources-to-a-catalog"></a>Lägga till resurser i en katalog

Om du vill inkludera resurser i ett åtkomstpaket måste resurserna finnas i en katalog. De typer av resurser som du kan lägga till är grupper, program och SharePoint Online-webbplatser. Grupperna kan vara molnskapade Office 365-grupper eller molnskapade Azure AD-säkerhetsgrupper. Programmen kan vara Azure AD-företagsprogram, inklusive både SaaS-program och dina egna program som är federerade till Azure AD. Webbplatserna kan vara SharePoint Online-webbplatser eller SharePoint Online-webbplatssamlingar.

**Viktig roll:** Visa [Obligatoriska roller för att lägga till resurser i en katalog](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Kataloger** på den vänstra menyn och öppna sedan den katalog som du vill lägga till resurser i.

1. Klicka på **Resurser**på den vänstra menyn.

1. Klicka på **Lägg till resurser**.

1. Klicka på en resurstyp: **Grupper och team,** **program**eller **SharePoint-webbplatser**.

    Om du inte ser en resurs som du vill lägga till eller om du inte kan lägga till en resurs, kontrollerar du att du har den nödvändiga Azure AD-katalogrollen och rättighetshanteringsrollen. Du kan behöva låta någon med de roller som krävs lägga till resursen i katalogen. Mer information finns i [Obligatoriska roller för att lägga till resurser i en katalog](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Välj en eller flera resurser av den typ som du vill lägga till i katalogen.

    ![Lägga till resurser i en katalog](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. När du är klar klickar du på **Lägg till**.

    Dessa resurser kan nu inkluderas i åtkomstpaket i katalogen.

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Lägga till en resurs i en katalog programmässigt

Du kan också lägga till en resurs i en katalog med Microsoft Graph.  En användare i en lämplig roll, eller en katalog och resursägare, med ett program som har delegerad `EntitlementManagement.ReadWrite.All` behörighet kan anropa API:et för att skapa en [accessPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta).

## <a name="remove-resources-from-a-catalog"></a>Ta bort resurser från en katalog

Du kan ta bort resurser från en katalog. En resurs kan bara tas bort från en katalog om den inte används i något av katalogens åtkomstpaket.

**Viktig roll:** Visa [Obligatoriska roller för att lägga till resurser i en katalog](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Kataloger** på den vänstra menyn och öppna sedan den katalog som du vill ta bort resurser från.

1. Klicka på **Resurser**på den vänstra menyn.

1. Markera de resurser som du vill ta bort.

1. Klicka på **Ta bort** (eller klicka på ellipsen (**...**) och klicka sedan på Ta **bort resurs**).

## <a name="add-additional-catalog-owners"></a>Lägg till ytterligare katalogägare

Användaren som skapade en katalog blir den första katalogägaren. Om du vill delegera hanteringen av en katalog lägger du till användare i katalogägarrollen. Detta hjälper till att dela kataloghanteringsansvaret. 

Så här ska du tilldela en användare till katalogägarens roll:

**Viktig roll:** Global administratör, användaradministratör eller katalogägare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Kataloger** på den vänstra menyn och öppna sedan den katalog som du vill lägga till administratörer i.

1. Klicka på Roller **och administratörer**på den vänstra menyn.

    ![Katalogroller och administratörer](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Klicka på **Lägg till ägare** om du vill välja medlemmar för dessa roller.

1. Klicka på **Markera** om du vill lägga till dessa medlemmar.

## <a name="edit-a-catalog"></a>Redigera en katalog

Du kan redigera namn och beskrivning för en katalog. Användare ser den här informationen i information om ett åtkomstpaket.

**Viktig roll:** Global administratör, användaradministratör eller katalogägare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Kataloger** på den vänstra menyn och öppna sedan den katalog som du vill redigera.

1. Klicka på **Redigera**på **katalogens** översiktssida.

1. Redigera katalogens namn, beskrivning eller aktiverade inställningar.

    ![Redigera kataloginställningar](./media/entitlement-management-shared/catalog-edit.png)

1. Klicka på **Spara**.

## <a name="delete-a-catalog"></a>Ta bort en katalog

Du kan ta bort en katalog, men bara om den inte har några åtkomstpaket.

**Viktig roll:** Global administratör, användaradministratör eller katalogägare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Kataloger** på den vänstra menyn och öppna sedan den katalog som du vill ta bort.

1. Klicka på **Ta bort**i **katalogens översikt**.

1. Klicka på **Ja**i meddelanderutan som visas .

### <a name="deleting-a-catalog-programmatically"></a>Ta bort en katalog programmässigt

Du kan också ta bort en katalog med Microsoft Graph.  En användare i en lämplig roll med `EntitlementManagement.ReadWrite.All` ett program som har delegerad behörighet kan anropa API:t för att [ta bort en accessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-delete?view=graph-rest-beta).

## <a name="next-steps"></a>Nästa steg

- [Delegera åtkomststyrning till åtkomstpakethanterare](entitlement-management-delegate-managers.md)
