---
title: Skapa en virtuell dator med en statisk offentlig IP-adress – Azure Portal | Microsoft Docs
description: Lär dig hur du skapar en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure Portal.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: allensu
ms.openlocfilehash: 745b12e2278c487ed49a9d2d726a760a7df8f276
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84703175"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Skapa en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure Portal

Du kan skapa en virtuell dator med en statisk offentlig IP-adress. Med en offentlig IP-adress kan du kommunicera med en virtuell dator från Internet. Tilldela en statisk offentlig IP-adress i stället för en dynamisk adress för att säkerställa att adressen aldrig ändras. Läs mer om [statiska offentliga IP-adresser](virtual-network-ip-addresses-overview-arm.md#allocation-method). Om du vill ändra en offentlig IP-adress som tilldelats till en befintlig virtuell dator från dynamisk till statisk, eller om du vill arbeta med privata IP-adresser, se [Lägg till, ändra eller ta bort IP-](virtual-network-network-interface-addresses.md)adresser. Offentliga IP-adresser har en [nominell avgift](https://azure.microsoft.com/pricing/details/ip-addresses)och det finns en [gräns](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) för hur många offentliga IP-adresser som du kan använda per prenumeration.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **Compute (beräkna**) och välj sedan **Windows Server 2016 VM**eller något annat operativ system som du väljer.
3. Ange eller Välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**:

    |Inställningen|Värde|
    |---|---|
    |Namn|myVM|
    |Användarnamn| Ange ett valfritt användarnamn.|
    |lösenordsinställning| Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Prenumeration| Välj din prenumeration.|
    |Resursgrupp| Välj **Använd befintlig** och sedan **myResourceGroup**.|
    |Location| Välj **USA, östra**|

4. Välj en storlek för den virtuella datorn och sedan **Välj**.
5. Under **Inställningar**väljer du **offentlig IP-adress**.
6. Ange *myPublicIpAddress*, Välj **statisk**och välj sedan **OK**, som du ser i följande bild:

   ![Välj statisk](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Om den offentliga IP-adressen måste vara en standard-SKU väljer du **standard** under **SKU**. Lär dig mer om [offentliga IP-adresser SKU: er](virtual-network-ip-addresses-overview-arm.md#sku). Om den virtuella datorn ska läggas till i backend-poolen för en offentlig Azure Load Balancer måste SKU: n för den virtuella datorns offentliga IP-adress matcha SKU: n för den offentliga IP-adressen för belastningsutjämnaren. Mer information finns i [Azure Load Balancer](../load-balancer/skus.md).

6. Välj en port eller inga portar under **Välj offentliga inkommande portar**. Portal 3389 är markerad, för att aktivera fjärråtkomst till den virtuella Windows Server-datorn från Internet. Det rekommenderas inte att öppna port 3389 från Internet för produktions arbets belastningar.

   ![Välj en port](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Godkänn de återstående standardinställningarna och välj **OK**.
8. På sidan **Sammanfattning** väljer du **Skapa**. Det tar några minuter att distribuera den virtuella datorn.
9. När den virtuella datorn har distribuerats anger du *myPublicIpAddress* i sökrutan längst upp i portalen. När **myPublicIpAddress** visas i Sök resultaten väljer du det.
10. Du kan visa den offentliga IP-adress som har tilldelats och att adressen är tilldelad den virtuella **myVM** -datorn, som du ser i följande bild:

    ![Visa offentlig IP-adress](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure tilldelade en offentlig IP-adress från adresser som används i den region där du skapade den virtuella datorn i. Du kan ladda ned listan över intervall (prefix) för [offentliga](https://www.microsoft.com/download/details.aspx?id=56519) Azure-moln och för Azure-moln för [amerikanska myndigheter](https://www.microsoft.com/download/details.aspx?id=57063), [Kina](https://www.microsoft.com/download/details.aspx?id=57062) eller [Tyskland](https://www.microsoft.com/download/details.aspx?id=57064).

11. Välj **konfiguration** för att bekräfta att tilldelningen är **statisk**.

    ![Visa offentlig IP-adress](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Ändra inte inställningarna för IP-adress i den virtuella datorns operativ system. Operativ systemet är inte medvetna om offentliga Azure-IP-adresser. Även om du kan lägga till inställningar för privata IP-adresser i operativ systemet rekommenderar vi att du inte gör det om det inte behövs, och inte förrän du har läst [lägga till en privat IP-adress till ett operativ system](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, skalningsuppsättningen och alla resurser som den innehåller:

1. Skriv *myResourceGroup* i **sökrutan** överst i portalen. När du ser **myResourceGroup** i sökresultatet väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Skriv *myResourceGroup* i **SKRIV RESURSGRUPPSNAMNET:** och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [offentliga IP-adresser](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) i Azure
- Läs mer om [Inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Läs mer om [privata IP-adresser](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) och tilldelning av en [statisk privat IP-adress](virtual-network-network-interface-addresses.md#add-ip-addresses) till en virtuell Azure-dator
- Lär dig mer om att skapa virtuella [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -och [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -datorer