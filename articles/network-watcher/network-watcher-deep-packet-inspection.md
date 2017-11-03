---
title: "Paketinspektion med Azure Nätverksbevakaren | Microsoft Docs"
description: "Den här artikeln beskriver hur du använder Nätverksbevakaren för att utföra djup paketinspektion samlas in från en virtuell dator"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 1ad6ca4abe73336ce9ce3539fdaf2a9d7dd23fa6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Paketinspektion med Azure Nätverksbevakaren

Funktionen paket avbildning av Nätverksbevakaren kan du starta och hantera insamlingar sessionerna på din virtuella Azure-datorer från portalen, PowerShell, CLI och programmässigt via SDK och REST-API. Du kan adressen scenarier som kräver nivån paketdata genom att tillhandahålla information i ett format som är användbara i paketinsamling. Utnyttja gratisverktyg för att granska data du undersöka kommunikation till och från dina virtuella datorer och få insyn i nätverkstrafiken. Vissa exempel användningsområden för avbildning paketdata är: undersöker problem med nätverket eller ett program, identifiera nätverket missbruk och intrångsidentifiering försök eller upprätthålla regelefterlevnad. I den här artikeln visar vi hur du öppnar en fil för avbildning av paket som tillhandahålls av Nätverksbevakaren verktyget populära öppen källkod. Vi ger även exempel som visar hur du beräkna en fördröjning för anslutning, identifiera onormalt trafik och undersöka nätverk statistik.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln går igenom några förkonfigurerade scenarier på paketinsamling som kördes tidigare. Dessa scenarier visar funktioner som kan nås genom att granska en paketinsamling. Det här scenariot använder [WireShark](https://www.wireshark.org/) att inspektera paketinsamling.

Det här scenariot förutsätter att du körde en paketinsamling på en virtuell dator. Mer information om hur du skapar ett paket avbilda besök [hantera paket som samlar in med portalen](network-watcher-packet-capture-manage-portal.md) eller med övriga genom att besöka [hantera paket som avbildar med REST API](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Scenario

I det här scenariot kan du:

* Granska en paketinsamling

## <a name="calculate-network-latency"></a>Beräkna nätverks-svarstid

I det här scenariot visar vi hur du visar inledande förfluten tid (RTT) i en konversation för Transmission Control Protocol (TCP) som sker mellan två slutpunkter.

När en TCP-anslutning har upprättats följer de första tre paket som skickats under anslutningen ett mönster som benämns trevägs-handskakningen. Genom att undersöka först två paket som skickats under den här handskakningen första begäran från klienten och ett svar från servern, kan vi beräkna svarstiden när anslutningen upprättades. Den här fördröjningen kallas som förfluten tid (RTT). Mer information om TCP-protokollet och trevägs handskakningen finns i följande resurs. https://support.microsoft.com/en-US/Help/172983/EXPLANATION-of-the-Three-Way-Handshake-via-TCP-IP

### <a name="step-1"></a>Steg 1

Starta WireShark

### <a name="step-2"></a>Steg 2

Läs in den **CAP** fil från din paketinsamling. Den här filen finns i blob som den har sparats i vårt lokalt på den virtuella datorn, beroende på hur du har konfigurerat.

### <a name="step-3"></a>Steg 3

Om du vill visa inledande förfluten tid (RTT) i TCP konversationer kommer vi bara att titta på de första två paket som ingår i TCP-handskakningen. Vi kommer att använda två första paketen i 3-vägs-handskakning som är [SYN], [SYN, ACK] paket. De har namngetts för flaggor i TCP-huvudet. Senaste paketet i handskakningen [ACK]-paketet används inte i det här scenariot. [SYN]-paket har skickats av klienten. När den tas emot skickar servern [ACK]-paketet som en bekräftelse för att ta emot SYN från klienten. Utnyttja det faktum att serverns svar kräver mycket lite belastning vi beräkna RTT genom att subtrahera tiden [SYN, ACK] paketet togs emot av klienten när [SYN] paket skickades av klienten.

Med WireShark beräknas detta värde för oss.

Om du vill visa de första två paket enklare i TCP trevägs handskakningen, ska vi använda filtrering funktioner av WireShark.

Om du vill använda filter i WireShark, expandera ”Transmission Control Protocol”-Segment i ett paket [SYN] i din avbildning och undersöka flaggor i TCP-huvudet.

Eftersom vi vill filtrera på alla [SYN] och [SYN ACK] paket under flaggor cofirm att Syn biten anges till 1 och sedan högerklickar du på Syn-bitars -> Använd som Filter -> valda.

![Bild 7][7]

### <a name="step-4"></a>Steg 4

Du kan enkelt välja konversationer som du är intresserad av att visa inledande RTT nu när du har filtrerat fönstret om du vill endast visa paket med bitars [SYN]. Ett enkelt sätt att visa RTT i WireShark klickar du på listrutan markerad ”SEQ-ACK” analys. Sedan visas RTT visas. I det här fallet har RTT 0.0022114 sekunder eller 2.211 ms.

![bild 8][8]

## <a name="unwanted-protocols"></a>Oönskade protokoll

Du kan ha många program som körs på en virtuell dator-instans som du har distribuerat i Azure. Många av dessa program kommunicera över nätverket, kanske utan uttryckligt tillstånd. Använder paketinsamling för att lagra nätverkskommunikation, kan vi undersöka hur programmet pratar i nätverket och leta efter eventuella problem.

I det här exemplet vi går igenom ett tidigare körde paketinsamling för oönskade protokoll som kan indikera obehörig kommunikation från ett program som körs på datorn.

### <a name="step-1"></a>Steg 1

Använda samma avbildning i scenariot ovan klickar du på **statistik** > **protokollet hierarki**

![protokollet hierarki-menyn][2]

Fönstret protokollet hierarki visas. Den här vyn visar en lista över protokoll som har använts under avbildningssessionen och antalet paket som skickas och tas emot med hjälp av protokollen. Den här vyn kan vara användbart för att hitta oönskad trafik på virtuella datorer eller i nätverket.

![protokollet hierachy öppnas][3]

Som du ser i följande skärmbild uppstod trafik via BitTorrent-protokollet som används för peer-to-peer-fildelning. Som administratör du inte räknar med att se BitTorrent trafik på denna specifika virtuella datorer. Nu kan du känner till den här trafiken, du kan ta bort peer-to-peer-program som installerats på den virtuella datorn eller blockera trafik med hjälp av Nätverkssäkerhetsgrupper eller en brandvägg. Dessutom kan du välja att köra paket insamlingar enligt ett schema så att du kan granska protokollet används på de virtuella datorerna regelbundet. Ett exempel på hur du automatiserar Nätverksaktiviteter i azure finns [övervaka nätverksresurser med azure automation](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Söka efter vanliga mål och portar

Förstå vilka typer av trafik, slutpunkterna och portar som kommunicerades via är en viktig vid övervakning eller felsöka program och resurser i nätverket. Använda filen ett paket från ovan kan vi snabbt lära vanliga mål våra VM kommunicerar med och portar som används.

### <a name="step-1"></a>Steg 1

Använda samma avbildning i scenariot ovan klickar du på **statistik** > **IPv4-statistik** > **mål och portar**

![paketet insamlings-fönstret][4]

### <a name="step-2"></a>Steg 2

Som vi titta igenom de resultat som en rad framhävs fanns det flera anslutningar på port 111. Var används mest port 3389, som är Fjärrskrivbord och de återstående är dynamiska RPC-portar.

När den här trafiken kan innebära att ingenting, är en port som användes för många anslutningar och är okänt för administratören.

![Bild 5][5]

### <a name="step-3"></a>Steg 3

Nu när vi har fastställt att en out-of plats port filtrera vi våra avbildning baserat på porten.

Filter i det här scenariot är:

```
tcp.port == 111
```

Vi ange filtertext ovan i textrutan filter och träffar.

![Bild 6][6]

Vi kan se all trafik kommer från en lokal virtuell dator i samma undernät från sökresultaten. Om vi inte fortfarande förstår varför den här trafiken uppstår, kan vi ytterligare inspektera paket för att avgöra varför den anropar dessa på port 111. Med den här informationen kan vi vidta lämplig åtgärd.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra diagnostiska funktioner i Nätverksbevakaren genom att besöka [Azure översikt för nätverksövervakning](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













