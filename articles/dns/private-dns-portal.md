---
title: Skapa en privat Azure DNS-zon med Azure portal
description: I den här självstudien skapar och testar du en privat DNS-zon och post i Azure DNS. Det här är en stegvis guide för att skapa och hantera din första privata DNS-zon och DNS-post med hjälp av Azure portal.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 6/15/2019
ms.author: victorh
ms.openlocfilehash: e3772d8f385ed93c96f8aa2f7ee2ded8f46d4e00
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147838"
---
# <a name="tutorial-create-an-azure-dns-private-zone-using-the-azure-portal"></a>Självstudier: Skapa en privat Azure DNS-zon med Azure portal

Den här självstudien vägleder dig genom stegen för att skapa din första privata DNS-zon och DNS-post med hjälp av Azure portal.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera en privat DNS-zon i det virtuella nätverket anger du den lista över virtuella nätverk som får lösa poster i zonen.  Dessa kallas *länkade* virtuella nätverk. När autoregistrering aktiveras, Azure DNS även uppdaterar zonposter när en virtuell dator skapas, ändringar dess ”IP-adress eller tas bort.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en privat DNS-zon
> * Skapa ett virtuellt nätverk
> * Länka virtuella nätverk
> * Skapa virtuella testdatorer
> * Skapa en ytterligare DNS-post
> * Testa den privata zonen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Om du vill kan du slutföra den här självstudien med [Azure PowerShell](private-dns-getstarted-powershell.md) eller [Azure CLI](private-dns-getstarted-cli.md).

## <a name="create-a-dns-private-zone"></a>Skapa en privat DNS-zon

I följande exempel skapas en DNS-zon med namnet **private.contoso.com** i en resursgrupp med namnet **MyAzureResourceGroup**.

En DNS-zon innehåller DNS-poster för en domän. Om du vill låta Azure DNS vara värd för din domän skapar du en DNS-zon för det domännamnet.

![Privata DNS-zoner sökning](media/private-dns-portal/search-private-dns.png)

1. På portalen sökfältet skriver **privata dns-zoner** i sökrutan och tryck på **RETUR**.
1. Välj **privata DNS-zon**.
2. Välj **skapa privata DNS-zon**.

1. På den **skapa privata DNS-zon** sidan, Skriv eller Välj följande värden:

   - **Resursgrupp**: Välj **Skapa nytt**, ange *MyAzureResourceGroup*, och välj **OK**. Resursgruppens namn måste vara unikt inom Azure-prenumerationen. 
   -  **Namn på**: Typ *private.contoso.com* i det här exemplet.
1. För **resursgruppsplats**väljer **USA, västra centrala**.

1. Välj **Granska + Skapa**.

1. Välj **Skapa**.

Det kan ta några minuter att skapa zonen.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. På portalsidan längst upp till vänster, Välj **skapa en resurs**, sedan **nätverk**och välj sedan **virtuellt nätverk**.
2. För **namn**, typ **myAzureVNet**.
3. För **resursgrupp**väljer **MyAzureResourceGroup**.
4. För **plats**väljer **USA, västra centrala**.
5. Acceptera de andra standardvärdena och välj **skapa**.

## <a name="link-the-virtual-network"></a>Länka virtuella nätverk

Om du vill länka privata DNS-zonen till ett virtuellt nätverk, skapar du en länk för virtuellt nätverk.

![Lägg till länk för virtuellt nätverk](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Öppna den **MyAzureResourceGroup** resource gruppen och välj den **private.contoso.com** privat zon.
2. I det vänstra fönstret, Välj **virtuella nätverkslänkar**.
3. Välj **Lägg till**.
4. Typ **myLink** för den **Länknamnet**.
5. För **virtuellt nätverk**väljer **myAzureVNet**.
6. Välj den **aktivera automatisk registrering** markerar du kryssrutan.
7. Välj **OK**.

## <a name="create-the-test-virtual-machines"></a>Skapa de virtuella testdatorerna

Nu skapar du två virtuella datorer så att du kan testa din privata DNS-zon:

1. På portalsidan längst upp till vänster, Välj **skapa en resurs**, och välj sedan **Windows Server 2016 Datacenter**.
1. Välj **MyAzureResourceGroup** för resursgruppen.
1. Typ **myVM01** – för namnet på den virtuella datorn.
1. Välj **USA, västra centrala** för den **Region**.
1. Typ **azureadmin** för administratörens användarnamn.
2. Typ **Azure12345678** för lösenordet och bekräfta lösenordet.

5. För **offentliga inkommande portar**väljer **valda portar**, och välj sedan **RDP (port 3389)** för **Välj ingående portar**.
10. Acceptera standardinställningarna för sidan och klicka sedan på **nästa: Diskar >** .
11. Acceptera standardinställningarna på den **diskar** sidan och klicka sedan på **nästa: Nätverk >** .
1. Se till att **myAzureVNet** har valts för det virtuella nätverket.
1. Acceptera standardinställningarna för sidan och klicka sedan på **nästa: Hantering av >** .
2. För **Startdiagnostik**väljer **av**, acceptera andra standardinställningar och väljer sedan **granska + skapa**.
1. Granska inställningarna och klicka sedan på **skapa**.

Upprepa de här stegen och skapa en annan virtuell dator med namnet **myVM02**.

Det tar några minuter för både virtuella datorer att slutföra.

## <a name="create-an-additional-dns-record"></a>Skapa en ytterligare DNS-post

 I följande exempel skapas en post med det relativa namnet **db** i DNS-zonen **private.contoso.com**, i resursgruppen **MyAzureResourceGroup**. Det fullständigt kvalificerade namnet på postuppsättningen är **db.private.contoso.com**. Posttypen är ”A”, med IP-adressen för **myVM01**.

1. Öppna den **MyAzureResourceGroup** resource gruppen och välj den **private.contoso.com** privat zon.
2. Välj **+ Postuppsättning**.
3. För **namn**, typ **db**.
4. För **IP-adress**, ange IP-adress som du ser för **myVM01**. Det bör vara registrerade när den virtuella datorn startar automatiskt.
5. Välj **OK**.

## <a name="test-the-private-zone"></a>Testa den privata zonen

Nu kan du testa namnmatchning för din **private.contoso.com** privat zon.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurera virtuella datorer för att tillåta inkommande ICMP

Du kan använda ping-kommandot för att testa namnmatchning. Konfigurera därför brandväggen på båda de virtuella datorerna att tillåta inkommande ICMP-paket.

1. Anslut till myVM01 och öppna ett Windows PowerShell-fönster med administratörsbehörighet.
2. Kör följande kommando:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Upprepa för myVM02.

### <a name="ping-the-vms-by-name"></a>Pinga de virtuella datorerna efter namn

1. Från Windows PowerShell-kommandotolken för myVM02 pingar du myVM01 med hjälp av det automatiskt registrerade värddatornamnet:
   ```
   ping myVM01.private.contoso.com
   ```
   Du bör se utdata som liknar följande:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Nu pingar du det **db**-namn som du skapade tidigare:
   ```
   ping db.private.contoso.com
   ```
   Du bör se utdata som liknar följande:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Ta bort alla resurser

När resursgruppen **MyAzureResourceGroup** inte längre behövs kan du ta bort den för att ta bort de resurser som skapades i den här självstudien.


## <a name="next-steps"></a>Nästa steg

I den här självstudien distribuerade du en privat DNS-zon, skapade en DNS-post och testade zonen.
Härnäst kan du läsa mer om privata DNS-zoner.

> [!div class="nextstepaction"]
> [Använda Azure DNS för privata domäner](private-dns-overview.md)
