---
title: Skapa en privat Azure DNS-zon med Azure CLI
description: I den här självstudien skapar och testar du en privat DNS-zon och post i Azure DNS. Det här är en stegvis guide om hur du skapar och hanterar din första privata DNS-zon och DNS-post med hjälp av Azure CLI.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 7/25/2018
ms.author: victorh
ms.openlocfilehash: 27a27cfb81362b070deb0bad367ff62e8c39460c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964405"
---
# <a name="create-an-azure-dns-private-zone-using-the-azure-cli"></a>Skapa en privat Azure DNS-zon med Azure CLI

Den här självstudien visar hur du skapar din första privata DNS-zon och DNS-post med Azure CLI.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera en privat DNS-zon i det virtuella nätverket anger du den lista över virtuella nätverk som får lösa poster i zonen.  De här kallas *virtuella lösningsnätverk*. Du kan även ange ett virtuellt nätverk där Azure DNS ska bibehålla värddatorposter varje gång en virtuell dator skapas, ändrar IP-adress eller tas bort.  Det här kallas *virtuellt registreringsnätverk*.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en privat DNS-zon
> * Skapa virtuella testdatorer
> * Skapa en ytterligare DNS-post
> * Testa den privata zonen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Om du vill kan du utföra den här självstudien med [Azure PowerShell](private-dns-getstarted-powershell.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

Först skapar du en resursgrupp som ska innehålla DNS-zonen: 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-dns-private-zone"></a>Skapa en privat DNS-zon

Du skapar en DNS-zon med hjälp av kommandot `az network dns zone create` med värdet *Private* för parametern **ZoneType**. I följande exempel skapas en DNS-zon med namnet **contoso.local** i resursgruppen som heter **MyAzureResourceGroup** och det gör DNS-zonen tillgänglig för det virtuella nätverket som heter **MyAzureVnet**.

Om parametern **ZoneType** utelämnas skapas zonen som en offentlig zon. Med andra ord krävs den om du vill skapa en privat zon.

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefix 10.2.0.0/24

az network dns zone create -g MyAzureResourceGroup \
   -n contoso.local \
  --zone-type Private \
  --registration-vnets myAzureVNet
```

Om du vill skapa en zon bara för namnmatchning (ingen automatisk generering av värddatornamn) använder du parametern *resolution-vnets* i stället för parametern *registration-vnets*.

> [!NOTE]
> Du kommer inte att kunna se de automatiskt skapade posterna för värddatornamn. Senare kommer du dock att testa för att säkerställa att de finns.

### <a name="list-dns-private-zones"></a>Lista privata DNS-zoner

Använd `az network dns zone list` om du vill räkna upp DNS-zoner. Om du vill ha hjälp, så gå till `az network dns zone list --help`.

När du anger resursgrupp så listas bara zonerna i resursgruppen:

```azurecli
az network dns zone list \
  --resource-group MyAzureResourceGroup
```

Om du utesluter resursgruppen listas alla zoner i prenumerationen:

```azurecli
az network dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>Skapa de virtuella testdatorerna

Nu skapar du två virtuella datorer så att du kan testa din privata DNS-zon:

```azurecli
az vm create \
 -n myVM01 \
 --admin-username test-user \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username test-user \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --image win2016datacenter
```

Det här kan ta några minuter.

## <a name="create-an-additional-dns-record"></a>Skapa en ytterligare DNS-post

Skapa en DNS-post genom att använda kommandot `az network dns record-set [record type] add-record`. Mer information att till exempel lägga till A-poster finns i `azure network dns record-set A add-record --help`.

 I följande exempel skapas en post med det relativa namnet **db** i DNS-zonen **contoso.local** i resursgruppen **MyAzureResourceGroup**. Postuppsättningens fullständigt kvalificerade namn är **db.contoso.local**. Posttypen är ”A” med IP-adressen ”10.2.0.4”.

```azurecli
az network dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z contoso.local \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>Visa DNS-poster

Om du vill visa en lista med DNS-poster i din zon kör du:

```azurecli
az network dns record-set list \
  -g MyAzureResourceGroup \
  -z contoso.local
```
Kom ihåg att du inte automatiskt kommer att se den skapade A-posten för dina två virtuella testdatorer.

## <a name="test-the-private-zone"></a>Testa den privata zonen

Nu kan du testa namnmatchning för din privata zon **contoso.local**.

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
   ping myVM01.contoso.local
   ```
   Du bör se utdata som liknar följande:
   ```
   PS C:\> ping myvm01.contoso.local

   Pinging myvm01.contoso.local [10.2.0.4] with 32 bytes of data:
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
   ping db.contoso.local
   ```
   Du bör se utdata som liknar följande:
   ```
   PS C:\> ping db.contoso.local

   Pinging db.contoso.local [10.2.0.4] with 32 bytes of data:
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

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien distribuerade du en privat DNS-zon, skapade en DNS-post och testade zonen.
Härnäst kan du läsa mer om privata DNS-zoner.

> [!div class="nextstepaction"]
> [Använda Azure DNS för privata domäner](private-dns-overview.md)
