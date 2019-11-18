---
title: Introduktion till Azure cache för Redis Premium-nivån
description: Lär dig hur du skapar och hanterar Redis beständighet, Redis klustring och VNET-stöd för din Premium-nivå i Azure-cache för Redis-instanser
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: aadcc13d2397f10ea40f06d1259c86b9a179c38b
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121664"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Introduktion till Azure cache för Redis Premium-nivån
Azure cache för Redis är en distribuerad, hanterad cache som hjälper dig att bygga mycket skalbara och fortare program genom att ge snabb åtkomst till dina data. 

Den nya Premium-nivån är en företags färdig nivå som innehåller alla funktioner på standard-nivå och fler, till exempel bättre prestanda, större arbets belastningar, haveri beredskap, import/export och förbättrad säkerhet. Fortsätt läsa om du vill veta mer om de ytterligare funktionerna i Premium cache-nivån.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Bättre prestanda jämfört med standard-eller Basic-nivån
**Bättre prestanda jämfört med standard-eller Basic-nivån.** Cacheminnen på Premium-nivån distribueras på maskin vara som har snabbare processorer och ger bättre prestanda jämfört med Basic-eller standard-nivån. Cacheminnen på Premium-nivån har högre genomflöde och lägre fördröjning. 

**Data flödet för samma storlek cache är högre i Premium jämfört med standard nivån.** Till exempel är data flödet för en 53 GB P4 (Premium)-cache 250 000-begäranden per sekund jämfört med 150K för C6 (standard).

Mer information om storlek, data flöde och bandbredd med Premium-cacheminnen finns i [Azure cache for Redis vanliga frågor och svar](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Redis-datapersistens
På Premium-nivån kan du spara cache-data i ett Azure Storage-konto. I en Basic/standard-cache lagras alla data i minnet. Om det uppstår problem med underliggande infrastruktur kan det vara möjligt att förlora data. Vi rekommenderar att du använder funktionen Redis data persistion på Premium-nivån för att öka återhämtningen mot data förlust. Azure cache för Redis erbjuder RDB och AOF (kommer snart) i [Redis persistence](https://redis.io/topics/persistence). 

Anvisningar om hur du konfigurerar persistence finns i [så här konfigurerar du persistence för en Premium Azure-cache för Redis](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Redis-kluster
Om du vill skapa cacheminnen som är större än 53 GB eller vill Shard data över flera Redis-noder kan du använda Redis-klustring som är tillgänglig på Premium-nivån. Varje nod består av ett primärt/replik cache-par som hanteras av Azure för hög tillgänglighet. 

**Redis-klustring ger maximal skalning och data flöde.** Data flödet ökar linjärt när du ökar antalet Shards (noder) i klustret. T.ex Om du skapar ett P4-kluster på 10 Shards är det tillgängliga data flödet 250 000 * 10 = 2 500 000 förfrågningar per sekund. Mer information om storlek, data flöde och bandbredd med Premium-cache finns i [Azure-cache för Redis vanliga frågor och svar](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) .

Information om hur du kommer igång med klustring finns i [så här konfigurerar du klustring för en Premium Azure-cache för Redis](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Förbättrad säkerhet och isolering
Cacheminnen som har skapats på Basic-eller standard-nivån är tillgängliga på det offentliga Internet. Åtkomst till cacheminnet är begränsad baserat på åtkomst nyckeln. Med Premium-nivån kan du ytterligare se till att endast klienter inom ett angivet nätverk har åtkomst till cacheminnet. Du kan distribuera Azure cache för Redis i ett [Azure-Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/). Du kan använda alla VNet-funktioner, till exempel undernät, principer för åtkomstkontroll och andra funktioner för att ytterligare begränsa åtkomsten till Redis.

Mer information finns i [så här konfigurerar du Virtual Network stöd för en Premium Azure-cache för Redis](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Import/Export
Import/export är en Azure-cache för Redis data hanterings åtgärd som gör att du kan importera data till Azure cache för Redis eller exportera data från Azure cache för Redis genom att importera och exportera en Azure-cache för Redis Database (RDB) ögonblicks bild från en Premium-cache till en sid-BLOB i ett Azure Storage-konto. På så sätt kan du migrera mellan olika Azure cache för Redis-instanser eller fylla i cachen med data innan du använder.

Import kan användas för att hämta Redis-kompatibla RDB-filer från valfri redis-server som körs i molnet eller i miljön, inklusive Redis som körs på Linux, Windows eller någon annan moln leverantör, till exempel Amazon Web Services och andra. Att importera data är ett enkelt sätt att skapa en cache med förifyllda data. Under importen läser Azure cache för Redis RDB-filerna från Azure Storage till minnet och infogar sedan nycklarna i cachen.

Med export kan du exportera data som lagras i Azure cache för Redis till Redis-kompatibla RDB-filer. Du kan använda den här funktionen för att flytta data från en Azure-cache för Redis-instans till en annan eller till en annan redis-server. Under exporten skapas en temporär fil på den virtuella datorn som är värd för Azure cache för Redis-Server-instansen och filen laddas upp till det angivna lagrings kontot. När export åtgärden har slutförts med statusen lyckad eller misslyckad tas den temporära filen bort.

Mer information finns i [så här importerar du data till och exporterar data från Azure cache för Redis](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Starta om
På Premium-nivån kan du starta om en eller flera noder i cachen på begäran. På så sätt kan du testa ditt program för återhämtning i händelse av ett haveri. Du kan starta om följande noder.

* Huvud nod i cacheminnet
* Sekundär nod i cacheminnet
* Både primära och sekundära noder i cacheminnet
* När du använder en Premium-cache med klustring kan du starta om de primära, sekundära eller båda noderna för enskilda Shards i cacheminnet

Mer information finns i [vanliga frågor och svar](cache-administration.md#reboot-faq) [om omstart](cache-administration.md#reboot) och omstart.

>[!NOTE]
>Funktionen för omstart har nu Aktiver ATS för alla Azure-cache för Redis-nivåer.
>
>

## <a name="schedule-updates"></a>Schemauppdateringar
Med funktionen schemalagda uppdateringar kan du ange ett underhålls fönster för din cache. När underhålls fönstret har angetts görs alla redis server-uppdateringar i det här fönstret. Om du vill ange en underhålls period väljer du önskade dagar och anger start timme för underhålls perioden för varje dag. Observera att tiden för underhålls perioden är UTC. 

Mer information finns i avsnittet om att [schemalägga uppdateringar](cache-administration.md#schedule-updates) och [Schemalägg uppdateringar](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Endast redis server-uppdateringar görs under det schemalagda underhålls fönstret. Underhålls perioden gäller inte för Azure-uppdateringar eller uppdateringar av den virtuella datorns operativ system.
> 
> 

## <a name="geo-replication"></a>Geo-replikering

**Geo-replikering** är en mekanism för att länka två Premium-nivåer i Azure-cache för Redis-instanser. Ett cacheminne har angetts som primärt länkat cache och den andra som sekundär länkad cache. Den sekundära länkade cachen blir skrivskyddad och data som skrivs till den primära cachen replikeras till den sekundära länkade cachen. Den här funktionen kan användas för att replikera en cache i Azure-regioner.

Mer information finns i [så här konfigurerar du geo-replikering för Azure cache för Redis](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Skala till Premium-nivån
Om du vill skala till Premium-nivån väljer du bara en av Premium nivåerna på bladet **ändra pris nivå** . Du kan också skala cacheminnet till Premium-nivån med PowerShell och CLI. Stegvisa instruktioner finns i [skala Azure cache för Redis](cache-how-to-scale.md) och [hur du automatiserar en skalnings åtgärd](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Nästa steg
Skapa en cache och utforska de nya funktionerna i Premium-nivån.

* [Så här konfigurerar du persistence för en Premium Azure-cache för Redis](cache-how-to-premium-persistence.md)
* [Så här konfigurerar du Virtual Network stöd för en Premium Azure-cache för Redis](cache-how-to-premium-vnet.md)
* [Så här konfigurerar du klustring för en Premium Azure-cache för Redis](cache-how-to-premium-clustering.md)
* [Så här importerar du data till och exporterar data från Azure cache för Redis](cache-how-to-import-export-data.md)
* [Så här administrerar du Azure cache för Redis](cache-administration.md)

