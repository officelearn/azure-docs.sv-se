---
title: Hantera DNS-postuppsättningar och poster med Azure DNS
description: Azure DNS ger möjlighet att hantera DNS-postuppsättningar och poster när du har din domän.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 10/6/2018
ms.author: victorh
ms.openlocfilehash: 891adfacde6e46b1d8fe8e2f6b5fb39c90ce27a0
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48853716"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Hantera DNS-poster och postuppsättningar med hjälp av Azure portal

Den här artikeln visar hur du hanterar postuppsättningar och poster för din DNS-zon med hjälp av Azure portal.

Det är viktigt att förstå skillnaden mellan DNS-postuppsättningar och enskilda DNS-poster. En postuppsättning är en samling av poster i en zon som har samma namn och är samma sak. Mer information finns i [skapa DNS-postuppsättningar och poster med hjälp av Azure portal](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Skapa en ny postuppsättning och poster

Om du vill skapa en post i Azure-portalen, se [skapa DNS-poster med hjälp av Azure portal](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>Visa en uppsättning av poster

1. I Azure-portalen går du till den **DNS-zon** bladet.
2. Sök efter postuppsättningen och markera den. Detta öppnar egenskaperna för uppsättningen av poster.

    ![Sök efter en postuppsättning](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Lägga till en ny post i en postuppsättning

Du kan lägga till upp till 20 poster valfri uppsättning av poster. En postuppsättning får inte innehålla två identiska poster. Tom postuppsättningar (med noll-poster) kan skapas, men visas inte i Azure DNS-namnservrarna. Postuppsättningar av typen CNAME får högst innehålla en post.

1. På den **postuppsättning egenskaper** bladet för DNS-zonen, klickar du på uppsättningen av poster som du vill lägga till en post till.

    ![Välj en uppsättning av poster](./media/dns-operations-recordsets-portal/selectset500.png)

2. Ange egenskaper postuppsättningens genom att fylla i fälten.

    ![Lägga till en post](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Klicka på **spara** överst på bladet för att spara dina inställningar. Stäng bladet.
4. I hörnet ser du att posten sparas.

    ![Sparar uppsättningen av poster](./media/dns-operations-recordsets-portal/saving150.png)

När posten har sparats, värdena på den **DNS-zon** bladet visas den nya posten.

## <a name="update-a-record"></a>Uppdatera en post

När du uppdaterar en post i en befintlig uppsättning av poster, beror vilka fält som du kan uppdatera på vilken typ av post du arbetar med.

1. På den **postuppsättning egenskaper** bladet för postuppsättningen, Sök efter posten.
2. Ändra posten. När du ändrar en post, kan du ändra de tillgängliga inställningarna för posten. I följande exempel visas den **IP-adress** fält är markerad och IP-adressen håller på att ändras.

    ![Ändra en post](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Klicka på **spara** överst på bladet för att spara dina inställningar. I det övre högra hörnet ser du meddelandet som posten har sparats.

    ![Uppsättning av poster sparades](./media/dns-operations-recordsets-portal/saved150.png)

När posten har sparats, värdet för posten in på den **DNS-zon** bladet visas den uppdaterade posten.

## <a name="remove-a-record-from-a-record-set"></a>Ta bort en post från en postuppsättning

Du kan använda Azure-portalen för att ta bort poster från en postuppsättning. Observera att om du tar bort den sista posten från en postuppsättning inte raderar postuppsättningen.

1. På den **postuppsättning egenskaper** bladet för postuppsättningen, Sök efter posten.
2. Klicka på den post som du vill ta bort. Välj sedan **ta bort**.

    ![Ta bort en post](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Klicka på **spara** överst på bladet för att spara dina inställningar.
4. När den har tagits bort, värden för posten på den **DNS-zon** bladet visas borttagningen.

## <a name="delete"></a>Ta bort en postuppsättning

1. På den **postuppsättning egenskaper** bladet för postuppsättningen, klickar du på **ta bort**.

    ![Ta bort en postuppsättning](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Ett meddelande visas som frågar om du vill ta bort postuppsättningen.
3. Kontrollera att namnet matchar den uppsättning av poster som du vill ta bort och klicka sedan på **Ja**.
4. På den **DNS-zon** bladet Kontrollera postuppsättningen inte längre visas.

## <a name="work-with-ns-and-soa-records"></a>Arbeta med NS och SOA-poster

NS och SOA-poster som skapas automatiskt hanteras annorlunda från andra posttyper.

### <a name="modify-soa-records"></a>Ändra SOA-poster

Du kan inte lägga till eller ta bort poster från den automatiskt skapade SOA-postuppsättning på zonens apex (namn = ”\@”). Men du kan ändra någon av parametrarna i SOA-posten (utom ”värd”) och postuppsättningens TTL-värde.

### <a name="modify-ns-records-at-the-zone-apex"></a>Ändra NS-poster i basdomänen

NS-postuppsättning på zonens apex skapas automatiskt med varje DNS-zon. Den innehåller namnen på Azure DNS-namnservrarna som tilldelats i zonen.

Du kan lägga till ytterligare servrar till den här NS-post anger för att stödja delad hosting domäner med fler än en DNS-leverantör ett namn. Du kan också ändra TTL-värde och metadata för den här uppsättningen av poster. Du kan inte ta bort eller ändra förifyllda Azure DNS-namnservrarna.

Observera att detta gäller endast för NS-postuppsättning på zonens apex. Andra NS postuppsättningar i din zon (som används för att delegera underordnade zoner) kan ändras utan begränsning.

### <a name="delete-soa-or-ns-record-sets"></a>Ta bort SOA eller NS postuppsättningar

Du kan inte ta bort SOA och NS-post anger på zonens apex (namn = ”\@”) som skapas automatiskt när zonen skapas. De tas bort automatiskt när du tar bort zonen.

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure DNS finns i den [översikt över Azure DNS](dns-overview.md).
* Läs mer om hur du automatiserar DNS [skapa DNS-zoner och postuppsättningar med .NET SDK](dns-sdk.md).
* Läs mer om omvänd DNS-poster, [översikt över omvänd DNS- och stöd i Azure](dns-reverse-dns-overview.md).
* Läs mer om Azure DNS alias poster [Azure DNS alias poster översikt](dns-alias.md).
