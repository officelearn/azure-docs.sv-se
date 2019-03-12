---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 880b630ae48eda086f6454f0d7108d27d3403b77
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555241"
---
Om du startar upp i en icke-DHCP-miljö, följer du stegen nedan för att distribuera den virtuella datorn för din Data Box-Gateway.

1. [Ansluta till Windows PowerShell-gränssnittet på enheten](#connect-to-the-powershell-interface).
2. Använd den `Get-HcsIpAddress` cmdlet för att lista de nätverksgränssnitt som är aktiverad på den virtuella enheten. Om enheten har ett enda nätverksgränssnitt aktiverad är det tilldelade standardnamnet för gränssnittet `Ethernet`.

    I följande exempel visas användningen av denna cmdlet:

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. Använd cmdleten `Set-HcsIpAddress` för att konfigurera nätverket. Se följande exempel:

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

