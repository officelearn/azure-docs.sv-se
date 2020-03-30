---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4e262c9e5bb88e77bc9c09853c06f4cdb41eedaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187536"
---
## <a name="preparing-for-updates"></a>Förbereda för uppdateringar
Du måste utföra följande steg innan du skannar och installerar uppdateringen:

1. Ta en ögonblicksbild av enhetsdata i molnet.
2. Se till att den fasta IP-enheten är dirigerbar och kan ansluta till Internet. Dessa fasta IPs kommer att användas för att betjäna uppdateringar av din enhet. Du kan testa detta genom att köra följande cmdlet på varje styrenhet från Enhetens Windows PowerShell-gränssnitt:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **Exempel på utdata för testanslutning när fasta IPs kan ansluta till Internet**

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

När du har slutfört dessa manuella förkontroller kan du fortsätta att skanna och installera uppdateringarna.

