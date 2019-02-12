---
title: Hur du administrerar Azure Cache för Redis | Microsoft Docs
description: Lär dig att utföra administrationsuppgifter, till exempel omstart och schemalägga uppdateringar för Azure Cache för Redis
services: azure-cache-for-redis
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 8c915ae6-5322-4046-9938-8f7832403000
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: azure-cache-for-redis
ms.workload: tbd
ms.date: 07/05/2017
ms.author: wesmc
ms.openlocfilehash: 72e27b7098d9891dc54b31c34e2211362e7c9f50
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56099980"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Hur du administrerar Azure Cache för Redis
Det här avsnittet beskriver hur du utför administrationsuppgifter som [omstart](#reboot) och [schemalägga uppdateringar](#schedule-updates) för din Azure-Cache för Redis-instanser.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Starta om
Den **omstart** kan du starta om en eller flera noder i cacheminnet. Den här funktionen för omstart kan du testa ditt program för återhämtning om det uppstår ett fel för en cachenod.

![Starta om](./media/cache-administration/redis-cache-administration-reboot.png)

Markera noderna att starta om och klicka på **omstart**.

![Starta om](./media/cache-administration/redis-cache-reboot.png)

Om du har en premium-cache med klustring aktiverat kan välja du cacheskärvor ska startas om.

![Starta om](./media/cache-administration/redis-cache-reboot-cluster.png)

Starta om en eller flera noder för din cachelagring, Välj önskade noderna och sedan **omstart**. Om du har en premium-cache med klustring aktiverat, Välj önskade shards att starta om och klickar sedan på **omstart**. Efter några minuter tillbaka på valda noderna omstart och är online ett par minuter senare.

Påverkan på klientprogram som varierar beroende på vilka noder att du startar om.

* **Master** – när den överordnade noden startas om, Azure Cache för Redis växlas över till noden replik och främjar till master-databasen. Under den här redundansen, kan det finnas en liten stund där anslutningar kan misslyckas till cachen.
* **Underordnad** – när den underordnade noden startas om, finns det vanligtvis ingen inverkan på cache-klienter.
* **Både master och slavar** -när både cachenoder startas om, försvinner alla data i cacheminnet och anslutningar till cachen misslyckas tills den primära noden är online igen. Om du har konfigurerat [datapersistence](cache-how-to-premium-persistence.md), den senaste aktuell säkerhetskopia återställs när cachen är online igen, men alla cache-skrivningar som inträffat efter den senaste säkerhetskopieringen går förlorade.
* **Noder i en premium-cache med klustring aktiverat** – när du startar om en eller flera noder i en premium-cache med klustring aktiverat beteendet för de valda noderna är samma som när du startar om motsvarande nod eller noder i en icke-klustrade cache.

> [!IMPORTANT]
> Omstart är nu tillgängligt för alla prisnivåer.
> 
> 

## <a name="reboot-faq"></a>Starta om vanliga frågor och svar
* [Vilken nod bör jag startas om för att testa mitt program?](#which-node-should-i-reboot-to-test-my-application)
* [Kan jag startar om cachen om du vill rensa klientanslutningar?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Kommer jag att förlora data från mitt cacheminne om jag gör en omstart?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Kan jag startar om mitt cacheminne med hjälp av PowerShell, CLI eller andra hanteringsverktyg?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
* [Vilka priser nivåerna kan du använda funktionen omstart?](#what-pricing-tiers-can-use-the-reboot-functionality)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Vilken nod bör jag startas om för att testa mitt program?
Testa elasticiteten i ditt program mot fel på den primära noden för din cachelagring genom att starta om den **Master** noden. Om du vill testa elasticiteten i ditt program mot fel på den sekundära noden, startar du om den **underordnad** noden. Testa elasticiteten i ditt program mot Totalt antal fel på cacheminnet genom att starta om **både** noder.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Kan jag startar om cachen om du vill rensa klientanslutningar?
Ja, om du startar om cachen rensas alla klientanslutningar. Omstart kan vara användbart i fall där alla klientanslutningar har förbrukats på grund av ett logikfel eller en bugg i klientprogrammet. Varje prisnivå har olika [klienten anslutningsgräns](cache-configure.md#default-redis-server-configuration) för olika storlekar och när de här gränserna uppnås, inga fler klientanslutningar accepteras. Starta om cachen är ett sätt att rensa alla klientanslutningar.

> [!IMPORTANT]
> Om du startar om din cache för att ta bort klientanslutningar återansluter StackExchange.Redis automatiskt när Redis-nod är online igen. Om det underliggande problemet kvarstår fortsätta klientanslutningarna för att användas.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Kommer jag att förlora data från mitt cacheminne om jag gör en omstart?
Om du startar om både den **Master** och **underordnad** noder, alla data i cacheminnet (eller i fragmentet om du använder en premium-cache med klustring aktiverat) går förlorade. Om du har konfigurerat [datapersistence](cache-how-to-premium-persistence.md), den senaste aktuell säkerhetskopia återställs när cachen är online igen, men alla skrivningar i cacheminnet som har inträffat när säkerhetskopieringen gjordes går förlorade.

Om du startar om bara en av noderna data förloras inte vanligtvis, men det kan fortfarande vara. Till exempel om den överordnade noden startas om och en cacheskrivning pågår, data från cacheskrivning är förlorad. Ett annat scenario för dataförlust skulle vara om du startar om en nod och den andra noden händer att gå nedåt på grund av ett fel på samma gång. Mer information om möjliga orsaker till förlust av data finns i [vad hände med mina data i Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Kan jag startar om mitt cacheminne med hjälp av PowerShell, CLI eller andra hanteringsverktyg?
Ja, PowerShell instruktioner finns i [att starta om Azure Cache för Redis](cache-howto-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Vilka priser nivåerna kan du använda funktionen omstart?
Omstart är tillgänglig för alla prisnivåer.

## <a name="schedule-updates"></a>Schemauppdateringar
Den **Schemalägg uppdateringar** kan du ange en underhållsperiod för din cachelagring för Premium-nivå. När underhållsfönstret har angetts görs alla uppdateringar för Redis-servern under det här fönstret. 

> [!NOTE] 
> Underhållsperioden gäller endast för att Redis serveruppdateringar, och inte till några Azure uppdaterar eller uppdateringar av operativsystemet på de virtuella datorerna som är värdar för cachen.
> 
> 

![Schemauppdateringar](./media/cache-administration/redis-schedule-updates.png)

Om du vill ange en underhållsperiod, de önskade dagarna och ange starttid för underhåll fönstret för varje dag och klicka på **OK**. Observera att underhållsfönstertiden är i UTC. 

Standard- och minimumkapacitet underhållsperiod för programuppdateringar är fem timmar. Det här värdet kan inte konfigureras från Azure-portalen, men du kan konfigurera det i PowerShell med hjälp av den `MaintenanceWindow` -parametern för den [New AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) cmdlet. Mer information finns i [kan jag hantera schemalagda uppdateringar med hjälp av PowerShell, CLI eller andra hanteringsverktyg?](#can-i-manage-scheduled-updates-using-powershell-cli-or-other-management-tools)


## <a name="schedule-updates-faq"></a>Schemalägga uppdateringar av vanliga frågor och svar
* [När uppdateringar göras om jag inte använder funktionen Schemalägg uppdateringar?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Vilken typ av uppdateringar som görs under den schemalagda underhållsperioden?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Kan jag hantera schemalagda uppdateringar med hjälp av PowerShell, CLI eller andra hanteringsverktyg?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
* [Vilka priser nivåerna kan använda funktionen Schemalägg uppdateringar?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>När uppdateringar göras om jag inte använder funktionen Schemalägg uppdateringar?
Om du inte anger en underhållsperiod, kan uppdateringar utföras när som helst.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Vilken typ av uppdateringar som görs under den schemalagda underhållsperioden?
Endast Redis-server som har uppdaterats under det schemalagda underhållsfönstret. Underhållsperioden gäller inte för Azure-uppdateringar eller uppdateringar av VM-operativsystem.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Kan jag hanterade schemalagda uppdateringar med hjälp av PowerShell, CLI eller andra hanteringsverktyg?
Ja, du kan hantera dina schemalagda uppdateringar med hjälp av följande PowerShell-cmdletar:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>Vilka priser nivåerna kan använda funktionen Schemalägg uppdateringar?
Den **Schemalägg uppdateringar** funktionen är endast tillgänglig i premiumprisnivån.

## <a name="next-steps"></a>Nästa steg
* Utforska mer [Azure Cache Redis premiumnivån](cache-premium-tier-intro.md) funktioner.

