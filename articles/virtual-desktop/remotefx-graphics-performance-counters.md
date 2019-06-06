---
title: Diagnostisera prestandaproblem för grafik i Fjärrskrivbord - Azure
description: Den här artikeln beskriver hur du använder RemoteFX grafik räknare i Fjärrskrivbordsprotokollet sessioner för att diagnostisera prestandaproblem med bilderna i virtuella Windows-skrivbordet.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 05/23/2019
ms.author: v-chjenk
ms.openlocfilehash: 0b4113f1e0024415135aa99d1fb4e881efe448a3
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499271"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnostisera prestandaproblem för grafik i Fjärrskrivbord

När systemet inte fungerar som förväntat, är det viktigt att identifiera orsaken till problemet. Den här artikeln hjälper dig att identifiera och åtgärda flaskhalsar i grafik-relaterade prestanda under Remote Desktop Protocol (RDP)-sessioner.

## <a name="find-your-remote-session-name"></a>Hitta fjärrsession namn

Du behöver din fjärrsession namn som identifierar prestandaräknarna för grafik. Följ anvisningarna i det här avsnittet för att identifiera namnet på din Windows Virtual Desktop förhandsversion fjärrsessionen.

1. Öppna Kommandotolken Windows från fjärrsessionen.
2. Kör den **qwinsta** kommando.
    - Om din session är värd för en flera session virtuell dator (VM): Suffix för varje räknarnamnet är samma suffixet i din sessionsnamn, till exempel ”rdp-tcp 37”.
    - Om din session är värd för en virtuell dator som har stöd för virtuella GPU-enheter (vGPU): Räknarna lagras på servern i stället för i den virtuella datorn. Räknarinstanser omfattar VM-namnet i stället för i sessionsnamn som ”Win8 Enterprise VM”.

>[!NOTE]
> Medan räknare har RemoteFX i sina namn kan inkludera de remote desktop grafik i vGPU-scenarier.

## <a name="access-performance-counters"></a>Prestandaräknare för åtkomst

Prestandaräknare i RemoteFX grafik hjälper dig identifiera flaskhalsar genom att hjälpa dig spåra saker som ram kodning tid och överhoppade ramar.

När du har bestämt fjärrsession-namn, följer du dessa instruktioner för att samla in prestandaräknare RemoteFX grafik för fjärrsessionen.

1. Välj **starta** > **Administrationsverktyg** > **Prestandaövervakaren**.
2. I den **Prestandaövervakaren** dialogrutan Expandera **övervakningsverktyg**väljer **Prestandaövervakaren**, och välj sedan **Lägg till**.
3. I den **Lägg till räknare** dialogrutan från den **tillgängliga räknare** Expandera prestandaräknarobjektet för RemoteFX grafik.
4. Välj räknarna som ska övervakas.
5. I den **instanser av markerade objekt** väljer du de specifika instanserna som ska övervakas för valda räknare och välj sedan **Lägg till**. Om du vill välja alla tillgängliga räknarinstanser, Välj **alla instanser**.
6. När du lägger till räknarna, Välj **OK**.

Valda prestandaräknare visas på skärmen Prestandaövervakaren.

>[!NOTE]
>Varje aktiv session på en värd har en egen instans av varje prestandaräknare.

## <a name="diagnosis"></a>Du kan undersöka

Grafik minnesrelaterade prestandaproblem vanligtvis faller inom fyra kategorier:

- Låg bildfrekvens
- Slumpmässig bås
- Fördröjningar som indata
- Dålig ramens kvalitet

Starta genom att ta itu med låg bildfrekvens och slumpmässiga bås fördröjningar som indata. Nästa avsnitt får du veta vilka prestandaräknare mäta varje kategori.

### <a name="performance-counters"></a>Prestandaräknare

Det här avsnittet hjälper dig att identifiera flaskhalsar.

Kontrollera först utdata Frames/Second räknaren. Mäter antalet ramar som blir tillgängliga för klienten. Om det här värdet är mindre än räknaren indata Frames/Second, ramar som hoppas över. Använd ramar ignoreras per sekund räknare för att identifiera flaskhalsen.

Det finns tre typer av ramar ignoreras per sekund räknare:

- Bildrutor hoppas över per sekund (otillräckligt nätverksresurser)
- Bildrutor hoppas över per sekund (inte tillräckligt med klientens resurser)
- Bildrutor hoppas över per sekund (inte tillräckligt med serverresurser)

Ett högt värde för någon av ramar som ignoreras per sekund räknare innebär att problemet är relaterat till resursen spårar räknaren. Till exempel om klienten inte avkoda och finns bildrutor enligt samma taxa servern bildrutorna, ska räknaren bildrutor ignoreras per sekund (otillräcklig i klientens resurser) vara hög.

Om räknaren utdata Frames/Second matchar räknaren indata Frames/Second, men du måste fortfarande ovanliga lag eller fördröjs, kan problemet vara genomsnittstiden kodning. Kodning är en synkron process som körs på servern i en session (vGPU)-scenariot och på den virtuella datorn i flera session-scenario. Genomsnittlig tid för kodning ska vara under 33 ms. Om kodning genomsnittstiden är under 33 ms, men du fortfarande har problem med prestanda, kan det finnas ett problem med appen eller operativsystemet som du använder.

Mer information om hur du diagnostiserar app-relaterade problem finns i [användarens indata fördröjning prestandaräknare](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters).

Eftersom RDP har stöd för en genomsnittlig kodning tid på 33 ms, stöder en inkommande bildfrekvens upp till 30 bilder per sekund. Observera att 33 ms är maximala stöds hastigheten. I många fall blir den erfarna av användaren hastigheten mindre, beroende på hur ofta en ram har angetts för RDP av källan. Uppgifter som till exempel tittar på en video som kräver en fullständig inkommande bildfrekvens på 30 bildrutor per sekund, men mindre omfattande resurs uppgifter, t.ex. sällan redigerar ett word-dokument som inte kräver en hög frekvens av inkommande bildrutor per sekund för en bra användarupplevelse.

Använd räknaren ramens kvalitet för att diagnostisera kvalitetsproblem i ramen. Den här räknaren uttrycker kvaliteten på utdata-ram som en procentandel av kvaliteten på käll-ramen. Kvalitetsförlust kan bero på RemoteFX eller det kan vara kommer med grafik-källan. Om RemoteFX orsakade kvalitetsförlust, vara problemet brist på resurser för nätverks- eller att skicka större exakthet innehållet.

## <a name="mitigation"></a>Åtgärd

Om serverresurser orsakar flaskhalsen, pröva någon av följande saker att förbättra prestanda:

- Minska antalet sessioner per värd.
- Öka mängden minne och beräkna resurser på servern.
- Ta bort lösning av anslutningen.

Om nätverksresurser som orsakar flaskhalsen, pröva någon av följande saker att förbättra nätverkets tillgänglighet per session:

- Minska antalet sessioner per värd.
- Ta bort lösning av anslutningen.
- Använd ett nätverk med högre bandbredd.

Om klientens resurser som orsakar flaskhalsen, gör du något eller båda av följande saker att förbättra prestanda:

- Installera den senaste fjärrskrivbordsklienten.
- Öka minne och beräkningsresurser på klientdatorn.

> [!NOTE]
> Vi stöder för närvarande inte källan Frames/Second räknaren. För tillfället anges räknaren källa Frames/Second alltid till 0.

## <a name="next-steps"></a>Nästa steg

- Om du vill skapa en Azure-dator i GPU-optimerad [konfigurera grafikprocessorer (GPU) unit acceleration för Windows Virtual Desktop förhandsversionsmiljön](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu).
- En översikt över felsökning och Eskalering spårar finns i [översikt, feedback och support](https://docs.microsoft.com/azure/virtual-desktop/troubleshoot-set-up-overview).
- Läs mer om förhandsversionen av tjänsten i [Windows Desktop förhandsversionsmiljön](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
