---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: e743128932f6a136820819c21e0386eb678c4203
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165082"
---
<!--author=jgerend last changed: 03/16/16-->

## <a name="preparing-for-updates"></a>Förbereda för uppdateringar
Behöver du utföra följande steg innan du skanna och tillämpa uppdateringen:

1. Ta en molnögonblicksbild av enhetsdata.
2. Kontrollera att din kontrollenheternas fasta IP-adresser är dirigerbara och kan ansluta till Internet. Dessa fasta IP-adresser används för att hantera uppdateringar till din enhet. Du kan testa detta genom att köra följande cmdlet på varje domänkontrollant från Windows PowerShell-gränssnittet på enheten:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
   
    **Exempel på utdata för Test-Connection när fasta IP-adresser kan ansluta till Internet**

        Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

        Source      Destination     IPV4Address      IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200

        Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

        Source      Destination       IPV4Address    IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200

När du har slutfört de här manuell förhandskontroller, kan du fortsätta att söka igenom och installera uppdateringarna.

