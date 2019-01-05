---
title: Uppgradera till det senaste klientbiblioteket för elastiska databaser | Microsoft Docs
description: Använd Nuget för att uppgradera elastic database-klientbibliotek.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 63671b38c952f5297be88f7b50ad9d6f9341fc72
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034428"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Uppgradera en app för att använda det senaste klientbiblioteket för elastiska databaser

Nya versioner av den [Elastic Database-klientbiblioteket](sql-database-elastic-database-client-library.md) är tillgängliga via NuGetand NuGetPackage Manager-gränssnittet i Visual Studio. Uppgraderingar innehåller felkorrigeringar och stöd för nya funktioner för klientbiblioteket.

**För den senaste versionen:** Gå till [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Återskapa ditt program med nya-biblioteket, samt ändra din befintliga Karthanteraren för metadata som lagras i din Azure SQL-databaser som stöd för nya funktioner.

Utför de här stegen i ordning säkerställer att äldre versioner av klientbiblioteket finns inte längre i din miljö när metadataobjekt uppdateras, vilket innebär att gamla version metadataobjekt inte skapas efter uppgraderingen.

## <a name="upgrade-steps"></a>Uppgraderingssteg

**1. Uppgradera dina program.** I Visual Studio, ladda ned och referera till den senaste versionen av klienten biblioteket i alla dina utvecklingsprojekt som använder biblioteket. sedan återskapa och distribuera.

* I Visual Studio-lösningen, Välj **verktyg** --> **NuGet-Pakethanteraren** -->  **hantera NuGet-paket för lösningen**.
* (Visual Studio 2013) I den vänstra panelen, väljer **uppdateringar**, och välj sedan den **uppdatering** knappen paketet **Azure SQL Database-klientbiblioteket med elastisk skala** som visas i fönstret.
* (Visual Studio 2015) Inställd filterfältet **uppgradera tillgängliga**. Välj paketet du uppdaterar och klicka på den **uppdatera** knappen.
* (Visual Studio 2017) Längst ned i dialogrutan Välj **uppdateringar**. Välj paketet du uppdaterar och klicka på den **uppdatera** knappen.
* Skapa och distribuera.

**2. Uppgradera dina skript.** Om du använder **PowerShell** skript för att hantera shards, [ladda ned den nya biblioteksversionen](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) och kopiera den till den katalog där du kör skript.

**3. Uppgradera din dela / sammanslå-tjänst.** Om du använder verktyget Dela och slå samman för elastic database att ordna om shardade data [ladda ned och distribuera den senaste versionen av verktyget](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Detaljerad Uppgraderingsstegen för tjänsten finns [här](sql-database-elastic-scale-overview-split-and-merge.md).

**4. Uppgradera dina Karthanteraren databaser**. Uppgradera de metadata som stöder dina Shard-kartor i Azure SQL Database.  Det finns två sätt som du kan göra detta, med hjälp av PowerShell eller C#. Båda alternativen visas nedan.

***Alternativ 1: Uppgradera metadata med hjälp av PowerShell***

1. Ladda ned det senaste kommandoradsverktyget för NuGet från [här](http://nuget.org/nuget.exe) och spara i en mapp.
2. Öppna en kommandotolk, navigerar du till samma mapp och utfärda kommandot: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Gå till undermappen som innehåller den nya klient DLL versionen som du har precis har laddat ned, till exempel: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Ladda ned elastisk databas klienten uppgraderingsskriptet från den [Script Center](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9), och spara den i samma mapp som innehåller DLL-filen.
5. Kör ”PowerShell.\upgrade.ps1” från Kommandotolken från denna mapp och följ anvisningarna.

***Alternativ 2: Uppgradera med metadataC#***

Du kan också skapa ett Visual Studio-program som öppnar din ShardMapManager itererar över alla shards och utför metadata uppgraderingen genom att anropa metoderna [UpgradeLocalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) och [UpgradeGlobalStore ](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) som i följande exempel:

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }

Dessa metoder för metadata uppgraderingar kan användas flera gånger utan att skada. Till exempel om en äldre klientversion skapar oavsiktligt ett fragment när du har uppdaterat, kan du köra uppgraderingen igen över alla shards så att den senaste versionen av metadata finns i hela infrastrukturen.

**Obs!**  Nya versioner av klientbiblioteket publicerade hittills fortsätta att arbeta med tidigare versioner av metadata för Karthanteraren för Azure SQL DB och vice versa.   Men för att dra nytta av några av de nya funktionerna i den senaste klienten, metadata måste uppgraderas.   Observera att metadata uppgraderingar inte påverkar användardata och programspecifik data, endast objekt som skapas och används av Fragmentkartehanteraren.  Och programmen fortsätter att fungera via uppgraderingsordningen som beskrivs ovan.

## <a name="elastic-database-client-version-history"></a>Versionshistorik för elastisk databas-klienten

För tidigare versioner, går du till [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png