---
title: Konfigurera privata IP-adresser för virtuella datorer (Klassisk) - Azure-portal | Microsoft-dokument
description: Lär dig hur du konfigurerar privata IP-adresser för virtuella datorer (Classic) med hjälp av Azure-portalen.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 9de9fd2eb7a46719b841a885ac173f8d9c1271c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71059112"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Konfigurera privata IP-adresser för en virtuell dator (Klassisk) med Azure-portalen

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också [hantera en statisk privat IP-adress i Resurshanterarens distributionsmodell](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Exempelstegen som följer förväntar sig att en enkel miljö redan har skapats. Om du vill köra stegen när de visas i det här dokumentet skapar du först testmiljön som beskrivs i [skapa ett vnet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Så här anger du en statisk privat IP-adress när du skapar en virtuell dator
Så här skapar du en virtuell dator med namnet *DNS01* i *FrontEnd-undernätet* i ett virtuellt nätverk med namnet *TestVNet* med en statisk privat IP på *192.168.1.101:*

1. Navigera till https://portal.azure.com från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Välj **NYTT** > **Compute** > **Windows Server 2012 R2 Datacenter**, observera att listan Välj en **distributionsmodell** redan visar **Klassisk**och välj sedan **Skapa**.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. Under **Skapa virtuell dator**anger du namnet på den virtuella datorn som ska skapas (*DNS01* i scenariot), det lokala administratörskontot och lösenordet.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Välj **Valfritt virtuellt nätverk för** > **konfigurationsnätverk** > **Virtual Network**och välj sedan **TestVNet**. Om **TestVNet** inte är tillgängligt kontrollerar du att du använder den *centrala amerikanska* platsen och har skapat testmiljön som beskrivs i början av den här artikeln.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. Under **Nätverk**kontrollerar du att det markerade undernätet är *FrontEnd*, välj sedan **IP-adresser**, under **IP-adresstilldelningen** väljer du **Statisk**och anger sedan *192.168.1.101* för **IP-adress** enligt nedan.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Välj **OK** under **IP-adresser**, välj **OK** under **Nätverk**och välj sedan **OK** under **Valfri config**.
7. Under **Skapa virtuell dator**väljer du **Skapa**. Lägg märke till panelen nedan som visas på instrumentpanelen:
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hämta statisk privat IP-adressinformation för en virtuell dator
Om du vill visa den statiska privata IP-adressinformationen för den virtuella datorn som skapats med stegen ovan kör du stegen nedan.

1. Från Azure-portalen väljer du **BLÄDDRA BLAND ALLA** > **virtuella datorer (klassiska)** > **DNS01** > **Alla inställningar** > **IP-adresser** och märker IP-adresstilldelning och IP-adress som visas nedan.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Så här tar du bort en statisk privat IP-adress från en virtuell dator

Under **IP-adresser**väljer du **Dynamisk** till höger om **IP-adresstilldelning,** väljer **Spara**och väljer sedan **Ja**, som visas i följande bild:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Så här lägger du till en statisk privat IP-adress till en befintlig virtuell dator

1. Under **IP-adresser**, som visas tidigare, väljer du **Statisk** till höger om **IP-adresstilldelning**.
2. Skriv *192.168.1.101* för **IP-adress,** välj **Spara**och välj sedan **Ja**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Ange IP-adresser i operativsystemet

Vi rekommenderar att du inte statiskt tilldelar den privata IP-adressen som tilldelats den virtuella Azure-datorn i operativsystemet för en virtuell dator, om det inte behövs. Om du manuellt anger den privata IP-adressen i operativsystemet, se till att det är samma adress som den privata IP-adressen som tilldelats Azure VM, eller så kan du förlora anslutningen till den virtuella datorn. Du bör aldrig tilldela den offentliga IP-adressen som tilldelats en virtuell Azure-dator i den virtuella datorns operativsystem manuellt.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [reserverade offentliga IP-adresser.](virtual-networks-reserved-public-ip.md)
* Lär dig mer om [ILPIP-adresser (Public IP)-adresser (instance-level).](virtual-networks-instance-level-public-ip.md)
* Konsultera [api:erna för reserverade IP REST](https://msdn.microsoft.com/library/azure/dn722420.aspx).

