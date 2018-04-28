---
title: Ange DNS-inställningar i en konfigurationsfil för tjänsten | Microsoft Docs
description: Ange anpassade DNS-inställningar med hjälp av tjänstkonfigurationsfilen för det virtuella nätverket
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
ms.openlocfilehash: 009206f1e0ba848538ed2c666032a63051d062e4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
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

