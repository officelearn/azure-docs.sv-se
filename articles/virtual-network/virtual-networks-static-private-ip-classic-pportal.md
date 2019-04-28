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
ms.openlocfilehash: 72d1c4d2ea3adf7d8751adfbb013435f8f2530f0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125754"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Konfigurera privata IP-adresser för en virtuell dator (klassisk) med Azure portal

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också [hantera en statisk privat IP-adress i Resource Manager-distributionsmodellen](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

De steg i exemplet som följer förväntar sig en enkel miljö som redan har skapats. Om du vill att köra stegen visas i det här dokumentet, först skapa testmiljön som beskrivs i [skapar ett virtuellt nätverk](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Så här anger du en statisk privat IP-adress när du skapar en virtuell dator
Skapa en virtuell dator med namnet *DNS01* i den *klientdel* undernät i ett virtuellt nätverk med namnet *TestVNet* med en statisk privat IP-Adressen för *192.168.1.101*, fullständigt följande steg:

1. Navigera till https://portal.azure.com från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Välj **NEW** > **Compute** > **Windows Server 2012 R2 Datacenter**, Observera att den **Välj en distributionsmodell** redan lista visas **klassiska**, och välj sedan **skapa**.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. Under **Create VM**, anger du namnet på den virtuella datorn skapas (*DNS01* i scenariot), lokalt administratörskonto och lösenord.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Välj **valfri konfiguration** > **nätverk** > **virtuellt nätverk**, och välj sedan **TestVNet** . Om **TestVNet** är inte tillgänglig, kontrollera att du använder den *centrala USA* plats och har skapat testmiljön som beskrivs i början av den här artikeln.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. Under **nätverk**, se till att det undernät som valts är *klientdel*och välj sedan **IP-adresser**under **IP-adresstilldelning** Välj **Statiska**, och ange sedan *192.168.1.101* för **IP-adress** enligt nedan.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Välj **OK** under **IP-adresser**väljer **OK** under **nätverk**, och välj sedan **OK** under **Valfri konfiguration**.
7. Under **Create VM**väljer **skapa**. Lägg märke till panelen nedan visas på instrumentpanelen:
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hur du hämtar statiska privata IP-adressinformation för en virtuell dator
Utföra stegen nedan om du vill visa statiska privata IP-adressinformation för den virtuella datorn skapas med stegen ovan.

1. Azure-portalen väljer du **Bläddra bland alla** > **virtuella datorer (klassiska)** > **DNS01** > **alla inställningar för** > **IP-adresser** och Lägg märke till IP-adresstilldelning och IP-adress som visas nedan.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Ta bort en statisk privat IP-adress från en virtuell dator

Under **IP-adresser**väljer **dynamisk** till höger om **IP-adresstilldelning**väljer **spara**, och välj sedan  **Ja**, enligt följande bild:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Hur du lägger till en statisk privat IP-adress till en befintlig virtuell dator

1. Under **IP-adresser**, tidigare visas, väljer **statiska** till höger om **IP-adresstilldelning**.
2. Typ *192.168.1.101* för **IP-adress**väljer **spara**, och välj sedan **Ja**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Ange IP-adresser i operativsystemet

Vi rekommenderar att du inte statiskt tilldelar privat IP-adress som tilldelats virtuella Azure-datorer i operativsystemet på en virtuell dator, om inte behövs. Om du manuellt anger den privata IP-adressen i operativsystemet, kontrollera att det är samma adress som den privata IP-adressen som tilldelas den virtuella Azure-datorn, eller du kan förlora anslutningen till den virtuella datorn. Du bör aldrig manuellt tilldela offentliga IP-adress som tilldelats till en Azure virtuell dator i den virtuella datorns operativsystem.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [reserverade offentliga IP-Adressen](virtual-networks-reserved-public-ip.md) adresser.
* Lär dig mer om [offentliga IP (ILPIP) på instansnivå](virtual-networks-instance-level-public-ip.md) adresser.
* Läs den [reserverade IP-REST API: er](https://msdn.microsoft.com/library/azure/dn722420.aspx).

