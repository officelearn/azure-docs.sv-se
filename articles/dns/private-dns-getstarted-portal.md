---
title: Snabbstart - Skapa en privat Azure DNS-zon med Azure-portalen
description: I den här snabbstarten skapar och testar du en privat DNS-zon och spelar in i Azure DNS. Det här är en steg-för-steg-guide för att skapa och hantera din första privata DNS-zon och spela in med Azure-portalen.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/11/2019
ms.author: rohink
ms.openlocfilehash: 52bf9e061eb57c7ce6ea698b7468b5ba5e11b4e8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78244982"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Snabbstart: Skapa en privat Azure DNS-zon med Azure-portalen

Den här snabbstarten går igenom stegen för att skapa din första privata DNS-zon och spela in med Azure-portalen.

En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera en privat DNS-zon i det virtuella nätverket anger du den lista över virtuella nätverk som får lösa poster i zonen.  Dessa kallas *länkade* virtuella nätverk. När autoregistration är aktiverat uppdaterar Azure DNS också zonposterna när en virtuell dator skapas, ändrar sin IP-adress eller tas bort.

I den här snabbstarten lär du dig att:

> [!div class="checklist"]
> * Skapa en privat DNS-zon
> * Skapa ett virtuellt nätverk
> * Länka det virtuella nätverket
> * Skapa virtuella testdatorer
> * Skapa en ytterligare DNS-post
> * Testa den privata zonen

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Om du vill kan du slutföra den här snabbstarten med [Azure PowerShell](private-dns-getstarted-powershell.md) eller [Azure CLI](private-dns-getstarted-cli.md).

## <a name="create-a-private-dns-zone"></a>Skapa en privat DNS-zon

I följande exempel skapas en DNS-zon som kallas **private.contoso.com** i en resursgrupp som heter **MyAzureResourceGroup**.

En DNS-zon innehåller DNS-poster för en domän. Om du vill låta Azure DNS vara värd för din domän skapar du en DNS-zon för det domännamnet.

![Sökning i privata DNS-zoner](media/private-dns-portal/search-private-dns.png)

1. Skriv **privata DNS-zoner** i söktextrutan i portalen och tryck på **Retur**.
1. Välj **Privat DNS-zon**.
2. Välj **Skapa privat dns-zon**.

1. Skriv eller välj följande värden på sidan **Skapa privat DNS-zon:**

   - **Resursgrupp**: Välj **Skapa ny**, ange *MyAzureResourceGroup*och välj **OK**. Resursgruppens namn måste vara unikt inom Azure-prenumerationen.
   -  **Namn**: Skriv *private.contoso.com* för det här exemplet.
1. För **Resursgruppsplats**väljer du **Västra centrala USA**.

1. Välj **Granska + Skapa**.

1. Välj **Skapa**.

Det kan ta några minuter att skapa zonen.

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resursgruppnamn>**  | MyAzureResourceGroup (Välj befintlig resursgrupp) |
| **\<>virtuellt nätverksnamn** | MyAzureVNet          |
| **\<regionnamn>**          | USA, västra centrala      |
| **\<IPv4-adress-utrymme>**   | 10.2.0.0\16          |
| **\<>i>**          | MyAzureSubnet (0)        |
| **\<>för>** | 10.2.0.0\24          |


[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="link-the-virtual-network"></a>Länka det virtuella nätverket

Om du vill länka den privata DNS-zonen till ett virtuellt nätverk skapar du en virtuell nätverkslänk.

![Lägg till länk till virtuella nätverk](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Öppna resursgruppen **MyAzureResourceGroup** och välj **den private.contoso.com** privata zonen.
2. Välj **Virtuella nätverkslänkar**i den vänstra rutan .
3. Välj **Lägg till**.
4. Skriv **myLink** för **länknamnet**.
5. För **virtuellt nätverk**väljer du **myAzureVNet**.
6. Markera kryssrutan **Aktivera automatisk registrering.**
7. Välj **OK**.

## <a name="create-the-test-virtual-machines"></a>Skapa de virtuella testdatorerna

Nu skapar du två virtuella datorer så att du kan testa din privata DNS-zon:

1. På portalsidan längst till vänster väljer du **Skapa en resurs**och väljer sedan Windows Server **2016 Datacenter**.
1. Välj **MyAzureResourceGroup** för resursgruppen.
1. Skriv **myVM01** - för namnet på den virtuella datorn.
1. Välj **Västra centrala USA** för **regionen**.
1. Ange ett namn på administratörsanvändarnamnet.
2. Ange ett lösenord och bekräfta lösenordet.
5. För **inkommande inkommande portar**väljer du **Tillåt valda portar**och väljer sedan **RDP (3389)** för **Välj inkommande portar**.
10. Acceptera de andra standardinställningarna för sidan och klicka sedan på **Nästa: Diskar >**.
11. Acceptera standardvärdena på sidan **Diskar** och klicka sedan på **Nästa: Nätverk >**.
1. Kontrollera att **myAzureVNet** är valt för det virtuella nätverket.
1. Acceptera de andra standardinställningarna för sidan och klicka sedan på **Nästa: Hantering >**.
2. För **Startdiagnostik**väljer du **Av**, accepterar de andra standardinställningarna och väljer sedan Granska **+ skapa**.
1. Granska inställningarna och klicka sedan på **Skapa**.

Upprepa dessa steg och skapa en annan virtuell dator med namnet **myVM02**.

Det tar några minuter för båda virtuella datorerna att slutföras.

## <a name="create-an-additional-dns-record"></a>Skapa en ytterligare DNS-post

 I följande exempel skapas en post med det relativa namnet **db** i DNS-zonen **private.contoso.com**i resursgruppen **MyAzureResourceGroup**. Det fullständigt kvalificerade namnet på postuppsättningen är **db.private.contoso.com**. Posttypen är "A", med IP-adressen **för myVM01**.

1. Öppna resursgruppen **MyAzureResourceGroup** och välj **den private.contoso.com** privata zonen.
2. Välj **+ Postuppsättning**.
3. För **Namn**skriver **du db**.
4. För **IP-adress**skriver du den IP-adress som visas för **myVM01**. Detta bör registreras automatiskt när den virtuella datorn startade.
5. Välj **OK**.

## <a name="test-the-private-zone"></a>Testa den privata zonen

Nu kan du testa namnmatchningen för din **private.contoso.com** privata zon.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurera virtuella datorer för att tillåta inkommande ICMP

Du kan använda ping-kommandot för att testa namnmatchning. Konfigurera därför brandväggen på båda de virtuella datorerna att tillåta inkommande ICMP-paket.

1. Anslut till myVM01 och öppna ett Windows PowerShell-fönster med administratörsbehörighet.
2. Kör följande kommando:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
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

När det inte längre behövs tar du bort resursgruppen **MyAzureResourceGroup** för att ta bort de resurser som skapas i den här snabbstarten.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Scenarier med Azure DNS Private Zones](private-dns-scenarios.md)

