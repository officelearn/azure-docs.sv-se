---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 880b630ae48eda086f6454f0d7108d27d3403b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67188011"
---
Om du startar upp i en icke-DHCP-miljö följer du dessa steg för att distribuera den virtuella datorn för databoxgatewayen.

1. [Anslut till enhetens Windows PowerShell-gränssnitt](#connect-to-the-powershell-interface).
2. Använd `Get-HcsIpAddress` cmdleten för att lista de nätverksgränssnitt som är aktiverade på den virtuella enheten. Om enheten har ett enda nätverksgränssnitt aktiverad är det tilldelade standardnamnet för gränssnittet `Ethernet`.

    I följande exempel visas användningen av den här cmdleten:

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

