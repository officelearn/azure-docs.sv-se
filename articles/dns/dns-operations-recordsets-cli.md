---
title: Hantera DNS-poster i Azure DNS med Azure CLI | Microsoft Docs
description: Hantera DNS-postuppsättningar och poster i Azure DNS när du har din domän i Azure DNS.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: victorh
ms.openlocfilehash: 4864a46b91b4e243ce6a2ae3d9d36df28fe74d8d
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650984"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>Hantera DNS-poster och postuppsättningar i Azure DNS med Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Den här artikeln visar hur du hanterar DNS-poster för din DNS-zon med hjälp av plattformsoberoende Azure CLI, som är tillgängligt för Windows, Mac och Linux. Du kan också hantera DNS-posterna med [Azure PowerShell](dns-operations-recordsets.md) eller [Azure-portalen](dns-operations-recordsets-portal.md).

Exemplen i den här artikeln förutsätter att du redan har [installerat Azure CLI, loggat in och skapat en DNS-zon](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Introduktion

Innan du skapar DNS-poster i Azure DNS, måste du först förstå hur Azure DNS organiserar DNS-poster i DNS-postuppsättningar.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Mer information om DNS-poster i Azure DNS finns i [DNS-zoner och poster](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Skapa en DNS-post

Du kan skapa en DNS-post med den `az network dns record-set <record-type> add-record` kommando (där `<record-type>` är typ av post, dvs en, srv, txt, etc.) Om du vill ha hjälp, så gå till `az network dns record-set --help`.

När du skapar en post måste du ange resursgruppsnamn, zonnamn, postuppsättningsnamn, posttyp och information om posten som skapas. Namnet på postuppsättningen beroende måste vara en *relativa* namn, vilket innebär att den får inte zonnamnet.

Om postuppsättningen inte redan finns, skapar det här kommandot den åt dig. Om postuppsättningen redan finns, lägger det här kommandot till posten som du anger till den befintliga postuppsättningen.

Om en ny postuppsättning skapas, används ett standard TTL-värde (Time to Live) på 3600. Anvisningar om hur du använder olika TTL: er finns i [skapa en DNS-postuppsättning](#create-a-dns-record-set).

Följande exempel skapar en A-post som heter *www* i zonen *contoso.com* i resursgruppen *MyResourceGroup*. IP-adressen för A-posten är *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Skapa en post överst i zonen (i det här fallet ”contoso.com”), använda postnamnet ”\@”, inklusive citattecken:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Skapa en DNS-postuppsättning

DNS-posten lades i ovanstående exempel, antingen till en befintlig uppsättning av poster eller postuppsättningen skapades *implicit*. Du kan också skapa postuppsättningen *uttryckligen* innan du lägger till poster. Azure DNS stöder ”tom” postuppsättningar som kan fungera som en platshållare för att reservera ett DNS-namn innan du skapar DNS-poster. Tom postuppsättningar visas i Azure DNS kontrollplanet, men visas inte i Azure DNS-namnservrarna.

Postuppsättningar skapas med hjälp av den `az network dns record-set <record-type> create` kommando. Om du vill ha hjälp, så gå till `az network dns record-set <record-type> create --help`.

Skapa postuppsättningen uttryckligen kan du ange postuppsättningens egenskaper som den [Time To Live (TTL)](dns-zones-records.md#time-to-live) och metadata. [Postuppsättningens metadata](dns-zones-records.md#tags-and-metadata) kan användas för att associera programspecifika data med varje uppsättning av poster, som nyckel / värde-par.

I följande exempel skapas en tom postuppsättning av typen ”A” med en 60-sekunders TTL med hjälp av den `--ttl` parametern (kort form `-l`):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

I följande exempel skapas en postuppsättning med två metadataposter ”Avd = ekonomi” och ”miljö = produktion”, med hjälp av den `--metadata` parameter:

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Har skapat en tom postuppsättning, poster kan läggas till med hjälp av `azure network dns record-set <record-type> add-record` enligt beskrivningen i [skapa en DNS-post](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Skapa poster för andra typer

Att ha sett i detalj hur du kan skapa ”A”-poster, visas i följande exempel hur du skapar post med andra posttyper som stöds av Azure DNS.

Parametrarna som används för att ange postdata varierar beroende på posttypen. För en post av typen "A", anger du exempelvis IPv4-adressen med parametern `--ipv4-address <IPv4 address>`. Parametrarna för varje posttyp kan anges med hjälp av `az network dns record-set <record-type> add-record --help`.

I varje fall visar vi hur du skapar en enskild post. Posten läggs till i den befintliga postuppsättningen eller en uppsättning av poster skapas implicit. Mer information om hur du skapar postuppsättningar och definiera post ange parametern uttryckligen, se [skapa en DNS-postuppsättning](#create-a-dns-record-set).

Vi ger inte ett exempel för att skapa en SOA-postuppsättning eftersom SOAs skapas och tas bort med varje DNS-zon och kan inte skapas eller tas bort separat. Dock [SOA kan ändras, som visas i en senare exempel](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record"></a>Skapa en AAAA-post

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>Skapa en CAA-post

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Skapa en CNAME-post

> [!NOTE]
> DNS-standarden inte tillåter CNAME-poster överst i en zon (`--Name "@"`), eller tillåter att uppsättningar av poster som innehåller fler än en post.
> 
> Mer information finns i [CNAME-poster](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Skapa en MX-post

I det här exemplet använder vi postuppsättningsnamnet ”\@” att skapa MX-posten i basdomänen (i det här fallet ”contoso.com”).

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Skapa en NS-post

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Skapa en PTR-post

I det här fallet ”mitt-arpa-zone.com” ARPA-zonen som representerar ditt IP-adressintervall. Varje PTR-post som har angetts i den här zonen motsvarar en IP-adress i IP-intervallet.  Postnamnet ”10” är den sista oktetten för IP-adress i IP-intervallet som representeras av den här posten.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Skapa en SRV-post

När du skapar en [SRV-postuppsättning](dns-zones-records.md#srv-records), ange den  *\_service* och  *\_protokollet* i namnet på postuppsättningen. Det finns inget behov att inkludera ”\@” i namnet på postuppsättningen när du skapar en SRV-posten i basdomänen.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Skapa en TXT-post

I följande exempel visas hur du skapar en TXT-post. Mer information om den maximala stränglängden som stöds i TXT-poster finns i [TXT-poster](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Hämta en uppsättning av poster

Använd för att hämta en befintlig uppsättning av poster `az network dns record-set <record-type> show`. Om du vill ha hjälp, så gå till `az network dns record-set <record-type> show --help`.

När du skapar en post eller en uppsättning av poster, namnet på postuppsättningen beroende måste vara en *relativa* namn, vilket innebär att den får inte zonnamnet. Du måste också ange posttypen zonen som innehåller uppsättningen av poster och resursgruppen som innehåller zonen.

I följande exempel hämtas posten *www* av typen A från zonen *contoso.com* i resursgruppen *MyResourceGroup*:

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Listan över postuppsättningar

Du kan visa alla poster i en DNS-zon med hjälp av den `az network dns record-set list` kommando. Om du vill ha hjälp, så gå till `az network dns record-set list --help`.

Det här exemplet returnerar alla postuppsättningar i zonen *contoso.com*, i resursgruppen *MyResourceGroup*, oavsett namn eller typ av post:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

Det här exemplet returnerar alla postuppsättningar som matchar den givna posttypen (i det här fallet ”A” poster):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Lägga till en post i en befintlig uppsättning av poster

Du kan använda `az network dns record-set <record-type> add-record` att skapa en post i en ny postuppsättning eller lägga till en post i en befintlig postuppsättning.

Mer information finns i [skapa en DNS-post](#create-a-dns-record) och [skapa poster för andra typer av](#create-records-of-other-types) ovan.

## <a name="remove-a-record-from-an-existing-record-set"></a>Ta bort en post från en befintlig uppsättning av poster.

Ta bort en DNS-post från en befintlig uppsättning av poster med `az network dns record-set <record-type> remove-record`. Om du vill ha hjälp, så gå till `az network dns record-set <record-type> remove-record -h`.

Det här kommandot tar bort en DNS-post från en postuppsättning. Om den sista posten i en postuppsättning tas bort raderas även postuppsättningen själva. Om du vill behålla tom postuppsättningen i stället använda den `--keep-empty-record-set` alternativet.

Du måste ange posten som ska tas bort och zonen den bör tas bort från, med samma parametrar som när du skapar en post med hjälp av `az network dns record-set <record-type> add-record`. Dessa parametrar beskrivs i [skapa en DNS-post](#create-a-dns-record) och [skapa poster för andra typer av](#create-records-of-other-types) ovan.

I följande exempel tar bort en post med värdet ”1.2.3.4” från posten uppsättning med namnet *www* i zonen *contoso.com*, i resursgruppen *MyResourceGroup*.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Ändra en befintlig uppsättning av poster

Varje postuppsättning innehåller en [time to live (TTL)](dns-zones-records.md#time-to-live), [metadata](dns-zones-records.md#tags-and-metadata), och DNS-poster. I följande avsnitt beskrivs hur du ändrar var och en av dessa egenskaper.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>To modify an A, AAAA, CAA, MX, NS, PTR, SRV, or TXT record

Om du vill ändra en befintlig post av typ A, AAAA, CAA, MX, NS, PTR, SRV och TXT, bör du först lägga till en ny post och ta sedan bort den befintliga posten. Detaljerade anvisningar om hur du tar bort och lägga till poster finns i tidigare avsnitt av den här artikeln.

I följande exempel visas hur du ändrar en ”A”-post från IP-adress 1.2.3.4 till IP-adressen 5.6.7.8:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Det går inte att lägga till, ta bort eller ändra poster i den automatiskt skapade NS-postuppsättning på zonens apex (`--Name "@"`, inklusive citattecken). För den här uppsättningen av poster är endast ändringar tillåts att ändra posten anger TTL och metadata.

### <a name="to-modify-a-cname-record"></a>Ändra en CNAME-post

Till skillnad från de flesta andra posttyper, får en CNAME-postuppsättning bara innehålla en enda post.  Därför kan du ersätta det aktuella värdet genom att lägga till en ny post och ta bort den befintliga posten som för andra typer av poster.

Använd i stället för att ändra en CNAME-post, `az network dns record-set cname set-record`. Hjälp finns i `az network dns record-set cname set-record --help`

Exemplet ändrar CNAME-postuppsättning *www* i zonen *contoso.com*, i resursgruppen *MyResourceGroup*, så att den pekar till 'www.fabrikam.net' i stället för det befintliga värde:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Ändra ett SOA-posten

Till skillnad från de flesta andra posttyper, får en CNAME-postuppsättning bara innehålla en enda post.  Därför kan du ersätta det aktuella värdet genom att lägga till en ny post och ta bort den befintliga posten som för andra typer av poster.

Använd i stället för att ändra SOA-posten, `az network dns record-set soa update`. Om du vill ha hjälp, så gå till `az network dns record-set soa update --help`.

I följande exempel visas hur du ställer in e-post ”-egenskapen för SOA-posten för zonen *contoso.com* i resursgruppen *MyResourceGroup*:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Ändra NS-poster i basdomänen

NS-postuppsättning på zonens apex skapas automatiskt med varje DNS-zon. Den innehåller namnen på Azure DNS-namnservrarna som tilldelats i zonen.

Du kan lägga till ytterligare servrar till den här NS-post anger för att stödja delad hosting domäner med fler än en DNS-leverantör ett namn. Du kan också ändra TTL-värde och metadata för den här uppsättningen av poster. Du kan inte ta bort eller ändra förifyllda Azure DNS-namnservrarna.

Observera att detta gäller endast för NS-postuppsättning på zonens apex. Andra NS postuppsättningar i din zon (som används för att delegera underordnade zoner) kan ändras utan begränsning.

I följande exempel visas hur du lägger till en ytterligare namnserver NS-postuppsättning på zonens apex:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Ändra TTL-värde på en befintlig uppsättning av poster

Använd för att ändra TTL-värde på en befintlig uppsättning av poster `azure network dns record-set <record-type> update`. Om du vill ha hjälp, så gå till `azure network dns record-set <record-type> update --help`.

I följande exempel visas hur du ändrar en postuppsättning TTL-värde, i det här fallet till 60 sekunder:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Ändra metadata för en befintlig uppsättning av poster

[Postuppsättningens metadata](dns-zones-records.md#tags-and-metadata) kan användas för att associera programspecifika data med varje uppsättning av poster, som nyckel / värde-par. Använd för att ändra metadata för en befintlig uppsättning av poster `az network dns record-set <record-type> update`. Om du vill ha hjälp, så gå till `az network dns record-set <record-type> update --help`.

I följande exempel visas hur du ändrar en postuppsättning med två metadataposter ”Avd = ekonomi” och ”miljö = produktion”. Observera att alla befintliga metadata är *ersätts* av de värden som anges.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Ta bort en postuppsättning

Uppsättningar av poster kan tas bort med hjälp av den `az network dns record-set <record-type> delete` kommando. Om du vill ha hjälp, så gå till `azure network dns record-set <record-type> delete --help`. Tar en postuppsättning även bort alla poster inom postuppsättningen.

> [!NOTE]
> Du kan inte ta bort SOA och NS-post anger i basdomänen (`--name "@"`).  Dessa skapas automatiskt när zonen skapades och tas bort automatiskt när zonen tas bort.

I följande exempel tar bort posten namngivna *www* av typen A från zonen *contoso.com* i resursgruppen *MyResourceGroup*:

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

Du uppmanas att bekräfta borttagningen. Om du vill ignorera varningen, använda den `--yes` växla.

## <a name="next-steps"></a>Nästa steg

Läs mer om [zoner och poster i Azure DNS](dns-zones-records.md).
<br>
Lär dig hur du [skydda zoner och poster](dns-protect-zones-recordsets.md) när du använder Azure DNS.
