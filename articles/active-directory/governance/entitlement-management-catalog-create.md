---
title: Skapa och hantera en katalog i Azure AD rättigheten hantering (förhandsversion) – Azure Active Directory
description: Lär dig hur du skapar en ny behållare för resurser och åtkomst-paket i Azure Active Directory rättigheten hantering (förhandsversion).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/29/2019
ms.author: rolyon
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5988f4723f1ef73cf0767ef8ac1b9adf3c1435d
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190244"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>Skapa och hantera en katalog i Azure AD rättigheten hantering (förhandsversion)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättigheten management är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-catalog"></a>Skapa en katalog

En katalog är en behållare för resurser och åtkomst-paket. Du skapar en katalog när du vill gruppera relaterade resurser och få åtkomst till paket. Den skapar katalogen blir den första katalog-ägaren. En katalog-ägare kan lägga till ytterligare catalog ägare.

**Nödvändiga roll:** Användaradministratör eller katalogens skapare

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Klicka på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **kataloger**.

    ![Berättigande management kataloger i Azure portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Klicka på **ny katalog**.

1. Ange ett unikt namn för katalogen och ange en beskrivning.

    Användarna ser den här informationen i en Paketinformation för åtkomst.

1. Om du vill åtkomst-paket i den här katalogen ska vara tillgängliga för användare att begära när de skapas, ange **aktiverad** till **Ja**.

1. Om du vill tillåta användare i valda externa kataloger för att kunna begära åtkomst paket i den här katalogen genom att ange **aktiverad för externa användare** till **Ja**.

    ![Den nya katalogen rutan](./media/entitlement-management-catalog-create/new-catalog.png)

1. Klicka på **skapa** att skapa katalogen.

## <a name="add-resources-to-a-catalog"></a>Lägg till resurser till en katalog

Resurserna som måste finnas i en katalog med resurser i ett paket för åtkomst. Vilka typer av resurser som du kan lägga till är grupper, program och SharePoint Online-platser. Grupperna som kan vara cloud skapade Office 365-grupper eller molnet – skapat Azure AD-säkerhetsgrupper. Programmen kan vara Azure AD-företagsprogram, inklusive både SaaS-program och dina egna program federerat till Azure AD. Platserna kan vara SharePoint Online-platser eller SharePoint Online-webbplatssamlingar.

**Nödvändiga roll:** Se [krävs roller för att lägga till resurser i en katalog](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **kataloger** och öppna sedan den katalog som du vill lägga till resurser i.

1. I den vänstra menyn klickar du på **resurser**.

1. Klicka på **Lägg till resurser**.

1. Klicka på en resurstyp: **Grupper**, **program**, eller **SharePoint-webbplatser**.

    Om du inte ser en resurs som du vill lägga till eller om det inte går att lägga till en resurs, kontrollera att du har de nödvändiga Azure AD directory-rollen och rätt roll. Du kan behöva ha någon med nödvändiga roller lägga till resursen i din katalog. Mer information finns i [krävs roller för att lägga till resurser i en katalog](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Välj en eller flera resurser av typen som du vill lägga till i katalogen.

1. När du är klar klickar du på **Lägg till**.

    Dessa resurser ingår nu i åtkomst-paket i katalogen.

## <a name="remove-resources-from-a-catalog"></a>Ta bort resurser från en katalog

Du kan ta bort resurser från en katalog. En resurs kan bara tas bort från en katalog om den inte används i den katalogen åtkomst-paket.

**Nödvändiga roll:** Se [krävs roller för att lägga till resurser i en katalog](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **kataloger** och öppna sedan den katalog som du vill ta bort resurser från.

1. I den vänstra menyn klickar du på **resurser**.

1. Välj de resurser som du vill ta bort.

1. Klicka på **ta bort** (eller klicka på ellipsen ( **...** ) och klicka sedan på **ta bort resursen**).

## <a name="edit-a-catalog"></a>Redigera en katalog

Du kan redigera namn och beskrivning för en katalog. Användarna ser den här informationen i en Paketinformation för åtkomst.

**Nödvändiga roll:** Användaradministratör eller katalogen ägare

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **kataloger** och öppna sedan den katalog som du vill redigera.

1. På katalogens **översikt** klickar du på **redigera**.

1. Redigera katalogens namn eller beskrivning.

1. Klicka på **Spara**.

## <a name="delete-a-catalog"></a>Ta bort en katalog

Du kan ta bort en katalog, men endast om den inte har någon åtkomst-paket.

**Nödvändiga roll:** Användaradministratör eller katalogen ägare

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **kataloger** och öppna sedan den katalog som du vill ta bort.

1. På katalogens **översikt**, klickar du på **ta bort**.

1. I meddelanderutan som visas klickar du på **Ja**.

## <a name="next-steps"></a>Nästa steg

- [Lägg till en katalog-skapare](entitlement-management-delegate.md#add-a-catalog-creator)
- [Skapa och hantera ett åtkomst-paket](entitlement-management-access-package-create.md)
