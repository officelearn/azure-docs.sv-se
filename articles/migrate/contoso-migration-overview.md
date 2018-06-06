---
title: Översikt över Contoso migrering till Azure | Microsoft Docs
description: En översikt över en migreringsstrategi för samt scenarier som används av Contoso för att migrera sina lokala datacenter till Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/05/2018
ms.author: raynew
ms.openlocfilehash: 6dd9e8a3bdd497b6bdc4a0a096bfddbd29d25069
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34805020"
---
# <a name="contoso-migration-overview"></a>Contoso-migrering: översikt


Det här är den första artikeln i en serie som visar hur fiktiva organisationen Contoso migrerar sin lokala infrastruktur till den [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) moln. 

Artikelserien ger exempel exempel som hjälper dig att förstå och prova att använda olika alternativ för migrering till Azure och för att arbeta i en hybridmiljö. Artiklar visar hur företaget Contoso är klar sitt uppdrag för migreringen, men pekare för allmän läsning och specifika anvisningar finns i.

## <a name="introduction"></a>Introduktion

Azure tillhandahåller åtkomst till en omfattande uppsättning molntjänster. Du kan använda dessa tjänster som utvecklare och IT-proffs, för att skapa, distribuera och hantera program på ett intervall av verktyg och ramverk, via ett globalt nätverk av datacenter. När ditt företag möter utmaningarna med digitaliseringen hjälper Azure-molnet dig att optimera resurser och åtgärder, interagera med dina kunder och anställda och transformera dina produkter.

Men även om det finns stora fördelar med att migrera till Azure och molnet i form av hastighet och flexibilitet, minskade kostnader, prestanda och tillförlitlighet, kan det för många organisationer ibland vara nödvändigt att köra lokala datacenter ibland. För att lösa problem med barriärer vid molnimplementering tillhandahåller Azure en hybridmolnstrategi, som bygger broar mellan dina lokala datacenter och det offentliga Azure-molnet. Man kan t.ex. använda molnresurser i Azure som säkerhetskopiering för att skydda lokala resurser, eller använda Azure Analytics för att få mer kunskap om lokala arbetsbelastningar. 

Som en del av strategin för hybrid cloud tillhandahåller Azure växande lösningar för att migrera lokala appar och arbetsbelastningar till molnet. Med de här enkla stegen kan du noggrant bedöma dina lokala resurser för att ta reda på hur de skulle fungera i Azure-molnet. Sedan kan du på ett säkert sätt migrera dina resurser till Azure. När resurserna är igång och körs i Azure, kan du optimera dem för att behålla och förbättra åtkomsten, flexibiliteten, säkerheten och tillförlitligheten.

## <a name="migration-strategies"></a>Migreringsstrategier

Strategier för migrering till molnet är indelade i fyra kategorier: rehost, flytta, rearchitect eller återskapa. Strategin du anta beror på din affärsdrivande faktorer och mål för migrering. Du kan använda flera strategier. Du kan till exempel välja att rehost (lift och SKIFT) enkla appar eller appar som inte är nödvändiga för ditt företag, men rearchitect som är mer komplexa och verksamhetskritiska. Nu ska vi titta på strategierna.


**Strategi** | **Definition** | **När du ska använda** 
--- | --- | --- 
**Rehost** | Kallas ofta för en ”lift och SKIFT”-migrering. Det här alternativet kräver inte kodändringar och vi du migrera dina befintliga appar till Azure snabbt. Varje app migreras som är att dra nytta av molnet, utan risk och kostnaden som är associerad med kodändringar. | När du behöver flytta appar snabbt till molnet.<br/><br/> När du vill flytta en app utan att ändra den.<br/><br/> När dina appar är konstruerad så att de kan utnyttja [Azure IaaS](https://azure.microsoft.com/overview/what-is-iaas/) skalbarhet efter migreringen.<br/><br/> När appar är viktiga för ditt företag, men du behöver inte direkta ändringar av funktionerna i appen.
**Flytta** | Kallas ofta ”paketera”, eftersom kräver minimal ändringar av appar, så att de kan ansluta till [Azure PaaS](https://azure.microsoft.com/overview/what-is-paas/), och använda molntjänster.<br/><br/> Du kan till exempel migrera befintliga appar till Azure App Service eller Azure Kubernetes Service (AKS). Eller så kan du refactor relationella och icke-relationella databaser i alternativ, till exempel hanteras Azure SQL Database-instans, Azure-databas för MySQL, Azure-databas för PostgreSQL och Azure Cosmos DB. | Om din app kan enkelt packas för att fungera i Azure.<br/><br/> Eftersom kan också användas om du vill använda innovativa DevOps-metoder som tillhandahålls av Azure, eller om du funderar DevOps med hjälp av en strategi för behållaren för arbetsbelastningar.<br/><br/> För eftersom, måste du tänka på mobiliteten i dina befintliga kodbas och tillgängliga utvecklarkunskaper.
**Rearchitect** | Omstrukturera för migrering är om att ändra och utöka funktionalitet och kodbas att optimera app-arkitekturen för skalbarhet i molnet.<br/><br/> Exempelvis kan du dela upp ett monolitisk program till en grupp med mikrotjänster som arbetar tillsammans och skala lätt att söka efter. Eller, du kan rearchitect relationella och icke-relationella databaser till fullständigt hanterade DBaaS lösningar, till exempel hanteras Azure SQL Database-instans, Azure-databas för MySQL, Azure-databas för PostgreSQL och Azure Cosmos DB. | När dina appar behöver större ändringar att inkludera nya funktioner eller ska fungera effektivt på en plattform i molnet.<br/><br/> Rearchitecture är ett alternativ när du vill kunna utnyttja befintliga investeringar i program, skalbarhet kraven på ett kostnadseffektivt sätt, tillämpa innovativa DevOps-metoder som tillhandahålls av Azure och minska användningen av virtuella datorer.
**Återskapa** | Återskapa tar saker ett steg ytterligare genom att återskapa en app med Azure-molntekniker. Du kan till exempel skapar grön fältet program med moln-intern tekniker som serverlösa, Azure AI hanteras Azure SQL Database-instans och Azure Cosmos DB. | När du vill använda snabb utveckling och befintliga appar har begränsad funktionalitet och livslängd. Rearchitect när du är redo att påskynda business innovation (inklusive DevOps-metoder som tillhandahålls av Azure), skapa nya program som använder molnet intern tekniker och dra nytta av förbättringarna av AI, blockchain och IoT.

## <a name="migration-articles"></a>Artiklar om datamigrering

Artiklar som utgör Contoso migrering serien sammanfattas i tabellen nedan.  

- Scenarierna är öka komplexiteten och vi lägger till dem över tid.
- Varje Migreringsscenario drivs av lite olika verksamhetsmål som fastställer migreringsstrategi.
- Vi ger information om affärsdrivande faktorer och mål, föreslagna arkitektur, åtgärder för att utföra migreringen och rekommendation för rensning och nästa steg när migreringen är klar för varje scenario för distribution.

**Artikel** | **Detaljer** | **Status**
--- | --- | ---
Artikel 1: Översikt över (den här artikeln) | En översikt över Contosos migreringsstrategi, artikelserien och exempelappar som används. | Tillgängligt
Artikel 2: Distribuera en Azure-infrastruktur | Beskriver hur Contoso förbereder dess lokalt och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla Contoso-Migreringsscenarier. | Tillgängligt
Artikel 3: Utvärdera lokala resurser | Visar hur Contoso körs en bedömning av sina lokala två nivåer SmartHotel app som körs på VMware. De utvärdera app virtuella datorer med den [Azure migrera](migrate-overview.md) tjänsten och SQL Server-databasen app med den [Azure databasen Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Tillgängligt
Artikel 4: Flytta (lift och SKIFT) till virtuella Azure-datorer och en hanterad SQL-instans | Visar hur Contoso migrerar SmartHotel appen till Azure. Migrerar appen klientdel VM med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), och app databasen med hjälp av [Azure Databasmigrering](https://docs.microsoft.com/azure/dms/dms-overview) tjänsten för att migrera till en hanterad SQL-instans. | Tillgängligt
Artikel 5: Flytta (lift och SKIFT) till virtuella Azure-datorer | Visar hur Contoso migrera SmartHotel appen VMs endast med Site Recovery.
Artikel 6: Flytta (lift och SKIFT) till virtuella datorer i Azure och SQL Server-Tillgänglighetsgrupper | Visar hur Contoso migrerar SmartHotel appen. De kan använda Site Recovery för att migrera appen virtuella datorer och tjänsten Databasmigrering för att migrera app-databas till en SQL Server-tillgänglighetsgrupp. | Tillgängligt
Artikel 7: Flytta (lift och SKIFT) till virtuella Azure-datorer och Azure MySQL-Server | Visar hur Contoso migrerar SmartHotel app virtuella datorer med hjälp av Site Recovery och MySQL-arbetsstationen för att migrera (säkerhetskopiering och återställning) till en Azure MySQL Server-instans. | Tillgängligt



### <a name="demo-apps"></a>Demoappar

Artiklarna använder två demoappar - SmartHotel och osTicket.

- SmartHotel360: Den här appen har utvecklats av Microsoft som en test-app som du kan använda när du arbetar med Azure. Den är öppen källkod och du kan ladda ned det från [GitHub](https://github.com/Microsoft/SmartHotel360). Det är en ASP.NET-app som är ansluten till en SQL Server-databas. Appen är för närvarande på två virtuella VMware-datorer kör Windows Server 2008 R2 och SQL Server 2008 R2. Appen virtuella datorer är lokalt och hanteras av vCenter-servern.
- osTicket är en öppen källkod helpdesk biljetter app som körs på Linux. Du kan ladda ned det från [GitHub](https://github.com/osTicket/osTicket). Aktuella appen är på två virtuella VMware-datorer kör Ubuntu 16.04LTS, som använder Apache 2, PHP 7.0 och MySQL 5.7
    

## <a name="next-steps"></a>Nästa steg

[Lär dig hur](contoso-migration-infrastructure.md) Contoso ställer in sina lokala och Azure-infrastrukturen under förberedelserna för migreringen.



