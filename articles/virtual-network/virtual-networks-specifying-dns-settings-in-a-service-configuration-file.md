---
title: Ange DNS-inställningar i en tjänstkonfigurationsfil | Microsoft Docs
description: Ange anpassade DNS-inställningar med hjälp av tjänstkonfigurationsfilen för virtuellt nätverk
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: genli
ms.openlocfilehash: 0ac488a67d8b9debf6539d199395997cf44cf1e4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247185"
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Ange DNS-inställningar i en Tjänstkonfigurationsfil
## <a name="dns-elements"></a>DNS-element
En tjänstkonfigurationsfil kan innehålla ett DnsServers-element med en lista över IPv4-adresser för de System DNS (Domain Name)-servrar som ska användas av tjänsten. Inställningarna i tjänstekonfigurationsfilen företräde framför inställningar i nätverkskonfigurationsfilen. Mer information finns i [Azure-Tjänstkonfigurationens Schema (.cscfg-filen)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**NetworkConfiguration-elementet**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> Den **namn** attribut i den **DnsServer** elementet används endast som referensnamn. Det motsvarar inte värdnamn för DNS-servern. Varje **DnsServer** attributvärdet måste vara unikt inom hela Microsoft Azure-prenumeration.
> 
> 

## <a name="see-also"></a>Se även
[Azure-Tjänstkonfigurationens Schema (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Konfigurationsschema för Azure-nätverk](https://go.microsoft.com/fwlink/?LinkId=248093)

[Konfigurera ett virtuellt nätverk med Network Configuration-filer](https://go.microsoft.com/fwlink/?LinkId=248094)

[Om virtuella nätverksinställningar i hanteringsportalen](https://go.microsoft.com/fwlink/?LinkId=248092)

