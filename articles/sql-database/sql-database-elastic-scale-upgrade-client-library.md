---
title: Uppgradera till det senaste klientbiblioteket för elastisk databas
description: Använd Nuget för att uppgradera elastiskt databasklientbibliotek.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: a1ab684f13c56698d4359f2bf74826f3dd696c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823510"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Uppgradera en app för att använda det senaste klientbiblioteket för elastisk databas

Nya versioner av [klientbiblioteket för elastisk databas](sql-database-elastic-database-client-library.md) är tillgängliga via NuGet och NuGet Package Manager-gränssnittet i Visual Studio. Uppgraderingar innehåller buggfixar och stöd för nya funktioner i klientbiblioteket.

**För den senaste versionen:** Gå till [Microsoft.Azure.SqlDatabase.elasticscale.client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Återskapa ditt program med det nya biblioteket och ändra dina befintliga Shard Map Manager-metadata som lagras i dina Azure SQL-databaser för att stödja nya funktioner.

Om du utför dessa steg för säkerställer du att gamla versioner av klientbiblioteket inte längre finns i din miljö när metadataobjekt uppdateras, vilket innebär att metadataobjekt i gamla versioner inte skapas efter uppgraderingen.

## <a name="upgrade-steps"></a>Uppgradera steg

**1. Uppgradera dina program.** I Visual Studio kan du hämta och referera till den senaste klientbiblioteksversionen till alla utvecklingsprojekt som använder biblioteket. sedan bygga om och distribuera.

* I din Visual Studio-lösning väljer du **Tools** --> **NuGet Package Manager** -->  **Manage NuGet Packages for Solution**.
* (Visual Studio 2013) På den vänstra panelen väljer du **Uppdateringar**och väljer sedan knappen **Uppdatera** på paketet **Azure SQL Database Elastic Scale Client Library** som visas i fönstret.
* (Visual Studio 2015) Ställ in filterrutan på **Uppgradera tillgänglig**. Markera det paket som ska uppdateras och klicka på knappen **Uppdatera.**
* (Visual Studio 2017) Högst upp i dialogrutan väljer du **Uppdateringar**. Markera det paket som ska uppdateras och klicka på knappen **Uppdatera.**
* Skapa och distribuera.

**2. Uppgradera skript.** Om du använder **PowerShell-skript** för att hantera shards [hämtar du den nya biblioteksversionen](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) och kopierar den till den katalog som du kör skript från.

**3. Uppgradera din tjänst för delad koppling.** Om du använder verktyget för delad sammanfogning av elastisk databas för att ordna om fragmenterade data [hämtar och distribuerar du den senaste versionen av verktyget](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Detaljerade uppgraderingssteg för tjänsten hittar du [här](sql-database-elastic-scale-overview-split-and-merge.md).

**4. Uppgradera dina Shard Map Manager-databaser**. Uppgradera metadata som stöder dina Shard-kartor i Azure SQL Database.  Det finns två sätt att åstadkomma detta, med PowerShell eller C#. Båda alternativen visas nedan.

***Alternativ 1: Uppgradera metadata med PowerShell***

1. Ladda ner det senaste kommandoradsverktyget för NuGet [härifrån](https://nuget.org/nuget.exe) och spara till en mapp.
2. Öppna en kommandotolk, navigera till samma mapp och utfärda kommandot:`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Navigera till undermappen som innehåller den nya klient-DLL-versionen som du just har hämtat, till exempel:`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Hämta uppgraderingsskriptet för elastisk databasklient från [Skriptcenter](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)och spara det i samma mapp som innehåller DLL.Download the elastic database client upgrade script from the Script Center , and save it into the same folder containing the DLL.
5. Från den mappen kör du "PowerShell .\upgrade.ps1" från kommandotolken och följer anvisningarna.

***Alternativ 2: Uppgradera metadata med C #***

Du kan också skapa ett Visual Studio-program som öppnar ShardMapManager, itererar över alla shards och utför metadatauppgraderingen genom att anropa metoderna [UpgradeLocalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) och [UpgradeGlobalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) som i det här exemplet:

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }

Dessa tekniker för metadatauppgraderingar kan tillämpas flera gånger utan att skada. Om till exempel en äldre klientversion av misstag skapar en fragment när du redan har uppdaterat kan du köra uppgraderingen igen över alla shards för att säkerställa att den senaste metadataversionen finns i hela infrastrukturen.

**Anm.:**  Nya versioner av klientbiblioteket som publicerats hittills fortsätter att fungera med tidigare versioner av Shard Map Manager-metadata på Azure SQL DB och vice versa.   Men för att dra nytta av några av de nya funktionerna i den senaste klienten måste metadata uppgraderas.   Observera att metadatauppgraderingar inte påverkar användardata eller programspecifika data, endast objekt som skapas och används av Shard Map Manager.  Och program fortsätter att fungera genom uppgraderingssekvensen som beskrivs ovan.

## <a name="elastic-database-client-version-history"></a>Versionshistorik för elastisk databasklient

Versionshistorik finns i [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
