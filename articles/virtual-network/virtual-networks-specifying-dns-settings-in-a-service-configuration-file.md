---
title: Ange DNS-inställningar i en tjänst konfigurations fil | Microsoft Docs
description: Ange anpassade DNS-inställningar med tjänst konfigurations fil för virtuellt nätverk
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: genli
ms.openlocfilehash: f27802d76a8b94a0d5f1eb0c35fd55c93712e557
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059068"
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Ange DNS-inställningar i en tjänst konfigurations fil
## <a name="dns-elements"></a>DNS-element
En tjänst konfigurations fil kan innehålla ett DnsServers-element med en lista med IPv4-adresser för de Domain Name System-servrar (DNS) som tjänsten ska använda. Inställningarna i tjänst konfigurations filen prioriteras framför inställningarna i nätverks konfigurations filen. Mer information finns i [konfigurations schema för Azure-tjänsten (. cscfg-fil)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**NetworkConfiguration-element**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> Namnattributet **i** **DNS Server** -elementet används endast som referens namn. Den representerar inte värd namnet för DNS-servern. Varje **DNS Server** -attributvärde måste vara unikt för hela Microsoft Azure prenumerationen.
> 
> 

## <a name="see-also"></a>Se även
[Konfigurations schema för Azure-tjänsten (. cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Konfigurations schema för Azure Virtual Network](https://go.microsoft.com/fwlink/?LinkId=248093)

[Konfigurera en Virtual Network med hjälp av konfigurationsfiler för nätverk](https://go.microsoft.com/fwlink/?LinkId=248094)

[Om Virtual Network inställningar i Hanteringsportal](https://go.microsoft.com/fwlink/?LinkId=248092)

