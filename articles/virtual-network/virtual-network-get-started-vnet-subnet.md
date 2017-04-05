---
title: "Skapa ditt första virtuella nätverk i Azure | Microsoft Docs"
description: "Lär dig hur du skapar ett virtuellt Azure-nätverk (VNet), ansluter två virtuella datorer (VM) till ditt VNet och ansluter till de virtuella datorerna."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: c731099cb91512f3bf0ecc2ffa5258788c90cd1b
ms.lasthandoff: 03/31/2017


---

# <a name="create-your-first-virtual-network"></a>Skapa ditt första virtuella nätverk

Lär dig hur du skapar ett virtuellt nätverk (VNet) med två undernät, skapar två virtuella datorer (VM) och ansluter de båda virtuella datorerna till var sitt undernät. Se följande bild:

![Diagram över virtuellt nätverk](./media/virtual-network-get-started-vnet-subnet/vnet-diagram.png)

Ett Azure-virtuellt nätverk (VNet) är en representation av ditt eget nätverk i molnet. Du kan själv styra dina Azure-nätverksinställningar och definiera DHCP-adressblock, DNS-inställningar, säkerhetsprinciper och routning. Du kan läsa mer om VNet-begreppen i artikeln [Virtuella nätverk](virtual-networks-overview.md). Så här skapar du resurserna som visas i bilden:

1. [skapa ett VNet med två undernät](#create-vnet)
2. [skapa två virtuella datorer med var sitt nätverksgränssnitt (NIC)](#create-vms) och koppla en nätverkssäkerhetsgrupp (NSG) till varje nätverkskort
3. [anslut till och från de virtuella datorerna](#connect-to-from-vms)
4. [ta bort alla resurser](#delete-resources). Det tillkommer avgifter för några av resurserna som skapas i den här övningen så länge de är etablerade. Du kan minimera avgifterna genom att utföra stegen i det här avsnittet och ta bort de resurser du skapar i övningen.

När du har utfört stegen i den här artikeln kommer du att ha en grundläggande förståelse för hur du kan använda ett VNet. I senare steg får du lära dig mer om hur du använder VNet på en djupare nivå.

## <a name="create-vnet"></a>Skapa ett virtuellt nätverk med två undernät

Så här skapar du ett virtuellt nätverk med två undernät. Olika undernät används normalt till att styra trafikflödet mellan undernät.

1. Logga in på [Azure-portalen](<https://portal.azure.com>). Om du inte redan har ett konto kan du [registrera dig för en kostnadsfri utvärderingsmånad](https://azure.microsoft.com/free). 
2. Klicka på **Nytt** i rutan **Favoriter** i portalen.
3. Klicka på **Nätverk** i bladet **Nytt**. Klicka på **Virtuellt nätverk** i bladet **Nätverk**, se följande bild:

    ![Diagram över virtuellt nätverk](./media/virtual-network-get-started-vnet-subnet/virtual-network.png)

4.  Lämna *Resource Manager* som distributionsmodell i bladet **Virtuellt nätverk** och klicka på **skapa**.
5.  Ange följande värden bladet **Skapa virtuellt nätverk** och klicka på **Skapa**:

    |**Inställning**|**Värde**|**Detaljer**|
    |---|---|---|
    |**Namn**|*MyVNet*|Namnet måste vara unikt inom resursgruppen.|
    |**Adressutrymme**|*10.0.0.0/16*|Du kan ange valfritt adressutrymme med CIDR-notation.|
    |**Namn på undernät**|*Klient*|Namnet på undernätet måste vara unikt inom det virtuella nätverket.|
    |**Adressintervall för undernätet**|*10.0.0.0/24*| Det intervall du anger måste ligga inom det adressutrymme du definierade för det virtuella nätverket.|
    |**Prenumeration**|*[din prenumeration]*|Välj den prenumeration där du vill skapa VNet-förekomsten. Ett VNet ligger i en enskild prenumeration.|
    |**Resursgrupp**|**Skapa ny:** *MyRG*|Skapa en resursgrupp. Resursgruppens namn måste vara unikt inom den prenumeration du valde. Mer information om resursgrupper finns i [översikten över Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups).|
    |**Plats**|*USA, västra*| Normalt den närmaste platsen rent geografiskt.|

    Det tar några sekunder att skapa ditt VNet. När det har skapats ser du instrumentpanelen för Azure Portal.

6. Klicka på **Alla resurser** i rutan **Favoriter** för det virtuella nätverk du skapade. Klicka på det virtuella nätverket **MyVNet** i bladet **Alla resurser**. Om den prenumeration du valde redan har flera resurser kan du ange *MyVNet* i rutan **Filtrera efter namn** när du ska hitta ditt VNet.
7. Bladet **MyVNet** öppnas och du ser information om ditt VNet, se följande bild:

    ![Diagram över virtuellt nätverk](./media/virtual-network-get-started-vnet-subnet/myvnet.png)

8. Så som visas i föregående bild kan du klicka på **Undernät** för att visa en lista med undernät i ditt VNet. Det enda undernät som finns är **Klient**, det undernät du skapade i steg 5.
9. Klicka på **+ Undernät** i bladet MyVNet – Undernät och skapa ett undernät genom att ange följande information och klicka på **OK**:

    |**Inställning**|**Värde**|**Detaljer**|
    |---|---|---|
    |**Namn**|*Server*|Namnet måste vara unikt inom det virtuella nätverket.|
    |**Adressintervall**|*10.0.1.0/24*|Det intervall du anger måste ligga inom det adressutrymme du definierade för det virtuella nätverket.|
    |**Nätverkssäkerhetsgrupp** och **Routningstabell**|*Ingen* (standardvärde)|Vi går igenom nätverkssäkerhetsgrupper (NSG:er) senare i den här artikeln. Mer information om användardefinierade vägar finns i artikeln [Användardefinierade vägar](virtual-networks-udr-overview.md).|

10. När du har lagt till det nya undernätet i ditt VNet kan du stänga bladet **MyVNet – Undernät** och sedan stänga bladet **Alla resurser**.

## <a name="create-vms"></a>Skapa virtuella datorer

När du har skapat ditt VNet och undernäten kan du skapa de virtuella datorerna. I den här övningen kör båda de virtuella datorerna operativsystemet Windows Server, men de kan köra alla operativsystem som stöds av Azure, bland annat flera olika Linux-distributioner.

### <a name="create-web-server-vm"></a>Skapa den virtuella webbserverdatorn

Så här skapar du den virtuella webbserverdatorn:

1. Klicka på **Ny**, **Compute** och sedan **Windows Server 2016 Datacenter** i rutan Favoriter i Azure Portal.
2. Klicka på **Skapa** i bladet **Windows Server 2016 Datacenter**.
3. Ange eller välj följande värden i bladet **Grundläggande inställningar** och klicka på **OK**:

    |**Inställning**| **Värde**|**Detaljer**|
    |---|---|---|
    |**Namn**|*MyWebServer*|Den här virtuella datorn fungerar som en webbserver som internetresurser ansluter till.|
    |**Typ av virtuell datordisk**|*SSD*|
    |**Användarnamn**|*Ditt val*|
    |**Lösenord och Bekräfta lösenord**|*Ditt val*|
    | **Prenumeration**|*<Your subscription>*|Prenumerationen måste vara samma prenumeration som du valde i steg 5 i avsnittet [Skapa ett virtuellt nätverk med två undernät](#create-vnet) i den här artikeln. Det VNet du ansluter en virtuell dator till måste finnas i samma prenumeration som den virtuella datorn.|
    |**Resursgrupp**|**Använd befintlig:** Välj *MyRG*|Även om vi använder samma resursgrupp som vi gjorde för VNet-förekomsten så måste inte resurserna ligga i samma resursgrupp.|
    |**Plats**|*USA, västra*|Platsen måste vara samma plats som du angav i steg 5 i avsnittet [Skapa ett virtuellt nätverk med två undernät](#create-vnet) i den här artikeln. Virtuella datorer och de VNet som de ansluter till måste finnas på samma plats.|

4. Klicka på *DS1_V2 Standard* på bladet **Välj en storlek** och klicka på **Välj**. I artikeln om [storlekar på virtuella Windows-datorer](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) finns en lista med alla storlekar på virtuella Windows-datorer som kan användas i Azure.
5. Ange eller välj följande värden i bladet **Inställningar** och klicka på **OK**:

    |**Inställning**|**Värde**|**Detaljer**|
    |---|---|---|
    |**Lagring: Använd hanterade diskar**|*Ja*||
    |**Virtuellt nätverk**| Välj *MyVNet*|Du kan välja alla VNet som finns på samma plats som den virtuella dator du skapar. Du kan läsa mer om VNet och undernät i artikeln [Virtuella nätverk](virtual-networks-overview.md).|
    |**Undernät**|Välj *Klient*|Du kan välja alla undernät som finns i ditt VNet.|
    |**Offentlig IP-adress**|Acceptera standardinställningarna|Med en offentlig IP-adress kan du ansluta till den virtuella datorn från internet. Mer information om offentliga IP-adresser finns i artikeln om [IP-adresser](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).|
    |**Nätverkssäkerhetsgrupp (brandvägg)**|Acceptera standardinställningarna|Klicka på standardnätverkssäkerhetsgruppen **(ny) MyWebServer-nsg** som portalen skapade så att inställningarna för den visas. Lägg märke till att det finns en regel för inkommande trafik i bladet **Skapa nätverkssäkerhetsgrupp**, som tillåter TCP/3389-trafik (RDP) trafik från alla IP-källadresser.|
    |**Alla andra värden**|Acceptera standardvärdena|Mer information om återstående inställningar finns i artikeln [Om virtuella datorer](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|

    Med nätverkssäkerhetsgrupper (NSG) kan du skapa regler för inkommande/utgående trafik för den typ av nätverkstrafik som kan flöda till och från den virtuella datorn. Som standard nekas all inkommande trafik till den virtuella datorn. Du kan lägga till ytterligare regler för inkommande trafik för TCP/80 (HTTP) och TCP/443 (HTTPS) för en webbserver i produktionsmiljö. Det finns ingen regel för utgående trafik eftersom all utgående trafik tillåts som standard. Du kan lägga till/ta bort regler för trafiken enligt era principer. Mer information om nätverkssäkerhetsgrupper finns i artikeln [Nätverkssäkerhetsgrupper](virtual-networks-nsg.md).

6.  Granska inställningarna på bladet **Sammanfattning** och klicka på **OK** så att den virtuella datorn skapas. En statusruta visas på portalens instrumentpanel när den virtuella datorn skapas. Det kan ta några minuter att skapa datorn. Du behöver inte vänta tills åtgärden har slutförts. Du kan fortsätta till nästa steg medan den virtuella datorn skapas.

### <a name="create-database-server-vm"></a>Skapa den virtuella databasserverdatorn

Så här skapar du den virtuella databasserverdatorn:

1.  Klicka på **Ny**, **Compute** och sedan **Windows Server 2016 Datacenter** i rutan Favoriter.
2.  Klicka på **Skapa** i bladet **Windows Server 2016 Datacenter**.
3.  Ange eller välj följande värden i bladet **Grundläggande inställningar** och klicka på **OK**:

    |**Inställning**|**Värde**|**Detaljer**|
    |---|---|---|
    |**Namn**|*MyDBServer*|Den här virtuella datorn fungerar som en databasserver som webbservern ansluter till, men som det inte går att ansluta till från internet.|
    |**Typ av virtuell datordisk**|*SSD*||
    |**Användarnamn**|Ditt val||
    |**Lösenord och Bekräfta lösenord**|Ditt val||
    |**Prenumeration**|<Your subscription>|Prenumerationen måste vara samma prenumeration som du valde i steg 5 i avsnittet [Skapa ett virtuellt nätverk med två undernät](#create-vnet) i den här artikeln.|
    |**Resursgrupp**|**Använd befintlig:** Välj *MyRG*|Även om vi använder samma resursgrupp som vi gjorde för VNet-förekomsten så måste inte resurserna ligga i samma resursgrupp.|
    |**Plats**|*USA, västra*|Platsen måste vara samma plats som du angav i steg 5 i avsnittet [Skapa ett virtuellt nätverk med två undernät](#create-vnet) i den här artikeln.|

4.  Klicka på *DS1_V2 Standard* på bladet **Välj en storlek** och klicka på **Välj**.
5.  Ange eller välj följande värden i bladet **Inställningar** och klicka på **OK**:

    |**Inställning**|**Värde**|**Detaljer**|
    |----|----|---|
    |**Lagring: Använd hanterade diskar**|*Ja*||
    |**Virtuellt nätverk**|Välj *MyVNet*|Du kan välja alla VNet som finns på samma plats som den virtuella dator du skapar.|
    |**Undernät**|Välj *Server* genom att klicka i rutan **Undernät** och sedan välja **Server** i bladet **Välj ett undernät**|Du kan välja alla undernät som finns i ditt VNet.|
    |**Offentlig IP-adress**|Ingen – klicka på standardadressen och sedan på **Ingen** i bladet **Välj offentlig IP-adress**|Utan en offentlig IP-adress kan du bara ansluta till den virtuella datorn från andra virtuella dator som är anslutna till samma VNet. Du kan inte ansluta till den direkt från internet.|
    |**Nätverkssäkerhetsgrupp (brandvägg)**|Acceptera standardinställningarna| Precis som standardnätverkssäkerhetsgruppen som skapades för den virtuella datorn MyWebServer VM så har den här nätverkssäkerhetsgruppen samma regel för inkommande trafik. Du kan lägga till en ytterligare regel för inkommande trafik för TCP/1433 (MS SQL) för en databasserver. Det finns ingen regel för utgående trafik eftersom all utgående trafik tillåts som standard. Du kan lägga till/ta bort regler för trafiken enligt era principer.|
    |**Alla andra värden**|Acceptera standardvärdena||

6.  Granska inställningarna på bladet **Sammanfattning** och klicka på **OK** så att den virtuella datorn skapas. En statusruta visas på portalens instrumentpanel när den virtuella datorn skapas. Det kan ta några minuter att skapa datorn. Du behöver inte vänta tills åtgärden har slutförts. Du kan fortsätta till nästa steg medan den virtuella datorn skapas.

## <a name="review"></a>Granska resurser

Även om du endast skapade ett VNet och två virtuella datorer så har Azure Portal skapat flera ytterligare resurser i resursgruppen MyRG. Så här kan du granska innehållet i resursgruppen MyRG:

1. Klicka på **Fler tjänster** i rutan **Favoriter**.
2. Skriv *Resursgrupper* i rutan **Fler tjänster**, i rutan som innehåller ordet *Filter*. Klicka på **Resursgrupper** när du ser det i den filtrerade listan.
3. Klicka på resursgruppen *MyRG* i rutan **Resursgrupper**. Om du har många resursgrupper i din prenumeration kan du skriva *MyRG* i rutan som innehåller texten *Filtrera efter namn ...* för att snabbt hitta resursgruppen MyRG.
4.  I bladet **MyRG** ser du att resursgruppen innehåller 12 resurser, se följande bild:

    ![Innehåll i resursgruppen](./media/virtual-network-get-started-vnet-subnet/resource-group-contents.png)

Mer information om virtuella datorer, diskar och lagringskonton finns i översiktsartiklarna [Virtuell dator](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Disk](../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Lagringskonto](../storage/storage-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Du ser de två nätverkssäkerhetsgrupper som portalen skapade som standard. Du kan också se att portalen har skapat två nätverksgränssnitt (NIC). Ett nätverkskort gör att en virtuell dator kan ansluta till andra resurser i ditt VNet. Mer information om nätverkskort finns i artikeln [Nätverkskort](virtual-network-network-interface.md). Dessutom skapas en offentlig IP-adressresurs i portalen. Offentliga IP-adresser är en inställning för en offentlig IP-adressresurs. Mer information om offentliga IP-adresser finns i artikeln om [IP-adresser](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).

## <a name="connect-to-from-vms"></a>Anslut till de virtuella datorerna

Med ditt VNet och de två virtuella datorerna du skapade så kan du nu ansluta till de virtuella datorerna genom att följa stegen i följande avsnitt:

### <a name="connect-from-internet"></a>Anslut till den virtuella webbserverdatorn från internet

Så här ansluter du till den virtuella webbserverdatorn från internet:

1. Öppna resursgruppen MyRG i portalen genom att följa stegen i avsnittet [Granska resurser](#review) i den här artikeln.
2. Klicka på den virtuella datorn **MyWebServer** i bladet **MyRG**.
3. Klicka på **Anslut** i bladet **MyWebServer**, se följande bild:

    ![Anslut till den virtuella webbserverdatorn](./media/virtual-network-get-started-vnet-subnet/webserver.png)

4. Tillåt att webbläsaren laddar ned filen *MyWebServer.rdp* och öppna den sedan.
5. Om du ser en dialogruta om att det inte går att verifiera utgivaren av fjärranslutningen klickar du på **Anslut**.
6. När du anger dina inloggningsuppgifter ska du använda det användarnamn och lösenord du angav i steg 3 i avsnittet [Skapa den virtuella webbserverdatorn](#create-web-server-vm) i den här artikeln. Om rutan **Windows-säkerhet** som visas inte innehåller rätt inloggningsuppgifter kan du behöva klicka på **Fler alternativ** och sedan på **Använd ett annat konto**, så du kan ange rätt användarnamn och lösenord. Klicka på **OK** för att ansluta till den virtuella datorn.
7. Om du ser rutan **Anslutning till fjärrskrivbord** med information om att det inte går att verifiera fjärrdatorns identitet klickar du på **Ja**.
8. Du är nu ansluten till den virtuella datorn MyWebServer från internet. Låt anslutningen till fjärrskrivbordet vara öppen när du utför stegen i nästa avsnitt.

Fjärranslutningen är till den offentliga IP-adress som tilldelats till den offentliga IP-adressresursen som portalen skapade i steg 5 i avsnittet [Skapa ett virtuellt nätverk med två undernät](#create-vnet) i den här artikeln. Anslutningen tillåts eftersom standardregeln som skapades i nätverkssäkerhetsgruppen **MyWebServer-nsg** tillåter inkommande TCP/3389-trafik (RDP) till den virtuella datorn från alla IP-källadresser. Om du försöker ansluta till den virtuella datorn över någon annan port upprättas inte anslutningen, såvida du inte lägger till ytterligare regler för inkommande trafik i nätverkssäkerhetsgruppen så att fler portar tillåts.

>[!NOTE]
>Om du lägger till fler regler för inkommande trafik i nätverkssäkerhetsgruppen ska du se till att samma portar är öppna i Windows-brandväggen, annars går det inte att ansluta.
>

### <a name="connect-to-internet"></a>Anslut till internet från den virtuella webbserverdatorn

Så här ansluter du till internet från den virtuella webbserverdatorn:

1. Om du inte redan har en fjärranslutning öppen till MyWebServerVM ska du skapa en fjärranslutning till den virtuella datorn genom att följa stegen i avsnittet [Anslut till den virtuella webbserverdatorn från internet](#connect-from-internet) i den här artikeln.
2. Öppna Internet Explorer från Windows-skrivbordet. Klicka på **Använd inte de rekommenderade inställningarna** i dialogrutan med **installationsprogrammet för Internet Explorer 11** och klicka på **OK**. För servrar i produktionsmiljö rekommenderar vi att du accepterar de rekommenderade inställningarna.
3. Ange [bing.com](http:www.bing.com) i adressfältet i Internet Explorer. Om du ser en dialogruta i Internet Explorer klickar du på **Lägg till**, och sedan på **Lägg till** i dialogrutan **Betrodda platser**. Klicka sedan på **Stäng**. Upprepa den här processen i eventuella andra dialogrutor i Internet Explorer.
4. Ange *whatsmyipaddress* på Bing-söksidan och klicka på förstoringsglaset. Bing returnerar den offentliga IP-adressen som tilldelades den offentliga IP-adressresursen som portalen skapade när du skapade den virtuella datorn. Om du undersöker inställningarna för resursen **MyWebServer-ip** ser du samma IP-adress som tilldelades till den offentliga IP-adressresursen, se bilden nedan. IP-adressen som tilldelades till den virtuella datorn är dock en annan.

    ![Anslut till den virtuella webbserverdatorn](./media/virtual-network-get-started-vnet-subnet/webserver-pip.png)

5.  Låt anslutningen till fjärrskrivbordet vara öppen när du utför stegen i nästa avsnitt.

Du kan ansluta till internet från den virtuella datorn eftersom all utgående trafik från den virtuella datorn tillåts som standard. Du kan begränsa den utgående trafiken genom att lägga till ytterligare regler i nätverkssäkerhetsgruppen för nätverkskortet, till undernätet som nätverkskortet är anslutet till, eller båda.

Om den virtuella datorn stoppas (avallokeras) via portalen så kan den offentliga IP-adressen ändras. Om du inte vill att den offentliga IP-adressen ska ändras så kan du använda statisk allokering för IP-adressen snarare än dynamisk allokering (som är standard). Mer information om skillnaderna mellan de båda allokeringsmetoderna finns i artikeln [IP-adresstyper och allokeringsmetoder i Azure](virtual-network-ip-addresses-overview-arm.md).

### <a name="webserver-to-dbserver"></a>Anslut till den virtuella databasserverdatorn från den virtuella webbserverdatorn

Så här ansluter du till den virtuella databasserverdatorn från den virtuella webbserverdatorn:

1. Om du inte redan har en fjärranslutning öppen till den virtuella datorn MyWebServer så skapar du en fjärranslutning till den virtuella datorn genom att följa stegen i avsnittet [Anslut till den virtuella webbserverdatorn från Internet](#connect-from-internet) i den här artikeln.
2. Klicka på Start i det nedre vänstra hörnet av Windows-skrivbordet och börja sedan skriva *fjärrskrivbord*. När du ser **Anslutning till fjärrskrivbord** på startmenyn klickar du på det alternativet.
3. Ange *MyDBServer* som datornamn i dialogrutan **Anslutning till fjärrskrivbord** och klicka på **Anslut**.
4. Ange det användarnamn och lösenord du angav i steg 3 i avsnittet [Skapa den virtuella databasserverdatorn](#create-database-server-vm) i den här artikeln och klicka på **OK**.
5. Om du ser en dialogruta med information om att det inte går att verifiera fjärrdatorns identitet klickar du på **Ja**.
6. Låt fjärranslutningen vara öppen till båda servrarna när du utför stegen i nästa avsnitt.

Det här gör att du kan ansluta till den virtuella databasserverdatorn från den virtuella webbserverdatorn:

- Inkommande TCP/3389-anslutningar är aktiverade för alla IP-källadresser i nätverkssäkerhetsgruppen som skapades i steg 5 i avsnittet [Skapa den virtuella databasserverdatorn](#create-database-server-vm) i den här artikeln.
- Du initierade anslutningen från den virtuella webbserverdatorn, som är ansluten till samma VNet som den virtuella databasserverdatorn. Om du vill ansluta till en virtuell dator som inte har tilldelats en offentlig IP-adress måste du ansluta från en annan virtuell dator som är ansluten till samma VNet, även om den virtuella datorn är ansluten till ett annat undernät.
- Även om de virtuella datorerna är anslutna till olika undernät så skapar Azure standardvägar för anslutning mellan undernät. Du kan däremot åsidosätta standardvägarna genom att skapa din egen väg. Mer information om routning och vägar i Azure finns i artikeln [Användardefinierade vägar](virtual-networks-udr-overview.md).

Om du försöker initiera en fjärranslutning till den virtuella databasserverdatorn från internet, som du gjorde i avsnittet [Anslut till den virtuella webbserverdatorn från internet](#connect-from-internet) i den här artikeln, så ser du att alternativet **Anslut** är nedtonat. Alternativet Anslut är nedtonat eftersom ingen offentlig IP-adress har tilldelats till den virtuella datorn, så det går inte att ansluta till datorn från internet.

### <a name="connect-to-the-internet-from-the-database-server-vm"></a>Anslut till internet från den virtuella databasserverdatorn

Så här ansluter du till internet från den virtuella databasserverdatorn:

1. Om du inte redan har en fjärranslutning öppen till den virtuella datorn MyDBServer från den virtuella datorn MyWebServer så följer du stegen i avsnittet [Anslut till den virtuella databasserverdatorn från den virtuella webbserverdatorn](#webserver-to-dbserver) i den här artikeln.
2. Öppna Internet Explorer från Windows-skrivbordet i den virtuella datorn MyDBServer och gör på samma sätt i dialogrutorna som du gjorde i steg 2 och 3 i avsnittet [Anslut till internet från den virtuella webbserverdatorn](#connect-to-internet) i den här artikeln.
3. Ange [bing.com](http:www.bing.com) i adressfältet.
4. Klicka på **Lägg till** i dialogrutan i Internet Explorer. Klicka på **Lägg till** och sedan på **Stäng** i dialogrutan **Betrodda platser**. Gör på samma sätt i eventuella ytterligare dialogrutor.
5. Ange *whatsmyipaddress* på Bing-söksidan och klicka på förstoringsglaset. Bing returnerar den offentliga IP-adress som för närvarande har tilldelats till den virtuella datorn via Azure-infrastrukturen. 6. Stäng fjärranslutningen till den virtuella datorn MyDBServer från den virtuella datorn MyWebServer och stäng sedan fjärranslutningen till den virtuella datorn MyWebServer.

Den utgående anslutningen till internet tillåts eftersom all utgående trafik tillåts som standard, även om ingen offentlig IP-adressresurs har tilldelats till den virtuella datorn MyDBServer. Alla virtuella datorer kan som standard ansluta utgående till internet, med eller utan någon tilldelad offentlig IP-adressresurs. Du kan däremot inte ansluta till den offentliga IP-adressen från internet, som du kunde för den virtuella datorn MyWebServer som fått en offentlig IP-adressresurs tilldelad.

## <a name="delete-resources"></a>Ta bort alla resurser

Så här tar du bort alla resurser som skapats i den här artikeln:

1. Visa resursgruppen MyRG genom att följa steg 1–3 i avsnittet [Granska resurser](#review) i den här artikeln. Granska på nytt resurserna i resursgruppen. Om du skapade resursgruppen MyRG enligt föregående steg så ser du de 12 resurserna som visas på bilden i steg 4.
2. Klicka på knappen **Ta bort** i bladet MyRG.
3. Du måste ange namnet på resursgruppen i portalen som bekräftelse på att du vill ta bort den. Om du ser andra resurser än de som visas i steg 4 i avsnittet [Granska resurser](#review) i den här artikeln klickar du på **Avbryt**. Om du bara ser de 12 resurser som skapats i den här artikeln anger du *MyRG* som namn på resursgruppen och klickar på **Ta bort**. När du tar bort en resursgrupp så tas alla resurser i resursgruppen bort, så du måste alltid kontrollera innehållet i en resursgrupp innan du tar bort den. Portalen tar bort alla resurser som finns i resursgruppen och sedan tas själva resursgruppen bort. Den här processen tar flera minuter.

## <a name="next-steps"></a>Nästa steg

I den här övningen har du skapat ett VNet och två virtuella datorer. Du angav anpassade inställningar när du skapade de virtuella datorerna och godkände flera standardinställningar. Vi rekommenderar att du läser följande artiklar innan du distribuerar VNet-förekomster och virtuella datorer i produktionsmiljö, så att du förstår alla tillgängliga inställningar:

- [Virtuella nätverk](virtual-networks-overview.md)
- [Offentliga IP-adresser](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- [Nätverksgränssnitt](virtual-network-network-interface.md)
- [Nätverkssäkerhetsgrupper](virtual-networks-nsg.md)
- [Virtuella datorer](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

