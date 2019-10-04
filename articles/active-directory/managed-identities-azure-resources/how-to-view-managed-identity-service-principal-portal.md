---
title: Så här visar du tjänstens huvud namn för en hanterad identitet i Azure Portal
description: Stegvisa instruktioner för att Visa tjänstens huvud namn för en hanterad identitet i Azure Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a8db0254f85980022115cab5d73f7f329b57001
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827557"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Visa tjänstens huvud namn för en hanterad identitet i Azure Portal

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du visar tjänstens huvud namn för en hanterad identitet med hjälp av Azure Portal.

 > [!NOTE] 
 > Tjänstens huvud namn är företags program. 

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md).
- Om du inte redan har ett Azure-konto kan du [Registrera dig för ett kostnads fritt konto](https://azure.microsoft.com/free/).
- Aktivera [systemtilldelad identitet på en virtuell dator eller ett](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) [program](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Visa tjänstens huvud namn

Den här proceduren visar hur du visar tjänstens huvud namn för en virtuell dator med systemtilldelad identitet aktive rad (samma steg gäller för ett program).

1. Klicka på **Azure Active Directory** och sedan på **företags program**.
2. Under **program typ**väljer du **alla program**.
3. I rutan Sök filter skriver du namnet på den virtuella dator eller det program som har hanterad identitet aktive rad eller väljer den i listan som visas.

   ![Visa Managed Identity service-huvudobjektet i portalen](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Nästa steg

[Hanterade identiteter för Azure-resurser](/azure/active-directory/managed-identities-azure-resources/overview)

