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
ms.openlocfilehash: 524293f9a1ded73ee7cb6ba4f53208a9f9c54ffa
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38670992"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Skapa en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure portal

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (klassisk)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md). Den här artikeln beskriver Resource Manager-distributionsmodellen, som Microsoft rekommenderar för de flesta nya distributioner i stället för den klassiska distributionsmodellen.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Skapa en virtuell dator med en statisk offentlig IP-adress

Om du vill skapa en virtuell dator med en statisk offentlig IP-adress i Azure-portalen gör du följande:

1. Navigera till [Azure-portalen](https://portal.azure.com) från en webbläsare och logga in med ditt Azure-konto vid behov.
2. I det övre vänstra hörnet i portalen klickar du på **skapa en resurs**>>**Compute**>**Windows Server 2012 R2 Datacenter**.
3. I den **Välj en distributionsmodell** väljer **Resource Manager** och klicka på **skapa**.
4. I den **grunderna** , anger du information för virtuell dator på följande sätt och klicka sedan på **OK**.
   
    ![Azure portal – grunderna](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. I den **väljer du en storlek** fönstret klickar du på **Standard A1** som följer och klicka sedan på **Välj**.
   
    ![Azure portal – Välj en storlek](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. I den **inställningar** fönstret klickar du på **offentliga IP-adressen**, i den **skapa offentlig IP-adress** fönstret under **tilldelning**, klickar du på  **Statisk** på följande sätt. Och klicka sedan på **OK**.
   
    ![Azure portal – skapa offentlig IP-adress](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. I den **inställningar** fönstret klickar du på **OK**.
8. Granska den **sammanfattning** fönstret som följer och klicka sedan på **OK**.
   
    ![Azure portal – skapa offentlig IP-adress](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Lägg märke till den nya panelen på instrumentpanelen.
   
    ![Azure portal – skapa offentlig IP-adress](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. När den virtuella datorn har skapats kan den **inställningar** fönstret visas på följande sätt:
    
    ![Azure portal – skapa offentlig IP-adress](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

## <a name="set-ip-addresses-within-the-operating-system"></a>Ange IP-adresser i operativsystemet

Du bör aldrig manuellt tilldela offentliga IP-adress som tilldelats till en Azure virtuell dator i den virtuella datorns operativsystem. Vi rekommenderar att du inte statiskt tilldelar privat IP-adress som tilldelats virtuella Azure-datorer i operativsystemet på en virtuell dator, om inte behövs, t.ex när [tilldela flera IP-adresser till en virtuell Windows-dator](virtual-network-multiple-ip-addresses-portal.md). Om du manuellt anger den privata IP-adressen i operativsystemet, kontrollera att det är samma adress som den privata IP-adress som tilldelats Azure [nätverksgränssnittet](virtual-network-network-interface-addresses.md#change-ip-address-settings), eller du kan förlora anslutningen till den virtuella datorn. Läs mer om [privata IP-adressen](virtual-network-network-interface-addresses.md#private) inställningar.

## <a name="next-steps"></a>Nästa steg

All nätverkstrafik kan flöda till och från den virtuella datorn skapas i den här artikeln. Du kan definiera inkommande och utgående säkerhetsregler inom en nätverkssäkerhetsgrupp som begränsar trafiken kan flöda till och från ett nätverksgränssnitt, undernätet eller båda. Mer information om nätverkssäkerhetsgrupper finns [översikt över Network security group](security-overview.md).