---
title: "Uppgradera till det senaste klientbiblioteket för elastisk databas | Microsoft Docs"
description: "Använd Nuget för att uppgradera elastisk databas klientbiblioteket."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
ms.assetid: 0a546510-76e7-465e-9271-f15ff0cfa959
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: ddove
ms.openlocfilehash: 62609f2488a01d9b406c6b730c53ecc9f9b6ea1b
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Uppgradera en app om du vill använda det senaste klientbiblioteket för elastisk databas
Nya versioner av den [klientbibliotek för elastisk databas](sql-database-elastic-database-client-library.md) är tillgängliga via NuGetand NuGetPackage Manager-gränssnittet i Visual Studio. Uppgraderingar innehåller felkorrigeringar och stöd för nya funktioner av klientbiblioteket.

**För den senaste versionen:** går du till [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Återskapar ditt program med det nya biblioteket samt ändra din befintliga Fragmentera kartan Manager metadata som lagras i Azure SQL-databaser till nya funktioner.

Utför stegen i ordning säkerställer att äldre versioner av klientbiblioteket inte längre finns i din miljö när metadataobjekt uppdateras, vilket innebär att gamla version metadataobjekt inte skapas efter uppgraderingen.   

## <a name="upgrade-steps"></a>Uppgradering
**1. Uppgradera dina program.** I Visual Studio, hämta och referera till den senaste versionen av klienten biblioteket i alla dina utvecklingsprojekt som använder biblioteket. sedan återskapa och distribuera. 

* Välj i Visual Studio-lösning **verktyg** --> **NuGet Package Manager** -->  **hantera NuGet-paket för lösningen**. 
* (Visual Studio 2013) I den vänstra rutan, Välj **uppdateringar**, och välj sedan den **uppdatering** knappen paketet **Azure SQL Database-klientbiblioteket med elastisk skala** som visas i fönstret.
* (Visual Studio 2015) Ange filterfältet till **uppgradera tillgängliga**. Välj paketet du uppdaterar och klicka på den **uppdatera** knappen.
* (Visual Studio 2017) Överst i dialogrutan Välj **uppdateringar**. Välj paketet du uppdaterar och klicka på den **uppdatera** knappen.
* Skapa och distribuera. 

**2. Uppgradera ditt skript.** Om du använder **PowerShell** skript för att hantera shards, [hämta den nya biblioteksversionen](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) och kopiera den till katalogen där du kan köra skript. 

**3. Uppgradera din delade kopplingstjänsten.** Om du använder verktyget elastisk databas delade dokument att ordna om shardade data [hämta och distribuera den senaste versionen av verktyget](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Detaljerad Uppgraderingsstegen för tjänsten finns [här](sql-database-elastic-scale-overview-split-and-merge.md). 

**4. Uppgradera din Fragmentera kartan Manager-databaser**. Uppgradera metadata stöder Fragmentera-Maps i Azure SQL Database.  Det finns två sätt som du kan göra detta, med PowerShell eller C#. Båda alternativen visas nedan.

***Alternativ 1: Uppgradera metadata med hjälp av PowerShell***

1. Hämta senaste kommandoradsverktyget för NuGet från [här](http://nuget.org/nuget.exe) och spara till en mapp. 
2. Öppna en kommandotolk och navigera till mappen samma utfärda kommandot:`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Navigera till undermappen som innehåller den nya DLL klientversionen du har precis har laddat ned, till exempel:`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Hämta elastisk databas klienten uppgradera skriptlet från den [Script Center](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9), och spara den i samma mapp som innehåller DLL-filen.
5. Kör ”PowerShell.\upgrade.ps1” från Kommandotolken från mappen, och följ anvisningarna.

***Alternativ 2: Uppgradera metadata med hjälp av C#***

Du kan också skapa ett Visual Studio-program som öppnar din ShardMapManager itererar över alla delar och utför uppgraderingen metadata genom att anropa metoderna [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) och [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) som i följande exempel: 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

Dessa tekniker för metadata uppgraderingar kan användas flera gånger utan att skada. Till exempel om en äldre klientversion skapar en Fragmentera oavsiktligt när du har uppdaterat, kan du köra uppgraderingen igen över alla delar så att den senaste versionen av metadata finns i hela infrastrukturen. 

**Obs:** nya versioner av klientbiblioteket publicerade hittills fortsätter att arbeta med tidigare versioner av Fragmentera kartan Manager metadata på Azure SQL DB och vice versa.   Men för att dra nytta av några av de nya funktionerna i den senaste klienten, metadata måste uppgraderas.   Observera att metadata uppgraderingar inte påverkar användardata och programspecifika data endast objekt som skapas och används av hanteraren för kartan Fragmentera.  Och programmen ska fungera via uppgraderingsordningen som beskrivs ovan. 

## <a name="elastic-database-client-version-history"></a>Versionshistorik för klienten för elastisk databas
För tidigare versioner, gå till [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png

