---
title: Integriteten hos Azure-infrastrukturen
description: Den här artikeln tar integriteten hos Azure-infrastrukturen.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 18d7fab30c0bbaa5292fe5d3003b7f2309b34d3b
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102688"
---
# <a name="integrity-of-azure-infrastructure"></a>Integriteten hos Azure-infrastrukturen   

## <a name="software-installation"></a>Installation av programvara
Alla komponenter i stacken programvara som är installerade i Azure-miljön är egna anpassade följande process för i Microsoft Security Development Lifecycle (SDL). Alla programkomponenter (inklusive operativsystemsavbildningar och SQL-databasen) distribueras som en del av processen för ändrings- och versionshantering. Operativsystemet som körs på alla noder är en anpassad version av Windows Server 2008 eller Windows Server 2012. Den exakta versionen väljs av FC enligt rollen den avser för OS att spela upp. Dessutom tillåter inte Värdoperativsystem installation för alla komponenter av otillåten programvara.

Vissa Microsoft Azure-komponenter (till exempel RDFE, Developer-portalen, etc.) distribueras som Azure-kunder på gäst-VM som körs på Gästoperativsystem.

## <a name="virus-scans-on-builds"></a>Virusgenomsökningar på versioner
Azure programversioner för komponent (inklusive OS) måste gå igenom en virusgenomsökning med verktyget Microsoft Endpoint Protection (MEP) antivirusprogram. Varje viruskontroll skapas en logg i katalogen associerade build, med information om vad skannad och resultatet av genomsökningen. Viruskontroll är en del av källkoden build för varje komponent i Azure. Koden kommer inte flyttas till produktion utan ren och lyckade virus skanna. Om det uppstår några problem som anges, bygga är låst och går sedan vidare till säkerhet teamen på Microsoft Security att identifiera där ”falska” kod angetts versionen.

## <a name="closedlocked-environment"></a>Stängd/låst miljö
Som standard har inte några användarkonton som skapats på dem i Azure-infrastrukturnoder och Gäst-VM. Dessutom inaktiveras även standard Windows administratörskonton. Administratörer från Microsoft (WALS Azure Live Support) kan – med korrekt autentisering – logga in på dessa datorer och administrera Azure-produktionsnätverket för reparation.

## <a name="microsoft-azure-sql-database-authentication"></a>Microsoft Azure SQL Database-autentisering
Precis som med någon implementering av SQL Server, måste hantering av användarkonton vara hårt styrda. Microsoft Azure SQL Database stöder endast SQL Server-autentisering. Användare med starka lösenord och konfigurerad med specifika rättigheter bör användas samt att komplettera säkerhetsmodell för kundens data.

## <a name="firewallacls-between-msft-corpnet-and-microsoft-azure-cluster"></a>Brandvägg/ACL: er mellan MSFT CorpNet och Microsoft Azure-kluster
ACL: er/brandvägg mellan Service-plattformen och MS företagsnätverket skydda Microsoft Azure SQL Database från insider obehörig åtkomst. Dessutom kan endast användare från IP-adressintervall från Microsoft CorpNet kan komma åt WinFabric platform management slutpunkten.

## <a name="firewallacls-between-nodes-in-an-azure-sql-db-cluster"></a>Brandvägg/ACL: er mellan noder i ett kluster med Azure SQL DB
Som ett ytterligare skydd som en del av den skydd på djupet strategin, har ACL-brandväggen implementerats mellan noder i ett Microsoft Azure SQL DB-kluster. All kommunikation inom klustret WinFabric plattform samt all kod som körs är betrodd.

## <a name="custom-mas-watchdogs"></a>Anpassade MAs (Watchdogs)
Microsoft Azure SQL Database använder anpassade MAs kallas watchdogs för att övervaka hälsotillståndet för Microsoft Azure SQL DB-klustret.

## <a name="web-protocols"></a>Webbprotokoll

### <a name="role-instance-monitoring-and-restart"></a>Rollen instans övervakning och omstart
Azure säkerställer att alla körs roller (Internet-riktade webbprogram eller arbetsroller i backend-bearbetning) distribueras regleras varaktigt hälsa övervakning för att säkerställa att de är effektivt och effektivt leverera tjänster där de har etablerats. En roll blir ohälsosamt, av ett allvarligt fel i programmet som värd eller underliggande konfigurationsproblem i instansen själva Microsoft Azure FC identifierar problem inom rollinstansen och initiera ett korrigerande tillstånd .

### <a name="compute-connectivity"></a>Compute-anslutning
Azure säkerställer att programmet/tjänsten har distribuerats kan nås via webbaserade standardprotokoll. Internet-riktade webbprogram rollen virtuella instanser ska ha extern Internet-anslutning och kan nås direkt av web-användare. Backend-bearbetning worker-rollen virtuella instanser har extern Internet-anslutning, men kan inte användas direkt av en extern webbplats användare för att kunna skydda känslighet och integriteten för de åtgärder arbetsroller utföra för den offentligt tillgängliga web rollen virtuella instanser.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure verksamhet, lokal och fysisk säkerhet](azure-physical-security.md)
- [Tillgängligheten för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Microsoft Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktion och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Skydd av kundinformation i Azure](azure-protection-of-customer-data.md)
