---
title: "Skapa en intern belastningsutjämnare – Azure-portalen | Microsoft Docs"
description: "Lär dig hur du skapar en intern belastningsutjämnare i Resource Manager med hjälp av Azure Portal"
services: load-balancer
documentationcenter: na
author: KumudD
manager: jennoc
editor: 
tags: azure-service-management
ms.assetid: 1ac14fb9-8d14-4892-bfe6-8bc74c48ae2c
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 5b983ca9ff28aac7f0e0501f353c48deeb6adcd5
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="create-an-internal-load-balancer-in-the-azure-portal"></a>Skapa en intern belastningsutjämnare i Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Mall](../load-balancer/load-balancer-get-started-ilb-arm-template.md)


[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="get-started-creating-an-internal-load-balancer-using-azure-portal"></a>Kom igång med att skapa en intern belastningsutjämnare med hjälp av Azure Portal

Använd följande steg för att skapa en intern belastningsutjämnare från Azure-portalen.

1. Öppna en webbläsare, navigera till [Azure Portal](http://portal.azure.com) och logga in med ditt Azure-konto.
2. Längst upp till vänster på skärmen klickar du på **Nytt** > **Nätverk** > **Belastningsutjämnare**.
3. På bladet **Skapa belastningsutjämnare** skriver du ett **namn** för belastningsutjämnaren.
4. Under **Schema** klickar du på **Intern**.
5. Klicka på **Virtuellt nätverk** och välj sedan det virtuella nätverket där du vill skapa belastningsutjämnaren.

   > [!NOTE]
   > Om du inte hittar det virtuella nätverk som du vill använda, kontrollerar du vilken **plats** du använder för belastningsutjämnaren och gör ändringar därefter.

6. Klicka på **Undernät** och välj sedan undernätet där du vill skapa belastningsutjämnaren.
7. Under **IP-adresstilldelning** klickar du antingen på **Dynamisk** eller **Statisk**, beroende på om du vill att IP-adressen för belastningsutjämnaren ska vara fast (statisk) eller inte.

   > [!NOTE]
   > Om du väljer att använda en statisk IP-adress måste du ange en adress för belastningsutjämnaren.

8. Under **Resursgrupp** anger du antingen namnet på en ny resursgrupp för belastningsutjämnaren eller så klickar du på **Välj befintlig** och väljer en befintlig resursgrupp.
9. Klicka på **Skapa**.

## <a name="configure-load-balancing-rules"></a>Konfigurera belastningsutjämningsregler

När du har skapat en belastningsutjämnare navigerar du till belastningsutjämningsresursen för att konfigurera den.
Konfigurera en serverdelsadresspool och en avsökning innan du konfigurerar en belastningsutjämningsregel.

### <a name="step-1-configure-a-backend-pool"></a>Steg 1: Konfigurera en serverdelspool

1. Klicka på **Bläddra** > **Belastningsutjämnare** i Azure-portalen och klicka sedan på belastningsutjämnaren som du skapade tidigare.
2. På bladet **Inställningar** klickar du på **Serverdelspooler**.
3. På bladet **Serverdelspooler** klickar du på **Lägg till**.
4. På bladet **Lägg till serverdelspool** anger du ett **namn** för serverdelspoolen och sedan klickar du på **OK**.

### <a name="step-2-configure-a-probe"></a>Steg 2: Konfigurera en avsökning

1. Klicka på **Bläddra** > **Belastningsutjämnare** i Azure-portalen och klicka sedan på belastningsutjämnaren som du skapade tidigare.
2. På bladet **Inställningar** klickar du på **Avsökningar**.
3. På bladet **Avsökningar** klickar du på **Lägg till**.
4. På bladet **Lägg till avsökning** anger du ett **namn** för avsökningen.
5. Under **Protokoll** väljer du **HTTP** (för webbplatser) eller **TCP** (för andra TCP-baserade program).
6. Under **Port** anger du porten som ska användas vid åtkomst till avsökningen.
7. Under **Sökväg** (endast för HTTP-avsökningar) anger du sökvägen som används som avsökning.
8. Under **Intervall** anger du hur ofta avsökning av programmet ska ske.
9. Under **Unhealthy threshold** (Tröskelvärde för felstatus) anger du hur många försök som får misslyckas innan den virtuella datorn på serversidan markeras som felaktig.
10. Klicka på **OK** för att skapa avsökningen.

### <a name="step-3-configure-load-balancing-rules"></a>Steg 3: Konfigurera belastningsutjämningsregler

1. Klicka på **Bläddra** > **Belastningsutjämnare** i Azure-portalen och klicka sedan på belastningsutjämnaren som du skapade tidigare.
2. På bladet **Inställningar** klickar du på **Belastningsutjämningsregler**.
3. På bladet **Belastningsutjämningsregler** klickar du på **Lägg till**.
4. På bladet **Add load balancing rule** (Lägg till belastningsutjämningsregel) anger du ett **namn** för regeln.
5. Under **Protokoll** väljer du **HTTP** (för webbplatser) eller **TCP** (för andra TCP-baserade program).
6. Under **Port** anger du den port som klienter ansluter till i belastningsutjämnaren.
7. Under **Serverdelsport** anger du porten som ska användas i serverdelspoolen (vanligtvis är belastningsutjämnarporten och serverdelsporten densamma).
8. Under **Serverdelspool** väljer du den serverdelspool du skapade tidigare.
9. Under **Sessionspermanens** väljer du hur du vill att sessioner ska sparas.
10. Under **Timeout för inaktivitet (minuter)** anger du tidsgränsen för inaktivitet.
11. Under **Flytande IP (direkt serverreturnering)** klickar du på **Inaktiverad** eller **Aktiverad**.
12. Klicka på **OK**.

## <a name="next-steps"></a>Nästa steg

[Konfigurera ett distributionsläge för belastningsutjämnare](load-balancer-distribution-mode.md)

[Konfigurera timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md)

