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
ms.openlocfilehash: 65fe541f61389a2e52033cdaedcfcec4944faf35
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171467"
---
# <a name="azure-infrastructure-integrity"></a>Azure-infrastrukturen integritet

## <a name="software-installation"></a>Programvaruinstallation
Alla komponenter i stacken programvara som är installerade i Azure-miljön är egna anpassade efter hur Microsoft Security Development Lifecycle (SDL). Alla programvarukomponenter, inklusive avbildningar av operativsystem (OS) och SQL-databas och distribueras som en del av ändringshantering och släpp hanteringen. Operativsystemet som körs på alla noder är en anpassad version av Windows Server 2008 eller Windows Server 2012. Den exakta versionen väljs av fabric controller (FC) enligt den roll som den har för avsikt för operativsystem och spela upp. Dessutom tillåter inte värdoperativsystemet installation av någon obehörig programvarukomponenter.

Vissa Azure-komponenter distribueras som Azure-kunder med en gäst VM som körs på ett gästoperativsystem.

## <a name="virus-scans-on-builds"></a>Virusgenomsökningar på versioner
Azure-program (inklusive OS)-komponenten versioner måste genomgå en virusgenomsökning som använder ett virusskyddsprogram Endpoint Protection-verktyget. Varje viruskontroll skapar en logg i den associera build-katalogen med information om vad söktes igenom och resultatet av genomsökningen. Genomsökningen virus är en del av källkoden build för varje komponent i Azure. Kod har inte flyttats till produktion utan en ren och lyckade virus skanna. Om eventuella problem anges versionen är låst och går sedan till säkerhet-teamen på Microsoft Security för att identifiera där ”falska”-kod angetts bygget.

## <a name="closed-and-locked-environment"></a>Stängda och låsta miljö
Som standard har inte användarkonton som skapats på dem i Azure-infrastrukturnoder och virtuella gästdatorer. Dessutom har standard Windows-administratörskonton också inaktiverats. Administratörer från live support för Azure kan med lämplig autentisering, logga in på dessa datorer och administrera Azure-produktionsnätverket för reparation.

## <a name="azure-sql-database-authentication"></a>Azure SQL Database-autentisering
Precis som med någon implementering av SQL Server, måste hantering av användarkonton vara hårt styrda. Azure SQL Database stöder SQL Server-autentisering. Om du vill komplettera säkerhetsmodell för en kunds data användaren konton med starka lösenord och konfigurerad med specifika rättigheter bör också användas.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>ACL: er och brandväggar mellan Microsoft företagsnätverket och ett Azure-kluster
Åtkomstkontrollistor (ACL) och brandväggar mellan service-plattformen och Microsoft företagsnätverket skydda SQL Database-instanser från obehöriga insider åtkomst. Dessutom kan endast användare från IP-adressintervall från företagsnätverket Microsoft kan komma åt Windows Fabric-plattformen-hanteringsslutpunkten.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>ACL: er och brandväggar mellan noder i ett kluster för SQL-databas
Som ett ytterligare skydd som en del av defense i djup-strategin, har ACL: er och en brandvägg implementerats mellan noder i ett kluster för SQL-databas. All kommunikation i Windows Fabric-plattformen-kluster samt alla köra kod är betrodd.

## <a name="custom-monitoring-agents"></a>Anpassad övervakning av agenter
SQL Database använder anpassade övervakningsagenter (MAs), även kallat watchdogs, att övervaka hälsotillståndet för SQL Database-klustret.

## <a name="web-protocols"></a>Webbprotokoll

### <a name="role-instance-monitoring-and-restart"></a>Rollen instans övervakning och omstart
Azure ser till att alla har distribuerats, roller (internet-riktade web eller backend-bearbetning worker-roller) omfattas av varaktigt hälsoövervakning för att säkerställa att de effektivt leverera de tjänster som de har etablerats. Om en roll blir ohälsosamt av ett kritiskt fel i programmet som finns eller en underliggande konfigurationsproblem i rollinstansen själva, i FC identifierar problemet i rollinstansen och initierar ett korrigerande tillstånd.

### <a name="compute-connectivity"></a>Databearbetningsanslutning
Azure ser till att det distribuerade programmet eller tjänsten kan nås via webbaserade standardprotokoll. Virtuella instanser av internet-riktade web-roller har externa internet-anslutning och kan nås direkt av webbanvändare. För att skydda känsliga och integritet hos de åtgärder arbetsroller utföra åt offentligt tillgängliga virtuella webbrollinstanserna virtuella instanser av backend-bearbetning worker-roller har extern anslutning för internet men får inte vara komma åt direkt via externa webbanvändare.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure anläggningar, plats och fysisk säkerhet](azure-physical-security.md)
- [Tillgänglighet för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktionsåtgärder och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Dataskydd för Azure-kund](azure-protection-of-customer-data.md)
