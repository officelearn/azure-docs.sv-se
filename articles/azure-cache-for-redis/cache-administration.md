---
title: Så här administrerar du Azure Cache for Redis
description: Lär dig hur du utför administrativa uppgifter som omstart och schemalägg uppdateringar för Azure Cache för Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 69686cad20bc4ce70bff2a92a216c9430522c301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278849"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Så här administrerar du Azure Cache for Redis
I det här avsnittet beskrivs hur du utför administrativa uppgifter som [att starta om](#reboot) och schemalägga [uppdateringar](#schedule-updates) för Dina Azure-cache för Redis-instanser.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Starta om
**Med bladet Starta om** kan du starta om en eller flera noder i cacheminnet. Med den här omstartsfunktionen kan du testa programmet för återhämtning om det finns ett fel på en cachenod.

![Starta om](./media/cache-administration/redis-cache-administration-reboot.png)

Markera de noder som ska startas om och klicka på **Starta om**.

![Starta om](./media/cache-administration/redis-cache-reboot.png)

Om du har en premiumcache med klusteraktiverad kan du välja vilka shards i cacheminnet som ska startas om.

![Starta om](./media/cache-administration/redis-cache-reboot-cluster.png)

Om du vill starta om en eller flera noder i cacheminnet markerar du önskade noder och klickar på **Starta om**. Om du har en premiumcache med klusteraktiverade väljer du önskade shards som ska startas om och klickar sedan på **Starta om**. Efter några minuter startas de valda noderna om och är online igen några minuter senare.

Effekten på klientprogram varierar beroende på vilka noder som du startar om.

* **Master** - När huvudnoden startas om, azure cache för Redis misslyckas över till repliknoden och befordrar den till master. Under den här redundansen kan det finnas ett kort intervall där anslutningar kan misslyckas med cachen.
* **Slav** - När slavnoden startas om, finns det vanligtvis ingen inverkan på cacheklienter.
* **Både master och slave** - När båda cachenoderna startas om går alla data förlorade i cacheminnet och anslutningarna till cachen misslyckas tills den primära noden är online igen. Om du har konfigurerat [databeständighet](cache-how-to-premium-persistence.md)återställs den senaste säkerhetskopian när cachen är online igen, men alla cacheskrivningar som inträffade efter den senaste säkerhetskopian går förlorade.
* **Noder i en premiumcache med klusteraktiverad** - När du startar om en eller flera noder i en premiumcache med klusteraktiverad, är beteendet för de valda noderna detsamma som när du startar om motsvarande nod eller noder i en icke-klustrad cache.

## <a name="reboot-faq"></a>Vanliga frågor om omstart
* [Vilken nod ska jag starta om för att testa mitt program?](#which-node-should-i-reboot-to-test-my-application)
* [Kan jag starta om cacheminnet för att rensa klientanslutningar?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Kommer jag att förlora data från min cache om jag gör en omstart?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Kan jag starta om cacheminnet med PowerShell, CLI eller andra hanteringsverktyg?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Vilken nod ska jag starta om för att testa mitt program?
Om du vill testa programmets återhämtning mot fel på den **Master** primära noden i cacheminnet startar du om masternoden. Om du vill testa programmets återhämtning mot fel på **Slave** den sekundära noden startar du om slavnoden. Om du vill testa programmets återhämtning mot totalt fel i cacheminnet startar du om **båda** noderna.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Kan jag starta om cacheminnet för att rensa klientanslutningar?
Ja, om du startar om cachen rensas alla klientanslutningar. Omstart kan vara användbart i det fall där alla klientanslutningar används på grund av ett logikfel eller ett fel i klientprogrammet. Varje prisnivå har olika [klientanslutningsgränser](cache-configure.md#default-redis-server-configuration) för de olika storlekarna, och när dessa gränser har uppnåtts accepteras inga fler klientanslutningar. Starta om cachen är ett sätt att rensa alla klientanslutningar.

> [!IMPORTANT]
> Om du startar om cachen för att rensa klientanslutningar återansluter StackExchange.Redis automatiskt när Redis-noden är online igen. Om det underliggande problemet inte är löst kan klientanslutningarna fortsätta att förbrukas.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Kommer jag att förlora data från min cache om jag gör en omstart?
Om du startar om både **master-** och **slavenoderna** kan alla data i cacheminnet (eller i fragmentet om du använder en premiumcache med kluster aktiverat) gå förlorade, men detta är inte heller garanterat. Om du har konfigurerat [databeständighet](cache-how-to-premium-persistence.md)återställs den senaste säkerhetskopian när cachen är online igen, men alla cacheskrivningar som har inträffat efter säkerhetskopieringen går förlorade.

Om du startar om bara en av noderna går data vanligtvis inte förlorade, men det kan fortfarande vara det. Om huvudnoden till exempel startas om och en cacheskrivning pågår går data från cacheskrivningen förlorade. Ett annat scenario för dataförlust skulle vara om du startar om en nod och den andra noden råkar gå ner på grund av ett fel samtidigt. Mer information om möjliga orsaker till dataförlust finns i [Vad hände med mina data i Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Kan jag starta om cacheminnet med PowerShell, CLI eller andra hanteringsverktyg?
Ja, för PowerShell-instruktioner se [Så här startar du om en Azure-cache för Redis](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

## <a name="schedule-updates"></a>Schemauppdateringar
Med bladet **Schemauppdateringar** kan du ange ett underhållsfönster för cacheinstansen. När underhållsfönstret har angetts görs alla Redis-serveruppdateringar under det här fönstret. 

> [!NOTE] 
> Underhållsfönstret gäller endast Redis-serveruppdateringar och inte för några Azure-uppdateringar eller uppdateringar av operativsystemet för de virtuella datorer som är värdar för cachen.
>

![Schemauppdateringar](./media/cache-administration/redis-schedule-updates.png)

Om du vill ange ett underhållsfönster kontrollerar du önskade dagar och anger starttimmen för underhållsfönstret för varje dag och klickar på **OK**. Observera att underhållsfönstret är i UTC. 

Standard- och minimiunderhållsfönstret för uppdateringar är fem timmar. Det här värdet kan inte konfigureras från Azure-portalen, men `MaintenanceWindow` du kan konfigurera det i PowerShell med parametern [för cmdlet New-AzRedisCacheScheduleEntry.](/powershell/module/az.rediscache/new-azrediscachescheduleentry) Mer information finns i Kan jag hantera schemalagda uppdateringar med PowerShell, CLI eller andra hanteringsverktyg?

## <a name="schedule-updates-faq"></a>Vanliga frågor och svar om uppdateringar
* [När sker uppdateringar om jag inte använder funktionen för schemauppdateringar?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Vilken typ av uppdateringar görs under det schemalagda underhållsfönstret?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Kan jag hantera schemalagda uppdateringar med Hjälp av PowerShell, CLI eller andra hanteringsverktyg?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>När sker uppdateringar om jag inte använder funktionen för schemauppdateringar?
Om du inte anger ett underhållsfönster kan uppdateringar göras när som helst.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Vilken typ av uppdateringar görs under det schemalagda underhållsfönstret?
Endast Redis-serveruppdateringar görs under det schemalagda underhållsfönstret. Underhållsfönstret gäller inte för Azure-uppdateringar eller uppdateringar av operativsystemet VM.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Kan jag hantera schemalagda uppdateringar med Hjälp av PowerShell, CLI eller andra hanteringsverktyg?
Ja, du kan hantera schemalagda uppdateringar med följande PowerShell-cmdlets:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [Ny-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [Ny-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Ta bort-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>Nästa steg
* Utforska fler [Azure-cache för Redis premiumnivåfunktioner.](cache-premium-tier-intro.md)

