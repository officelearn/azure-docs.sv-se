---
title: Konfigurera privata IP-adresser för virtuella datorer (klassisk) - Azure-portalen | Microsoft Docs
description: Lär dig hur du konfigurerar den privata IP-adresser för virtuella datorer (klassisk) med Azure-portalen.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1aae74d8077a75e5ab556703b1c1531f540bbdb4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Konfigurera privat IP-adresser för en virtuell dator (klassisk) som använder Azure portal

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också [hantera en statisk privat IP-adress i Resource Manager-distributionsmodellen](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

De steg i exemplet som följer förväntar sig en enkel miljö som redan har skapats. Om du vill köra stegen som de visas i det här dokumentet först skapa testmiljön som beskrivs i [skapa ett vnet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Så här anger du en statisk privat IP-adress när du skapar en virtuell dator
Skapa en virtuell dator med namnet *DNS01* i den *klientdel* undernätet i ett VNet med namnet *TestVNet* med en statisk privat IP-adress för *192.168.1.101*, fullständig följande steg:

1. Navigera till https://portal.azure.com från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Välj **ny** > **Compute** > **Windows Server 2012 R2 Datacenter**, Lägg märke till att den **Välj en distributionsmodell** redan lista visas **klassiska**, och välj sedan **skapa**.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. Under **skapa VM**, ange namnet på den virtuella datorn skapas (*DNS01* i scenariot), lokalt administratörskonto och lösenord.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Välj **valfri konfiguration** > **nätverk** > **virtuellt nätverk**, och välj sedan **TestVNet** . Om **TestVNet** är inte tillgänglig, kontrollera att du använder den *centrala USA* plats och har skapat testmiljön som beskrivs i början av den här artikeln.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. Under **nätverk**, se till att undernätet markerade *klientdel*och välj **IP-adresser**under **IP-adresstilldelning** Välj **Statiska**, och ange sedan *192.168.1.101* för **IP-adress** som visas nedan.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Välj **OK** under **IP-adresser**väljer **OK** under **nätverk**, och välj sedan **OK** under **Valfria config**.
7. Under **skapa VM**väljer **skapa**. Lägg märke till panelen nedan visas på instrumentpanelen:
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hur du hämtar statisk privat IP-adressinformation för en virtuell dator
Utför stegen nedan om du vill visa statisk privat IP-adressinformation för den virtuella datorn skapas med stegen ovan.

1. Välj den Azure-portalen **Bläddra bland alla** > **virtuella datorer (klassisk)** > **DNS01** > **alla inställningar för** > **IP-adresser** och Lägg märke till tilldelning av IP-adresser och IP-adress som du ser nedan.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Ta bort en statisk privat IP-adress från en virtuell dator

Under **IP-adresser**väljer **dynamiska** till höger om **IP-adresstilldelning**väljer **spara**, och välj sedan  **Ja**, enligt följande bild:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Hur du lägger till en statisk privat IP-adress till en befintlig virtuell dator

1. Under **IP-adresser**, tidigare visas, väljer **statiska** till höger om **IP-adresstilldelning**.
2. Typen *192.168.1.101* för **IP-adress**väljer **spara**, och välj sedan **Ja**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Ange IP-adresser inom operativsystemet

Vi rekommenderar att du inte statiskt tilldelar privata IP-Adressen som tilldelats den virtuella Azure-datorn i operativsystemet på en virtuell dator, om nödvändigt. Om du manuellt anger den privata IP-adressen i operativsystemet, kontrollera att det är samma adress som privat IP-adress som tilldelats den virtuella Azure-datorn eller du kan förlora anslutningen till den virtuella datorn. Du bör aldrig manuellt tilldela den offentliga IP-adress som tilldelats en virtuell Azure-dator i den virtuella datorns operativsystem.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [reserverade offentliga IP-Adressen](virtual-networks-reserved-public-ip.md) adresser.
* Lär dig mer om [instansnivå offentliga IP-går](virtual-networks-instance-level-public-ip.md) adresser.
* Läs den [reserverade IP-REST API: er](https://msdn.microsoft.com/library/azure/dn722420.aspx).

