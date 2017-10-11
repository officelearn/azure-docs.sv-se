---
title: Hur man administrerar Azure Redis-Cache | Microsoft Docs
description: "Lär dig att utföra administrationsuppgifter, till exempel omstart och schema-uppdateringar för Azure Redis-Cache"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 8c915ae6-5322-4046-9938-8f7832403000
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 07/05/2017
ms.author: sdanie
ms.openlocfilehash: 3352fec59d7dfbfab9b0416992a60f11d0ec2402
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-administer-azure-redis-cache"></a>Hur man administrerar Azure Redis-Cache
Det här avsnittet beskriver hur du utför administrationsuppgifter som [omstart](#reboot) och [schemalägga uppdateringar](#schedule-updates) för Azure Redis-Cache-instanser.

## <a name="reboot"></a>Starta om
Den **omstart** bladet kan du starta om en eller flera noder i ditt cacheminne. Den här funktionen för omstart kan du testa ditt program för återhämtning om det finns ett fel i en cachenod.

![Starta om](./media/cache-administration/redis-cache-administration-reboot.png)

Markera noderna för att starta om och klickar på **omstart**.

![Starta om](./media/cache-administration/redis-cache-reboot.png)

Om du har en premium-cache med aktiverad klustring, kan du välja vilka delar av cacheminnet för att starta om.

![Starta om](./media/cache-administration/redis-cache-reboot-cluster.png)

Starta om en eller flera noder i ditt cacheminne, Välj önskade noder och sedan **omstart**. Om du har en premium-cache med aktiverad klustring, Välj önskade shards omstart och klicka sedan på **omstart**. Efter några minuter tillbaka på valda noderna omstart och är online några minuter senare.

Påverkan på klientprogram som varierar beroende på vilka noder att du startar om.

* **Master** - när Huvudnoden är omstartad, Azure Redis-Cache misslyckas till noden replik och främjar till huvudservern. Under den här redundansen kan det finnas ett kort intervall där anslutningar kan misslyckas till cachen.
* **Underordnade** – när den underordnade noden startas påverkas vanligtvis inte till cacheklienter.
* **Huvud- och underordnade** -när både cachenoder startas om försvinner alla data i cacheminnet och anslutningar till cachen misslyckas tills den primära noden är online igen. Om du har konfigurerat [-datapersistence](cache-how-to-premium-persistence.md), den senaste aktuell säkerhetskopia återställs när cachen är online igen, men alla cache-skrivningar som gjorts efter den senaste säkerhetskopieringen går förlorade.
* **Noder i ett premium-cache med aktiverad klustring** – när du startar om en eller flera noder i premium-cache med aktiverad, klustring beteendet för de valda noderna är densamma som när du startar om motsvarande nod eller noder i en icke-klustrade cache.

> [!IMPORTANT]
> Starta om datorn är nu tillgänglig för alla prisnivåer.
> 
> 

## <a name="reboot-faq"></a>Starta om vanliga frågor och svar
* [Vilken nod bör jag startas om för att testa Mina program?](#which-node-should-i-reboot-to-test-my-application)
* [Kan jag startar om cachen om du vill rensa klientanslutningar?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Jag förlorar data från min cachen om jag göra en omstart?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Kan jag startar om min cacheminne med hjälp av PowerShell, CLI eller andra hanteringsverktyg?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
* [Priser för vilka nivåer kan använda funktionen omstart?](#what-pricing-tiers-can-use-the-reboot-functionality)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Vilken nod bör jag startas om för att testa Mina program?
Om du vill testa återhämtningen för ditt program mot fel på den primära noden för ditt cacheminne, starta om den **Master** nod. Om du vill testa återhämtningen för ditt program mot fel på den sekundära noden måste starta om den **slavserver** nod. Om du vill testa återhämtning av programmet mot totalt cachens omstart **både** noder.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Kan jag startar om cachen om du vill rensa klientanslutningar?
Ja, om du startar om cachen alla klientanslutningar avmarkerad. Omstart kan vara användbart i fall där alla klientanslutningar används på grund av ett logiskt fel eller ett programfel i klientprogrammet. Varje prisnivå har olika [klienten anslutningsgränser](cache-configure.md#default-redis-server-configuration) inga fler klientanslutningar accepteras för olika storlekar och när dessa gränser har uppnåtts. Starta om cachen kan du rensa alla klientanslutningar.

> [!IMPORTANT]
> Om du startar om ditt cacheminne om du vill rensa klientanslutningar återansluter StackExchange.Redis automatiskt när Redis-noden är online igen. Om det underliggande problemet kvarstår fortsätta klientanslutningarna för att användas.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Jag förlorar data från min cachen om jag göra en omstart?
Om du startar om både den **Master** och **slavserver** noder, alla data i cacheminnet (eller i den Fragmentera om du använder en premium-cache med aktiverad klustring) går förlorade. Om du har konfigurerat [-datapersistence](cache-how-to-premium-persistence.md), den senaste aktuell säkerhetskopia kommer att återställas när cachen är online igen, men alla cache-skrivningar som har inträffat när säkerhetskopieringen gjordes går förlorade.

Om du startar om en av noderna data är inte vanligtvis förlorade, men det kan fortfarande vara. Till exempel om huvudnoden startas om och en cache-skrivning pågår, data från cache-write är förlorade. Ett annat scenario för dataförlust skulle vara om du startar om en nod och den andra noden händer gå nedåt på grund av ett fel på samma gång. Mer information om möjliga orsaker till förlust av data finns [vad hände med data i Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Kan jag startar om min cacheminne med hjälp av PowerShell, CLI eller andra hanteringsverktyg?
Ja, PowerShell instruktioner finns i [att starta om ett Redis-cache](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Priser för vilka nivåer kan använda funktionen omstart?
Starta om datorn är tillgänglig för alla prisnivåer.

## <a name="schedule-updates"></a>Schemauppdateringar
Den **schemalägga uppdateringar** bladet låter dig ange en underhållsperiod för ditt cacheminne för Premium-nivån. När underhållsfönstret har angetts görs alla uppdateringar för Redis-servern under den här perioden. 

> [!NOTE] 
> Underhållsfönstret gäller endast för Redis-serveruppdateringar och inte till någon Azure-uppdateringar eller uppdateringar för operativsystemet på de virtuella datorerna som är värdar för cachen.
> 
> 

![Schemauppdateringar](./media/cache-administration/redis-schedule-updates.png)

Om du vill ange en underhållsperiod de önskade dagarna och ange Starttimme för underhåll fönster för varje dag och klickar på **OK**. Observera att fönstret Underhåll i UTC. 

> [!NOTE]
> Standardunderhållsfönstret för uppdateringar är fem timmar. Det här värdet kan inte konfigureras från Azure-portalen, men du kan konfigurera i PowerShell med hjälp av den `MaintenanceWindow` parameter för den [ny AzureRmRedisCacheScheduleEntry](/powershell/module/azurerm.rediscache/new-azurermrediscachescheduleentry) cmdlet. Mer information finns i [kan jag hantera schemalagda uppdateringar med hjälp av PowerShell, CLI eller andra hanteringsverktyg?](#can-i-manage-scheduled-updates-using-powershell-cli-or-other-management-tools)
> 
> 

## <a name="schedule-updates-faq"></a>Schemalägg uppdateringar vanliga frågor och svar
* [När uppdateringar, sker om jag inte använder schema-uppdateringar?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Vilken typ av uppdateringar har gjorts under den schemalagda underhållsperioden?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Kan jag hantera schemalagda uppdateringar med hjälp av PowerShell, CLI eller andra hanteringsverktyg?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
* [Priser för vilka nivåer kan använda funktionen för schema-uppdateringar?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>När uppdateringar, sker om jag inte använder schema-uppdateringar?
Om du inte anger en underhållsperiod kan uppdateringar göras när som helst.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Vilken typ av uppdateringar har gjorts under den schemalagda underhållsperioden?
Endast Redis-server uppdateringar har gjorts under den schemalagda underhållsperioden. Underhållsfönstret gäller inte för Azure-uppdateringar eller uppdateringar av operativsystemet VM.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Kan jag hanterade schemalagda uppdateringar med hjälp av PowerShell, CLI eller andra hanteringsverktyg?
Ja, kan du hantera din schemalagda uppdateringar med följande PowerShell-cmdlets:

* [Get-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/get-azurermrediscachepatchschedule)
* [Ny AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/new-azurermrediscachepatchschedule)
* [Ny AzureRmRedisCacheScheduleEntry](/powershell/module/azurerm.rediscache/new-azurermrediscachescheduleentry)
* [Ta bort AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/remove-azurermrediscachepatchschedule)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>Priser för vilka nivåer kan använda funktionen för schema-uppdateringar?
Den **schemalägga uppdateringar** funktionen är endast tillgänglig i premium-prisnivån.

## <a name="next-steps"></a>Nästa steg
* Utforska mer [Azure Redis-Cache premium-nivån](cache-premium-tier-intro.md) funktioner.

