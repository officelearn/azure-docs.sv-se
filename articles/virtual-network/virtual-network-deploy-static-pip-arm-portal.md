---
title: Skapa en virtuell dator med en statisk offentlig IP-adress - Azure-portal | Microsoft-dokument
description: Lär dig hur du skapar en virtuell dator med en statisk offentlig IP-adress med Azure-portalen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 66050c16f40e0a06117327ef53e3aae87d03c5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76043539"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Skapa en virtuell dator med en statisk offentlig IP-adress med Azure-portalen

Du kan skapa en virtuell dator med en statisk offentlig IP-adress. Med en offentlig IP-adress kan du kommunicera med en virtuell dator från Internet. Tilldela en statisk offentlig IP-adress i stället för en dynamisk adress för att säkerställa att adressen aldrig ändras. Läs mer om [statiska offentliga IP-adresser](virtual-network-ip-addresses-overview-arm.md#allocation-method). Information om hur du ändrar en offentlig IP-adress som tilldelats en befintlig virtuell dator från dynamisk till statisk eller för att arbeta med privata IP-adresser finns [i Lägga till, ändra eller ta bort IP-adresser](virtual-network-network-interface-addresses.md). Offentliga IP-adresser har en [nominell avgift](https://azure.microsoft.com/pricing/details/ip-addresses)och det finns en [gräns](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) för antalet offentliga IP-adresser som du kan använda per prenumeration.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **Beräkna**och välj sedan **Windows Server 2016 VM**eller ett annat operativsystem som du väljer.
3. Ange, eller välj, följande information, acceptera standardinställningarna för de återstående inställningarna och välj sedan **OK:**

    |Inställning|Värde|
    |---|---|
    |Namn|myVM (på)|
    |Användarnamn| Ange ett valfritt användarnamn.|
    |lösenord| Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Prenumeration| Välj din prenumeration.|
    |Resursgrupp| Välj **Använd befintlig** och sedan **myResourceGroup**.|
    |Location| Välj **USA, östra**|

4. Välj en storlek för den virtuella datorn och sedan **Välj**.
5. Under **Inställningar**väljer du **Offentlig IP-adress**.
6. Ange *myPublicIpAddress*, välj **Statisk**och välj sedan **OK**, som visas i följande bild:

   ![Välj statisk](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Om den offentliga IP-adressen måste vara en standard-SKU väljer du **Standard** under **SKU**. Läs mer om [SKU:er för offentlig IP-adress](virtual-network-ip-addresses-overview-arm.md#sku). Om den virtuella datorn läggs till i backend-poolen för en offentlig Azure Load Balancer måste SKU för den virtuella datorns offentliga IP-adress matcha SKU för belastningsutjämnarens offentliga IP-adress. Mer information finns i [Azure Load Balancer](../load-balancer/concepts-limitations.md#skus).

6. Välj en port eller inga portar under **Välj offentliga inkommande portar**. Portal 3389 är markerad för att möjliggöra fjärråtkomst till den virtuella windows server-datorn från internet. Att öppna port 3389 från internet rekommenderas inte för produktionsarbetsbelastningar.

   ![Välj en port](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Acceptera de återstående standardinställningarna och välj **OK**.
8. På sidan **Sammanfattning** väljer du **Skapa**. Den virtuella datorn tar några minuter att distribuera.
9. När den virtuella datorn har distribuerats anger du *myPublicIpAddress* i sökrutan högst upp på portalen. När **myPublicIpAddress** visas i sökresultaten markerar du den.
10. Du kan visa den offentliga IP-adressen som har tilldelats och att adressen är tilldelad den virtuella **datorn myVM,** som visas i följande bild:

    ![Visa offentlig IP-adress](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure tilldelade en offentlig IP-adress från adresser som används i den region som du skapade den virtuella datorn i. Du kan ladda ned listan över intervall (prefix) för [offentliga](https://www.microsoft.com/download/details.aspx?id=56519) Azure-moln och för Azure-moln för [amerikanska myndigheter](https://www.microsoft.com/download/details.aspx?id=57063), [Kina](https://www.microsoft.com/download/details.aspx?id=57062) eller [Tyskland](https://www.microsoft.com/download/details.aspx?id=57064).

11. Välj **Konfiguration** för att bekräfta att tilldelningen är **statisk**.

    ![Visa offentlig IP-adress](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Ändra inte IP-adressinställningarna i den virtuella datorns operativsystem. Operativsystemet känner inte till offentliga Azure-IP-adresser. Även om du kan lägga till privata IP-adressinställningar i operativsystemet rekommenderar vi att du inte gör det om det inte är nödvändigt, och inte förrän efter att ha läst [Lägg till en privat IP-adress i ett operativsystem](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, skalningsuppsättningen och alla resurser som den innehåller:

1. Skriv *myResourceGroup* i **sökrutan** överst i portalen. När du ser **myResourceGroup** i sökresultatet väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Skriv *myResourceGroup* i **SKRIV RESURSGRUPPSNAMNET:** och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [offentliga IP-adresser](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) i Azure
- Läs mer om alla [inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Läs mer om [privata IP-adresser](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) och tilldela en [statisk privat IP-adress](virtual-network-network-interface-addresses.md#add-ip-addresses) till en virtuell Azure-dator
- Läs mer om hur du skapar virtuella [Linux-](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Windows-datorer](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)