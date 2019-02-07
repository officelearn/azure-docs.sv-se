---
title: Paketinspektion med Azure Network Watcher | Microsoft Docs
description: Den här artikeln beskriver hur du använder Network Watcher för att utföra djup paketinspektion som samlas in från en virtuell dator
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 1c30e679b250c2d6c6b03a1fe5d70dbd06acf052
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818370"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Paketinspektion med Azure Network Watcher

Funktionen packet capture i Network Watcher kan du initiera och hantera avbildningar sessioner på virtuella datorer i Azure Portal, PowerShell, CLI, och programmässigt via SDK och REST API. Du kan hantera scenarier som kräver att paketet på data genom att tillhandahålla information i ett format som är enkel att använda infångade paket. Utnyttja gratisverktyg för att granska data du undersöka kommunikation till och från dina virtuella datorer och få insikter i din nätverkstrafik. Vissa exempel användningsområden för paket-infångade data är: undersöka problem med nätverket eller ett program, identifiera nätverket missbruk och intrång försök eller underhålla regelefterlevnad. I den här artikeln visar vi hur du öppnar en fil för avbildning av paket som tillhandahålls av Network Watcher med ett populära öppen källkod-verktyg. Vi ger även exempel som visar hur du beräkna en fördröjning i anslutningen, identifiera onormalt trafik och granska nätverk statistik.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln går igenom några förkonfigurerade scenarier på ett infångat paket som kördes tidigare. Dessa scenarier visar funktioner som kan nås genom att granska ett infångat paket. Det här scenariot använder [WireShark](https://www.wireshark.org/) att inspektera paketfångsten.

Det här scenariot förutsätter att du redan har kört ett infångat paket på en virtuell dator. Lär dig hur du skapar ett packet capture besök [fjärrinfångade hantera paket med portalen](network-watcher-packet-capture-manage-portal.md) eller med REST genom att besöka [hantera Fjärrinfångade paket med REST API](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Scenario

I det här scenariot kan du:

* Granska ett infångat paket

## <a name="calculate-network-latency"></a>Beräkna svarstid för nätverk

I det här scenariot visar vi hur du visar inledande serveranrop tid (RTT) av en Transmission Control Protocol (TCP)-konversation mellan två slutpunkter.

När en TCP-anslutning har upprättats, följer de första tre paket som skickats under anslutningen ett mönster som vanligtvis kallas 3-vägs-handskakningen. Genom att undersöka de första två paket som skickats under den här handskakning en ursprunglig begäran från klienten och ett svar från servern, kan vi beräkna svarstiden när den här anslutningen har upprättats. Den här fördröjningen refereras till som serveranrop tid (RTT). Mer information om TCP-protokollet och trevägs handskakningen finns i följande resurs. https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip

### <a name="step-1"></a>Steg 1

Launch WireShark

### <a name="step-2"></a>Steg 2

Läs in den **.cap** fil från din infångade paket. Den här filen finns i den blob som den har sparats på vår lokalt på den virtuella datorn, beroende på hur du har konfigurerat.

### <a name="step-3"></a>Steg 3

Om du vill visa inledande serveranrop tid (RTT) i TCP-konversationer, kommer vi endast att titta på de första två paket som ingår i TCP-handskakningen. Vi kommer att använda två första paketen i 3-vägs-handskakningen som är [SYN], [SYN, ACK] paket. De har namngetts för flaggor i TCP-huvudet. Senaste paketet i handskakningen [ACK]-paketet ska inte användas i det här scenariot. [SYN]-paketet har skickats av klienten. När den tas emot skickar servern [ACK]-paketet som en bekräftelse att få SYN från klienten. Utnyttja det faktum att serverns svar kräver mycket lite belastning, vi beräkna RTT genom att subtrahera tiden [SYN, ACK] paketet togs emot av klienten med tiden [SYN] paket skickades av klienten.

Med hjälp av WireShark beräknas det här värdet för oss.

Om du vill visa de första två paket enklare i 3-vägs-handskakning TCP, ska vi använda filtrering krypteringsfunktionen i WireShark.

Expandera det ”Transmission Control Protocol”-segmentet i en [SYN]-paketet i din avbildning för att tillämpa filtret i WireShark och granska de flaggor som angetts i TCP-huvudet.

Eftersom vi vill filtrera på alla [SYN] och [SYN, ACK] paket under flaggor bekräfta att Syn-bitars har angetts till 1 och sedan högerklickar du på Syn-bitars -> Använd som Filter -> valda.

![Bild 7][7]

### <a name="step-4"></a>Steg 4

Nu när du har filtrerat fönstret om du vill endast visa paket med bitars [SYN], kan du enkelt välja konversationer som du är intresserad av att visa den första RTT. Ett enkelt sätt att visa RTT i WireShark klickar du på listrutan markerats ”SEQ-ACK” analys. Därefter visas RTT visas. I det här fallet har RTT 0.0022114 sekunder eller 2.211 ms.

![bild 8][8]

## <a name="unwanted-protocols"></a>Oönskad protokoll

Du kan ha många program som körs på en virtuell datorinstans som du har distribuerat i Azure. Många av dessa program kommunicera över nätverket, kanske utan ditt uttryckliga medgivande. Vi kan använda infångade paket för att lagra nätverkskommunikation, för att undersöka hur programmet talar i nätverket och leta efter eventuella problem.

I det här exemplet vi går igenom ett tidigare körde infångade paket för oönskade protokoll som kan tyda på obehörig kommunikation från ett program som körs på din dator.

### <a name="step-1"></a>Steg 1

Med hjälp av samma avbildningen i scenariot ovan klickar du på **statistik** > **protokollet hierarki**

![protokollet hierarki menyn][2]

Fönstret protokollet hierarki visas. Den här vyn visar en lista över alla protokoll som har använts under avbildningssessionen och antalet paket som skickas och tas emot med hjälp av protokollen. Den här vyn kan användas för att hitta oönskad nätverkstrafik på virtuella datorer eller i nätverket.

![protokollet hierarki öppnas][3]

Uppstod trafik med hjälp av protokollet BitTorrent som används för peer-to-peer-fildelning som du ser i följande skärmbild. Som administratör du inte räknar med att se BitTorrent trafik på den här specifika virtuella datorer. Nu kan du tänka på den här trafiken, du kan ta bort peer-to-peer-programvara som installeras på den här virtuella datorn eller blockera trafik med hjälp av Nätverkssäkerhetsgrupper eller en brandvägg. Dessutom kan välja du att köra infångade paket enligt ett schema, så att du kan granska hur protokoll på dina virtuella datorer regelbundet. Ett exempel på hur du automatiserar uppgifter i nätverket i azure finns [övervaka nätverksresurser med azure automation](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Söka efter vanliga mål och portar

Förstå vilka typer av trafik, slutpunkter och de portar som kommunicerades via är en viktig vid övervakning eller felsökning av program och resurser i nätverket. Använda en paket-infångade filen ovan kan vi kan snabbt lära dig de främsta mål vår virtuella dator kommunicerar med och de portar som används.

### <a name="step-1"></a>Steg 1

Med hjälp av samma avbildningen i scenariot ovan klickar du på **statistik** > **IPv4-statistik** > **mål och portar**

![Packet capture desto][4]

### <a name="step-2"></a>Steg 2

När vi tittar igenom de resultat som en rad sticker ut fanns det flera anslutningar på port 111. Har den mest använda porten 3389, vilket är Fjärrskrivbord och de återstående är dynamiska RPC-portar.

Den här trafiken kan innebära att ingenting, men det är en port som har använts för många anslutningar och är okända för administratören.

![Bild 5][5]

### <a name="step-3"></a>Steg 3

Nu när vi har bestämt en out of plats port filtrerar vi vår avbildning baserat på porten.

Filtret i det här scenariot är:

```
tcp.port == 111
```

Vi ange filtertext ovan i filtrets textruta och tryck på RETUR.

![Bild 6][6]

Vi kan se all trafik kommer från en lokal virtuell dator i samma undernät från resultatet. Om vi inte fortfarande förstår varför den här trafiken sker, kan vi ytterligare granska paket för att avgöra varför den gör dessa anrop på port 111. Med den här informationen kan vi dra lämplig åtgärd.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra diagnostiska funktioner i Network Watcher genom att besöka [Azure översikt för nätverksövervakning](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













