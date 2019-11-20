---
title: Ange DNS-inställningar i konfigurations filen för Azure VNet
description: Ändra DNS-serverinställningar i ett virtuellt nätverk med hjälp av en konfigurations fil för virtuellt nätverk i den klassiska distributions modellen
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: db8b1817bb14d293632d16fe02792dbb85766559
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196643"
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Ange DNS-inställningar i en virtuell nätverks konfigurations fil
En nätverks konfigurations fil har två element som du kan använda för att ange inställningar för Domain Name System (DNS): **DnsServers** och **DnsServerRef**. Du kan lägga till en lista över DNS-servrar genom att ange deras IP-adresser och referens namn i **DnsServers** -elementet. Du kan sedan använda ett **DnsServerRef** -element för att ange vilka DNS-Server poster från DnsServers-elementet som används för olika nätverks platser i det virtuella nätverket.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen.

Nätverks konfigurations filen kan innehålla följande element. Rubriken för varje element är länkad till en sida som innehåller ytterligare information om värde inställningarna för elementet.

> [!IMPORTANT]
> Information om hur du konfigurerar nätverks konfigurations filen finns i [Konfigurera en Virtual Network med hjälp av en nätverks konfigurations fil](virtual-networks-using-network-configuration-file.md). Information om varje element som finns i nätverks konfigurations filen finns i [konfigurations schema för Azure Virtual Network](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[DNS-element](https://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> Namnattributet **i** **DNS Server** -elementet används endast som en referens för elementet **DnsServerRef** . Den representerar inte värd namnet för DNS-servern. Varje **DNS Server** -attributvärde måste vara unikt för hela Microsoft Azure prenumerationen
> 
> 

[Element för Virtual Network-platser](https://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> För att kunna ange den här inställningen för elementet Virtual Network Sites måste det tidigare definierats i DNS-elementet. DnsServerRef *namn* i Virtual Network Sites-elementet måste referera till ett namn värde som anges i DNS-elementet för DNS Server- *namn*.
> 
> 

## <a name="next-steps"></a>Nästa steg
* Förstå [konfigurations schemat för Azure Virtual Network](https://go.microsoft.com/fwlink/?LinkId=248093).
* Förstå [konfigurations schemat för Azure-tjänsten](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Konfigurera ett virtuellt nätverk med hjälp av konfigurationsfiler för nätverk](virtual-networks-using-network-configuration-file.md).

