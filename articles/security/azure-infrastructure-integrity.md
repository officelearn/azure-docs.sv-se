---
title: Azure-infrastrukturen integritet
description: Den här artikeln tar upp integriteten hos Azure-infrastrukturen.
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
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 867bc66a68bec662153d8336e649cf46df02f101
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901324"
---
# <a name="azure-infrastructure-integrity"></a>Azure-infrastrukturen integritet

## <a name="software-installation"></a>Programvaruinstallation
Alla komponenter i stacken programvara som är installerade i Azure-miljön är egna anpassade följande process för den Microsoft Security Development Lifecycle (SDL). Alla programkomponenter (inklusive OS-avbildningar och SQL Database) distribueras som en del av ändrings- och versionshantering. Operativsystemet som körs på alla noder är en anpassad version av Windows Server 2008 eller Windows Server 2012. Den exakta versionen väljs av FC enligt den roll som den har för avsikt för operativsystem och spela upp. Dessutom tillåter inte Värdoperativsystem installation av någon obehörig programvarukomponenter.

Vissa Microsoft Azure-komponenter (till exempel RDFE, Developer-portalen, osv) distribueras som Azure-kunder på Virtuella gästdatorer som körs på gäst-OS.

## <a name="virus-scans-on-builds"></a>Virusgenomsökningar på versioner
Azure-program (inklusive OS)-komponenten versioner måste gå igenom en virusgenomsökning med verktyget Microsoft Endpoint Protection (MEP) ett virusskyddsprogram. Varje viruskontroll skapar en logg i den associera build-katalogen med information om vad söktes igenom och resultatet av genomsökningen. Genomsökningen virus är en del av källkoden build för varje komponent i Azure. Kod kommer inte att flytta till produktion utan en ren och lyckade virus skanna. Om det finns några problem som anges, bygget är låst och går sedan vidare till säkerhet-teamen på Microsoft Security för att identifiera där ”falska”-kod angetts bygget.

## <a name="closedlocked-environment"></a>Stängd/låst miljö
Som standard har inte alla användarkonton som skapats på dem i Azure-infrastrukturnoder och virtuella gästdatorer. Dessutom har standard Windows-administratörskonton också inaktiverats. Administratörer från Microsoft (WALS Azure Live Support) kan – med korrekt autentisering – logga in på dessa datorer och administrera Azure-produktionsnätverket för reparation.

## <a name="microsoft-azure-sql-database-authentication"></a>Microsoft Azure SQL Database-autentisering
Precis som med någon implementering av SQL Server, måste hantering av användarkonton vara hårt styrda. Microsoft Azure SQL Database stöder endast SQL Server-autentisering. Användaren konton med starka lösenord och konfigurerad med specifika rättigheter ska användas samt att komplettera säkerhetsmodell för kundens data.

## <a name="firewallacls-between-msft-corpnet-and-microsoft-azure-cluster"></a>Brandvägg/ACL: er mellan MSFT CorpNet och Microsoft Azure-kluster
ACL: er/brandvägg mellan Service-plattformen och MS företagsnätverket skydda Microsoft Azure SQL Database från obehöriga insider åtkomst. Dessutom kan endast användare från IP-adressintervall från Microsoft CorpNet kan komma åt hanteringsslutpunkten WinFabric-plattformen.

## <a name="firewallacls-between-nodes-in-an-azure-sql-db-cluster"></a>Brandvägg/ACL: er mellan noder i ett kluster i Azure SQL DB
Som ett ytterligare skydd som en del av defense i djup-strategin, har ACL: er/brandväggen implementerats mellan noder i ett kluster med Microsoft Azure SQL DB. All kommunikation i WinFabric plattform klustret samt alla köra kod är betrodd.

## <a name="custom-mas-watchdogs"></a>Anpassade MAs (Watchdogs)
Microsoft Azure SQL Database använder anpassade MAs kallas watchdogs för att övervaka hälsotillståndet för Microsoft Azure SQL DB-klustret.

## <a name="web-protocols"></a>Webbprotokoll

### <a name="role-instance-monitoring-and-restart"></a>Rollen instans övervakning och omstart
Azure ser till att alla pågående roller (Internet-riktade web eller backend-bearbetning worker-roller) distribueras omfattas varaktigt hälsotillstånd övervakning för att säkerställa att de är effektivt och effektivt leverera de tjänster som de har etablerats. I den händelse att en roll blir ohälsosamt, av ett kritiskt fel i programmet som värdbaserad eller underliggande problem inom rollinstansen själva, Microsoft Azure FC identifierar problemet i rollinstansen och initiera en korrigerande tillstånd .

### <a name="compute-connectivity"></a>Databearbetningsanslutning
Azure ser till att program/tjänst som distribueras kan nås via webbaserade standardprotokoll. Internet-ansluten virtuell webbrollinstanserna har externa Internet-anslutning och kan nås direkt av webbanvändare. Backend-bearbetning virtuella arbetsrollinstanser extern Internet-anslutning, men kan inte nås direkt från en extern webbplats-användare för att skydda känsliga och integritet hos de åtgärder arbetsroller utföra för den offentligt tillgänglig webbserver-rollen virtuella instanser.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure anläggningar, plats och fysisk säkerhet](azure-physical-security.md)
- [Tillgänglighet för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Microsoft Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktionsåtgärder och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Skydd av kunddata i Azure](azure-protection-of-customer-data.md)
