---
title: "Skapa DNS-poster med hjälp av Azure CLI 1.0 | Microsoft Docs"
description: "Så här skapar du värdposter för Azure DNS. Konfigurera postuppsättningar och poster med Azure CLI 1.0"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 45599ea87b536b74cc652ef28f91a6058c7c8e4a
ms.lasthandoff: 02/27/2017

---

# <a name="create-dns-records-using-the-azure-cli-10"></a>Skapa DNS-poster med hjälp av Azure CLI 1.0

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md)

Den här artikeln beskriver steg för steg hur du skapar poster och postuppsättningar med hjälp av Azure CLI 1.0.

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften

Du kan slutföra uppgiften med någon av följande CLI-versioner:

* [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md) – vår CLI för distributionsmodellerna klassisk och resurshantering.
* [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md) – vår nästa generations CLI för distributionsmodellen resurshantering.

## <a name="introduction"></a>Introduktion

Innan du skapar DNS-poster i Azure DNS, måste du först förstå hur Azure DNS organiserar DNS-poster i DNS-postuppsättningar.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Mer information om DNS-poster i Azure DNS finns i [DNS-zoner och poster](dns-zones-records.md).

## <a name="create-a-record-set-and-record"></a>Skapa en postuppsättning och poster

I det här avsnittet beskrivs hur du kan skapa DNS-poster i Azure DNS. I exemplen antar vi att du redan har [installerat Azure CLI 1.0, loggat in och skapat en DNS-zon](dns-getstarted-create-dnszone-cli-nodejs.md).

Alla exempel på den här sidan använder DNS-posttypen A. När det gäller övriga posttyper och ytterligare information om hur man hanterar DNS-poster och registrerar postuppsättningar finns i [Hantera DNS-poster och registrera postuppsättningar med hjälp av Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md).

## <a name="create-a-dns-record"></a>Skapa en DNS-post

Skapa en DNS-post genom att använda kommandot `azure network dns record-set add-record`. Om du vill ha hjälp, så gå till `azure network dns record-set add-record -h`.

När du skapar en post måste du ange resursgruppsnamn, zonnamn, postuppsättningsnamn, posttyp och information om posten som skapas.

Om postuppsättningen inte redan finns, skapar det här kommandot den åt dig. Om postuppsättningen redan finns, lägger det här kommandot till posten som du anger till den befintliga postuppsättningen. 

Om en ny postuppsättning skapas, används ett standard TTL-värde (Time to Live) på 3600. Anvisningar för hur du använder olika TTL:er, finns i [Hantera DNS-poster i Azure DNS med Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md).

Följande exempel skapar en A-post som heter *www* i zonen *contoso.com* i resursgruppen *MyResourceGroup*. IP-adressen för A-posten är *1.2.3.4*.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

Skapa en post överst i zonen (i det här fallet ”contoso.com”) genom att använda postnamnet "@", inklusive citattecknen.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

Parametrarna som används för att ange postdata varierar beroende på posttypen. För en post av typen "A", anger du exempelvis IPv4-adressen med parametern `-a <IPv4 address>`. Läs i `azure network dns record-set add-record -h` om hur du listar parametrar för andra posttyper. För exempel för varje posttyp, kan du se [Hantera DNS-poster och postuppsättningar med Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md).


## <a name="verify-name-resolution"></a>Verifiera namnmatchning

Du kan testa om din DNS-zon är närvarande på Azure DNS-namnservrarna genom att använda DNS-verktyg som nslookup, dig eller cmdleten [Resolve-DnsName PowerShell](https://technet.microsoft.com/library/jj590781.aspx).

Om du inte har delegerat domänen att använda den nya zonen i Azure DNS än, så måste du [dirigera DNS-frågan direkt till någon av zonens namnservrar](dns-getstarted-create-dnszone.md#test-name-servers). Var noga med att ange de korrekta värdena för din postzon i kommandot nedan.

```
nslookup
> set type=A
> server ns1-01.azure-dns.com
> www.contoso.com

Server:  ns1-01.azure-dns.com
Address:  40.90.4.1

Name:    www.contoso.com
Address:  1.2.3.4
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [delegerar ditt domännamn till Azure DNS-namnservrarna](dns-domain-delegation.md)

Läs mer om hur du kan [Hantera DNS-zoner med Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md).

Läs mer om hur du kan [Hantera DNS-poster och postuppsättningar med Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md).


