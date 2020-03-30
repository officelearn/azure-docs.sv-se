---
title: Importera och exportera data i Azure Cache för Redis
description: Lär dig hur du importerar och exporterar data till och från blob-lagring med dina premium Azure-cache för Redis-instanser
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: 29ad5ca6c9058b88a539c7a3bb8ace4d9a65083a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278095"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Importera och exportera data i Azure Cache för Redis
Import/export är en Azure Cache for Redis-datahanteringsåtgärd, som gör att du kan importera data till Azure Cache for Redis eller exportera data från Azure Cache for Redis genom att importera och exportera en ögonblicksbild av Azure Cache for Redis Database (RDB) från en premiumcache till en blob i ett Azure Storage-konto.

- **Exportera** – du kan exportera din Azure-cache för Redis RDB-ögonblicksbilder till en sidblob.
- **Importera** – du kan importera azure-cache för Redis RDB-ögonblicksbilder från antingen en sidblob eller en Block Blob.

Med import/export kan du migrera mellan olika Azure-cache för Redis-instanser eller fylla i cacheminnet med data före användning.

Den här artikeln innehåller en guide för import och export av data med Azure Cache för Redis och ger svar på vanliga frågor.

> [!IMPORTANT]
> Import/export är endast tillgängligt för [cacheminnen på premiumnivå.](cache-premium-tier-intro.md)
>
>

## <a name="import"></a>Importera
Import kan användas för att få Redis-kompatibla RDB-filer från alla Redis-servrar som körs i alla moln eller miljöer, inklusive Redis som körs på Linux, Windows eller någon molnleverantör som Amazon Web Services och andra. Att importera data är ett enkelt sätt att skapa en cache med förifyllda data. Under importen läses RDB-filerna från Azure-lagringen in i minnet och infogar sedan nycklarna i cacheminnet.

> [!NOTE]
> Innan du påbörjar importåtgärden ska du se till att filen eller filerna i Redis Database (RDB) överförs till sid- eller blockblobar i Azure-lagring, i samma region och prenumeration som din Azure-cache för Redis-instans. Mer information finns i [Komma igång med Azure Blob storage](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Om du exporterade rdb-filen med hjälp av funktionen [Azure Cache for Redis Export](#export) lagras filen RDB redan i en sidblob och är klar för import.
>
>

1. Om du vill importera en eller flera exporterade cacheblobar [bläddrar du till cacheminnet](cache-configure.md#configure-azure-cache-for-redis-settings) i Azure-portalen och klickar på **Importera data** från **resursmenyn**.

    ![Importera data](./media/cache-how-to-import-export-data/cache-import-data.png)
2. Klicka på **Välj Blob(er)** och välj det lagringskonto som innehåller de data som ska importeras.

    ![Välj lagringskonto](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. Klicka på behållaren som innehåller de data som ska importeras.

    ![Välj behållare](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. Markera en eller flera blobbar som ska importeras genom att klicka på området till vänster om blobnamnet och klicka sedan på **Markera**.

    ![Välj blobbar](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. Klicka på **Importera** om du vill påbörja importprocessen.

   > [!IMPORTANT]
   > Cachen är inte tillgänglig för cacheklienter under importen och alla befintliga data i cacheminnet tas bort.
   >
   >

    ![Importera](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    Du kan övervaka hur importen fortskrider genom att följa meddelandena från Azure-portalen eller genom att visa händelserna i [granskningsloggen](../azure-resource-manager/management/view-activity-logs.md).

    ![Importera förlopp](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Exportera
Med export kan du exportera data som lagras i Azure Cache for Redis till Redis-kompatibla RDB-filer. Du kan använda den här funktionen för att flytta data från en Azure Cache for Redis-instans till en annan eller till en annan Redis-server. Under exportprocessen skapas en temporär fil på den virtuella datorn som är värd för Azure Cache for Redis-serverinstansen och filen överförs till det angivna lagringskontot. När exportåtgärden slutförs med antingen status som lyckad eller misslyckad tas den temporära filen bort.

1. Om du vill exportera det aktuella innehållet i cacheminnet till lagring [bläddrar du till cacheminnet](cache-configure.md#configure-azure-cache-for-redis-settings) i Azure-portalen och klickar på **Exportera data** från **resursmenyn**.

    ![Välj lagringsbehållare](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. Klicka på **Välj lagringsbehållare** och välj önskat lagringskonto. Lagringskontot måste finnas i samma prenumeration och region som cacheminnet.

   > [!IMPORTANT]
   > Export fungerar med sidblobar, som stöds av både klassiska lagringskonton och Resource Manager-lagringskonton, men som inte stöds av Blob-lagringskonton just nu. Mer information finns i [kontoöversikten för Azure Storage](../storage/common/storage-account-overview.md).
   >

    ![Lagringskonto](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. Välj önskad blob-behållare och klicka på **Markera**. Om du vill använda en ny behållare klickar du på **Lägg till behållare** för att lägga till den först och markerar den sedan i listan.

    ![Välj lagringsbehållare](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. Skriv ett **Blob-namnprefix** och klicka på **Exportera** för att starta exportprocessen. Blob-namnprefixet används för att prefixa namnen på filer som genereras av den här exportåtgärden.

    ![Exportera](./media/cache-how-to-import-export-data/cache-export-data.png)

    Du kan övervaka hur exportåtgärden fortskrider genom att följa meddelandena från Azure-portalen eller genom att visa händelserna i [granskningsloggen](../azure-resource-manager/management/view-activity-logs.md).

    ![Exportera data slutförd](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    Cacheminnen är fortfarande tillgängliga för användning under exportprocessen.

## <a name="importexport-faq"></a>Vanliga frågor och svar om import/export
Det här avsnittet innehåller vanliga frågor om funktionen Importera/exportera.

* [Vilka prisnivåer kan använda Import/Export?](#what-pricing-tiers-can-use-importexport)
* [Kan jag importera data från valfri Redis-server?](#can-i-import-data-from-any-redis-server)
* [Vilka RDB-versioner kan jag importera?](#what-rdb-versions-can-i-import)
* [Är min cache tillgänglig under en import/export-åtgärd?](#is-my-cache-available-during-an-importexport-operation)
* [Kan jag använda Import/Export med Redis-kluster?](#can-i-use-importexport-with-redis-cluster)
* [Hur fungerar import/export med en anpassad databasinställning?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Hur skiljer sig import/export från Redis persistens?](#how-is-importexport-different-from-redis-persistence)
* [Kan jag automatisera import/export med PowerShell, CLI eller andra hanteringsklienter?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Jag fick ett timeout-fel under min import/export-åtgärd. Vad betyder det?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Jag fick ett felmeddelande när jag exporterade mina data till Azure Blob Storage. Vad hände?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Vilka prisnivåer kan använda Import/Export?
Import/export är endast tillgängligt på premiumprisnivån.

### <a name="can-i-import-data-from-any-redis-server"></a>Kan jag importera data från valfri Redis-server?
Ja, förutom att importera data som exporteras från Azure Cache för Redis-instanser kan du importera RDB-filer från alla Redis-servrar som körs i alla moln eller miljöer, till exempel Linux, Windows eller molnleverantörer som Amazon Web Services. Det gör du genom att överföra RDB-filen från önskad Redis-server till en sid- eller blockblob i ett Azure Storage-konto och sedan importera den till din premium Azure-cache för Redis-instans. Du kanske till exempel vill exportera data från produktionscachen och importera dem till en cache som används som en del av en mellanlagringsmiljö för testning eller migrering.

> [!IMPORTANT]
> Om du vill importera data som exporteras från Redis-servrar än Azure Cache för Redis när du använder en sidblob måste sidblobstorleken justeras på en gräns på 512 byte. Information om hur du utför en obligatorisk utfyllnad av byte finns i [Exempel på sidblolobuppladdning](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
>
>

### <a name="what-rdb-versions-can-i-import"></a>Vilka RDB-versioner kan jag importera?

Azure Cache för Redis stöder RDB-import via RDB version 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Är min cache tillgänglig under en import/export-åtgärd?
* **Exportera** – Cacheminnen är fortfarande tillgängliga och du kan fortsätta att använda cacheminnet under en exportåtgärd.
* **Import** - Cacheminnen blir otillgängliga när en importåtgärd startar och blir tillgänglig för användning när importen är klar.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Kan jag använda Import/Export med Redis-kluster?
Ja, och du kan importera/exportera mellan en klustrad cache och en cache som inte är grupperad. Eftersom Redis-klustret [endast stöder databas 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)importeras inte data i andra databaser än 0. När klustrade cachedata importeras omfördelas nycklarna bland klustrets fragment.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Hur fungerar import/export med en anpassad databasinställning?
Vissa prisnivåer har olika [databasgränser,](cache-configure.md#databases)så det finns vissa överväganden när du importerar om du har konfigurerat ett anpassat värde för `databases` inställningen när cacheminnet skapas.

* När du importerar till en `databases` prisnivå med en lägre gräns än den nivå som du exporterade:
  * Om du använder standardnumret `databases`för , som är 16 för alla prisnivåer, går inga data förlorade.
  * Om du använder ett `databases` anpassat antal som ligger inom gränserna för den nivå som du importerar till, går inga data förlorade.
  * Om dina exporterade data innehöll data i en databas som överskrider gränserna för den nya nivån importeras inte data från dessa högre databaser.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Hur skiljer sig import/export från Redis persistens?
Med Azure Cache for Redis persistens kan du spara data som lagras i Redis till Azure Storage. När beständighet har konfigurerats behåller Azure Cache för Redis en ögonblicksbild av Azure Cache for Redis i ett Redis binärt format till disk baserat på en konfigurerbar säkerhetskopieringsfrekvens. Om en oåterkallelig händelse inträffar som inaktiverar både den primära och replikcachen återställs cachedata automatiskt med den senaste ögonblicksbilden. Mer information finns i [Så här konfigurerar du databeständighet för en Premium Azure-cache för Redis](cache-how-to-premium-persistence.md).

Med import/export kan du föra in data i eller exportera från Azure Cache för Redis. Det konfigurerar inte säkerhetskopiering och återställning med Redis persistens.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Kan jag automatisera import/export med PowerShell, CLI eller andra hanteringsklienter?
Ja, för PowerShell-instruktioner finns [Så här importerar du en Azure-cache för Redis](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) och [exporterar en Azure-cache för Redis](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Jag fick ett timeout-fel under min import/export-åtgärd. Vad betyder det?
Om du finns kvar på **databladet Importera data** eller **Exportera data** längre än 15 minuter innan du påbörjar åtgärden visas ett felmeddelande med ett felmeddelande som liknar följande exempel:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Lös detta genom att initiera import- eller exportåtgärden innan 15 minuter har förflutit.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Jag fick ett felmeddelande när jag exporterade mina data till Azure Blob Storage. Vad hände?
Export fungerar bara med RDB-filer som lagras som sidblobar. Andra blob-typer stöds för närvarande inte, inklusive Blob-lagringskonton med frekventa och svala nivåer. Mer information finns i [kontoöversikten för Azure Storage](../storage/common/storage-account-overview.md).

## <a name="next-steps"></a>Nästa steg
Läs om hur du använder fler premiumcachefunktioner.

* [Introduktion till Azure Cache för Redis Premium-nivå](cache-premium-tier-intro.md)
