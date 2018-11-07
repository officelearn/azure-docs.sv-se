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
ms.openlocfilehash: 36f7ed9b02b66718327c1a05a6cf29eedf39e7a5
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248393"
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Ange DNS-inställningar i en virtuell nätverkskonfigurationsfil
En nätverkskonfigurationsfil innehåller två element som du kan använda för att ange inställningar för System DNS (Domain Name): **DnsServers** och **DnsServerRef**. Du kan lägga till en lista över DNS-servrar genom att ange sina IP-adresser och referera till som ska den **DnsServers** element. Du kan sedan använda en **DnsServerRef** element att ange vilka DNS-serverposter från DnsServers-elementet används för olika nätverksplatser i det virtuella nätverket.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen.

Nätverkskonfigurationsfilen kan innehålla följande element. Rubriken på varje element är länkad till en sida som innehåller ytterligare information om inställningar för elementet värde.

> [!IMPORTANT]
> Information om hur du konfigurerar nätverkskonfigurationsfilen finns i [konfigurera ett virtuellt nätverk med en Nätverkskonfigurationsfil](virtual-networks-using-network-configuration-file.md). Läs om hur varje element i nätverkskonfigurationsfilen [konfigurationsschema för Azure virtuellt nätverk](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[DNS-Element](https://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> Den **namn** attribut i den **DnsServer** elementet används bara som referens för den **DnsServerRef** element. Det motsvarar inte värdnamn för DNS-servern. Varje **DnsServer** attributvärdet måste vara unikt inom hela Microsoft Azure-prenumeration
> 
> 

[Webbplatselement för virtuellt nätverk](https://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> För att ange den här inställningen för elementet virtuella nätverksplatser, måste den ha tidigare definierats i DNS-elementet. DnsServerRef *namn* på de virtuella nätverksplatserna elementet måste referera till ett namnvärde som anges i DNS-element för DnsServer *namn*.
> 
> 

## <a name="next-steps"></a>Nästa steg
* Förstå den [konfigurationsschema för Azure-nätverk](https://go.microsoft.com/fwlink/?LinkId=248093).
* Förstå den [Azure-Tjänstkonfigurationens Schema](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Konfigurera ett virtuellt nätverk med nätverkskonfigurationen](virtual-networks-using-network-configuration-file.md).

