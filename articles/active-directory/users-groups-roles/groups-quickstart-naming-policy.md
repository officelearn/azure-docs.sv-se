---
title: Snabbstart för gruppnamnprincip – Azure Active Directory | Microsoft-dokument
description: Beskriver hur du lägger till nya användare eller tar bort befintliga användare i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e419ac5c3d292b7e630d1ebb3d3b9f59ef7b8a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74026937"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Snabbstart: Namnprincip för grupper i Azure Active Directory

I den här snabbstarten konfigurerar du en namnprincip i din Azure Active Directory-klientorganisationen (Azure AD) för användarskapade Office 365-grupper för att hjälpa dig att sortera och söka i klientorganisationens grupper. Du kan till exempel använda namnprincipen till att:

* Kommunicera funktionen för en grupp, ett medlemskap, en geografisk region eller vem som har skapat gruppen.
* Kategorisera grupper i adressboken.
* Blockera specifika ord från att användas i gruppnamn och alias.

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-portal"></a>Konfigurera gruppnamnprincipen för en klient med Azure-portal

1. Logga in på [Azure AD-administrationscentret](https://aad.portal.azure.com) med ett användarkonto för användaradministratör.
1. Välj **Grupper**och välj sedan **Namngivningsprincip** för att öppna sidan Namngivningsprincip.

    ![öppna sidan Namngivningsprincip i administrationscentret](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Visa eller redigera namngivningsprincipen prefix-suffix

1. Välj **Gruppnamnsprincip**på sidan **Namngivningsprincip** .
1. Du kan visa eller redigera det aktuella prefixet eller suffixets namngivningsprinciper individuellt genom att välja de attribut eller strängar som du vill tillämpa som en del av namngivningsprincipen.
1. Om du vill ta bort ett prefix eller ett suffix från listan markerar du prefixet eller suffixet och väljer sedan **Ta bort**. Flera objekt kan tas bort samtidigt.
1. Välj **Spara** för att ändringarna i principen ska börja gälla.

### <a name="view-or-edit-the-custom-blocked-words"></a>Visa eller redigera anpassade blockerade ord

1. På sidan **Namngivningsprincip** väljer du **Blockerade ord**.

    ![redigera och ladda upp listan med blockerade ord för namngivningsprincip](./media/groups-naming-policy/blockedwords.png)

1. Visa eller redigera den aktuella listan med anpassade blockerade ord genom att välja **Hämta**.
1. Ladda upp den nya listan med anpassade blockerade ord genom att välja filikonen.
1. Välj **Spara** för att ändringarna i principen ska börja gälla.

Klart! Du har angett en namnprincip och lagt till dina anpassade blockerade ord.

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="remove-the-naming-policy-using-azure-portal"></a>Ta bort namngivningsprincipen med Azure-portalen

1. På sidan **Namngivningsprincip** väljer du **Ta bort princip**.
1. När du har bekräftat borttagningen tas namngivningsprincipen bort, inklusive alla namngivningsprincipen prefix-suffix och eventuella anpassade blockerade ord.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du ställer in namngivningsprincipen för din Azure AD-organisation via Azure-portalen.

Gå vidare till nästa artikel för mer information, inklusive PowerShell-cmdletar för namngivningsprincip, tekniska begränsningar, lägga till en lista över anpassade blockerade ord och slutanvändarens upplevelser i Office 365-appar.
> [!div class="nextstepaction"]
> [Namngivningsprincipen PowerShell](groups-naming-policy.md)