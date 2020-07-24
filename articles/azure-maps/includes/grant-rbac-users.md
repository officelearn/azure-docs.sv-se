---
title: Bevilja rollbaserad åtkomst för användare
titleSuffix: Azure Maps
description: Använd rollbaserad åtkomst kontroll för att bevilja användare tillstånd att Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2b138dab2a97537a93b8d873f79b6ee9c00b4af4
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87126761"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Bevilja rollbaserad åtkomst för användare att Azure Maps

Du beviljar *rollbaserad åtkomst kontroll* (RBAC) genom att tilldela antingen en Azure AD-grupp eller säkerhets objekt till en eller flera Azure Maps åtkomst kontroll roll definitioner. Om du vill visa de roll definitioner för RBAC som är tillgängliga för Azure Maps går du till **åtkomst kontroll (IAM)**. Välj **roller**och Sök sedan efter roller som börjar med *Azure Maps*.

* För att effektivt hantera en stor mängd användares åtkomst till Azure Maps, se [Azure AD-grupper](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups).
* För att användarna ska kunna autentisera till programmet måste användarna skapas i Azure AD. Se [Lägg till eller ta bort användare med Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory).

Läs mer i [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/) för att effektivt hantera en katalog för användare.

1. Gå till ditt **Azure Maps-konto**. Välj roll tilldelningen **åtkomst kontroll (IAM)**  >  **Role assignment**.

    ![Bevilja RBAC](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. På fliken **roll tilldelningar** under **roll**väljer du en inbyggd Azure Maps roll definition som **Azure Maps data läsare** eller **Azure Maps data deltagare**. Under **tilldela åtkomst till**väljer du **Azure AD-användare, grupp eller tjänstens huvud namn**. Välj huvudobjektet efter namn. Välj sedan **Spara**.

   * Se information om [Lägg till eller ta bort roll tilldelningar](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

> [!WARNING]
> Azure Maps inbyggda roll definitioner ger en mycket stor behörighet till många Azure Maps REST-API: er. Om du vill begränsa API: erna för användare till ett minimum, se [skapa en anpassad roll definition och tilldela användare](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) till den anpassade roll definitionen. Detta gör det möjligt för användarna att ha den minsta behörighet som krävs för programmet.