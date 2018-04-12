---
title: Hantera åtkomst och behörighet med RBAC - Azure RBAC | Microsoft Docs
description: Kom igång med åtkomsthantering med rollbaserad åtkomstkontroll i Azure i Azure Portal. Använd rolltilldelningar för att tilldela behörigheter i din katalog.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/02/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: d6911b8deb92fe6781bf79b4d5794101b266c63e
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2018
---
# <a name="get-started-with-role-based-access-control-in-the-azure-portal"></a>Kom igång med rollbaserad åtkomstkontroll i Azure-portalen
Säkerhet indatavärdena företag bör tänka på att ge anställda behörigheterna exakt som de behöver. För många behörigheter kan exponera ett konto för attacker. För få behörigheter innebär att anställda kan få arbetet gjort effektivt. Azure rollbaserad åtkomstkontroll (RBAC) kan du lösa det här problemet genom att erbjuda detaljerad åtkomsthantering för Azure.

Med RBAC kan du särskilja uppgifter i din grupp och ge bara mängden åtkomst till användare som de behöver för att utföra sitt arbete. Obegränsad istället för att ge alla behörigheter i din Azure-prenumeration eller resurser, kan du tillåta endast vissa åtgärder. Till exempel använda RBAC så att en medarbetare som hanterar virtuella datorer i en prenumeration medan en annan kan hantera SQL-databaser inom samma prenumeration.

## <a name="basics-of-access-management-in-azure"></a>Grunderna i åtkomsthantering i Azure
Varje Azure-prenumeration är associerad med en katalog i Azure Active Directory (AD). Användare, grupper och program från katalogen kan hantera resurser i Azure-prenumerationen. Tilldela behörigheter med Azure-portalen, Azure kommandoradsverktyg och Azure Management-API: er.

Bevilja åtkomst genom att tilldela rollen RBAC till användare, grupper och program för ett visst område. Omfånget för en rolltilldelning kan vara en prenumeration, resursgrupp eller en enskild resurs. En roll som tilldelats en överordnad omfattning även ger åtkomst till underordnade som finns i den. En användare med åtkomst till en resursgrupp kan exempelvis hantera alla resurser som den innehåller som webbplatser, virtuella datorer och undernät.

![Relationen mellan element i Azure Active Directory - diagram](./media/role-based-access-control-what-is/rbac_aad.png)

Rollen RBAC som du tilldelar avgör vilka resurser som användare, grupp eller programmet kan hantera i omfattningen.

## <a name="built-in-roles"></a>Inbyggda roller
Azure RBAC har tre grundläggande roller som gäller för alla typer av resurser:

* **Ägare** har fullständig åtkomst till alla resurser som bland annat att delegera åtkomst till andra.
* **Deltagare** kan skapa och hantera alla typer av Azure-resurser, men det går inte att bevilja åtkomst till andra.
* **Läsaren** kan visa befintliga Azure-resurser.

Resten av rollerna i Azure RBAC kan hanteringen av specifika Azure-resurser. Till exempel tillåter virtuella deltagarrollen användaren att skapa och hantera virtuella datorer. Det ger dem åtkomst till virtuella nätverk eller undernät som den virtuella datorn ansluter till. 

[Inbyggda RBAC-roller](role-based-access-built-in-roles.md) listar rollerna som är tillgängliga i Azure. Det anger åtgärder och scope som varje inbyggd roll som ger användare. Om du behöver för att definiera egna roller för ännu mer kontroll, se hur du skapar [anpassade roller i Azure RBAC](role-based-access-control-custom-roles.md).

## <a name="resource-hierarchy-and-access-inheritance"></a>Resursen hierarki- och arv
* Varje **prenumeration** hör till endast en katalog i Azure. (Men varje katalog kan ha mer än en prenumeration.)
* Varje **resursgruppen** hör till en enda prenumeration.
* Varje **resurs** hör till endast en resursgrupp.

Åtkomst som du beviljar i överordnade omfattningar ärvs på underordnade omfattningar. Exempel:

* Du kan tilldela rollen Läsare för en Azure AD-gruppen på prenumerationsomfattningen. Medlemmar i gruppen kan visa varje resursgrupp och resurser i prenumerationen.
* Du tilldelar deltagarrollen till ett program på resursen Gruppomfång. Den kan hantera resurser av alla typer i resursgruppen, men inte andra resursgrupper i prenumerationen.

## <a name="azure-rbac-vs-classic-subscription-administrators"></a>Azure RBAC kontra klassiska prenumerationsadministratörer
[Klassiska prenumerationsadministratörer och medadministratörer](../billing/billing-add-change-azure-subscription-administrator.md) har fullständig åtkomst till Azure-prenumerationen. De kan hantera resurser med hjälp av den [Azure-portalen](https://portal.azure.com), Azure Resource Manager API: er och den klassiska distributionsmodellen API: er. I RBAC-modellen tilldelas klassiska administratörer rollen som ägare i omfånget för prenumerationen.

Azure portal och nya Azure Resource Manager API: erna stöder Azure RBAC. Användare och program som tilldelats RBAC-roller kan inte använda Azure klassiska distributionsmodellen API: er.

## <a name="authorization-for-management-vs-data-operations"></a>Tillstånd för hantering jämfört med dataåtgärder
Azure RBAC stöder endast hanteringsåtgärder Azure-resurser i Azure-portalen och Azure Resource Manager API: er. Det går inte att tillåta alla data på åtgärder för Azure-resurser. Du kan till exempel auktorisera någon att hantera Storage-konton, men inte till blobbar eller tabeller i ett Lagringskonto. På samma sätt kan kan en SQL-databas hanteras, men inte tabellerna i den.

## <a name="next-steps"></a>Nästa steg
* Kom igång med [rollbaserad åtkomstkontroll i Azure portal](role-based-access-control-configure.md).
* Gå till [Inbyggda RBAC-roller](role-based-access-built-in-roles.md)
* Definiera egna [anpassade roller i Azure RBAC](role-based-access-control-custom-roles.md)
