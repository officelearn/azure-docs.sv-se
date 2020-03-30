---
title: Upptäck Azure-resurser att hantera i PIM - Azure AD | Microsoft-dokument
description: Lär dig hur du identifierar Azure-resurser för att hantera i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87da43100c7494937ddc842e0f903ba3a360959e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022896"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Identifiera Azure-resurser för hantering i privilegierad identitetshantering

Med hjälp av Azure Active Directory (Azure AD) Privilegierad identitetshantering (PIM) kan du förbättra skyddet för dina Azure-resurser. Detta är användbart för organisationer som redan använder Privilegierad identitetshantering för att skydda Azure AD-roller och hanteringsgrupps- och prenumerationsägare som vill skydda produktionsresurser.

När du först konfigurerar Privilegierad identitetshantering för Azure-resurser måste du identifiera och välja de resurser som ska skyddas med privilegierad identitetshantering. Det finns ingen gräns för hur många resurser du kan hantera med privilegierad identitetshantering. Vi rekommenderar dock att du börjar med dina mest kritiska (produktions)resurser.

## <a name="discover-resources"></a>Identifiera resurser

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure-resurser**.

    Om det är första gången du använder Privilegierad identitetshantering för Azure-resurser visas sidan **Identifiera resurser.**

    ![Identifiera resursfönstret utan resurser listade för första gången](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Om en annan administratör i organisationen redan hanterar Azure-resurser i Privilegierad identitetshantering visas en lista över de resurser som för närvarande hanteras.

    ![Identifiera resurser i fönstret med resurser som för närvarande hanteras](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Välj **Identifiera resurser** för att starta identifieringsupplevelsen.

    ![Identifieringsfönstret med resurser som kan hanteras, till exempel prenumerationer och hanteringsgrupper](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. På sidan **Identifiering** använder du **resurstillståndsfiltret** och **Välj resurstyp** för att filtrera de hanteringsgrupper eller prenumerationer som du har skrivbehörighet till. Det är nog lättast att börja med **alla** från början.

    Du kan bara söka efter och välja hanteringsgrupps- eller prenumerationsresurser som ska hanteras med privilegierad identitetshantering. När du hanterar en hanteringsgrupp eller en prenumeration i Privilegierad identitetshantering kan du också hantera dess underordnade resurser.

1. Markera kryssrutan bredvid alla ohanterliga resurser som du vill hantera.

1. Välj **Hantera resurs** om du vill börja hantera de valda resurserna.

    > [!NOTE]
    > När en hanteringsgrupp eller prenumeration har hanterats kan den inte hanteras utan förvaltning. Detta förhindrar att en annan resursadministratör tar bort inställningar för privilegierad identitetshantering.

    ![Identifieringsfönster med en resurs markerad och alternativet Hantera resurs markerad](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Om du ser ett meddelande för att bekräfta introduktionen av den valda resursen för hantering väljer du **Ja**.

    ![Meddelande som bekräftar att de valda resurserna för hantering finns ombord](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure-resursrollinställningar i Privilegierad identitetshantering](pim-resource-roles-configure-role-settings.md)
- [Tilldela Azure-resursroller i Privilegierad identitetshantering](pim-resource-roles-assign-roles.md)
