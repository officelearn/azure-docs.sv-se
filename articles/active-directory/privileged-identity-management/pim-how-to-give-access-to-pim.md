---
title: Bevilja åtkomst till andra administratörer att hantera PIM - Azure | Microsoft Docs
description: Lär dig mer om att bevilja åtkomst till andra myndigheter att hantera Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: d6b2d9f43ce9bb86f4557c92887689c83beb49fa
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189563"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Bevilja åtkomst till andra administratörer att hantera PIM
Den globala administratören som gör Azure AD Privileged Identity Management (PIM) för en organisation att automatiskt få rolltilldelningar och åtkomst till PIM. Ingen annan hämtar skrivåtkomst som standard, men även andra globala administratörer. Andra globala administratörer, säkerhetsadministratörer och säkerhetsläsare har skrivskyddad åtkomst till Azure AD PIM. Om du vill ge åtkomst till PIM, kan den första användaren tilldela andra användare till den **privilegierad rolladministratör** roll.

> [!NOTE]
> Hantera Azure AD PIM kräver Azure MFA. Eftersom Microsoft-konton inte kan registrera dig för Azure MFA, en användare som loggar in med ett Microsoft-konto kan inte komma åt Azure AD PIM.
> 
> 

Kontrollera att det finns alltid minst två användare i en roll för administratör av Privilegierade roller, om en användare är utelåst eller deras kontot har tagits bort.

## <a name="give-another-user-access-to-manage-pim"></a>Ge en annan användare tillgång till hantera PIM
1. Logga in på den [Azure-portalen](https://portal.azure.com/) och välj den **Azure AD Privileged Identity Management** app på instrumentpanelen.
2. Välj **hantera Privilegierade roller** > **privilegierad rolladministratör** > **Lägg till**.
   
    ![Lägg till Privilegierade rolladministratörer – skärmbild](./media/pim-how-to-give-access-to-pim/PIM_add_PRA.png)
3. Steg 1 har redan slutförts på bladet Lägg till hanterade användare. Välj steg 2, **Välj användare** och Sök efter den användare som du vill lägga till.
   
    ![Välj användare – skärmbild](./media/pim-how-to-give-access-to-pim/PIM_select_users.png)
4. Välj användaren i sökresultatet och klicka på **klar**.
5. Klicka på **OK** att spara ditt val. Användare som du har valt visas i listan över Privilegierade rolladministratörer.
   
   * När du tilldelar en ny roll till någon ställs de automatiskt in som berättigad att aktivera rollen. Om du vill göra dem permanent i rollen klickar du på användaren i listan. Välj **gör permanent** i menyn användaren information.
6. Skicka användaren en länk till [komma igång med Azure AD Privileged Identity Management](pim-getting-started.md).

## <a name="remove-another-users-access-rights-for-managing-pim"></a>Ta bort en annan användares åtkomsträttigheter för att hantera PIM
Innan du tar bort någon från en administratör för privilegierade roller kan du alltid kontrollera kommer fortfarande att två användare som är tilldelade till den.

1. I PIM-instrumentpanelen klickar du på rollen **privilegierad rolladministratör**.  En lista över användare för närvarande i den rollen visas.
2. Klicka på användare i användarlistan.
3. Klicka på **ta bort**.  Visas ett bekräftelsemeddelande.
4. Klicka på **Ja** att ta bort användaren från rollen.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg

- [Aktivera prenumerationshantering i din klient](pim-resource-roles-enable-subscription-management.md)