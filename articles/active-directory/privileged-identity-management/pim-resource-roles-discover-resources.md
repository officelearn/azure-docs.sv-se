---
title: Identifiera Azure-resurser du hanterar i PIM | Microsoft Docs
description: Lär dig att identifiera Azure-resurser kan hantera i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 01/23/2019
ms.author: rolyon
ms.openlocfilehash: e81aab8268266c99c9f08e165a98df31f2106123
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54912279"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>Identifiera Azure-resurser du hanterar i PIM

Du kan använda Azure AD Privileged Identity Management (PIM), för att förbättra skydd av dina Azure-resurser. Det här är användbart att organisationer som redan använder PIM för att skydda Azure AD-katalogroller och hantering av grupp och prenumeration ägare som vill skydda produktionsresurser.

När du först ställa in PIM för Azure-resurser, måste du identifiera och markerar resurserna som ska skyddas med PIM. Det finns ingen gräns för hur många resurser som du kan hantera med PIM. Vi rekommenderar dock att börja med dina viktigaste resurser (produktion).

## <a name="discover-resources"></a>Identifiera resurser

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure-resurser**.

    Om det här är första gången du använder PIM för Azure-resurser, visas ett fönster som identifierar resurser.

    ![Identifiera resurser – första gången](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Om en annan resurs eller directory-administratör i din organisation redan hanterar Azure-resurser i PIM, visas en lista över de resurser som hanteras för närvarande.

    ![Identifiera resurser fönstret](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Klicka på **Identifieringsresurser** att starta upplevelsen av identifieringen.

    ![Identifiering av fönstret](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. I fönstret identifiering använder **resurstillståndsfilter** och **Välj resurstyp** för att filtrera hanteringen grupper eller prenumerationer som du har skrivbehörighet till. Det är troligen enklast att börja med **alla** från början.

    Du kan bara söka efter och välja management grupp eller prenumeration resurser för att hantera med hjälp av PIM. När du hanterar en hanteringsgrupp eller i PIM-prenumeration kan hantera du också dess underordnade resurser.

1. Lägg till en bock bredvid eventuella ohanterade resurser som du vill hantera.

1. Klicka på **hantera resurs** och börja hantera de markerade resurserna.

    > [!NOTE]
    > När en hanteringsgrupp eller prenumeration har angetts till hanterade, kan den inte ohanterade. Detta förhindrar att en annan resursadministratör tar bort PIM-inställningar.

    ![Identifiering – hantera resurs](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Om du ser ett meddelande att bekräfta registrering av den markerade resursen för hantering, klickar du på **Ja**.

    ![Identifiering av, hantera resursen visas](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure-resurs rollinställningar i PIM](pim-resource-roles-configure-role-settings.md)
- [Tilldela Azure-resursroller i PIM](pim-resource-roles-assign-roles.md)
