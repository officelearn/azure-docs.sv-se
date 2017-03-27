---
title: "Komma igång med Azure DNS med hjälp av Azure CLI 1.0 | Microsoft Docs"
description: "Läs om hur du skapar en DNS-zon och en DNS-post i Azure DNS. Detta är en steg-för-steg-guide om hur du skapar och hanterar din första DNS-zon och DNS-post med Azure CLI 1.0."
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: f84ac7f1fa20af2e495df89609c810107c65583a
ms.lasthandoff: 03/21/2017

---

# <a name="get-started-with-azure-dns-using-azure-cli-10"></a>Komma igång med Azure DNS med hjälp av Azure CLI 1.0

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

Den här artikeln visar hur du skapar din första DNS-zon och DNS-post med hjälp av plattformsoberoende Azure CLI 1.0, som är tillgängligt för Windows, Mac och Linux. Du kan också utföra de här stegen med Azure Portal eller Azure PowerShell.

En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera din DNS-zon på Internet måste du konfigurera namnservrarna för domänen. Dessa steg beskrivs nedan.

Anvisningarna förutsätter att du redan har installerat och loggat in på Azure CLI 1.0. Mer information finns i [Hantera DNS-zoner med Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md).


## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

En DNS-zon skapas med hjälp av kommandot `azure network dns zone create`. Om du vill se hjälpen för det här kommandot skriver du `azure network dns zone create -h`.

Exemplet nedan skapar en DNS-zon som heter *contoso.com* i resursgruppen med namnet *MyResourceGroup*. Använd exemplet när du vill skapa en DNS-zon, och ersätt värdena med dina egna.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```


## <a name="create-a-dns-record"></a>Skapa en DNS-post

Skapa en DNS-post genom att använda kommandot `azure network dns record-set add-record`. Om du vill ha hjälp, så gå till `azure network dns record-set add-record -h`.

I följande exempel skapas en post med det relativa namnet "www" i resursgruppen "MyResourceGroup" i DNS-zonen "contoso.com". Postuppsättningens fullständigt kvalificerade namn är ”www.contoso.com”. Postens typ är "A", IP-adressen är "1.2.3.4" och en standard-TTL på 3 600 sekunder (1 timme) används.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

Information om andra posttyper, postuppsättningar med fler än en post, alternativa TTL-värden och ändring av befintliga poster finns i [Hantera DNS-poster och postuppsättningar med Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md).


## <a name="view-records"></a>Visa poster

Om du vill visa en lista med DNS-poster i din zon använder du:

```azurecli
azure network dns record-set list MyResourceGroup contoso.com
```


## <a name="update-name-servers"></a>Uppdatera namnservrar

När du är nöjd med konfigurationen av DNS-zonen och DNS-posterna måste du konfigurera ditt domännamn för användning med Azure DNS-namnservrarna. Det gör att andra användare på Internet kan hitta dina DNS-poster.

Namnservrarna för din zon anges av `azure network dns zone show`-kommandot:

```azurecli
azure network dns zone show MyResourceGroup contoso.com

info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 3
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            :
info:    network dns zone show command OK
```

Dessa namnservrar ska konfigureras med domännamnsregistratorn (där du köpte domännamnet). Registratorn erbjuder möjligheten att konfigurera namnservrar för domänen. Mer information finns i [Delegera en domän till Azure DNS](dns-domain-delegation.md).


## <a name="next-steps"></a>Nästa steg

Läs mer om Azure DNS i [Översikt över Azure DNS](dns-overview.md).

Mer information om hur du hanterar DNS-zoner i Azure DNS finns i [Hantera DNS-zoner i Azure DNS med Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md).

Mer information om hur du hanterar DNS-poster i Azure DNS finns i [Hantera DNS-poster och postuppsättningar i Azure DNS med Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md).


