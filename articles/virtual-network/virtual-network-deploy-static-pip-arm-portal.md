---
title: Skapa en virtuell dator med en statisk offentlig IP-adress - Azure-portalen | Microsoft Docs
description: Lär dig hur du skapar en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure portal.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: jdial
ms.openlocfilehash: 9b6db45e38267c70adef3f5a341b8b918b9e78fb
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714435"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Skapa en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure portal

Du kan skapa en virtuell dator med en statisk offentlig IP-adress. En offentlig IP-adress kan du kommunicera till en virtuell dator från internet. Tilldela en statisk offentlig IP-adress i stället för en dynamisk adress, så att adressen aldrig ändras. Läs mer om [Statiska offentliga IP-adresser](virtual-network-ip-addresses-overview-arm.md#allocation-method). Ändra en offentlig IP-adress som tilldelats till en befintlig virtuell dator från dynamisk till statisk, eller arbeta med privata IP-adresser i avsnittet [Lägg till, ändra eller ta bort IP-adresser](virtual-network-network-interface-addresses.md). Offentliga IP-adresser har en [nominell avgift tas ut](https://azure.microsoft.com/pricing/details/ip-addresses), och det finns en [gränsen](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) för antalet offentliga IP-adresser som du kan använda per prenumeration.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **Compute**, och välj sedan **Windows Server 2016 VM**, eller ett annat operativsystem som du väljer.
3. Ange eller välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVM|
    |Användarnamn| Ange ett valfritt användarnamn.|
    |Lösenord| Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Prenumeration| Välj din prenumeration.|
    |Resursgrupp| Välj **Använd befintlig** och sedan **myResourceGroup**.|
    |Plats| Välj **USA, östra**|

4. Välj en storlek för den virtuella datorn och sedan **Välj**.
5. Under **inställningar**väljer **offentliga IP-adressen**.
6. Ange *myPublicIpAddress*väljer **statiska**, och välj sedan **OK**, enligt följande bild:

   ![Välj statiskt](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Om den offentliga IP-adressen måste vara en standard-SKU, väljer **Standard** under **SKU**. Läs mer om [offentliga IP-adressen SKU: er](virtual-network-ip-addresses-overview-arm.md#sku). Om den virtuella datorn kommer att läggas till backend-poolen med en offentlig Azure Load Balancer, måste SKU: N för den virtuella datorns offentliga IP-adress matcha SKU för belastningsutjämnarens offentliga IP-adressen. Mer information finns i [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

6. Välj en port eller inga portar under **Välj offentliga inkommande portar**. Portalen 3389 är markerad för att aktivera fjärråtkomst till Windows Server-dator från internet. Du rekommenderas inte att öppna port 3389 från internet för produktionsarbetsbelastningar.

   ![Välj en port](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Acceptera standardinställningarna för återstående och välj **OK**.
8. På den **sammanfattning** väljer **skapa**. Det tar några minuter att distribuera den virtuella datorn.
9. När den virtuella datorn har distribuerats, ange *myPublicIpAddress* i sökrutan överst på portalen. När **myPublicIpAddress** visas i sökresultatet väljer du det.
10. Du kan visa den offentliga IP-adressen som är tilldelad, och som adressen tilldelas den **myVM** virtuell dator, enligt följande bild:

    ![Visa offentliga IP-adress](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure har tilldelats en offentlig IP-adress från adresser som används i den region som du skapade den virtuella datorn i. Du kan ladda ned listan över intervall (prefix) för [offentliga](https://www.microsoft.com/download/details.aspx?id=56519) Azure-moln och för Azure-moln för [amerikanska myndigheter](https://www.microsoft.com/download/details.aspx?id=57063), [Kina](https://www.microsoft.com/download/details.aspx?id=57062) eller [Tyskland](https://www.microsoft.com/download/details.aspx?id=57064).

11. Välj **Configuration** att bekräfta att tilldelningen är **statiska**.

    ![Visa offentliga IP-adress](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
Ändra inte inställningar för IP-adresser i den virtuella datorns operativsystem. Operativsystemet är inte medveten om Azure offentliga IP-adresser. Även om du kan lägga till privata IP-adressinställningarna för operativsystemet, vi rekommenderar att du inte gör det, såvida inte behövs, och inte förrän efter läsning [lägga till en privat IP-adress till ett operativsystem](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, skalningsuppsättningen och alla resurser som den innehåller:

1. Skriv *myResourceGroup* i **sökrutan** överst i portalen. När du ser **myResourceGroup** i sökresultatet väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Skriv *myResourceGroup* där du uppmanas att **skriva resursgruppens namn:** (Skriv resursgruppens namn) och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [offentliga IP-adresser](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) i Azure
- Mer information om alla [inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Läs mer om [privata IP-adresser](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) och tilldela en [statiska privata IP-adressen](virtual-network-network-interface-addresses.md#add-ip-addresses) till en Azure-dator
- Läs mer om hur du skapar [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuella datorer