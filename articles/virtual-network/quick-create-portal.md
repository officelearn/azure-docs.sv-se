---
title: "Skapa ett virtuellt nätverk i Azure - portalen | Microsoft Docs"
description: "Snabbt lära dig skapa ett virtuellt nätverk med Azure-portalen. Ett virtuellt nätverk gör det möjligt för flera olika typer av Azure-resurser till privat kommunicera med varandra."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 3c040f677aa25656148081d533e87cc55f1e22e7
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Skapa ett virtuellt nätverk med Azure Portal

I den här artikeln får du lära dig hur du skapar ett virtuellt nätverk. När du har skapat ett virtuellt nätverk distribuerar du två virtuella datorer i det virtuella nätverket för att testa privata nätverkskommunikation mellan dem.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Välj **+ skapa en resurs** i övre vänstra hörnet på Azure-portalen.
2. Välj **nätverk**, och välj sedan **för virtuella nätverk**.
3. I följande bild visas anger *myVirtualNetwork* för **namn**, *myResourceGroup* för **resursgruppen**, Välj en  **Plats** och **prenumeration**återstående standardvärdena och välj sedan **skapa**. 

    ![Ange grundläggande information om ditt virtuella nätverk](./media/quick-create-portal/virtual-network.png)

    Den **adressutrymmet** anges i CIDR-notering. Adressutrymme 10.0.0.0/16 omfattar 10.0.0.0-10.0.255.254. Ett virtuellt nätverk innehåller noll eller flera undernät. Det angivna undernätet **adressintervall** innehåller 10.0.0.0-10.0.0.255 för IP-adresser. Endast 10.0.0.4-10.0.0.254 finns dock eftersom Azure reserverar de första fyra adresserna (0-3) och den sista adressen i varje undernät. Tillgängliga IP-adresser tilldelas resurser har distribuerats inom ett virtuellt nätverk.

## <a name="test-network-communication"></a>Testa nätverkskommunikation

Ett virtuellt nätverk gör det möjligt för flera typer av Azure-resurser till privat kommunicera med varandra. En typ av resurs som du kan distribuera till ett virtuellt nätverk är en virtuell dator. Skapa två virtuella datorer i det virtuella nätverket så att du kan verifiera privata kommunikationen mellan dem i ett senare steg.

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

1. Välj **+ skapa en resurs för** hittades i det övre, vänstra hörnet i Azure-portalen.
2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**.
3. Ange information om virtuella datorn visas i bilden nedan. Den **användarnamn** och **lösenord** du anger används för att logga in på den virtuella datorn i ett senare steg. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). Välj din **prenumeration**, välja att använda det befintliga *myResourceGroup* resurs gruppen och kontrollerar att den **plats** valts är på samma plats som du skapade den virtuellt nätverk i. När du är klar väljer **OK**.

    ![Ange grundläggande information om en virtuell dator](./media/quick-create-portal/virtual-machine-basics.png)
4. Välj en storlek för den virtuella datorn och välj sedan **Välj**. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. De storlekar som du kan skilja sig i följande exempel: 

    ![Välj en storlek för en virtuell dator](./media/quick-create-portal/virtual-machine-size.png)
5. Under **inställningar**, *myVirtualNetwork* bör redan vara markerad för **för virtuella nätverk**, men om det inte välja **för virtuella nätverk** , välj sedan *myVirtualNetwork*. Lämna *standard* markerade för **undernät**, och välj sedan **OK**.

    ![Välj ett virtuellt nätverk](./media/quick-create-portal/virtual-machine-network-settings.png)
6. På den **sammanfattning** väljer **skapa** att starta distributionen av virtuella datorer. 
7. Det tar några minuter att skapa den virtuella datorn. Den virtuella datorn är fäst på instrumentpanelen för Azure-portalen och sammanfattning för virtuell dator öppnas automatiskt när den har skapats. Välj **nätverk**.

    ![Virtuella nätverksinformation](./media/quick-create-portal/virtual-machine-networking.png)

    Du ser som den **privat IP** adressen är *10.0.0.4*. I steg 5, under **inställningar**, du har valt den *myVirtualNetwork* virtuella nätverk och undernät med namnet har accepterat *standard* för **undernät**. När du [skapade det virtuella nätverket](#create-a-virtual-network), du har accepterat standardvärdet 10.0.0.0/24 för undernätet **adressintervall**. Azures DHCP-server tilldelar den första tillgängliga adressen för undernätet som du väljer den virtuella datorn. Eftersom Azure reserverar de första fyra adresserna (0-3) för varje undernät, är 10.0.0.4 den första tillgängliga IP-adressen för undernätet.

    Den **offentliga IP-Adressen** adress skiljer sig från den adress som tilldelats till den virtuella datorn. Azure tilldelar en offentliga Internet dirigerbara IP-adress till varje virtuell dator, som standard. Offentlig IP-adress har tilldelats den virtuella datorn från en [adresspool som tilldelats varje Azure-region](https://www.microsoft.com/download/details.aspx?id=41653). Medan Azure vet vilken offentliga IP-adress har tilldelats en virtuell dator, har operativsystemet som körs på en virtuell dator inga medvetenhet om någon offentlig IP-adress som tilldelats.
8. Slutför steg 1-7 igen, men i steg 3, namnge den virtuella datorn *myVm2*. 
9. När den virtuella datorn har skapats, Välj **nätverk**, som du i steg 7. Du ser den **privat IP** adressen är *10.0.0.5*. Eftersom Azure tidigare tilldelats den första adressen som kan användas av *10.0.0.4* i undernätet i *myVm1* virtuell dator kan tilldelas den *10.0.0.5* till den  *myVm2* virtuella datorn eftersom den var den nästa tillgängliga adressen i undernätet.

### <a name="connect-to-a-virtual-machine"></a>Ansluta till en virtuell dator

1. Fjärransluta till den *myVm1* virtuella datorn. Längst upp i Azure-portalen, ange *myVm1*. När **myVm1** visas i sökresultaten väljer den. Välj den **Anslut** knappen.

    ![Översikt över virtuella datorer](./media/quick-create-portal/virtual-machine-overview.png)
2. När du har valt den **Anslut** knapp och en Remote Desktop Protocol (RDP)-fil skapas och hämtas till datorn.  
3. Öppna hämtade rdp-filen. Välj **Anslut**. Ange användarnamn och lösenord som du angav när du skapar den virtuella datorn och välj sedan **OK**. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** att fortsätta med anslutningen.

### <a name="validate-communication"></a>Validera kommunikation

Försök att pinga en Windows virtuell dator misslyckas eftersom ping inte tillåts via Windows-brandväggen som standard. Att tillåta ping till *myVm1*, anger du följande kommando från en kommandotolk:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Validera kommunikation med *myVm2*, anger du följande kommando från en kommandotolk den *myVm1* virtuella datorn. Ange de autentiseringsuppgifter som du använde när du skapade den virtuella datorn och slutföra anslutningen:

```
mstsc /v:myVm2
```

Anslutning till fjärrskrivbord är lyckas eftersom både virtuella datorer har den privata IP-adresser tilldelas från den *standard* undernät och eftersom fjärrskrivbord är öppen via Windows-brandväggen som standard. Du kan ansluta till *myVm2* av värdnamn eftersom automatiskt ger DNS-namnmatchning för alla värdar inom ett virtuellt nätverk i Azure. Från en kommandotolk pinga *myVm1*, från *myVm2*.

```
ping myvm1
```

Ping lyckas eftersom du tillåts via Windows-brandväggen på den *myVm1* virtuell dator i föregående steg. För att bekräfta utgående kommunikation till Internet, anger du följande kommando:

```
ping bing.com
```

Du får fyra svar från bing.com. Som standard kan en virtuell dator i ett virtuellt nätverk kommunicera utgående till Internet. 

Avsluta fjärrskrivbords-sessionen.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort resursgruppen och alla resurser som den innehåller:

1. Ange *myResourceGroup* i den **Sök** överst i portalen. När du ser **myResourceGroup** i sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Ange *myResourceGroup* för **typ av RESURSGRUPPENS namn:** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

Du har distribuerat en standard virtuellt nätverk med ett undernät i den här artikeln. Mer information om hur du skapar en anpassad virtuellt nätverk med flera undernät, även fortsättningsvis självstudierna för att skapa egna virtuella nätverk.

> [!div class="nextstepaction"]
> [Skapa en anpassad virtuellt nätverk](virtual-networks-create-vnet-arm-pportal.md)
