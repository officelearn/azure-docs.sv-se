---
title: Skapa ett nytt åtkomst-paket i Azure AD-rättigheten hantering (förhandsversion) – Azure Active Directory
description: Lär dig hur du skapar ett nytt paket för åtkomst av resurser som du vill dela i Azure Active Directory rättigheten hantering (förhandsversion).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/24/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ad6570a3f30e40e4074502a8ce85bf739f58d3f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866419"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>Skapa ett nytt åtkomst-paket i Azure AD rättigheten hantering (förhandsversion)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättigheten management är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ett åtkomst-paket kan du göra konfigurationen en gång av resurser och principer som automatiskt tillämpar åtkomst för resten av åtkomst-paketet. Den här artikeln beskriver hur du skapar ett nytt åtkomst-paket.

## <a name="overview"></a>Översikt

Alla åtkomst-paket måste placeras i en behållare som kallas en katalog. En katalog definierar vilka resurser som du kan lägga till ditt åtkomst-paket. Om du inte anger en katalog, placeras ditt åtkomst-paket i den allmänna katalogen. För närvarande kan flytta du inte ett befintligt paket för åtkomst till en annan katalog.

Alla åtkomst-paket måste ha minst en princip. Principer kan du ange vem som kan begära åtkomst till paketet och också inställningar för godkännande och upphör att gälla. När du skapar ett nytt åtkomst-paket, du kan skapa en inledande principen för användare i din katalog för användare inte i din katalog för administratören direkt tilldelningar, eller välja att skapa principen senare.

Följande diagram visar den övergripande processen att skapa ett nytt åtkomst-paket.

![Skapa en process för åtkomst-paket](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>Starta nytt åtkomst-paket

**Nödvändiga roll:** Användaradministratör eller katalogen ägare

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Klicka på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **åt paket**.

    ![Berättigande hantering i Azure portal](./media/entitlement-management-shared/elm-access-packages.png)

1. Klicka på **nya åtkomst paketet**.

## <a name="basics"></a>Grundläggande inställningar

På den **grunderna** fliken du namnge paketets åtkomst och ange vilken katalog för att skapa åtkomst-paketet i.

1. Ange ett namn och beskrivning för åtkomst-paketet. Användarna ser den här informationen när de skickar en begäran om åtkomst-paketet.

1. I den **Catalog** listrutan, väljer den katalog som du vill skapa åtkomst paketet i. Du kan till exempel ha en katalog-ägare som hanterar alla marketing-resurser som kan begäras. I det här fallet kan du välja från katalogen.

    Kataloger visas bara du har behörighet att skapa åtkomst-paket i. För att skapa åtkomst-paket i en befintlig katalog, måste du vara minst en användare med rollen, catalog ägare eller åtkomst-Pakethanteraren.

    ![Komma åt paket - grunderna](./media/entitlement-management-access-package-create/basics.png)

    Om du vill skapa ditt åtkomst-paket i en ny katalog klickar du på **Skapa ny**. Ange namnet för datakatalogen och beskrivning och klicka sedan på **skapa**.

    Åtkomst-paketet som du skapar och eventuella resurser som ingår i den kommer att läggas till den nya katalogen. Dessutom kan katalogens du automatiskt första ägare av katalogen. Du kan lägga till ytterligare catalog ägare.

    För att skapa en ny katalog, måste du vara minst en användare med rollen eller katalogens skapare.

1. Klicka på **Nästa**.

## <a name="resource-roles"></a>Resursroller

På den **resursroller** fliken, markerar du resurserna som ska ingå i åtkomst-paketet.

1. Klicka på den resurstyp som du vill lägga till (**grupper**, **program**, eller **SharePoint-webbplatser**).

1. I Välj fönstret som visas väljer du en eller flera resurser i listan.

    ![Paket för åtkomst - resursroller](./media/entitlement-management-access-package-create/resource-roles.png)

    Om du skapar paketet åtkomst i den allmänna katalogen eller en ny katalog, kommer du att kunna välja en resurs från den katalog som du äger. Du måste vara minst en användare med rollen eller skapare-katalogen.

    Om du skapar paketet åtkomst i en befintlig katalog, kan du välja alla resurser som redan finns i katalogen utan att äga den.

    Om du är en användare med rollen eller katalogen ägare kan ha ytterligare alternativ för att välja resurser som du äger och som inte ännu i katalogen. Om du väljer resurser för närvarande inte i den valda katalogen läggs även dessa resurser till katalogen för andra catalog-administratörer att skapa åtkomst-paket med. Om du vill välja resurser som finns i den valda katalogen, kontrollera den **kan bara se** kryssrutan högst upp väljer pan.

1. När du har valt resurserna i den **rollen** väljer du den roll som du vill att användare som ska tilldelas för resursen.

    ![Paket för åtkomst - resurs för Systemroll](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Klicka på **Nästa**.

## <a name="policy"></a>Princip

På den **princip** fliken du skapar den första principen om du vill ange vem som kan begära åtkomst till paketet och också inställningar för godkännande och upphör att gälla. Senare, kan du skapa flera principer för att tillåta ytterligare grupper av användare att begära åtkomst till paketet med sina egna inställningar för godkännande och upphör att gälla. Du kan också välja att skapa principen senare.

1. Ange den **skapa första princip** växla till **nu** eller **senare**.

    ![Komma åt paket - princip](./media/entitlement-management-access-package-create/policy.png)

1. Om du väljer **senare**, hoppa över ned till den [granska + skapa](#review--create) avsnitt för att skapa ditt åtkomst-paket.

1. Om du väljer **nu**, utföra stegen i följande avsnitt för principen.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>Granska + skapa

På den **granska + skapa** fliken kan du granska dina inställningar och Sök efter eventuella verifieringsfel.

1. Granska inställningar för åtkomst-paketet

    ![Paket för åtkomst - princip – aktivera principinställningen](./media/entitlement-management-access-package-create/review-create.png)

1. Klicka på **skapa** skapa åtkomst-paketet.

    Det nya åtkomst paketet visas i listan över åtkomst paket.

## <a name="next-steps"></a>Nästa steg

- [Redigera och hantera ett befintligt paket för åtkomst](entitlement-management-access-package-edit.md)
- [Skapa och hantera en katalog](entitlement-management-catalog-create.md)
