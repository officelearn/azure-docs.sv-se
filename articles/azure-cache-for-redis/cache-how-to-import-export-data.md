---
title: Importera och exportera data i Azure Cache för Redis | Microsoft Docs
description: Lär dig att importera och exportera data till och från blob storage med ditt Azure-Cache på premiumnivå för Redis-instanser
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 4a68ac38-87af-4075-adab-569d37d7cc9e
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
origin.date: 07/31/2017
ms.date: 02/27/2019
ms.author: v-junlch
ms.openlocfilehash: dfa8b47ced70386efa1daa44af318f1da55f49e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542343"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Importera och exportera data i Azure Cache för Redis
Import/Export är en Azure-Cache för Redis data management-åtgärd, där du kan importera data till Azure Cache för Redis eller exportera data från Azure Cache för Redis genom att importera och exportera en Azure-Cache för Redis-databasen (RDB) ögonblicksbild från en premium-cache för en BLOB i ett Azure Storage-konto. 

- **Exportera** – du kan exportera din Azure-Cache för Redis RDB ögonblicksbilder till en Sidblobb.
- **Importera** – du kan importera din Azure-Cache för Redis RDB-ögonblicksbilder från en Sidblobb eller en Blockblob.

Import/Export kan du migrera mellan olika Azure-Cache för Redis-instanser eller fylla i cachen med data före användning.

Den här artikeln innehåller en guide för import och export av data med Azure Cache för Redis och ger svar på vanliga frågor.

> [!IMPORTANT]
> Import/Export är en förhandsversion och är endast tillgänglig för [premiumnivån](cache-premium-tier-intro.md) cachelagrar.
>
>

## <a name="import"></a>Importera
Import kan användas för att hämta Redis kompatibla RDB-filer från en Redis-server som kör i valfritt moln eller en miljö, inklusive Redis som körs på Linux, Windows eller någon annan molnleverantör, till exempel Amazon Web Services och andra. Importera data är ett enkelt sätt att skapa ett cacheminne med fyllda. Under importen, Azure Cache för Redis läser in RDB-filer från Azure storage i minnet och sedan infogar nycklarna i cachen.

> [!NOTE]
> Se till att Redis-databasen (RDB) filen eller filerna laddas upp SID- eller blockblob blobar i Azure storage, i samma region och prenumeration som din Azure-Cache för Redis-instans innan du påbörjar importen. Mer information finns i [komma igång med Azure Blob storage](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Om du har exporterat din RDB-fil med den [Azure Cache för Redis exportera](#export) funktion, RDB-fil är lagrad i en sidblobb och är redo för import.
>
>

1. Att importera en eller flera blobbar för exporterade cacheminnet [Bläddra till ditt cacheminne](cache-configure.md#configure-azure-cache-for-redis-settings) i Azure-portalen och klicka på **dataimport** från den **resursmenyn**.

    ![Importera data][cache-import-data]
2. Klicka på **Välj BLOB(ar)** och välj det lagringskonto som innehåller data som ska importeras.

    ![Välj lagringskonto][cache-import-choose-storage-account]
3. Klicka på den behållare som innehåller data som ska importeras.

    ![Välj behållare][cache-import-choose-container]
4. Välj en eller flera BLOB-och importera genom att klicka på området till vänster om blob-namnet och klicka sedan på **Välj**.

    ![Välj blobar][cache-import-choose-blobs]
5. Klicka på **importera** att starta importen.

   > [!IMPORTANT]
   > Cacheminnet är inte tillgängliga för cache-klienter under importen och alla befintliga data i cacheminnet tas bort.
   >
   >

    ![Importera][cache-import-blobs]

    Du kan övervaka förloppet för importen genom att följa meddelanden från Azure portal eller genom att visa händelser i den [granskningsloggen](../azure-resource-manager/resource-group-audit.md).

    ![Importförlopp][cache-import-data-import-complete]

## <a name="export"></a>Exportera
Exportera kan du exportera de data som lagras i Azure Cache för Redis Redis kompatibla RDB-fil(er). Du kan använda den här funktionen för att flytta data från en Azure Cache för Redis-instans till en annan eller till en annan Redis-server. Under exporten, skapas en temporär fil på den virtuella datorn som är värd för Azure Cache för Redis server-instansen och filen har överförts till det angivna lagringskontot. När exporten är klar med antingen en status för en lyckad eller misslyckad tas den tillfälliga filen bort.

1. Att exportera det aktuella innehållet i cachen till lagring, [Bläddra till ditt cacheminne](cache-configure.md#configure-azure-cache-for-redis-settings) i Azure-portalen och klicka på **exportera data** från den **resursmenyn**.

    ![Välj lagringsbehållare][cache-export-data-choose-storage-container]
2. Klicka på **välja lagringsbehållare** och välj önskat lagringskonto. Lagringskontot måste finnas i samma prenumeration och region som din cache.

   > [!IMPORTANT]
   > Exportera fungerar med sidblobbar som stöds av både klassiska och Resource Manager-lagringskonton, men stöds inte av Blob storage-konton just nu. Mer information finns i [kontoöversikten för Azure Storage](../storage/common/storage-account-overview.md).
   >
   >

    ![Lagringskonto][cache-export-data-choose-account]
3. Välj önskad blob-behållaren och klicka på **Välj**. Om du vill använda nya en behållare, klickar du på **Lägg till behållare** att lägga till den först och välj sedan i listan.

    ![Välj lagringsbehållare][cache-export-data-container]
4. Ange ett **prefix för blobbnamn** och klicka på **exportera** att starta exportprocessen. Blob-namnprefix används som prefix i namnen på de filer som genererats av den här exportåtgärden.

    ![Exportera][cache-export-data]

    Du kan övervaka förloppet för exportåtgärden genom att följa meddelanden från Azure portal eller genom att visa händelser i den [granskningsloggen](../azure-resource-manager/resource-group-audit.md).

    ![Exportera data som är klar][cache-export-data-export-complete]

    Cacheminnen är tillgängliga för användning under exporten.

## <a name="importexport-faq"></a>Import/Export vanliga frågor och svar
Det här avsnittet innehåller vanliga frågor och svar om Import/Export-funktionen.

- [Vilka priser nivåerna kan du använda Import/Export?](#what-pricing-tiers-can-use-importexport)
- [Kan jag importera data från en Redis-server?](#can-i-import-data-from-any-redis-server)
- [Vilka RDB-versioner kan jag importera?](#what-rdb-versions-can-i-import)
- [Är Mina cache tillgänglig under en Import/Export?](#is-my-cache-available-during-an-importexport-operation)
- [Kan jag använda Import/Export med Redis-kluster?](#can-i-use-importexport-with-redis-cluster)
- [Hur fungerar Import/Export med en anpassad databaser inställningen?](#how-does-importexport-work-with-a-custom-databases-setting)
- [Hur skiljer sig Import/Export från Redis-persistens?](#how-is-importexport-different-from-redis-persistence)
- [Kan jag automatisera Import/Export med PowerShell, CLI eller andra hanteringsklienter?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
- [Jag har fått ett timeout-fel under min Import/Export-åtgärden. Vad betyder det?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
- [Jag får ett felmeddelande när du exporterar data till Azure Blob Storage. Vad hände?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Vilka priser nivåerna kan du använda Import/Export?
Import/Export är endast tillgängliga i premiumprisnivån.

### <a name="can-i-import-data-from-any-redis-server"></a>Kan jag importera data från en Redis-server?
Ja, förutom att importera data från Azure Cache för Redis-instanser, du kan importera RDB-filer från en Redis-server som kör i valfritt moln eller en miljö, till exempel Linux, Windows, eller leverantörer, till exempel Amazon Web Services i molnet. Om du vill göra detta måste överföra RDB-fil från önskad Redis-servern till en sida eller block blob i ett Azure Storage-konto och sedan importera den till din Azure-Cache på premiumnivå för Redis-instans. Du kan till exempel vill exportera data från ditt cacheminne i produktion och importera den till en cache som används som en del av en mellanlagringsmiljö för att testa eller migrering.

> [!IMPORTANT]
> Om du vill importera data från Redis-servrar än Azure Cache för Redis när du använder en sidblobb, måste sidblobens storlek anpassas en 512 byte-gräns. Exempelkod för att utföra alla nödvändiga byte utfyllnad, se [exempel sidan blob uppladdning](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
> 
> 

### <a name="what-rdb-versions-can-i-import"></a>Vilka RDB-versioner kan jag importera?

Azure Redis-Cache stöder RDB-import upp via RDB version 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Är Mina cache tillgänglig under en Import/Export?
- **Exportera** – cacheminnen är tillgängliga och du kan fortsätta att använda din cache när du exporterar.
- **Importera** – cacheminnen blir inte tillgänglig när en importåtgärden startas och blir tillgängliga för användning när importen är klar.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Kan jag använda Import/Export med Redis-kluster?
Ja, och du kan importera/exportera mellan en klustrad cache och ett icke-klustrade cacheminne. Sedan Redis-kluster [endast har stöd för databasen 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), inte importera alla data i databaser än 0. När klustrade cachelagrade data importeras om nycklarna mellan shards i klustret.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Hur fungerar Import/Export med en anpassad databaser inställningen?
Vissa prisnivåer har olika [databaser gränser](cache-configure.md#databases), så det finns några överväganden när du importerar om du har konfigurerat ett anpassat värde för den `databases` konfigurera under Skapa cache.

- När du importerar till en prisnivå med ett lägre `databases` gränsen än den nivå som du exporterade:
  - Om du använder standardantalet `databases`, vilket är 16 för alla prisnivåer, ingen data går förlorad.
  - Om du använder en anpassad antalet `databases` som är inom gränserna för nivån som du importerar, ingen data går förlorad.
  - Om din exporterade data innehöll data i en databas som har överskridit gränserna för den nya nivån, importeras inte data från de högre databaserna.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Hur skiljer sig Import/Export från Redis-persistens?
Azure Cache för Redis-persistens gör att du kan bevara data lagrade i Redis till Azure Storage. När persistence konfigureras, kvarstår Azure Cache för en ögonblicksbild av Azure Cache för Redis i ett Redis-binärformat till disk utifrån en konfigurerbar säkerhetskopieringsfrekvens. Om ett allvarligt fel inträffar som inaktiverar både den primära servern och repliken cache, återställs cachelagrade data automatiskt med den senaste ögonblicksbilden. Mer information finns i [hur du konfigurerar datapersistence för Premium Azure Cache för Redis](cache-how-to-premium-persistence.md).

Import / Export kan du hämta data till eller exportera från Azure Cache för Redis. Den inte konfigurera säkerhetskopiering och återställning med hjälp av Redis-persistens.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Kan jag automatisera Import/Export med PowerShell, CLI eller andra hanteringsklienter?
Ja, PowerShell instruktioner finns i [att importera en Azure Cache för Redis](cache-howto-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) och [vill exportera en Azure Cache för Redis](cache-howto-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Jag har fått ett timeout-fel under min Import/Export-åtgärden. Vad betyder det?
Om du är kvar på den **dataimport** eller **exportera data** bladet för längre än 15 minuter innan du påbörjar igen, du får ett felmeddelande med ett felmeddelande som liknar följande exempel:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

För att lösa problemet, starta importen eller exportåtgärden innan 15 minuter har gått ut.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Jag får ett felmeddelande när du exporterar data till Azure Blob Storage. Vad hände?
Export fungerar bara med RDB-filer som lagras som sidblobar. Andra blobtyper stöds för närvarande inte, inklusive Blob storage-konton med frekventa och lågfrekventa nivåer. Mer information finns i [kontoöversikten för Azure Storage](../storage/common/storage-account-overview.md).

## <a name="next-steps"></a>Nästa steg
Lär dig hur du använder mer premiumfunktioner för cache.

- [Introduktion till Azure Cache Redis Premium-nivån](cache-premium-tier-intro.md)    

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


<!-- Update_Description: update metedata properties -->