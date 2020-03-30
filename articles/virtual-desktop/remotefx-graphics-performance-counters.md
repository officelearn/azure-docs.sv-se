---
title: Diagnostisera grafikprestandaproblem Fjärrskrivbord – Azure
description: I den hÃ¤r artikeln beskrivs hur du anvÃ¤nder RemoteFX-grafikräknare i protokollsessioner fÃ¶r fjÃ¤rrskrivbord fÃ¶r att diagnostisera prestandaproblem med grafik i Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 84cee86dbddff77f6142925eec01889cf793a466
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127551"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnostisera problem med grafikprestanda i Fjärrskrivbord

För att diagnostisera kvalitetsproblem med dina fjärrsessioner har räknare tillhandahållits under avsnittet RemoteFX Graphics i Performance Monitor. Den här artikeln hjälper dig att identifiera och åtgärda grafikrelaterade flaskhalsar under RDP-sessioner (Remote Desktop Protocol) med hjälp av dessa räknare.

## <a name="find-your-remote-session-name"></a>Hitta ditt fjärrsessionsnamn

Du behöver fjärrsessionsnamnet för att identifiera grafikprestandaräknarna. Följ instruktionerna i det här avsnittet för att identifiera din instans av varje räknare.

1. Öppna Kommandotolken i Windows från fjärrsessionen.
2. Kör **kommandot qwinsta** och hitta ditt sessionsnamn.
    - Om sessionen finns på en virtuell dator med flera sessioner (VM): Din instans av varje räknare suffixeras av samma nummer som suffixar sessionsnamnet, till exempel "rdp-tcp 37".
    - Om din session finns i en virtuell dator som stöder virtuella grafikbearbetningsenheter (vGPU): Din instans av varje räknare lagras på servern i stället för i den virtuella datorn. Räknarinstanserna innehåller VM-namnet i stället för numret i sessionsnamnet, till exempel "Win8 Enterprise VM".

>[!NOTE]
> Medan räknare har RemoteFX i sina namn, de inkluderar fjärrskrivbordsgrafik i vGPU scenarier också.

## <a name="access-performance-counters"></a>Få tillgång till prestandaräknare

När du har bestämt fjärrsessionens namn följer du dessa instruktioner för att samla in prestandaräknarna för RemoteFX Graphics för fjärrsessionen.

1. Välj **Starta** > **prestandaövervakare****för administrationsverktyg** > .
2. Expandera **Övervakningsverktyg**i dialogrutan **Prestandaövervakare,** välj **Prestandaövervakare**och välj sedan **Lägg till**.
3. Expandera avsnittet för RemoteFX-grafik i listan Tillgängliga räknare i dialogrutan Lägg till **räknare.** **Add Counters**
4. Välj de räknare som ska övervakas.
5. I **instanserna av den markerade objektlistan** markerar du de specifika förekomster som ska övervakas för de markerade räknarna och väljer sedan **Lägg till**. Om du vill markera alla tillgängliga räknarinstanser väljer du **Alla instanser**.
6. När du har lagt till räknarna väljer du **OK**.

De valda prestandaräknarena visas på skärmen Prestandaövervakare.

>[!NOTE]
>Varje aktiv session på en värd har sin egen instans av varje prestandaräknare.

## <a name="diagnose-issues"></a>Diagnostisera problem

Grafikrelaterade prestandaproblem kan i allmänhet delas in i fyra kategorier:

- Låg bildhastighet
- Slumpmässiga bås
- Hög svarstid för indata
- Dålig ramkvalitet

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>Ta itu med låg bildhastighet, slumpmässiga bås och hög inmatningsfördröjning

Kontrollera först räknaren Utdataram/andra. Den mäter antalet ramar som gjorts tillgängliga för klienten. Om det här värdet är mindre än räknaren Indataram/Andra hoppas bildrutor över. Om du vill identifiera flaskhalsen använder du räknarna Ramar överhoppade/andra.

Det finns tre typer av ramar som hoppas över/andra räknare:

- Ramar som hoppas över/andra (otillräckliga serverresurser)
- Ramar som hoppas över/andra (otillräckliga nätverksresurser)
- Ramar som hoppas över/andra (otillräckliga klientresurser)

Ett högt värde för någon av räknarena Överhoppade ramar/andra innebär att problemet är relaterat till resursen som räknaren spårar. Om klienten till exempel inte avkodar och presenterar ramar i samma takt som servern tillhandahåller ramarna, är räknaren Ramar överhoppade/andra (otillräckliga klientresurser) hög.

Om räknaren Utdataram/andra matchar räknaren Indataram/Andra, men du fortfarande märker ovanlig fördröjning eller stoppning, kan genomsnittlig kodningstid vara den skyldige. Kodning är en synkron process som sker på servern i single-session (vGPU) scenario och på den virtuella datorn i scenariot med flera sessioner. Genomsnittlig kodningstid bör vara under 33 ms. Om genomsnittlig kodningstid är under 33 ms men du fortfarande har prestandaproblem kan det vara problem med appen eller operativsystemet som du använder.

Mer information om hur du diagnostiserar apprelaterade problem finns i Prestandaräknare för [fördröjning av indata](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

Eftersom RDP stöder en genomsnittlig kodningstid på 33 ms stöder den en indatabildhastighet på upp till 30 bildrutor/sekund. Observera att 33 ms är den maximala bildrutehastigheten som stöds. I många fall kommer bildfrekvensen som användaren upplever att vara lägre, beroende på hur ofta en ram tillhandahålls till RDP av källan. Uppgifter som att titta på en video kräver till exempel en full indatabildhastighet på 30 bildrutor/sekund, men mindre beräkningsintensiva uppgifter som att sällan redigera ett dokument resulterar i ett mycket lägre värde för indataramar/sekund utan försämring i användarens erfarenhetskvalitet.

### <a name="addressing-poor-frame-quality"></a>Ta itu med dålig ramkvalitet

Använd räknaren Ramkvalitet för att diagnostisera problem med ramkvaliteten. Den här räknaren uttrycker kvaliteten på utdataramen som en procentandel av källramens kvalitet. Kvalitetsförlusten kan bero på RemoteFX, eller så kan den vara inneboende i grafikkällan. Om RemoteFX orsakade kvalitetsförlusten kan problemet bero på brist på nätverks- eller serverresurser för att skicka innehåll med högre återgivning.

## <a name="mitigation"></a>Åtgärd

Om serverresurser orsakar flaskhalsen kan du prova någon av följande metoder för att förbättra prestanda:

- Minska antalet sessioner per värd.
- Öka minnet och beräkna resurser på servern.
- Släpp anslutningens upplösning.

Om nätverksresurser orsakar flaskhalsen kan du prova någon av följande metoder för att förbättra nätverkstillgängligheten per session:

- Minska antalet sessioner per värd.
- Använd ett nätverk med högre bandbredd.
- Släpp anslutningens upplösning.

Om klientresurser orsakar flaskhalsen kan du prova någon av följande metoder för att förbättra prestanda:

- Installera den senaste fjärrskrivbordsklienten.
- Öka minne och beräkna resurser på klientdatorn.

> [!NOTE]
> Vi stöder för närvarande inte källbildrutor/andraräknaren. För tillfället visas alltid 0 räknaren Source Frames/Second.

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar en GPU-optimerad virtuell Azure-dator finns i [Konfigurera GPU-acceleration (Graphics Processing Unit) för Windows Virtual Desktop-miljö](configure-vm-gpu.md).
- En översikt över felsöknings- och eskaleringsspår finns i [Felsökningsöversikt, feedback och support](troubleshoot-set-up-overview.md).
- Mer information om tjänsten finns i [Windows Desktop-miljö](environment-setup.md).
