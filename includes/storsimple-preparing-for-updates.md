---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0c89ed34b21ca0c41d4f7765d99d8fe8bf7c647d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889164"
---
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

