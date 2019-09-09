---
title: Identifiera Azure-resurser som ska hanteras i PIM – Azure Active Directory | Microsoft Docs
description: Lär dig hur du identifierar Azure-resurser som ska hanteras i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef1f1712ab7474747829c8744a39e74b78777668
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804175"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>Identifiera Azure-resurser som ska hanteras i PIM

Med hjälp av Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kan du förbättra skyddet av dina Azure-resurser. Detta är användbart för organisationer som redan använder PIM för att skydda Azure AD-roller och för hanterings grupper och prenumerations ägare som vill skydda produktions resurser.

När du först konfigurerar PIM för Azure-resurser måste du identifiera och välja de resurser som du vill skydda med PIM. Det finns ingen gräns för antalet resurser som du kan hantera med PIM. Vi rekommenderar dock att du börjar med de mest kritiska (produktions) resurserna.

## <a name="discover-resources"></a>Identifiera resurser

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure-resurser**.

    Om det här är första gången du använder PIM för Azure-resurser visas fönstret identifiera resurser.

    ![Fönstret identifiera resurser utan resurser som visas för första gången](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Om en annan resurs eller katalog administratör redan hanterar Azure-resurser i PIM visas en lista över de resurser som hanteras för närvarande.

    ![Fönstret identifiera resurser som visar resurser som för närvarande hanteras](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Klicka på **identifiera resurser** för att starta identifierings upplevelsen.

    ![Identifierings fönster som visar resurser som kan hanteras som prenumerationer och hanterings grupper](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. I identifierings fönstret använder du **resurs tillstånds filter** och **väljer resurs typ** för att filtrera hanterings grupper eller prenumerationer som du har Skriv behörighet till. Det är förmodligen enklast att börja med **alla** inlednings vis.

    Du kan bara söka efter och välja hanterings grupp eller prenumerations resurser för hantering med hjälp av PIM. När du hanterar en hanterings grupp eller en prenumeration i PIM kan du också hantera dess underordnade resurser.

1. Lägg till en bock bredvid eventuella ohanterade resurser som du vill hantera.

1. Klicka på **Hantera resurs** för att börja hantera de valda resurserna.

    > [!NOTE]
    > När en hanterings grupp eller prenumeration har angetts till hanterad kan den inte hanteras. Detta förhindrar att en annan resurs administratör tar bort PIM-inställningar.

    ![Identifierings fönstret med en resurs vald och alternativet hantera resurs är markerat](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Om du ser ett meddelande om att bekräfta onboarding av den valda resursen för hantering klickar du på **Ja**.

    ![Meddelande som bekräftar att de valda resurserna har publicerats för hantering](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera inställningar för Azure-resurs roller i PIM](pim-resource-roles-configure-role-settings.md)
- [Tilldela Azure-resurs roller i PIM](pim-resource-roles-assign-roles.md)
