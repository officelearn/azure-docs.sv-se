---
title: Hantera DNS-poster i Azure DNS med Azure CLI
description: Hantera DNS-postuppsättningar och poster på Azure DNS när du är värd för din domän på Azure DNS.
author: rohinkoul
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: rohink
ms.openlocfilehash: 4e017dc940e1d32888ff279904e44d34db1fd5c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936888"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>Hantera DNS-poster och postmängder i Azure DNS med Hjälp av Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [Powershell](dns-operations-recordsets.md)

Den här artikeln visar hur du hanterar DNS-poster för din DNS-zon med hjälp av plattformsoberoende Azure CLI, som är tillgängligt för Windows, Mac och Linux. Du kan också hantera dina DNS-poster med [Azure PowerShell](dns-operations-recordsets.md) eller [Azure-portalen](dns-operations-recordsets-portal.md).

Exemplen i den här artikeln förutsätter att du redan har [installerat Azure CLI, loggat in och skapat en DNS-zon](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Introduktion

Innan du skapar DNS-poster i Azure DNS, måste du först förstå hur Azure DNS organiserar DNS-poster i DNS-postuppsättningar.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Mer information om DNS-poster i Azure DNS finns i [DNS-zoner och poster](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Skapa en DNS-post

Om du vill skapa `az network dns record-set <record-type> add-record` en DNS-post använder du kommandot (var `<record-type>` är typen av post, d.v.s. a, srv, txt, etc.) Mer hjälp `az network dns record-set --help`finns i .

När du skapar en post måste du ange resursgruppsnamn, zonnamn, postuppsättningsnamn, posttyp och information om posten som skapas. Det angivna postnamnet måste vara ett *relativt* namn, vilket innebär att zonnamnet måste uteslutas.

Om postuppsättningen inte redan finns, skapar det här kommandot den åt dig. Om postuppsättningen redan finns, lägger det här kommandot till posten som du anger till den befintliga postuppsättningen.

Om en ny postuppsättning skapas, används ett standard TTL-värde (Time to Live) på 3600. Instruktioner om hur du använder olika TTLs finns i [Skapa en DNS-postuppsättning](#create-a-dns-record-set).

Följande exempel skapar en A-post som heter *www* i zonen *contoso.com* i resursgruppen *MyResourceGroup*. IP-adressen för A-posten är *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Om du vill skapa en post som anges i zonens spets (i det\@här fallet "contoso.com" använder du postnamnet " ", inklusive citattecken:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Skapa en DNS-postuppsättning

I ovanstående exempel lades DNS-posten antingen till i en befintlig postuppsättning eller så skapades postuppsättningen *implicit*. Du kan också skapa postuppsättningen *explicit* innan du lägger till poster i den. Azure DNS stöder "tomma" postuppsättningar, som kan fungera som platshållare för att reservera ett DNS-namn innan DNS-poster skapas. Tomma postuppsättningar visas i Azure DNS-kontrollplanet, men visas inte på Azure DNS-namnservrarna.

Postuppsättningar skapas `az network dns record-set <record-type> create` med kommandot. Om du vill ha hjälp, så gå till `az network dns record-set <record-type> create --help`.

Genom att skapa postuppsättningen kan du uttryckligen ange postuppsättningsegenskaper som [TTL (Time-To-Live)](dns-zones-records.md#time-to-live) och metadata. [Datauppsättningsmetadata](dns-zones-records.md#tags-and-metadata) kan användas för att associera programspecifika data med varje postuppsättning, som nyckelvärdespar.

I följande exempel skapas en tom postuppsättning av typen "A" med en `--ttl` 60-sekunders TTL, med hjälp av parametern (kort form): `-l`

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

I följande exempel skapas en postuppsättning med två metadataposter, "dept=finance" och `--metadata` "environment=production", med hjälp av parametern :

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Efter att ha skapat en tom `azure network dns record-set <record-type> add-record` postuppsättning kan poster läggas till med beskrivningen i [Skapa en DNS-post](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Skapa poster av andra typer

Efter att ha sett i detalj hur du skapar A-poster visar följande exempel hur du skapar post av andra posttyper som stöds av Azure DNS.

Parametrarna som används för att ange postdata varierar beroende på posttypen. För en post av typen "A", anger du exempelvis IPv4-adressen med parametern `--ipv4-address <IPv4 address>`. Parametrarna för varje posttyp kan `az network dns record-set <record-type> add-record --help`visas med .

I varje enskilt fall visar vi hur du skapar en enda post. Posten läggs till i den befintliga postuppsättningen eller en postuppsättning som skapats implicit. Mer information om hur du skapar postuppsättningar och definierar parametern för postuppsättning uttryckligen finns i [Skapa en DNS-postuppsättning](#create-a-dns-record-set).

Vi ger inte ett exempel för att skapa en SOA-postuppsättning, eftersom SOA skapas och tas bort med varje DNS-zon och inte kan skapas eller tas bort separat. SOA kan dock [ändras, vilket visas i ett senare exempel](#to-modify-an-soa-record).

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
> DNS-standarderna tillåter inte CNAME-poster i toppen`--Name "@"`av en zon ( ), inte heller tillåter de postuppsättningar som innehåller mer än en post.
> 
> Mer information finns i [CNAME-poster](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Skapa en MX-post

I det här exemplet använder vi\@postuppsättningsnamnet " " för att skapa MX-posten i zonapexen (i det här fallet "contoso.com").

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Skapa en NS-post

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Skapa en PTR-post

I det här fallet representerar "my-arpa-zone.com" ARPA-zonen som representerar ditt IP-intervall. Varje PTR-post som har angetts i den här zonen motsvarar en IP-adress i IP-intervallet.  Postnamnet '10' är den sista oktetten av IP-adressen inom det här IP-intervallet som representeras av den här posten.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Skapa en SRV-post

När du skapar en [SRV-postuppsättning](dns-zones-records.md#srv-records)anger du * \_tjänsten* och * \_protokollet* i postuppsättningens namn. Det finns ingen anledning\@att inkludera " " i postuppsättningsnamnet när du skapar en SRV-post som anges vid zonsponsen.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Skapa en TXT-post

I följande exempel visas hur du skapar en TXT-post. Mer information om den maximala stränglängden som stöds i TXT-poster finns i [TXT-poster](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Hämta en postuppsättning

Om du vill hämta `az network dns record-set <record-type> show`en befintlig postuppsättning använder du . Om du vill ha hjälp, så gå till `az network dns record-set <record-type> show --help`.

Som när du skapar en post eller postuppsättning måste det angivna postnamnet vara ett *relativt* namn, vilket innebär att det måste utesluta zonnamnet. Du måste också ange posttypen, zonen som innehåller postuppsättningen och resursgruppen som innehåller zonen.

I följande exempel hämtas posten *www* av typen A från zonen *contoso.com* i resursgruppen *MyResourceGroup:*

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Listpostuppsättningar

Du kan visa alla poster i `az network dns record-set list` en DNS-zon med kommandot. Om du vill ha hjälp, så gå till `az network dns record-set list --help`.

I det här exemplet returneras alla postuppsättningar i zonen *contoso.com*i resursgruppen *MyResourceGroup*, oavsett namn eller posttyp:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

I det här exemplet returneras alla postuppsättningar som matchar den angivna posttypen (i det här fallet A-poster):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Lägga till en post i en befintlig postuppsättning

Du kan `az network dns record-set <record-type> add-record` använda båda för att skapa en post i en ny postuppsättning eller för att lägga till en post i en befintlig postuppsättning.

Mer information finns i [Skapa en DNS-post](#create-a-dns-record) och [Skapa poster av andra typer](#create-records-of-other-types) ovan.

## <a name="remove-a-record-from-an-existing-record-set"></a>Ta bort en post från en befintlig postuppsättning.

Om du vill ta bort en `az network dns record-set <record-type> remove-record`DNS-post från en befintlig postuppsättning använder du . Om du vill ha hjälp, så gå till `az network dns record-set <record-type> remove-record -h`.

Det här kommandot tar bort en DNS-post från en postuppsättning. Om den sista posten i en postuppsättning tas bort tas även själva postuppsättningen bort. Om du vill behålla den `--keep-empty-record-set` tomma posten i stället använder du alternativet.

Du måste ange den post som ska tas bort och den zon som den `az network dns record-set <record-type> add-record`ska tas bort från, med samma parametrar som när du skapar en post med . Dessa parametrar beskrivs i [Skapa en DNS-post](#create-a-dns-record) och [Skapa poster av andra typer](#create-records-of-other-types) ovan.

I följande exempel tas A-posten bort med värdet '1.2.3.4' från postuppsättningen *www* i zonen *contoso.com*i resursgruppen *MyResourceGroup*.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Ändra en befintlig postuppsättning

Varje postuppsättning innehåller en [time-to-live (TTL),](dns-zones-records.md#time-to-live) [metadata](dns-zones-records.md#tags-and-metadata)och DNS-poster. I följande avsnitt beskrivs hur du ändrar var och en av dessa egenskaper.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Så här ändrar du en A-, AAAA-, CAA-, MX-, NS-, PTR-, SRV- eller TXT-post

Om du vill ändra en befintlig post av typen A, AAAA, CAA, MX, NS, PTR, SRV eller TXT bör du först lägga till en ny post och sedan ta bort den befintliga posten. Detaljerade instruktioner om hur du tar bort och lägger till poster finns i de tidigare avsnitten i den här artikeln.

I följande exempel visas hur du ändrar en A-post, från IP-adress 1.2.3.4 till IP-adress 5.6.7.8:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Du kan inte lägga till, ta bort eller ändra posterna i den`--Name "@"`automatiskt skapade NS-postuppsättningen vid zonapexen ( , inklusive citattecken). För den här postuppsättningen är de enda ändringar som tillåts att ändra postuppsättningen TTL och metadata.

### <a name="to-modify-a-cname-record"></a>Så här ändrar du en CNAME-post

Till skillnad från de flesta andra posttyper kan en CNAME-postuppsättning bara innehålla en enda post.  Därför kan du inte ersätta det aktuella värdet genom att lägga till en ny post och ta bort den befintliga posten, som för andra posttyper.

Om du vill ändra en `az network dns record-set cname set-record`CNAME-post använder du i stället . Mer information finns i`az network dns record-set cname set-record --help`

Exemplet ändrar CNAME-posten som *anges www* i zonen *contoso.com*i resursgruppen *MyResourceGroup*så att den pekar på "www.fabrikam.net" i stället för dess befintliga värde:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Så här ändrar du en SOA-post

Till skillnad från de flesta andra posttyper kan en CNAME-postuppsättning bara innehålla en enda post.  Därför kan du inte ersätta det aktuella värdet genom att lägga till en ny post och ta bort den befintliga posten, som för andra posttyper.

Om du vill ändra SOA-posten använder du `az network dns record-set soa update`i stället . Om du vill ha hjälp, så gå till `az network dns record-set soa update --help`.

I följande exempel visas hur du ställer in egenskapen "e-post" för SOA-posten för zonen *contoso.com* i resursgruppen *MyResourceGroup:*

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Så här ändrar du NS-poster i zonsponsen

NS-posten som anges vid zonapexen skapas automatiskt med varje DNS-zon. Den innehåller namnen på Azure DNS-namnservrar som tilldelats zonen.

Du kan lägga till ytterligare namnservrar i den här NS-postuppsättningen för att stödja samhostingdomäner med mer än en DNS-provider. Du kan också ändra TTL och metadata för den här postuppsättningen. Du kan dock inte ta bort eller ändra de förifyllda Azure DNS-namnservrarna.

Observera att detta endast gäller för NS-posten som anges vid zonsponsan. Andra NS-postuppsättningar i zonen (som används för att delegera underordnade zoner) kan ändras utan begränsning.

I följande exempel visas hur du lägger till ytterligare en namnserver i NS-posten som angetts vid zonsponsen:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Så här ändrar du TTL för en befintlig postuppsättning

Om du vill ändra TTL för `azure network dns record-set <record-type> update`en befintlig postuppsättning använder du . Om du vill ha hjälp, så gå till `azure network dns record-set <record-type> update --help`.

I följande exempel visas hur du ändrar en postuppsättning TTL, i det här fallet till 60 sekunder:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Så här ändrar du metadata för en befintlig postuppsättning

[Datauppsättningsmetadata](dns-zones-records.md#tags-and-metadata) kan användas för att associera programspecifika data med varje postuppsättning, som nyckelvärdespar. Om du vill ändra metadata för `az network dns record-set <record-type> update`en befintlig postuppsättning använder du . Om du vill ha hjälp, så gå till `az network dns record-set <record-type> update --help`.

I följande exempel visas hur du ändrar en postuppsättning med två metadataposter, "dept=finance" och "environment=production". Observera att alla befintliga metadata *ersätts av* de angivna värdena.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Ta bort en postuppsättning

Postuppsättningar kan tas `az network dns record-set <record-type> delete` bort med kommandot. Om du vill ha hjälp, så gå till `azure network dns record-set <record-type> delete --help`. Om du tar bort en postuppsättning tas även alla poster i postuppsättningen bort.

> [!NOTE]
> Du kan inte ta bort SOA- och NS-postuppsättningarna vid zonapexen (`--name "@"`).  Dessa skapas automatiskt när zonen skapades och tas bort automatiskt när zonen tas bort.

I följande exempel tas postuppsättningen med namnet *www* av typen A bort från zonen *contoso.com* i resursgruppen *MyResourceGroup:*

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

Du uppmanas att bekräfta borttagningsåtgärden. Om du vill ignorera `--yes` den här prompten använder du växeln.

## <a name="next-steps"></a>Nästa steg

Läs mer om [zoner och poster i Azure DNS](dns-zones-records.md).
<br>
Lär dig hur du [skyddar dina zoner och poster](dns-protect-zones-recordsets.md) när du använder Azure DNS.
