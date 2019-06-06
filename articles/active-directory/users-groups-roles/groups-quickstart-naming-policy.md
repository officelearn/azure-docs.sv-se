---
title: Snabbstart om att lägga till namngivningsprincip för Office 365-grupper – Azure Active Directory | Microsoft Docs
description: Beskriver hur du lägger till nya användare eller tar bort befintliga användare i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 04/24/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b17ef24d753041934f68f3daee950aaa0bec46ba
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734715"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Snabbstart: Namnprincip för grupper i Azure Active Directory

I den här snabbstarten konfigurerar du en namnprincip i din Azure Active Directory-klientorganisationen (Azure AD) för användarskapade Office 365-grupper för att hjälpa dig att sortera och söka i klientorganisationens grupper. Du kan till exempel använda namnprincipen till att:

* Kommunicera funktionen för en grupp, ett medlemskap, en geografisk region eller vem som har skapat gruppen.
* Kategorisera grupper i adressboken.
* Blockera specifika ord från att användas i gruppnamn och alias.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-portal"></a>Konfigurera gruppen namngivningspolicy för en klient med hjälp av Azure portal

1. Logga in på den [Azure AD administratörscenter](https://aad.portal.azure.com) användare med ett administratörskonto.
1. Välj **grupper**och välj sedan **Namngivningspolicy** att öppna sidan namngivning av principen.

    ![Öppna sidan namngivning av principen i administrationscentret](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Visa eller redigera namnprincip för Prefix-suffix

1. På den **Namngivningspolicy** väljer **gruppen namnprincip**.
1. Du kan visa eller redigera det aktuella prefixet eller suffixet naming principer individuellt genom att välja attribut eller strängar som du vill framtvinga som en del av namnprincip.
1. Om du vill ta bort ett prefix eller suffix i listan, Välj prefix eller suffix och välj sedan **ta bort**. Flera objekt kan tas bort samtidigt.
1. Välj **spara** för dina ändringar till principen ska träda i kraft.

### <a name="view-or-edit-the-custom-blocked-words"></a>Visa eller redigera anpassade spärrad ord

1. På den **Namngivningspolicy** väljer **blockeras ord**.

    ![Redigera och ladda upp blockerade ord lista för namngivning av princip](./media/groups-naming-policy/blockedwords.png)

1. Visa eller redigera den aktuella listan över anpassade spärrad ord genom att välja **hämta**.
1. Ladda upp den nya listan över anpassade spärrad ord genom att välja filikonen.
1. Välj **spara** för dina ändringar till principen ska träda i kraft.

Klart! Du har angett en namnprincip och lagt till dina anpassade blockerade ord.

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="remove-the-naming-policy-using-azure-portal"></a>Ta bort principen namngivning med hjälp av Azure portal

1. På den **Namngivningspolicy** väljer **ta bort princip**.
1. När du bekräftar borttagningen namngivning principen tas bort, inklusive alla prefix-suffix namngivning av principen och eventuella anpassade blockerade orden.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du ställer in namnprincip för din Azure AD-organisation via Azure portal.

Gå vidare till nästa artikel för mer information, inklusive PowerShell-cmdlets för namngivning av principen, tekniska begränsningar, att lägga till en lista över anpassade blockerade ord och upplevelser för slutanvändare i Office 365-appar.
> [!div class="nextstepaction"]
> [Namngivningspolicy PowerShell](groups-naming-policy.md)