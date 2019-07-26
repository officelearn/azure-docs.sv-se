---
title: Skapa och hantera en katalog i Azure AD-hantering av rättigheter (för hands version) – Azure Active Directory
description: Lär dig hur du skapar en ny behållare med resurser och åtkomst paket i Azure Active Directory rättighets hantering (för hands version).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e563d86abe3817e4c77cc0d5c8df928e41563f8
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489075"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>Skapa och hantera en katalog i Azure AD-hantering av rättigheter (för hands version)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättighets hantering är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-catalog"></a>Skapa en katalog

En katalog är en behållare för resurser och åtkomst paket. Du skapar en katalog när du vill gruppera relaterade resurser och åtkomst paket. Vem som skapar katalogen blir den första katalog ägaren. En katalog ägare kan lägga till ytterligare katalog ägare.

**Nödvändig roll:** Användar administratör eller katalog skapare

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Klicka på **Azure Active Directory** och klicka sedan på **identitets styrning**.

1. På den vänstra menyn klickar du på **kataloger**.

    ![Rättighets hanterings kataloger i Azure Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Klicka på **ny katalog**.

1. Ange ett unikt namn för katalogen och ange en beskrivning.

    Användare ser den här informationen i ett åtkomst pakets information.

1. Om du vill att åtkomst paketen i den här katalogen ska vara tillgängliga för användare att begära så fort de skapas, ställer du in **aktiverat** på **Ja**.

1. Om du vill tillåta att användare i valda externa kataloger kan begära åtkomst paket i den här katalogen, ställer du in **aktiverat för externa användare** på **Ja**.

    ![Fönstret ny katalog](./media/entitlement-management-catalog-create/new-catalog.png)

1. Skapa katalogen genom att klicka på **skapa** .

## <a name="add-resources-to-a-catalog"></a>Lägga till resurser i en katalog

Om du vill inkludera resurser i ett Access-paket måste resurserna finnas i en katalog. De typer av resurser som du kan lägga till är grupper, program och SharePoint Online-webbplatser. Grupperna kan vara molnbaserade Office 365-grupper eller molnbaserade Azure AD-säkerhetsgrupper. Programmen kan vara Azure AD Enterprise-program, inklusive både SaaS-program och dina egna program federerade till Azure AD. Platserna kan vara SharePoint Online-webbplatser eller SharePoint Online-webbplats samlingar.

**Nödvändig roll:** Se de [roller som krävs för att lägga till resurser i en katalog](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. Klicka på **kataloger** i den vänstra menyn och öppna den katalog som du vill lägga till resurser i.

1. Klicka på **resurser**på den vänstra menyn.

1. Klicka på **Lägg till resurser**.

1. Klicka på en resurs typ: **Grupper**, **program**eller **SharePoint-webbplatser**.

    Om du inte ser en resurs som du vill lägga till, eller om du inte kan lägga till en resurs, kontrollerar du att du har den nödvändiga rollen för Azure AD-katalogen och rättighets hanterings rollen. Du kanske måste ha någon med de nödvändiga rollerna för att lägga till resursen i katalogen. Mer information finns i de [roller som krävs för att lägga till resurser i en katalog](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Välj en eller flera resurser av den typ som du vill lägga till i katalogen.

1. Klicka på **Lägg till**när du är färdig.

    Dessa resurser kan nu inkluderas i åtkomst paket i katalogen.

## <a name="remove-resources-from-a-catalog"></a>Ta bort resurser från en katalog

Du kan ta bort resurser från en katalog. En resurs kan bara tas bort från en katalog om den inte används i någon av katalogens åtkomst paket.

**Nödvändig roll:** Se de [roller som krävs för att lägga till resurser i en katalog](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. Klicka på **kataloger** i den vänstra menyn och öppna den katalog som du vill ta bort resurser från.

1. Klicka på **resurser**på den vänstra menyn.

1. Välj de resurser som du vill ta bort.

1. Klicka på **ta bort** (eller klicka på ellipsen ( **...** ) och klicka sedan på **ta bort resurs**).

## <a name="edit-a-catalog"></a>Redigera en katalog

Du kan redigera namn och beskrivning för en katalog. Användarna ser den här informationen i ett åtkomst pakets information.

**Nödvändig roll:** Användar administratör eller katalog ägare

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. Klicka på **kataloger** i den vänstra menyn och öppna den katalog som du vill redigera.

1. På sidan **Översikt** för katalogen klickar du på **Redigera**.

1. Redigera katalogens namn eller beskrivning.

1. Klicka på **Spara**.

## <a name="delete-a-catalog"></a>Ta bort en katalog

Du kan ta bort en katalog, men bara om den inte har några åtkomst paket.

**Nödvändig roll:** Användar administratör eller katalog ägare

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. Klicka på **kataloger** i den vänstra menyn och öppna den katalog som du vill ta bort.

1. Klicka på **ta bort**i katalogens **Översikt**.

1. Klicka på **Ja**i meddelande rutan som visas.

## <a name="next-steps"></a>Nästa steg

- [Lägg till en katalog skapare](entitlement-management-delegate.md#add-a-catalog-creator)
- [Skapa och hantera ett Access-paket](entitlement-management-access-package-create.md)
