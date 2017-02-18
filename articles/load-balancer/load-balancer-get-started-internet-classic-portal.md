---
title: "Skapa en Internetaktiverad belastningsutjämnare – den klassiska Azure-portalen | Microsoft Docs"
description: "Lär dig hur du skapar en Internetuppkopplad belastningsutjämnare i den klassiska distributionsmodellen med hjälp av den klassiska Azure-portalen"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: fa3e93c0-968a-472d-a17c-65665c050db2
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: a022154f5eca6de2d2dbfc1b9aa30d2ea0a7d650

---

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-portal"></a>Komma igång med att skapa en Internetuppkopplad belastningsutjämnare (klassisk) på den klassiska Azure-portalen

> [!div class="op_single_selector"]
> * [Klassisk Azure-portal](../load-balancer/load-balancer-get-started-internet-classic-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Innan du börjar arbeta med Azure-resurser är det viktigt att du vet att Azure för närvarande har två distributionsmodeller: Azure Resource Manager och klassisk. Se till att du förstår [distributionsmodeller och verktyg](../azure-classic-rm.md) innan du börjar arbeta med Azure-resurser. Du kan granska dokumentationen för olika verktyg genom att klicka på flikarna överst i den här artikeln. Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också läsa artikeln om [hur du skapar en Internetuppkopplad belastningsutjämnare med hjälp av Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-an-internet-facing-load-balancer-for-virtual-machines"></a>Konfigurera en Internetuppkopplad belastningsutjämnare för virtuella datorer

För att kunna belastningsutjämna nätverkstrafik från Internet på de virtuella datorerna i en molntjänst måste du skapa en belastningsutjämnad uppsättning. I den här proceduren förutsätter vi att du redan har skapat de virtuella datorerna och att alla finns i samma molntjänst.

**Så här konfigurerar du en belastningsutjämnad uppsättning för virtuella datorer**

1. Klicka på **Virtuella datorer** på den klassiska Azure-portalen och klicka sedan på namnet på en virtuell dator i den belastningsutjämnade uppsättningen.
2. Klicka på **Slutpunkter** och sedan på **Lägg till**.
3. Klicka på högerpilen på sidan **Add an endpoint to a virtual machine** (Lägg till en slutpunkt för en virtuell dator).
4. På sidan **Specify the details of the endpoint** (Ange information om slutpunkten):

   * I **Namn** skriver du ett namn för slutpunkten eller väljer namnet i listan över fördefinierade slutpunkter för vanliga protokoll.
   * I **Protokoll** väljer du det protokoll som krävs av typen av slutpunkt, antingen TCP eller UDP.
   * I **Public Port and Private Port** (Offentlig port och privat port) anger du de portnummer som du vill att den virtuella datorn ska använda. Du kan använda den privata porten och brandväggsregler på den virtuella datorn för att omdirigera trafik på det sätt som passar dig. Den privata porten kan vara samma som den offentliga porten. För en slutpunkt för webbtrafik (HTTP) kan du till exempel tilldela port 80 till både den offentliga och privata porten.

5. Välj **Skapa en belastningsutjämnad uppsättning** och klicka sedan på högerpilen.
6. På sidan **Configure the load-balanced set** (Konfigurera den belastningsutjämnade uppsättningen) skriver du ett namn för den belastningsutjämnade uppsättningen och tilldelar värdena för avsökningsbeteendet i Azure Load Balancer. Load Balancer använder avsökningar för att avgöra om de virtuella datorerna i den belastningsutjämnade uppsättningen är tillgängliga för att ta emot inkommande trafik.
7. Klicka på kryssmarkeringen för att skapa den belastningsutjämnade slutpunkten. **Ja** visas i kolumnen **Load-balanced set name** (Namn på belastningsutjämnad uppsättning) på sidan **Slutpunkter** för den virtuella datorn.
8. På portalen klickar du på **Virtuella datorer**, klickar på namnet på en ytterligare virtuell dator i den belastningsutjämnade uppsättningen, klickar på **Slutpunkter** och sedan på **Lägg till**.
9. På sidan **Add an endpoint to a virtual machine** (Lägg till en slutpunkt för en virtuell dator) klickar du på **Add endpoint to an existing load-balanced set** (Lägg till en slutpunkt i en befintlig belastningsutjämnad uppsättning), markerar namnet på den belastningsutjämnade uppsättningen och klickar sedan på högerpilen.
10. På sidan **Specify the details of the endpoint** (Ange information om slutpunkten) skriver du ett namn för slutpunkten och klickar sedan på kryssmarkeringen.

Upprepa steg 8–10 om du vill lägga till fler virtuella datorer i den belastningsutjämnade uppsättningen.

## <a name="next-steps"></a>Nästa steg

[Komma igång med att konfigurera en intern belastningsutjämnare](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurera ett distributionsläge för belastningsutjämnare](load-balancer-distribution-mode.md)

[Konfigurera timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md)



<!--HONumber=Jan17_HO4-->


