---
title: Ange DNS-inställningar i en konfigurationsfil för virtuellt nätverk | Microsoft Docs
description: Så här ändrar du DNS-serverinställningarna i ett virtuellt nätverk med en konfigurationsfil för virtuellt nätverk i den klassiska distributionsmodellen
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: a8905927-92ac-42b5-8c33-8e42c000692c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: genli
ms.openlocfilehash: ed7f02d3e389db3bc772c4fcb00a7b3877d60173
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31794532"
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Ange DNS-inställningar i en konfigurationsfil för virtuellt nätverk
En konfigurationsfil för nätverk innehåller två element som du kan använda för att ange inställningar för Domain Name System (DNS): **DnsServers** och **DnsServerRef**. Du kan lägga till en lista över DNS-servrar genom att ange sina IP-adresser och namn för att referera den **DnsServers** element. Du kan sedan använda en **DnsServerRef** element för att ange vilka DNS-server transaktioner från DnsServers-element som används för olika nätverksplatserna i det virtuella nätverket.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen.

Nätverket konfigurationsfilen kan innehålla följande element. Rubrik på varje element är kopplad till en sida som innehåller ytterligare information om inställningar för elementet värde.

> [!IMPORTANT]
> Information om hur du konfigurerar nätverket konfigurationsfilen finns [konfigurera ett virtuellt nätverk med en konfigurationsfil för nätverket](virtual-networks-using-network-configuration-file.md). Information om varje element som finns i konfigurationsfilen nätverket finns [Azure Virtual Network Konfigurationsschemat](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[DNS-Element](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> Den **namn** attribut i den **DnsServer** elementet används endast som en referens för den **DnsServerRef** element. Det är inte ett värdnamn för DNS-servern. Varje **DnsServer** attributvärdet måste vara unikt i hela Microsoft Azure-prenumerationen
> 
> 

[Virtuellt nätverk platser Element](http://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> För att ange den här inställningen för elementet virtuella nätverksplatser, måste den tidigare definieras i DNS-elementet. DnsServerRef *namn* i virtuella nätverksplatser-elementet måste referera till en namn-värde som angetts i DNS-elementet för DnsServer *namn*.
> 
> 

## <a name="next-steps"></a>Nästa steg
* Förstå de [virtuella Azure-nätverket Konfigurationsschemat](http://go.microsoft.com/fwlink/?LinkId=248093).
* Förstå de [Azure-tjänsten Konfigurationsschemat](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Konfigurera ett virtuellt nätverk som använder nätverket konfigurationsfiler](virtual-networks-using-network-configuration-file.md).

