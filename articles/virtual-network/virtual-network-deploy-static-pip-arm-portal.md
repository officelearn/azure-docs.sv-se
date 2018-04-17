---
title: Skapa en virtuell dator med en statisk offentlig IP-adress - Azure-portalen | Microsoft Docs
description: Lär dig hur du skapar en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure portal.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ebbc31016417dd6fecb91f6cdc0bba3fcf0a3f50
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Skapa en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure portal

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [Mall](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (klassisk)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md). Den här artikeln täcker distributionsmodell hanteraren för filserverresurser, som Microsoft rekommenderar för de flesta nya distributioner i stället för den klassiska distributionsmodellen.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Skapa en virtuell dator med en statisk offentlig IP-adress

Om du vill skapa en virtuell dator med en statisk offentlig IP-adress i Azure-portalen, gör du följande:

1. Navigera till [Azure-portalen](https://portal.azure.com) från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Klicka på det övre vänstra hörnet av portalen **skapar du en resurs**>>**Compute**>**Windows Server 2012 R2 Datacenter**.
3. I den **Välj en distributionsmodell** väljer **Resource Manager** och på **skapa**.
4. I den **grunderna** , ange information om Virtuellt enligt nedan, och klickar sedan på **OK**.
   
    ![Azure portal – grunderna](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. I den **välja en storlek** rutan klickar du på **A1 Standard** som följer och klicka sedan på **Välj**.
   
    ![Azure portal – Välj en storlek](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. I den **inställningar** rutan klickar du på **offentliga IP-adressen**, i den **skapa offentlig IP-adress** rutan under **tilldelning**, klickar du på  **Statisk** enligt följande. Och klicka sedan på **OK**.
   
    ![Azure portal – skapa offentlig IP-adress](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. I den **inställningar** rutan klickar du på **OK**.
8. Granska de **sammanfattning** rutan som följer och klicka sedan på **OK**.
   
    ![Azure portal – skapa offentlig IP-adress](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Lägg märke till den nya ikonen på instrumentpanelen.
   
    ![Azure portal – skapa offentlig IP-adress](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. När den virtuella datorn har skapats kan den **inställningar** fönstret visas på följande sätt:
    
    ![Azure portal – skapa offentlig IP-adress](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

## <a name="set-ip-addresses-within-the-operating-system"></a>Ange IP-adresser inom operativsystemet

Du bör aldrig manuellt tilldela den offentliga IP-adress som tilldelats en virtuell Azure-dator i den virtuella datorns operativsystem. Vi rekommenderar att du inte statiskt tilldelar privata IP-Adressen som tilldelats den virtuella Azure-datorn i operativsystemet på en virtuell dator, om nödvändigt, t.ex när [tilldela flera IP-adresser till en Windows-VM](virtual-network-multiple-ip-addresses-portal.md). Om du manuellt anger den privata IP-adressen i operativsystemet, kontrollera att det är samma adress som den privata IP-adress som tilldelats i Azure [nätverksgränssnittet](virtual-network-network-interface-addresses.md#change-ip-address-settings), eller du kan förlora anslutningen till den virtuella datorn. Lär dig mer om [privata IP-adressen](virtual-network-network-interface-addresses.md#private) inställningar.

## <a name="next-steps"></a>Nästa steg

All nätverkstrafik kan flöda till och från den virtuella datorn skapas i den här artikeln. Du kan definiera inkommande och utgående säkerhetsregler inom en nätverkssäkerhetsgrupp som begränsar trafiken kan flöda till och från nätverksgränssnittet undernätet eller båda. Läs mer om nätverkssäkerhetsgrupper i [nätverk Säkerhetsöversikt för gruppen](security-overview.md).