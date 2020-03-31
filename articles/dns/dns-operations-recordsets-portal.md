---
title: Hantera DNS-postuppsättningar och -poster med Azure DNS
description: Azure DNS ger möjlighet att hantera DNS-postuppsättningar och poster när du är värd för din domän.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/6/2018
ms.author: rohink
ms.openlocfilehash: 4012b32eb2684126b8dc64b9e86bf35a016e9fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936845"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Hantera DNS-poster och postuppsättningar med hjälp av Azure-portalen

Den här artikeln visar hur du hanterar postuppsättningar och poster för DIN DNS-zon med hjälp av Azure-portalen.

Det är viktigt att förstå skillnaden mellan DNS-postuppsättningar och enskilda DNS-poster. En postuppsättning är en samling poster i en zon som har samma namn och har samma typ. Mer information finns i [Skapa DNS-postuppsättningar och poster med hjälp av Azure-portalen](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Skapa en ny postuppsättning och post

Information om hur du skapar en postuppsättning i Azure-portalen finns i [Skapa DNS-poster med hjälp av Azure-portalen](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>Visa en postuppsättning

1. Gå till **DNS-zonbladet** i Azure-portalen.
2. Sök efter postuppsättningen och välj den. Då öppnas egenskaperna för postuppsättningen.

    ![Sök efter en postuppsättning](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Lägga till en ny post i en postuppsättning

Du kan lägga till upp till 20 poster i valfri postuppsättning. En postuppsättning får inte innehålla två identiska poster. Tomma postuppsättningar (med noll poster) kan skapas, men visas inte på Azure DNS-namnservrarna. Postuppsättningar av typen CNAME kan innehålla högst en post.

1. Klicka på den postuppsättning som du vill lägga till en post i i egenskapsbladet För **postuppsättningen** för DNS-zonen.

    ![Välj en postuppsättning](./media/dns-operations-recordsets-portal/selectset500.png)

2. Ange postuppsättningsegenskaperna genom att fylla i fälten.

    ![Lägga till en post](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Klicka på **Spara** högst upp på bladet för att spara inställningarna. Stäng sedan bladet.
4. I hörnet ser du att posten sparar.

    ![Spara postuppsättning](./media/dns-operations-recordsets-portal/saving150.png)

När posten har sparats återspeglar värdena på **DNS-zonbladet** den nya posten.

## <a name="update-a-record"></a>Uppdatera en post

När du uppdaterar en post i en befintlig postuppsättning beror de fält som du kan uppdatera på vilken typ av post du arbetar med.

1. Sök efter posten i **egenskapsbladet För postuppsättning** för posten.
2. Ändra posten. När du ändrar en post kan du ändra de tillgängliga inställningarna för posten. I följande exempel väljs **IP-adressfältet** och IP-adressen håller på att ändras.

    ![Ändra en post](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Klicka på **Spara** högst upp på bladet för att spara inställningarna. I det övre högra hörnet visas meddelandet om att posten har sparats.

    ![Sparad postuppsättning](./media/dns-operations-recordsets-portal/saved150.png)

När posten har sparats återspeglar värdena för posten som angetts på **DNS-zonbladet** den uppdaterade posten.

## <a name="remove-a-record-from-a-record-set"></a>Ta bort en post från en postuppsättning

Du kan använda Azure-portalen för att ta bort poster från en postuppsättning. Observera att om du tar bort den sista posten från en postuppsättning tas inte postuppsättningen bort.

1. Sök efter posten i **egenskapsbladet För postuppsättning** för posten.
2. Klicka på den post som du vill ta bort. Välj sedan **Ta bort**.

    ![Ta bort en post](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Klicka på **Spara** högst upp på bladet för att spara inställningarna.
4. När posten har tagits bort återspeglar värdena för posten på **DNS-zonbladet** borttagningen.

## <a name="delete-a-record-set"></a><a name="delete"></a>Ta bort en postuppsättning

1. Klicka på **Ta bort**i egenskapsbladet **För postuppsättning** för postuppsättningen .

    ![Ta bort en postuppsättning](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Ett meddelande visas som frågar om du vill ta bort postuppsättningen.
3. Kontrollera att namnet matchar den postuppsättning som du vill ta bort och klicka sedan på **Ja**.
4. Kontrollera att postuppsättningen inte längre är synlig på **DNS-zonbladet.**

## <a name="work-with-ns-and-soa-records"></a>Arbeta med NS- och SOA-poster

NS- och SOA-poster som skapas automatiskt hanteras på ett annat sätt än andra posttyper.

### <a name="modify-soa-records"></a>Ändra SOA-poster

Du kan inte lägga till eller ta bort poster från den automatiskt\@skapade SOA-postuppsättningen vid zonapexen (namn = " "). Du kan dock ändra någon av parametrarna i SOA-posten (förutom "Host") och posten som TTL.

### <a name="modify-ns-records-at-the-zone-apex"></a>Ändra NS-poster i zonsponsen

NS-posten som anges vid zonapexen skapas automatiskt med varje DNS-zon. Den innehåller namnen på Azure DNS-namnservrar som tilldelats zonen.

Du kan lägga till ytterligare namnservrar i den här NS-postuppsättningen för att stödja samhostingdomäner med mer än en DNS-provider. Du kan också ändra TTL och metadata för den här postuppsättningen. Du kan dock inte ta bort eller ändra de förifyllda Azure DNS-namnservrarna.

Observera att detta endast gäller för NS-posten som anges vid zonsponsan. Andra NS-postuppsättningar i zonen (som används för att delegera underordnade zoner) kan ändras utan begränsning.

### <a name="delete-soa-or-ns-record-sets"></a>Ta bort SOA- eller NS-postuppsättningar

Du kan inte ta bort SOA- och NS-postuppsättningarna vid zonapexen (namn = "\@") som skapas automatiskt när zonen skapas. De tas bort automatiskt när du tar bort zonen.

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure DNS finns i [Azure DNS översikt](dns-overview.md).
* Mer information om hur du automatiserar DNS finns i [Skapa DNS-zoner och postuppsättningar med .NET SDK](dns-sdk.md).
* Mer information om omvända DNS-poster finns i [Översikt över omvänd DNS och support i Azure](dns-reverse-dns-overview.md).
* Mer information om Azure DNS-aliasposter finns i [översikt över Azure DNS-aliasposter](dns-alias.md).
