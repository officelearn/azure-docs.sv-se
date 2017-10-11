---
title: "Använda Azure hanterade program i marketplace | Microsoft Docs"
description: "Describeshow skapa en Azure-hanterad App som är tillgängligt via Marketplace."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/11/2017
ms.author: gauravbh; tomfitz
ms.openlocfilehash: baf456740bddd562391ed64d707f990c8921d710
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2017
---
# <a name="consume-azure-managed-applications-in-the-marketplace"></a>Använda Azure hanterade program i Marketplace

Enligt beskrivningen i den [hanteras Programöversikt](managed-application-overview.md) artikel, det finns två scenarier i slutpunkt till slutpunkt-upplevelsen. En är utgivare eller leverantören som vill skapa ett hanterat program för användning av kunder. Andra är slutkunden eller förbrukare som det hanterade programmet. Den här artikeln beskrivs i det andra scenariot. Beskriver hur du distribuerar ett hanterat program från Microsoft Azure Marketplace.

## <a name="create-from-the-marketplace"></a>Skapa från Marketplace

Distribuera ett hanterat program från Marketplace liknar distribuerar alla typer av resurser från Marketplace. 

I portalen, Välj **+ ny** och Sök efter typ av lösningen för att distribuera. Välj det du behöver från de tillgängliga alternativen.

![söklösningar](./media/managed-application-consume-marketplace/search-apps.png)

Granska sammanfattningen av programmet och välj **skapa**.

![Skapa hanterade program](./media/managed-application-consume-marketplace/create-marketplace-managed-app.png)

Precis som alla andra lösningar visas med fält för att ange värden för. De här fälten varierar beroende på vilken typ av hanterade program som du skapar. 

## <a name="view-support-information"></a>Visa information om support

Visa supportinformation för programmet när det hanterade programmet har distribuerats. Supportinformation visas i bladet hanterade program.

![Support](./media/managed-application-consume-marketplace/support.png)

## <a name="view-publisher-permissions"></a>Visa publisher behörigheter

Leverantören som hanterar ditt program har beviljats åtkomst till resurser. Om du vill se de behörigheterna, Välj **tillstånd**.

![Tillstånd](./media/managed-application-consume-marketplace/authorizations.png)

## <a name="next-steps"></a>Nästa steg

* Information om hur du publicerar ett hanterat program i Marketplace finns [Azure hanterade program i Marketplace](managed-application-author-marketplace.md).
* För att publicera hanterade program som bara är tillgängliga för användare i din organisation, se [skapa och publicera katalog hanteras tjänstprogrammet](managed-application-publishing.md).
* Om du vill använda hanterade program som bara är tillgängliga för användare i din organisation, se [använda en katalog hanteras tjänstprogrammet](managed-application-consumption.md).