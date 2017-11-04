---
title: Importera och exportera data i Azure Redis-Cache | Microsoft Docs
description: "Lär dig hur du importera och exportera data till och från blob storage med dina instanser för premium Azure Redis-Cache"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 4a68ac38-87af-4075-adab-569d37d7cc9e
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: sdanie
ms.openlocfilehash: 5e6d731f0a1cecc1a191c74a45e37a9b94fd98ee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="import-and-export-data-in-azure-redis-cache"></a>Importera och exportera data i Azure Redis-Cache
Importera och exportera är en Azure Redis-Cache data management-åtgärd, där du kan importera data till Azure Redis-Cache eller exportera data från Azure Redis-Cache genom att importera och exportera en ögonblicksbild för Redis-Cache databasen (RDB) från cache premium till en blobb i Azure Storage-konto. 

- **Exportera** -du kan exportera din Azure Redis-Cache RDB ögonblicksbilder till en Sidblobb.
- **Importera** -du kan importera Redis-Cache RDB-ögonblicksbilder från en Sidblob eller en Blockblob.

Import/Export kan du migrera mellan olika instanser av Azure Redis-Cache eller fylla i cachen med data innan de används.

Den här artikeln innehåller en guide för import och export av data med Azure Redis-Cache och ger svar på vanliga frågor.

> [!IMPORTANT]
> Import/Export är i förhandsvisning och finns bara för [premiumnivån](cache-premium-tier-intro.md) cachelagrar.
>
>

## <a name="import"></a>Importera
Import kan användas för att Redis kompatibel RDB filer från en Redis-server som kör i molnet eller miljö, inklusive Redis som körs på Linux, Windows eller valfri provider som molntjänster, till exempel Amazon Web Services och andra. Importera data är ett enkelt sätt att skapa ett cacheminne med data i förväg. Under importen, Azure Redis-Cache läser in RDB-filer från Azure storage i minnet och infogar nycklarna i cacheminnet.

> [!NOTE]
> Kontrollera att din Redis-databasen (RDB) eller filer överförs till sidan eller blockera blobbar i Azure storage, i samma region och prenumerationen som Azure Redis-Cache-instansen innan du börjar importen. Mer information finns i [komma igång med Azure Blob storage](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Om du har exporterat din RDB filen med den [Azure Redis-Cache exportera](#export) funktionen RDB filen lagras redan i en sidblob och är redo för att importera.
>
>

1. Så här importerar du en eller flera exporterade cacheminnet BLOB [Bläddra till ditt cacheminne](cache-configure.md#configure-redis-cache-settings) i Azure-portalen och klicka på **importera data** från den **resurs menyn**.

    ![Importera data][cache-import-data]
2. Klicka på **Välj Blob(s)** och välj lagringskontot som innehåller data som ska importeras.

    ![Välj lagringskonto][cache-import-choose-storage-account]
3. Klicka på den behållare som innehåller data som ska importeras.

    ![Välj behållare][cache-import-choose-container]
4. Välj en eller flera blobbar att importera genom att klicka på området till vänster om blob-namnet och klicka sedan på **Välj**.

    ![Välj blobbar][cache-import-choose-blobs]
5. Klicka på **importera** att starta importen.

   > [!IMPORTANT]
   > Cacheminnet är inte tillgänglig av cacheklienter under importen och alla befintliga data i cacheminnet tas bort.
   >
   >

    ![Importera][cache-import-blobs]

    Du kan övervaka förloppet för importen genom att följa meddelanden från Azure-portalen eller genom att visa händelser i den [granskningsloggen](../azure-resource-manager/resource-group-audit.md).

    ![Importera pågår][cache-import-data-import-complete]

## <a name="export"></a>Exportera
Exportera kan du exportera de data som lagras i Azure Redis-Cache för Redis-kompatibel RDB fil(er). Du kan använda den här funktionen för att flytta data från en Azure Redis-Cache-instans till en annan eller till en annan Redis-server. Skapa en tillfällig fil på den virtuella datorn som är värd för Azure Redis-Cache server-instansen under exporten, och överföra filen till det angivna lagringskontot. När exporten har slutförts med antingen en status för lyckad eller misslyckad, tas den temporära filen bort.

1. Exportera det aktuella innehållet i cachen till lagring, [Bläddra till ditt cacheminne](cache-configure.md#configure-redis-cache-settings) i Azure-portalen och klicka på **exportera data** från den **resurs menyn**.

    ![Välj lagringsbehållare][cache-export-data-choose-storage-container]
2. Klicka på **Välj lagringsbehållaren** och välj önskat lagringskonto. Lagringskontot måste vara i samma prenumeration och region som ditt cacheminne.

   > [!IMPORTANT]
   > Exportera fungerar med sidblobbar som stöds av både klassiska eller Resource Manager storage-konton, men stöds inte av [Blob storage-konton](../storage/blobs/storage-blob-storage-tiers.md#blob-storage-accounts) just nu.
   >
   >

    ![Lagringskonto][cache-export-data-choose-account]
3. Välj önskad blob-behållaren och klicka på **Välj**. Om du vill använda nya behållare, **lägga till behållaren** att lägga till först och välj sedan i listan.

    ![Välj lagringsbehållare][cache-export-data-container]
4. Ange en **Blob namnprefix** och på **exportera** att starta exportprocessen. Blob-namnprefix för prefixet namnen på de filer som genererats av den här exporten.

    ![Exportera][cache-export-data]

    Du kan övervaka förloppet för exportåtgärden genom att följa meddelanden från Azure-portalen eller genom att visa händelser i den [granskningsloggen](../azure-resource-manager/resource-group-audit.md).

    ![Exportera data är klar][cache-export-data-export-complete]

    Cacheminnen är tillgängliga för användning under exporten.

## <a name="importexport-faq"></a>Importera och exportera vanliga frågor och svar
Det här avsnittet innehåller vanliga frågor och svar om Import/Export-funktionen.

* [Priser för vilka nivåer använder Import/Export?](#what-pricing-tiers-can-use-importexport)
* [Kan jag importera data från Redis-servern?](#can-i-import-data-from-any-redis-server)
* [Vilka RDB versioner kan importera?](#what-rdb-versions-can-i-import)
* [Är Mina cache under en Import/Export-åtgärden?](#is-my-cache-available-during-an-importexport-operation)
* [Kan jag använda Import/Export med Redis-kluster?](#can-i-use-importexport-with-redis-cluster)
* [Hur fungerar Import/Export med en anpassad databaser inställningen?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Hur skiljer sig Import/Export från Redis-persistence?](#how-is-importexport-different-from-redis-persistence)
* [Kan jag automatisera Import/Export med PowerShell, CLI eller annan av hanteringsklienter?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Jag har fått ett timeout-fel under min Import/Export-åtgärd. Vad innebär det?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Jag fick ett fel när du exporterar data till Azure Blob Storage. Vad hände?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Priser för vilka nivåer använder Import/Export?
Importera och exportera är bara tillgängliga i premium-prisnivån.

### <a name="can-i-import-data-from-any-redis-server"></a>Kan jag importera data från Redis-servern?
Ja, förutom importerar data som exporteras från Azure Redis-Cache-instanser kan du importera RDB filer från en Redis-server som kör i molnet eller miljö, till exempel Linux, Windows, eller moln providrar, till exempel Amazon Web Services. Gör detta genom att överföra filen RDB från önskad Redis-servern till en sida eller blockera blobb i Azure Storage-konto och sedan importera den till din premium Azure Redis-Cache-instans. Du kan till exempel vill exportera data från ditt cacheminne för produktion och importera den till en cache som används som en del av en mellanlagringsmiljön för testning eller migrering.

> [!IMPORTANT]
> Om du vill importera data som har exporterats från Redis-servrar än Azure Redis-Cache när du använder en sidblob, vara blob sidstorleken justerad på en 512 byte-gräns. Exempelkod att utföra alla nödvändiga byte utfyllnad finns [exempel sidan blogg överför](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
> 
> 

### <a name="what-rdb-versions-can-i-import"></a>Vilka RDB versioner kan importera?

Azure Redis-Cache stöder RDB importera upp via RDB version 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Är Mina cache under en Import/Export-åtgärden?
* **Exportera** - cacheminnen fortfarande är tillgängliga och du kan fortsätta att använda ditt cacheminne när du exporterar.
* **Importera** - cacheminnen blir tillgänglig när en importåtgärden startas och blir tillgängliga för användning när importen är klar.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Kan jag använda Import/Export med Redis-kluster?
Ja, och du kan importera och exportera mellan en klustrad cache och en icke-klustrade cache. Eftersom Redis cluster [endast stöder databasen 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), inte importera alla data i databaser än 0. När du importerar Cachedata för klustrade, omdistribueras nycklarna bland shards i klustret.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Hur fungerar Import/Export med en anpassad databaser inställningen?
Vissa prisnivåer har olika [databaser gränser](cache-configure.md#databases), så det finns vissa saker när du importerar om du har konfigurerat ett anpassat värde för den `databases` anger under Skapa cache.

* När du importerar till en prisnivå med en lägre `databases` gränsen än den nivå som du exporterade:
  * Om du använder standardantalet `databases`, vilket är 16 för alla prisnivåer, inga data går förlorade.
  * Om du använder ett anpassat antal `databases` som faller inom ramen för den nivå som du importerar, inga data går förlorade.
  * Om din exporterade data innehöll data i en databas som överskrider gränserna för ny nivå, importeras inte data från de högre databaserna.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Hur skiljer sig Import/Export från Redis-persistence?
Azure Redis-Cache beständiga kan du bevara data som lagras i Redis till Azure Storage. När beständiga konfigureras kvarstår en ögonblicksbild av Redis-cache i binärformat Redis på disk baserat på en konfigurerbar säkerhetskopieringsfrekvens Azure Redis-Cache. Om ett allvarligt fel inträffar som inaktiverar både den primära servern och repliken cache, återställs cachelagrade data automatiskt med den senaste ögonblicksbilden. Mer information finns i [hur du konfigurerar datapersistence för Premium Azure Redis-Cache](cache-how-to-premium-persistence.md).

Import / Export kan du hämta data till eller exportera från Azure Redis-Cache. Det inte konfigurera säkerhetskopiering och återställning med hjälp av Redis-persistence.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Kan jag automatisera Import/Export med PowerShell, CLI eller annan av hanteringsklienter?
Ja, PowerShell instruktioner finns i [att importera ett Redis-cache](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) och [så här exporterar du ett Redis-cache](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Jag har fått ett timeout-fel under min Import/Export-åtgärd. Vad innebär det?
Om du finns kvar i den **dataimport** eller **exportera data** bladet för längre än 15 minuter innan du påbörjar igen, du får ett felmeddelande med ett felmeddelande som liknar följande exempel:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Om du vill lösa problemet, starta importen eller exportåtgärd innan 15 minuter har gått ut.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Jag fick ett fel när du exporterar data till Azure Blob Storage. Vad hände?
Export fungerar bara med RDB filer som lagras som sidblobar. Andra blob-typer stöds för närvarande inte, inklusive blob storage-konton med frekvent och lågfrekvent nivåer. Mer information finns i [Blob storage-konton](../storage/blobs/storage-blob-storage-tiers.md#blob-storage-accounts).

## <a name="next-steps"></a>Nästa steg
Lär dig hur du använder flera funktioner som cache premium.

* [Introduktion till Azure Redis Cache Premium-nivån](cache-premium-tier-intro.md)    

<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png
