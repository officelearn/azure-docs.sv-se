---
title: Paketinspektion med Azure Network Watcher | Microsoft-dokument
description: I den här artikeln beskrivs hur du använder Network Watcher för att utföra djup paketinspektion som samlats in från en virtuell dator
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 7d32043ca73e9cf810b3eab5e65cb4b42b599d18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152932"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Paketinspektion med Azure Network Watcher

Med hjälp av paketinsamlingsfunktionen i Network Watcher kan du initiera och hantera insamlingar av sessioner på dina virtuella Azure-datorer från portalen, PowerShell, CLI och programmässigt via SDK- och REST-API:et. Med paketinsamling kan du hantera scenarier som kräver paketnivådata genom att tillhandahålla informationen i ett lättanpassat format. Genom att utnyttja fritt tillgängliga verktyg för att granska data kan du granska kommunikation som skickas till och från dina virtuella datorer och få insikter om nätverkstrafiken. Några exempel på användning av paketinsamlingsdata är: undersöka nätverks- eller programproblem, identifiera nätverksmissbruk och intrångsförsök eller upprätthålla regelefterlevnad. I den här artikeln visar vi hur du öppnar en paketfångstfil som tillhandahålls av Network Watcher med ett populärt verktyg med öppen källkod. Vi kommer också att ge exempel som visar hur du beräknar en anslutningsfördröjning, identifierar onormal trafik och undersöker nätverksstatistik.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln går igenom några förkonfigurerade scenarier på en paketfångst som kördes tidigare. Dessa scenarier illustrerar funktioner som kan nås genom att granska en paketfångst. I det här scenariot används [WireShark](https://www.wireshark.org/) för att inspektera paketfångsten.

Det här scenariot förutsätter att du redan har kört en paketfångst på en virtuell dator. Om du vill veta hur du skapar ett paketfångstbesök [Hantera paketinfångningar med portalen](network-watcher-packet-capture-manage-portal.md) eller med REST genom att besöka [Hantera paketinfångningar med REST API](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Scenario

I det här fallet kan du:

* Granska en paketfångst

## <a name="calculate-network-latency"></a>Beräkna nätverksfördröjning

I det här fallet visar vi hur du visar den första rundresatid (RTT) för en TCP-konversation (Transmission Control Protocol) som inträffar mellan två slutpunkter.

När en TCP-anslutning upprättas följer de tre första paketen som skickas i anslutningen ett mönster som vanligen kallas trevägshandskakning. Genom att undersöka de två första paketen som skickades i det här handslaget, en första begäran från klienten och ett svar från servern, kan vi beräkna svarstiden när den här anslutningen upprättades. Den här latensen kallas RTT (Round Trip Time). Mer information om TCP-protokollet och trevägshandskakningen finns i följande resurs. [https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip](https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip)

### <a name="step-1"></a>Steg 1

Starta WireShark

### <a name="step-2"></a>Steg 2

Läs in **kapsylfilen** från paketfångsten. Den här filen finns i blobben som sparades lokalt på den virtuella datorn, beroende på hur du konfigurerade den.

### <a name="step-3"></a>Steg 3

För att se den första rundresatiden (RTT) i TCP-konversationer kommer vi bara att titta på de två första paketen som är involverade i TCP-handskakningen. Vi kommer att använda de två första paketen i trevägshandskakningen, som är [SYN], [SYN, ACK] paket. De är namngivna efter flaggor som anges i TCP-huvudet. Det sista paketet i handskakningen, [ACK]-paketet, kommer inte att användas i det här scenariot. [SYN]-paketet skickas av klienten. När den har tagits emot skickar servern [ACK]-paketet som en bekräftelse på att ta emot SYN från klienten. Genom att utnyttja det faktum att serverns svar kräver mycket lite omkostnader beräknar vi RTT genom att subtrahera den tid då [SYN, ACK]-paketet togs emot av klienten när [SYN]-paketet skickades av klienten.

Med WireShark beräknas det här värdet för oss.

För att lättare visa de två första paketen i TCP trevägshandskakning, kommer vi att använda filtreringskapaciteten som tillhandahålls av WireShark.

Om du vill använda filtret i WireShark expanderar du segmentet "Transmission Control Protocol" för ett [SYN]-paket i insamlingen och undersöker flaggorna som anges i TCP-huvudet.

Eftersom vi funderar på att filtrera på alla [SYN] och [SYN, ACK] paket, under flaggor bekräfta att Syn bit är inställd på 1, högerklicka sedan på Syn bit -> Apply som Filter -> Selected.

![bild 7][7]

### <a name="step-4"></a>Steg 4

Nu när du har filtrerat fönstret för att bara se paket med [SYN]-bituppsättningen kan du enkelt välja konversationer som du är intresserad av för att visa den ursprungliga RTT. Ett enkelt sätt att visa RTT i WireShark klickar bara på listrutan märkt "SEQ/ACK"-analys. Du kommer då att se RTT visas. I det här fallet var RTT 0,0022114 sekunder, eller 2,211 ms.

![bild 8][8]

## <a name="unwanted-protocols"></a>Oönskade protokoll

Du kan ha många program som körs på en instans för virtuella datorer som du har distribuerat i Azure. Många av dessa program kommunicerar över nätverket, kanske utan ditt uttryckliga tillstånd. Med paketfångst för att lagra nätverkskommunikation kan vi undersöka hur programmet talar i nätverket och leta efter eventuella problem.

I det här exemplet granskar vi en tidigare körning paketfångst för oönskade protokoll som kan tyda på obehörig kommunikation från ett program som körs på din dator.

### <a name="step-1"></a>Steg 1

Använda samma hämtning i föregående scenario klicka på > **Statistikprotokollhierarki** **Statistics**

![menyn protokollhierarki][2]

Fönstret protokollhierarki visas. Den här vyn innehåller en lista över alla protokoll som användes under insamlingssessionen och antalet paket som överförs och tas emot med hjälp av protokollen. Den här vyn kan vara användbar för att hitta oönskad nätverkstrafik på dina virtuella datorer eller nätverk.

![protokollhierarkin har öppnats][3]

Som du kan se i följande skärmdump, det var trafik med BitTorrent-protokollet, som används för peer to peer-fildelning. Som administratör förväntar du dig inte att se BitTorrent-trafik på just den här virtuella datorern. Nu när du känner till den här trafiken kan du ta bort peer to peer-programvaran som är installerad på den här virtuella datorn eller blockera trafiken med nätverkssäkerhetsgrupper eller en brandvägg. Dessutom kan du välja att köra paketinsamlingar enligt ett schema, så att du kan granska protokollanvändningen på dina virtuella datorer regelbundet. Ett exempel på hur du automatiserar nätverksaktiviteter i azure besök [Övervaka nätverksresurser med azure automation](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Hitta populära destinationer och hamnar

Att förstå vilka typer av trafik, slutpunkter och portar som kommuniceras över är viktigt när du övervakar eller felsöker program och resurser i nätverket. Med hjälp av en paketfångstfil ovanifrån kan vi snabbt lära oss de bästa destinationerna som vår virtuella dator kommunicerar med och de portar som används.

### <a name="step-1"></a>Steg 1

Använda samma hämtning i föregående scenario klicka på **Statistik** > **IPv4 Statistik** > **destinationer och portar**

![fönstret paketinsamling][4]

### <a name="step-2"></a>Steg 2

När vi tittar igenom resultaten en linje sticker ut, det fanns flera anslutningar på port 111. Den mest använda porten var 3389, vilket är fjärrskrivbord, och de återstående är RPC dynamiska portar.

Även om den här trafiken inte betyder någonting, är det en port som användes för många anslutningar och är okänd för administratören.

![bild 5][5]

### <a name="step-3"></a>Steg 3

Nu när vi har bestämt en plats port kan vi filtrera vår fångst baserat på hamnen.

Filtret i det här scenariot skulle vara:

```
tcp.port == 111
```

Vi anger filtertexten ovanifrån i filtertextrutan och tryck enter.

![bild 6][6]

Från resultaten kan vi se all trafik kommer från en lokal virtuell dator på samma undernät. Om vi fortfarande inte förstår varför denna trafik sker, kan vi ytterligare inspektera paketen för att avgöra varför det gör dessa samtal på port 111. Med denna information kan vi vidta lämpliga åtgärder.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om de andra diagnostiska funktionerna i Network Watcher genom att besöka [Översikt över Övervakning av Azure-nätverk](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













