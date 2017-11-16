---
title: "Hantera DNS-poster i Azure DNS med hjälp av Azure CLI 1.0 | Microsoft Docs"
description: "Hantera DNS-postuppsättningar och på Azure DNS-poster när värd för din domän på Azure DNS. Alla CLI 1.0-kommandon för åtgärder på uppsättningar av poster och poster."
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/20/2016
ms.author: jonatul
ms.openlocfilehash: 76473349b2c6121d8f289a86b46c10a8b8697b34
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="manage-dns-records-in-azure-dns-using-the-azure-cli-10"></a>Hantera DNS-poster i Azure DNS med hjälp av Azure CLI 1.0

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Den här artikeln visar hur du hanterar DNS-posterna för DNS-zonen med hjälp av plattformsoberoende Azure-kommandoradsgränssnittet (CLI) som är tillgänglig för Windows, Mac och Linux. Du kan också hantera DNS-posterna med [Azure PowerShell](dns-operations-recordsets.md) eller [Azure-portalen](dns-operations-recordsets-portal.md).

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften

Du kan slutföra uppgiften med någon av följande CLI-versioner:

* [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md) – vår CLI för distributionsmodellerna klassisk och resurshantering.
* [Azure CLI 2.0](dns-operations-recordsets-cli.md) – vår nästa generations CLI för distributionsmodellen resurshantering.

Exemplen i den här artikeln förutsätter att du redan har [installerat Azure CLI 1.0 loggat in, och skapa en DNS-zon](dns-operations-dnszones-cli-nodejs.md).

## <a name="introduction"></a>Introduktion

Innan du skapar DNS-poster i Azure DNS, måste du först förstå hur Azure DNS organiserar DNS-poster i DNS-postuppsättningar.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Mer information om DNS-poster i Azure DNS finns i [DNS-zoner och poster](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Skapa en DNS-post

Skapa en DNS-post genom att använda kommandot `azure network dns record-set add-record`. Om du vill ha hjälp, så gå till `azure network dns record-set add-record -h`.

När du skapar en post måste du ange resursgruppsnamn, zonnamn, postuppsättningsnamn, posttyp och information om posten som skapas. Namnet på postuppsättningen anges måste vara en *relativa* namn, vilket innebär att den inte får innehålla zonnamnet.

Om postuppsättningen inte redan finns, skapar det här kommandot den åt dig. Om uppsättningen av poster redan det här kommandot adda posten som du anger att den befintliga posten anges.

Om en ny postuppsättning skapas, används ett standard TTL-värde (Time to Live) på 3600. Instruktioner om hur du använder olika TTLs finns [skapa en DNS-postuppsättning](#create-a-dns-record-set).

Följande exempel skapar en A-post som heter *www* i zonen *contoso.com* i resursgruppen *MyResourceGroup*. IP-adressen för A-posten är *1.2.3.4*.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

Skapa en post i apex i zonen (i det här fallet ”contoso.com”), Använd postnamnet ”@”, inklusive citattecknen:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Skapa en uppsättning av DNS-poster

I ovanstående exempel DNS-posten antingen har lagts till i en befintlig postuppsättning eller uppsättningen av poster har skapats *implicit*. Du kan också skapa uppsättningen av poster *explicit* innan du lägger till poster. Azure DNS stöder 'empty-postuppsättningar som kan fungera som en platshållare för att reservera en DNS-namn innan du skapar DNS-poster. Tom postuppsättningar syns i Azure DNS-kontrollplan, men visas inte i Azure DNS-namnservrar.

Postuppsättningar skapas med hjälp av den `azure network dns record-set create` kommando. Om du vill ha hjälp, så gå till `azure network dns record-set create -h`.

Skapar posten explicit kan du ange postuppsättning egenskaper som den [Time To Live (TTL)](dns-zones-records.md#time-to-live) och metadata. [Postuppsättning metadata](dns-zones-records.md#tags-and-metadata) kan användas för att koppla programspecifika data till varje postuppsättningen, som nyckel / värde-par.

I följande exempel skapas en tom postuppsättning med en 60-sekunders TTL med hjälp av den `--ttl` parameter (kort form `-l`):

```azurecli
azure network dns record-set create MyResourceGroup contoso.com www A --ttl 60
```

I följande exempel skapas en postuppsättning med två metadataposter ”Avd = ekonomi” och ”miljö = produktion”, med hjälp av den `--metadata` parameter (kort form `-m`):

```azurecli
azure network dns record-set create MyResourceGroup contoso.com www A --metadata "dept=finance;environment=production"
```

Att ha skapat en tom postuppsättningen, poster kan läggas till med `azure network dns record-set add-record` enligt beskrivningen i [skapa en DNS-post](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Skapa poster för andra typer

Har sett i detalj hur du skapar ”A” poster, visar i följande exempel hur du skapar andra posttyper som stöds av Azure DNS-post.

Parametrarna som används för att ange postdata varierar beroende på posttypen. För en post av typen "A", anger du exempelvis IPv4-adressen med parametern `-a <IPv4 address>`. Parametrarna för varje posttyp kan visas med hjälp av `azure network dns record-set add-record -h`.

I varje fall visar vi hur du skapar en enskild post. Posten läggs till den befintliga uppsättningen av poster eller en uppsättning av poster som skapats implicit. Mer information om hur du skapar postuppsättningar och definierar post parametern uttryckligen finns i avsnittet [skapa en DNS-postuppsättning](#create-a-dns-record-set).

Vi inte ger ett exempel för att skapa en SOA-poster, eftersom SOAs skapas och tas bort tillsammans med varje DNS-zon och kan inte skapas eller tas bort separat. Dock [SOA kan ändras, som visas i en senare exempel](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record"></a>Skapa en AAAA-post

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com test-aaaa AAAA --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-a-caa-record"></a>Skapa en CAA-post

I följande exempel visas hur du skapar en CAA-post. 

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com ca1 CAA --caaflags 0 --caatag issue --caavalue "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Skapa en CNAME-post

> [!NOTE]
> DNS-standarden inte tillåter CNAME-poster på apex för en zon (`-Name "@"`), eller tillåter att postuppsättningar som innehåller fler än en post.
> 
> Mer information finns i [CNAME-poster](dns-zones-records.md#cname-records).

```azurecli
azure network dns record-set add-record  MyResourceGroup contoso.com  test-cname CNAME --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Skapa en MX-post

I det här exemplet använder vi postuppsättningsnamnet "@" för att skapa MX-posten i basdomänen (i det här fallet "contoso.com").

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com  "@" MX --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Skapa en NS-post

```azurecli
azure network dns record-set add-record MyResourceGroup  contoso.com  test-ns NS --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Skapa en PTR-post

I det här fallet ”min-arpa-zone.com' representerar ARPA zonen som motsvarar IP-adressintervall. Varje PTR-post som har angetts i den här zonen motsvarar en IP-adress i IP-intervallet.  Postnamnet ”10” är den sista oktetten i IP-adress inom den här IP-adressintervall som representeras av den här posten.

```azurecli
azure network dns record-set add-record MyResourceGroup my-arpa-zone.com "10" PTR --ptrdname "myservice.contoso.com"
```

### <a name="create-an-srv-record"></a>Skapa en SRV-post

När du skapar en [SRV-postuppsättning](dns-zones-records.md#srv-records), ange den  *\_service* och  *\_protokollet* i postuppsättningens namn. Det finns inget behov av att inkludera ”@” i namnet på postuppsättningen när du skapar en SRV-post på zonens apex.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com  "_sip._tls" SRV --priority 10 --weight 5 --port 8080 --target "sip.contoso.com"
```

### <a name="create-a-txt-record"></a>Skapa en TXT-post

I följande exempel visas hur du skapar en TXT-post. Mer information om den maximala stränglängden som stöds i TXT-poster finns [TXT-poster](dns-zones-records.md#txt-records).

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com test-txt TXT --text "This is a TXT record"
```

## <a name="get-a-record-set"></a>Hämta en uppsättning poster

Använd för att hämta en befintlig postuppsättning `azure network dns record-set show`. Om du vill ha hjälp, så gå till `azure network dns record-set show -h`.

När du skapar en post eller en postuppsättning postuppsättningsnamnet måste vara en *relativa* namn, vilket innebär att den inte får innehålla zonnamnet. Du måste också ange posttypen zonen som innehåller uppsättningen av poster och resursgruppen som innehåller zonen.

I följande exempel hämtas posten *www* av typen A från zonen *contoso.com* i resursgruppen *MyResourceGroup*:

```azurecli
azure network dns record-set show MyResourceGroup contoso.com www A
```

## <a name="list-record-sets"></a>Lista över postuppsättningar

Du kan visa alla poster i en DNS-zon med hjälp av den `azure network dns record-set list` kommando. Om du vill ha hjälp, så gå till `azure network dns record-set list -h`.

Det här exemplet returnerar alla postuppsättningar i zonen *contoso.com*, i resursgrupp *MyResourceGroup*, oavsett namn eller posttyp:

```azurecli
azure network dns record-set list MyResourceGroup contoso.com
```

Det här exemplet returnerar alla uppsättningar av poster som matchar den givna posttypen (i det här fallet ”A” poster):

```azurecli
azure network dns record-set list MyResourceGroup contoso.com --type A
```

## <a name="add-a-record-to-an-existing-record-set"></a>Lägga till en post i en befintlig postuppsättning

Du kan använda `azure network dns record-set add-record` att skapa en post i en ny postuppsättning eller lägga till en post i en befintlig postuppsättningen.

Mer information finns i [skapa en DNS-post](#create-a-dns-record) och [skapa poster för andra typer](#create-records-of-other-types) ovan.

## <a name="remove-a-record-from-an-existing-record-set"></a>Ta bort en post från en befintlig post.

Ta bort en DNS-post från en befintlig post med `azure network dns record-set delete-record`. Om du vill ha hjälp, så gå till `azure network dns record-set delete-record -h`.

Det här kommandot tar bort en DNS-post från en postuppsättning. Om den sista posten i en postuppsättning raderas postuppsättningen själva är **inte** tas bort. I stället lämnas en tom postuppsättning. Om du vill ta bort posten i stället Se [ta bort en postuppsättning](#delete-a-record-set).

Du måste ange posten som ska tas bort och zonen den bör tas bort från, med samma parametrar som när du skapar en post med hjälp av `azure network dns record-set add-record`. Dessa parametrar beskrivs i [skapa en DNS-post](#create-a-dns-record) och [skapa poster för andra typer](#create-records-of-other-types) ovan.

Det här kommandot uppmanas att bekräfta. Den här uppmaningen kan undertryckas med hjälp av den `--quiet` växla (kort form `-q`).

I följande exempel tar bort en post med värdet '1.2.3.4 ”från posten uppsättning med namnet *www* i zonen *contoso.com*, i resursgrupp *MyResourceGroup*. Bekräfta åtgärden ignoreras.

```azurecli
azure network dns record-set delete-record MyResourceGroup contoso.com www A -a 1.2.3.4 --quiet
```

## <a name="modify-an-existing-record-set"></a>Ändra en befintlig postuppsättning

Varje post innehåller en [time to live (TTL)](dns-zones-records.md#time-to-live), [metadata](dns-zones-records.md#tags-and-metadata), och DNS-poster. I följande avsnitt beskrivs hur du ändrar var och en av dessa egenskaper.

### <a name="to-modify-an-a-aaaa-mx-ns-ptr-srv-or-txt-record"></a>Att ändra en A, AAAA, MX, NS, PTR, SRV och TXT-post

Om du vill ändra en befintlig post av typen A, AAAA, MX, NS, PTR, SRV och TXT, bör du först lägga till en ny post och ta sedan bort den befintliga posten. För detaljerade anvisningar om hur du tar bort och lägga till poster i avsnitten tidigare i den här artikeln.

I följande exempel visas hur du ändrar en ”A” post från IP-adress: 1.2.3.4 till IP-adressen 5.6.7.8:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 5.6.7.8
azure network dns record-set delete-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

### <a name="to-modify-a-cname-record"></a>Så här ändrar du en CNAME-post

Om du vill ändra en CNAME-post använder `azure network dns record-set add-record` att lägga till det nya värdet för posten. Till skillnad från andra typer av poster, får en CNAME-postuppsättning endast innehålla en enskild post. Den befintliga posten är därför *ersättas* när den nya posten har lagts till och behöver inte tas bort separat.  Du uppmanas att godkänna den här ersättning.

Exemplet ändrar CNAME-postuppsättning *www* i zonen *contoso.com*, i resursgrupp *MyResourceGroup*, peka på 'www.fabrikam.net' i stället för det befintliga värdet:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www CNAME --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Att ändra en SOA-post

Använd `azure network dns record-set set-soa-record` att ändra SOA för en viss DNS-zon. Om du vill ha hjälp, så gå till `azure network dns record-set set-soa-record -h`.

I följande exempel visas hur du ställer in egenskapen 'e-post för SOA-post för zonen *contoso.com* i resursgruppen *MyResourceGroup*:

```azurecli
azure network dns record-set set-soa-record rg1 contoso.com --email admin.contoso.com
```


### <a name="to-modify-ns-records-at-the-zone-apex"></a>Att ändra NS-poster på zonens apex

NS-postuppsättning på zonens apex skapas automatiskt med varje DNS-zon. Den innehåller namnen på Azure DNS-namnservrar tilldelas zonen.

Du kan lägga till ytterligare servrar till den här NS-post inställda på en värd domäner med mer än en DNS-leverantör har stöd ett namn. Du kan också ändra TTL-värde och metadata för den här postuppsättningen. Du kan inte ta bort eller ändra de i förväg Azure DNS-namnservrarna.

Observera att detta gäller endast NS-postuppsättning på zonens apex. Andra NS postuppsättningar i zonen (som används för att delegera underordnade zoner) kan ändras utan begränsning.

I följande exempel visas hur du lägger till en ytterligare namnserver NS-postuppsättning på zonens apex:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Att ändra TTL-värde på en befintlig postuppsättning

Om du vill ändra TTL-värde på en befintlig postuppsättningen, Använd `azure network dns record-set set`. Om du vill ha hjälp, så gå till `azure network dns record-set set -h`.

I följande exempel visas hur du ändrar en postuppsättning TTL-värde, i det här fallet till 60 sekunder:

```azurecli
azure network dns record-set set MyResourceGroup contoso.com www A --ttl 60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Ändra metadata för en befintlig postuppsättning

[Postuppsättning metadata](dns-zones-records.md#tags-and-metadata) kan användas för att koppla programspecifika data till varje postuppsättningen, som nyckel / värde-par. Om du vill ändra metadata för en befintlig postuppsättningen, Använd `azure network dns record-set set`. Om du vill ha hjälp, så gå till `azure network dns record-set set -h`.

I följande exempel visas hur du ändrar en postuppsättning med två metadataposter ”Avd = ekonomi” och ”miljö = produktion”, med hjälp av den `--metadata` parameter (kort form `-m`). Observera att alla befintliga metadata är *ersättas* med angivna värden.

```azurecli
azure network dns record-set set MyResourceGroup contoso.com www A --metadata "dept=finance;environment=production"
```

## <a name="delete-a-record-set"></a>Ta bort en uppsättning poster

Postuppsättningar kan tas bort med hjälp av den `azure network dns record-set delete` kommando. Om du vill ha hjälp, så gå till `azure network dns record-set delete -h`. En postuppsättning också tar du bort alla posterna i uppsättningen av poster.

> [!NOTE]
> Du kan inte ta bort SOA och NS-post anger på zonens apex (`-Name "@"`).  Dessa skapas automatiskt när zonen skapades och tas bort automatiskt när zonen tas bort.

I följande exempel tar bort posten namngivna *www* av typen A från zonen *contoso.com* i resursgruppen *MyResourceGroup*:

```azurecli
azure network dns record-set delete MyResourceGroup contoso.com www A
```

Du uppmanas att bekräfta borttagningen. Om du vill ignorera den här uppmaningen använder den `--quiet` växla (kort form `-q`).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [zoner och poster i Azure DNS](dns-zones-records.md).
<br>
Lär dig hur du [skydda zoner och poster](dns-protect-zones-recordsets.md) när du använder Azure DNS.
