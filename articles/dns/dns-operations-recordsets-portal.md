---
title: Hantera uppsättningar av DNS-poster och poster med Azure DNS | Microsoft Docs
description: Azure DNS ger möjlighet att hantera uppsättningar av DNS-poster och poster om värd för din domän.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 18ed44a1-7bfe-454f-964e-922ad978264a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: kumud
ms.openlocfilehash: da7d2118a0fb6bc5004856d994a01a932094ca45
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Hantera DNS-poster och postuppsättningar med hjälp av Azure portal

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Den här artikeln visar hur du hanterar postuppsättningar och posterna för DNS-zonen med hjälp av Azure portal.

Det är viktigt att förstå skillnaden mellan DNS-postuppsättningar och enskilda DNS-poster. En postuppsättning är en uppsättning poster i en zon som har samma namn och är av samma typ. Mer information finns i [skapa DNS-postuppsättningar och poster med hjälp av Azure portal](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Skapa en ny postuppsättning och registrera

Om du vill skapa en postuppsättning i Azure portal finns [skapa DNS-poster med hjälp av Azure portal](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>Visa en postuppsättning

1. I Azure-portalen går du till den **DNS-zonen** bladet.
2. Sök efter uppsättningen av poster och markera den. Detta öppnar egenskaperna för uppsättningen av poster.

    ![Sök efter en postuppsättning](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Lägg till en ny post i en postuppsättning

Du kan lägga till upp till 20 poster en postuppsättning. En postuppsättning får inte innehålla två identiska poster. Tom postuppsättningar (med noll poster) kan skapas, men visas inte i Azure DNS-namnservrar. Postuppsättningar av typen CNAME kan som mest innehålla en post.

1. På den **postuppsättning egenskaper** bladet för din DNS-zonen, klickar du på postuppsättningen som du vill lägga till en post.

    ![Välj en uppsättning poster](./media/dns-operations-recordsets-portal/selectset500.png)

2. Ange egenskaper för postuppsättning genom att fylla i fälten.

    ![Lägga till en post](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Klicka på **spara** längst upp på bladet för att spara dina inställningar. Stäng bladet.
4. I hörnet ser du att posten sparas.

    ![Spara uppsättningen av poster](./media/dns-operations-recordsets-portal/saving150.png)

När posten har sparats, värdena på den **DNS-zonen** bladet visar den nya posten.

## <a name="update-a-record"></a>Uppdatera en post

När du uppdaterar en post i en befintlig postuppsättning beror fälten som du kan uppdatera på vilken typ av post du arbetar med.

1. På den **postuppsättning egenskaper** bladet för postuppsättningen, Sök efter posten.
2. Ändra posten. När du ändrar en post kan du ändra tillgängliga inställningar för posten. I följande exempel visas den **IP-adress** är markerat och IP-adressen håller på att ändras.

    ![Ändra en post](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Klicka på **spara** längst upp på bladet för att spara dina inställningar. I det övre högra hörnet ser du meddelandet som posten har sparats.

    ![Uppsättningen av poster](./media/dns-operations-recordsets-portal/saved150.png)

När posten har sparats, ange värden för posten för den **DNS-zonen** bladet visar den uppdaterade posten.

## <a name="remove-a-record-from-a-record-set"></a>Ta bort en post från en postuppsättning

Du kan använda Azure-portalen för att ta bort poster från en postuppsättning. Observera att den sista posten från en postuppsättning inte bort uppsättningen av poster.

1. På den **postuppsättning egenskaper** bladet för postuppsättningen, Sök efter posten.
2. Klicka på posten som du vill ta bort. Välj sedan **ta bort**.

    ![Ta bort en post](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Klicka på **spara** längst upp på bladet för att spara dina inställningar.
4. När den har tagits bort, värden för posten på den **DNS-zonen** bladet visar borttagningen.

## <a name="delete"></a>Ta bort en uppsättning poster

1. På den **postuppsättning egenskaper** bladet för postuppsättningen, klickar du på **ta bort**.

    ![Ta bort en uppsättning poster](./media/dns-operations-recordsets-portal/deleterecordset500.png)

2. Ett meddelande visas som frågar om du vill ta bort uppsättningen av poster.
3. Kontrollera att namnet matchar uppsättningen av poster som du vill ta bort och klicka sedan på **Ja**.
4. På den **DNS-zonen** bladet, kontrollera att uppsättningen av poster visas inte längre.

## <a name="work-with-ns-and-soa-records"></a>Arbeta med NS och SOA-poster

NS och SOA-poster som skapas automatiskt hanteras annorlunda från andra typer av poster.

### <a name="modify-soa-records"></a>Ändra SOA-poster

Du kan inte lägga till eller ta bort poster från den automatiskt skapade SOA-postuppsättning på zonens apex (namn = ”@”). Men du kan ändra någon av parametrarna i SOA-post (utom ”värd”) och postuppsättning TTL-värde.

### <a name="modify-ns-records-at-the-zone-apex"></a>Ändra NS-poster på zonens apex

NS-postuppsättning på zonens apex skapas automatiskt med varje DNS-zon. Den innehåller namnen på Azure DNS-namnservrar tilldelas zonen.

Du kan lägga till ytterligare servrar till den här NS-post inställda på en värd domäner med mer än en DNS-leverantör har stöd ett namn. Du kan också ändra TTL-värde och metadata för den här postuppsättningen. Du kan inte ta bort eller ändra de i förväg Azure DNS-namnservrarna.

Observera att detta gäller endast NS-postuppsättning på zonens apex. Andra NS postuppsättningar i zonen (som används för att delegera underordnade zoner) kan ändras utan begränsning.

### <a name="delete-soa-or-ns-record-sets"></a>Ta bort SOA- eller NS postuppsättningar

Du kan inte ta bort SOA och NS-post anger på zonens apex (namn = ”@”) som skapas automatiskt när zonen skapas. De tas bort automatiskt när du tar bort zonen.

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure DNS finns i [översikt över Azure DNS](dns-overview.md).
* Mer information om hur du automatiserar DNS finns [skapar DNS-zoner och postuppsättningar med .NET SDK](dns-sdk.md).
* Mer information om omvänd DNS-poster finns [översikt över omvänd DNS- och support i Azure](dns-reverse-dns-overview.md).
