---
title: Kända problem/migreringsbegränsningar med användning av hybridläge
description: Lär dig mer om kända problem/migreringsbegränsningar med hjälp av Azure Database Migration Service i hybridläge.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: aedc7ea3d778d52f6f348837430987568af188ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649610"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>Kända problem/migreringsbegränsningar med användning av hybridläge

Kända problem och begränsningar som är associerade med att använda Azure Database Migration Service i hybridläge beskrivs i följande avsnitt.

## <a name="installer-fails-to-authenticate"></a>Installationsprogrammet kan inte autentisera

När du har laddat upp certifikatet till din AdApp är det en fördröjning på upp till ett par minuter innan det kan autentisera med Azure. Installationsprogrammet försöker försöka igen med viss fördröjning, men det är möjligt att spridningsfördröjningen är längre än återförsöket och ett **Message ofattas** på nytt. Om certifikatet har överförts till rätt AdApp och rätt AppId angavs i dmsSettings.json provar du att köra installationskommandot igen.

## <a name="service-offline-after-successful-installation"></a>Tjänsten "offline" efter lyckad installation

Om tjänsten visas som offline när installationen har slutförts kan du prova att använda följande steg.

1. I Azure-portalen, i din instans av Azure Database Migration Service, navigera till fliken **Hybridinställningar** och kontrollera sedan att arbetaren är registrerad genom att kontrollera rutnätet för registrerade arbetare.

    Status för den här arbetaren ska vara **online**, men det kan visas som **offline** om det finns ett problem.

2. Kontrollera tjänstens status på arbetsdatorn genom att köra följande PowerShell-kommando:

    ```
    Get-Service Scenario*
    ```

    Det här kommandot ger dig status för Windows-tjänsten som kör arbetaren. Det bör bara finnas ett enda resultat. Om arbetaren stoppas kan du försöka starta om den med hjälp av följande PowerShell-kommando:

    ```
    Start-Service Scenario*
    ```

    Du kan också kontrollera tjänsten i windows services-användargränssnittet.

3. Om Windows-tjänsten växlar mellan Löpning och Stoppad, stötte arbetaren på problem med att starta. Kontrollera hybridarbetstagarloggarna för Azure Database Migration Service för att ta reda på problemet.

    - Installationsprocessloggar lagras i mappen "loggar" i mappen som installationsprogrammet kördes från.
    - Hybridarbetstjänstloggar för Azure Database Migration Service lagras i mappen **WorkerLogs** i mappen där arbetaren är installerad. Standardplatsen för hybridarbetsarloggfilerna är **C:\Program\DatabaseMigrationServiceHybrid\WorkerLogs**.

## <a name="using-your-own-signed-certificate"></a>Använda ditt eget signerade certifikat

Certifikatet som genereras av åtgärden GenerateCert är ett självsignerat certifikat som kanske inte är godtagbart baserat på dina interna säkerhetsprinciper. I stället för att använda det här certifikatet kan du ange ett eget certifikat och ange tumavtrycket i dmsSettings.json. Det här certifikatet måste överföras till din AdApp och installeras på den dator där du installerar hybridarbetaren för Azure Database Migration Service. Installera sedan det här certifikatet med den privata nyckeln i certifikatarkivet för den lokala datorn.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>Köra arbetartjänsten som ett konto med låg behörighet

Som standard körs hybridarbetstjänsten för Azure Database Migration Service som kontot lokalt system. Du kan ändra kontot som används för den här tjänsten så länge kontot du använder har nätverksbehörigheter. Om du vill ändra kontot "kör som" använder du följande process.

1. Stoppa tjänsten, antingen via Windows Services eller med kommandot Stop-Service i PowerShell.

2. Uppdatera tjänsten för att använda ett annat inloggningskonto.

3. I certmgr för certifikat för lokal dator ger du privata nyckelbehörigheter till det nya kontot för **DMS Hybrid-appnyckeln** och **DMS-scenariomotornyckelparcertifikaten.**

    a. Öppna certmgr för att visa följande tangenter:

    - Dms-hybridappnyckel
    - Installationsnyckel för DMS Hybrid Worker
    - Nyckelpar för DMS-scenariomotor

    b. Högerklicka på posten **DMS Hybrid-appnyckel,** peka på **Alla uppgifter**och välj sedan **Hantera privata nycklar**.

    c. Välj **Lägg till**på fliken **Säkerhet** och ange sedan namnet på kontot.

    d. Följ samma steg för att bevilja privat nyckelbehörighet för det nya kontot till **DMS-scenariomotornyckelparet.**

## <a name="unregistering-the-worker-manually"></a>Avregistrera arbetaren manuellt

Om du inte längre har åtkomst till arbetardatorn kan du avregistrera arbetaren och återanvända din Azure Database Migration Service-instans genom att utföra följande steg:

1. I Azure-portalen kom du till din Azure Database Migration Service-instans och navigerar sedan till sidan **Hybridinställningar.**

   Arbetarposten visas i listan, med statusen **offline**.

2. Längst till höger om arbetarpostlistan väljer du ellipserna och väljer sedan **Avregistrera**.

## <a name="addressing-issues-for-specific-migration-scenarios"></a>Åtgärda problem för specifika migreringsscenarier

I avsnitten nedan beskrivs scenariospecifika problem relaterade till att använda hybridläget för Azure Database Migration Service för att utföra en onlinemigrering.

### <a name="online-migrations-to-azure-sql-database-managed-instance"></a>Onlinemigreringar till hanterad azure SQL-databas-instans

**Hög processoranvändning**

**Problem:** För onlinemigreringar till SQL Database-hanterad instans stöter datorn som kör hybridarbetaren på hög CPU-användning om det finns för många säkerhetskopior eller om säkerhetskopiorna är för stora.

**Begränsning:** För att minska problemet använder du komprimerade säkerhetskopior, delar upp migreringen så att den använder flera resurser eller skala upp datorn som kör hybridarbetaren.
