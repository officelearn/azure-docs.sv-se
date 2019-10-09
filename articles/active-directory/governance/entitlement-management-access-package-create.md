---
title: Skapa ett nytt Access-paket i Azure AD-hantering av rättigheter (för hands version) – Azure Active Directory
description: Lär dig hur du skapar ett nytt Access-paket med resurser som du vill dela i Azure Active Directory rättighets hantering (för hands version).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/24/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a99aa766ed4e6cacbe22933db226b2037d3e736d
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170000"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>Skapa ett nytt Access-paket i Azure AD-hantering av rättigheter (för hands version)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättighets hantering är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med ett Access-paket kan du utföra en enstaka konfiguration av resurser och principer som automatiskt administrerar åtkomst för åtkomst paketets livs längd. I den här artikeln beskrivs hur du skapar ett nytt Access-paket.

## <a name="overview"></a>Översikt

Alla åtkomst paket måste placeras i en behållare som kallas för en katalog. En katalog definierar vilka resurser du kan lägga till i ditt åtkomst paket. Om du inte anger någon katalog placeras ditt åtkomst paket i den allmänna katalogen. För närvarande kan du inte flytta ett befintligt Access-paket till en annan katalog.

Alla åtkomst paket måste ha minst en princip. Principer anger vem som kan begära åtkomst paketet och även inställningar för godkännande och förfallo datum. När du skapar ett nytt Access-paket kan du skapa en första princip för användare i din katalog, för användare som inte finns i katalogen, endast för administratörs direkta tilldelningar eller så kan du välja att skapa principen senare.

Följande diagram visar den övergripande processen för att skapa ett nytt Access-paket.

![Skapa en process för Access-paket](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>Starta nytt Access-paket

**Nödvändig roll:** Global administratör, användar administratör eller katalog ägare

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Klicka på **Azure Active Directory** och klicka sedan på **identitets styrning**.

1. Klicka på **åtkomst paket**på den vänstra menyn.

    ![Hantering av rättigheter i Azure Portal](./media/entitlement-management-shared/elm-access-packages.png)

1. Klicka på **nytt Access-paket**.

## <a name="basics"></a>Grundläggande inställningar

På fliken **grundläggande** ger du åtkomst paketet ett namn och anger vilken katalog som du vill skapa åtkomst paketet i.

1. Ange ett visnings namn och en beskrivning för Access-paketet. Användarna ser den här informationen när de skickar en begäran om åtkomst paketet.

1. I list rutan **katalog** väljer du den katalog som du vill skapa åtkomst paketet i. Du kan till exempel ha en katalog ägare som hanterar alla marknadsförings resurser som kan begäras. I det här fallet kan du välja marknadsförings katalogen.

    Du ser bara kataloger som du har behörighet att skapa åtkomst paket i. Om du vill skapa ett Access-paket i en befintlig katalog måste du vara minst en global administratör, en användar administratör, katalog ägare i katalogen eller komma åt paket hanteraren i katalogen.

    ![Åtkomst paket – grundläggande information](./media/entitlement-management-access-package-create/basics.png)

    Om du är global administratör eller en användar administratör och vill skapa ditt åtkomst paket i en ny katalog som inte finns med i listan klickar du på **Skapa nytt**. Ange katalogens namn och beskrivning och klicka sedan på **skapa**.

    Det åtkomst paket som du skapar och alla resurser som ingår i det kommer att läggas till i den nya katalogen. Du kan också lägga till ytterligare katalog ägare senare.


1. Klicka på **Nästa**.

## <a name="resource-roles"></a>Resursroller

På fliken **resurs roller** väljer du de resurser som du vill ta med i åtkomst paketet.  Användare som begär och tar emot åtkomst paketet får alla resurs roller i åtkomst paketet.

1. Klicka på den resurs typ som du vill lägga till (**grupper och team**, **program**eller **SharePoint-platser**).

1. I fönstret Välj som visas väljer du en eller flera resurser i listan.

    ![Åtkomst paket – resurs roller](./media/entitlement-management-access-package-create/resource-roles.png)

    Om du skapar åtkomst paketet i den allmänna katalogen eller i en ny katalog kan du välja vilken resurs som helst från den katalog som du äger. Du måste vara minst en global administratör, en användar administratör eller en katalogs kapare.

    Om du skapar ett Access-paket i en befintlig katalog kan du välja vilken resurs som helst som redan finns i katalogen utan att du äger den.

    Om du är global administratör, en användar administratör eller katalog ägare har du ytterligare möjlighet att välja resurser som inte finns i katalogen än. Om du väljer resurser som inte finns i den valda katalogen, kommer dessa resurser också att läggas till i katalogen för andra katalog administratörer för att bygga åtkomst paket med. Om du bara vill välja resurser som för närvarande finns i den valda katalogen markerar du kryss rutan **endast se** överst i Välj panorera.

1. När du har valt resurserna väljer du den roll som du vill att användarna ska tilldelas för resursen i listan **roll** .

    ![Åtkomst paket – resurs roll val](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Klicka på **Nästa**.

## <a name="policy"></a>Princip

På fliken **princip** skapar du den första principen för att ange vem som kan begära åtkomst paketet och även inställningar för godkännande och förfallo datum. Senare kan du skapa fler principer för att tillåta att fler grupper av användare begär åtkomst paketet med sina egna inställningar för godkännande och förfallo datum. Du kan också välja att skapa principen senare.

1. Ange den **första principen** växla till **nu** eller **senare**.

    ![Åtkomst paket – princip](./media/entitlement-management-access-package-create/policy.png)

1. Om du väljer **senare**går du vidare till avsnittet [Granska + skapa](#review--create) för att skapa ditt åtkomst paket.

1. Om du väljer **nu**utför du stegen i någon av följande princip avsnitt.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>Granska + skapa

På fliken **Granska + skapa** kan du granska dina inställningar och kontrol lera eventuella verifierings fel.

1. Granska åtkomst paketets inställningar

    ![Åtkomst paket-princip-aktivera princip inställning](./media/entitlement-management-access-package-create/review-create.png)

1. Skapa åtkomst paketet genom att klicka på **skapa** .

    Det nya Access-paketet visas i listan över åtkomst paket.

## <a name="next-steps"></a>Nästa steg

- [Redigera och hantera ett befintligt åtkomstpaket](entitlement-management-access-package-edit.md)
