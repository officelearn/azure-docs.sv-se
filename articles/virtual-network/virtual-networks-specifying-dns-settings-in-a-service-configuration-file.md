---
title: "Ange DNS-inställningar i en konfigurationsfil för tjänsten | Microsoft Docs"
description: "Ange anpassade DNS-inställningar med hjälp av tjänstkonfigurationsfilen för det virtuella nätverket"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: jdial
ms.openlocfilehash: 0fba2ea06827aff29a7a092933edb8120d668b29
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Ange DNS-inställningar i en konfigurationsfil för tjänsten
## <a name="dns-elements"></a>DNS-element
En konfigurationsfil för tjänsten kan innehålla ett DnsServers-element med en lista över IPv4-adresser för Domain Name System (DNS)-servrar som ska användas av tjänsten. Inställningarna i tjänstkonfigurationsfilen företräde framför inställningar i konfigurationsfilen på nätverket. Mer information finns i [Konfigurationsschemat för Azure-tjänsten (.cscfg-filen)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**NetworkConfiguration-elementet**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> Den **namn** attribut i den **DnsServer** elementet används endast som en referensnamn. Det är inte ett värdnamn för DNS-servern. Varje **DnsServer** attributvärdet måste vara unikt i hela Microsoft Azure-prenumerationen.
> 
> 

## <a name="see-also"></a>Se även
[Konfigurationsschemat för Azure-tjänsten (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Virtuella Azure-nätverket Konfigurationsschemat](http://go.microsoft.com/fwlink/?LinkId=248093)

[Konfigurera ett virtuellt nätverk med Network Configuration-filer](http://go.microsoft.com/fwlink/?LinkId=248094)

[Om inställningarna för virtuella nätverk i hanteringsportalen](http://go.microsoft.com/fwlink/?LinkId=248092)

