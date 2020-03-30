---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361549"
---
### <a name="bastion-tier"></a>Bastion nivå

Skyddsvärden är en valfri komponent som du kan använda som hoppserver för att komma åt program- och databasinstanserna. Den virtuella skyddsvärd datorn kan ha en offentlig IP-adress tilldelad, även om rekommendationen är att konfigurera en ExpressRoute-anslutning eller plats-till-plats-VPN med ditt lokala nätverk för säker åtkomst. Dessutom bör endast SSH (port 22, Linux) eller RDP (port 3389, Windows Server) öppnas för inkommande trafik. För hög tillgänglighet distribuerar du en skyddsvärd i två tillgänglighetszoner eller i en enda tillgänglighetsuppsättning.

Du kan också aktivera SSH-agent vidarebefordran på dina virtuella datorer, vilket gör att du kan komma åt andra virtuella datorer i det virtuella nätverket genom att vidarebefordra autentiseringsuppgifterna från din bastionvärd. Du kan också använda SSH-tunnlar för att komma åt andra instanser.

Här är ett exempel på agent vidarebefordran:

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

Det här kommandot ansluter till bastionen och körs `ssh` sedan omedelbart igen, så att du får en terminal på målinstansen. Du kan behöva ange en annan användare än roten på målinstansen om klustret är konfigurerat på ett annat sätt. Argumentet `-A` vidarebefordrar agentanslutningen så att din privata nyckel på din lokala dator används automatiskt. Observera att agent vidarebefordran är en `ssh` kedja, `-A` så det andra kommandot innehåller också så att alla efterföljande SSH-anslutningar som initierats från målinstansen också använder din lokala privata nyckel.