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
ms.date: 08/30/2018
ms.author: rolyon
ms.openlocfilehash: d9a6ab49d619e487eee6fb13abe128cfc167b560
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306697"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>Identifiera Azure-resurser du hanterar i PIM

Du kan använda Azure AD Privileged Identity Management (PIM), för att förbättra skydd av dina Azure-resurser. Det här är användbart att organisationer som redan använder PIM för att skydda Azure AD-katalogroller och hantering av grupp och prenumeration ägare som vill skydda produktionsresurser.

När du först ställa in PIM för Azure-resurser, måste du identifiera och markerar resurserna som ska skyddas med PIM. Det finns ingen gräns för hur många resurser som du kan hantera med PIM. Vi rekommenderar dock att börja med dina viktigaste resurser (produktion).

## <a name="discover-resources"></a>Identifiera resurser

1. Logga in på [Azure-portalen](https://portal.azure.com/).

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

    > [!NOTE]
    > När en hanteringsgrupp eller prenumeration har angetts till hanterade, kan den inte ohanterade. Detta förhindrar att en annan resursadministratör tar bort PIM-inställningar.

    ![Identifiering – hantera resurs](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Klicka på **hantera resurs** och börja hantera de markerade resurserna.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure-resurs rollinställningar i PIM](pim-resource-roles-configure-role-settings.md)
- [Tilldela Azure-resursroller i PIM](pim-resource-roles-assign-roles.md)
