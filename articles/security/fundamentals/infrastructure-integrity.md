---
title: Azure-infrastrukturintegritet
description: Den här artikeln behandlar integriteten för Azure-infrastrukturen.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: ef81e74b07a351139aa8feefbdf1b89ea7e4994f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727195"
---
# <a name="azure-infrastructure-integrity"></a>Azure-infrastrukturintegritet

## <a name="software-installation"></a>Installation av programvara
Alla komponenter i programvarustacken som är installerade i Azure-miljön är specialbyggda enligt Microsoft Security Development Lifecycle (SDL) process. Alla programvarukomponenter, inklusive operativsystemavbildningar och SQL Database, distribueras som en del av processen för hantering av ändringshantering och utgivning. Operativsystemet som körs på alla noder är en anpassad version av Windows Server 2008 eller Windows Server 2012. Den exakta versionen väljs av tyget controller (FC) enligt den roll den avser för OS att spela. Dessutom tillåter värdoperativsystemet inte installation av obehöriga programvarukomponenter.

Vissa Azure-komponenter distribueras som Azure-kunder på en gästd-VM som körs på ett gästoperativsystem.

## <a name="virus-scans-on-builds"></a>Virussökningar på byggen
Azure-programvarukomponent (inklusive OS) bygger måste genomgå en virussökning som använder verktyget för skydd mot virus i slutpunkten. Varje virussökning skapar en logg i den associerade byggkatalogen, med uppgifter om vad som skannades och resultatet av genomsökningen. Virussökningen är en del av byggkällan för varje komponent i Azure. Koden flyttas inte till produktion utan att ha en ren och framgångsrik virussökning. Om några problem noteras är versionen frusen och går sedan till säkerhetsteamen inom Microsoft Security för att identifiera var "rogue"-koden angavs i versionen.

## <a name="closed-and-locked-environment"></a>Sluten och låst miljö
Som standard har Azure-infrastrukturnoder och gäst-virtuella datorer inte användarkonton skapade på dem. Dessutom är standardkonton för Windows-administratörer inaktiverade. Administratörer från Azure live-support kan, med korrekt autentisering, logga in på dessa datorer och administrera Azure-produktionsnätverket för nödreparationer.

## <a name="azure-sql-database-authentication"></a>Azure SQL Database-autentisering
Som med alla implementeringar av SQL Server måste hantering av användarkonton vara hårt kontrollerad. Azure SQL Database stöder endast SQL Server-autentisering. För att komplettera en kunds datasäkerhetsmodell bör användarkonton med starka lösenord och konfigureras med specifika rättigheter också användas.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>Åtkomstkontrollistor och brandväggar mellan Microsofts företagsnätverk och ett Azure-kluster
Åtkomstkontrollistor (ACL: er) och brandväggar mellan tjänstplattformen och Microsofts företagsnätverk skyddar SQL Database-instanser från obehörig insideråtkomst. Dessutom är det bara användare från IP-adressen som sträcker sig från Microsofts företagsnätverk som kan komma åt slutpunkten för plattformshantering i Windows Fabric.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>Åtkomstkontrollistor och brandväggar mellan noder i ett SQL Database-kluster
Som ett ytterligare skydd, som en del av den djupgående strategin för försvar, har ACL:er och en brandvägg implementerats mellan noder i ett SQL Database-kluster. All kommunikation i Windows Fabric-plattformsklustret samt all kod som körs är betrodd.

## <a name="custom-monitoring-agents"></a>Anpassade övervakningsagenter
SQL Database använder anpassade övervakningsagenter (MAs), även kallade watchdogs, för att övervaka hälsotillståndet för SQL Database-klustret.

## <a name="web-protocols"></a>Webbprotokoll

### <a name="role-instance-monitoring-and-restart"></a>Övervakning och omstart av rollinstans
Azure säkerställer att alla distribuerade roller som körs (internetinriktad webb eller bakåtdiand bearbetning av arbetarroller) är föremål för varaktig hälsoövervakning för att säkerställa att de effektivt och effektivt levererar de tjänster som de har etablerats för. Om en roll blir felfritt, antingen genom ett kritiskt fel i programmet som finns eller ett underliggande konfigurationsproblem i själva rollinstansen, identifierar FC problemet i rollinstansen och initierar ett korrigerande tillstånd.

### <a name="compute-connectivity"></a>Beräkna anslutning
Azure säkerställer att det distribuerade programmet eller tjänsten kan nås via standard webbaserade protokoll. Virtuella instanser av internetvända webbroller har extern internetanslutning och kan nås direkt av webbanvändare. För att skydda känsligheten och integriteten för de åtgärder som arbetarroller utför för de offentligt tillgängliga virtuella webbrollsinstanserna, har virtuella instanser av bakåtdelade bearbetningsarbetareroller extern internetanslutning men kan inte direkt av externa webbanvändare.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokaler och fysisk säkerhet](physical-security.md)
- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Komponenter och gränser för Azure-informationssystem](infrastructure-components.md)
- [Azure-nätverksarkitektur](infrastructure-network.md)
- [Azure-produktionsnätverk](production-network.md)
- [Säkerhetsfunktioner i Azure SQL Database](infrastructure-sql.md)
- [Azure-produktionsåtgärder och -hantering](infrastructure-operations.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Azure-kunddataskydd](protection-customer-data.md)
