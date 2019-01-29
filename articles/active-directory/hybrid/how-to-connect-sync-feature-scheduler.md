---
title: 'Azure AD Connect-synkronisering: Scheduler | Microsoft Docs'
description: Det här avsnittet beskriver funktionen inbyggda scheduler i Azure AD Connect-synkronisering.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 6fe23d0f7597f4739c8588fbb9ee3eb008e1dce5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173708"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Azure AD Connect-synkronisering: Scheduler
Det här avsnittet beskrivs de inbyggda scheduler i Azure AD Connect-synkronisering (kallas även) Synkroniseringsmotorn).

Den här funktionen introducerades med build 1.1.105.0 (publicerad februari 2016).

## <a name="overview"></a>Översikt
Azure AD Connect-synkronisering synkronisera ändringar som sker i din lokala katalog med hjälp av en scheduler. Det finns två sätt för scheduler, en för synkronisering av lösenord och ett för objektattribut/uppgifter för synkronisering och underhåll. Det här avsnittet beskrivs det senare.

I tidigare versioner har scheduler för objekt och attribut utanför Synkroniseringsmotorn. Den används Schemaläggaren i Windows eller en separat Windows-tjänst för att utlösa synkroniseringen. Scheduler är med 1.1 versioner inbyggt i synkroniseringen motorn och låta vissa anpassning. Den nya standard-synkroniseringsfrekvensen är 30 minuter.

Scheduler ansvarar för två saker:

* **Synkroniseringscykel**. Processen för att importera, synkronisera och exportera ändringar.
* **Underhållsaktiviteter**. Förnya nycklar och certifikat för återställning av lösenord och Enhetsregistreringstjänsten (DRS). Rensa gamla posterna i operations-loggen.

Scheduler själva körs alltid, men den kan konfigureras så att endast köra en eller ingen av dessa uppgifter. Om du behöver ha egna cykel synkroniseringsprocessen kan du inaktivera den här aktiviteten i scheduler men fortfarande köra underhållsaktiviteten.

## <a name="scheduler-configuration"></a>Konfiguration av Schemaläggaren
Om du vill se den aktuella konfigurationen, gå till PowerShell och kör `Get-ADSyncScheduler`. Den visar ungefär som den här bilden:

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Om du ser **synkronisering kommandot eller cmdlet: en är inte tillgänglig** när du kör denna cmdlet kan sedan PowerShell-modulen har inte lästs in. Det här problemet kan inträffa om du kör Azure AD Connect på en domänkontrollant eller på en server med PowerShell begränsning högre än standardinställningarna. Om du ser det här felet, kör `Import-Module ADSync` att tillgängliggöra cmdlet: en.

* **AllowedSyncCycleInterval**. Kortast tidsintervall mellan synkronisering cykler som tillåts av Azure AD. Du kan inte synkronisera oftare än den här inställningen och stöds fortfarande.
* **CurrentlyEffectiveSyncCycleInterval**. Schema för närvarande. Den har samma värde som CustomizedSyncInterval (om Ange) om den inte oftare än AllowedSyncInterval. Om du använder en version före 1.1.281 och du ändrar CustomizedSyncCycleInterval, börjar den här ändringen gälla efter nästa synkroniseringscykel. Från build 1.1.281 träder ändringen i kraft omedelbart.
* **CustomizedSyncCycleInterval**. Om du vill att scheduler för att köra med andra frekvens än standardvärdet 30 minuter kan konfigurera du den här inställningen. Scheduler har ställts in att köra varje timme i stället i bilden ovan. Om du ställer in den här inställningen till ett värde mindre än AllowedSyncInterval används det senare.
* **NextSyncCyclePolicyType**. Delta eller den initiala. Anger om nästa körning bör endast processen deltaändringar, eller om nästa körning ska göra en fullständig importera och synkronisera. Det senare skulle också ombearbetning av alla nya eller ändrade regler.
* **NextSyncCycleStartTimeInUTC**. Nästa gång scheduler startar kommer nästa synkroniseringscykel.
* **PurgeRunHistoryInterval**. Den tid som åtgärdsloggar bör hållas. Dessa loggar kan ses i hanteraren för synkroniseringstjänsten. Standardvärdet är att hålla dessa loggar för 7 dagar.
* **SyncCycleEnabled**. Anger om scheduler Kör import, synkronisering och export processer som en del av dess drift.
* **MaintenanceEnabled**. Visar om underhållsprocessen är aktiverad. Den uppdaterar certifikat/nycklar och tar bort operations-loggen.
* **StagingModeEnabled**. Visar om [mellanlagringsläge](how-to-connect-sync-operations.md#staging-mode) är aktiverad. Om den här inställningen är aktiverad, sedan den Undertrycker export från att köras men fortfarande köra import och synkronisering.
* **SchedulerSuspended**. Ange av Connect under en uppgradering till tillfälligt block scheduler från att köras.

Du kan ändra några av de här inställningarna med `Set-ADSyncScheduler`. Du kan ändra följande parametrar:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

I tidigare versioner av Azure AD Connect **isStagingModeEnabled** exponerades i Set-ADSyncScheduler. Det är **stöds inte** att använda den här egenskapen. Egenskapen **SchedulerSuspended** bör endast ändras av Connect. Det är **stöds inte** att konfigurera detta med PowerShell direkt.

Scheduler-konfigurationen lagras i Azure AD. Om du har en mellanlagringsserver påverkar alla ändringar på den primära servern också mellanlagringsserver (utom IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Syntax: `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - dagar, TT - timmar, mm - minuter, ss - sekunder

Exempel: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Ändringar av scheduler för att köra var tredje timme.

Exempel: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Du ändrar scheduler för att köras varje dag.

### <a name="disable-the-scheduler"></a>Inaktivera scheduler  
Om du behöver göra ändringar i konfigurationen vill du inaktivera scheduler. Till exempel när du [filtreringen](how-to-connect-sync-configure-filtering.md) eller [göra ändringar i Synkroniseringsregler](how-to-connect-sync-change-the-configuration.md).

Om du vill inaktivera scheduler kör `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Inaktivera scheduler](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

När du har gjort dina ändringar, Glöm inte att aktivera scheduler igen med `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="start-the-scheduler"></a>Starta scheduler
Scheduler är som standard körs var 30: e minut. I vissa fall kanske du vill köra en synkroniseringscykel mellan schemalagda cykler eller måste du köra en annan typ.

**Delta synkroniseringscykel**  
En delta-synkroniseringscykel omfattar följande steg:

* Deltaimport på alla kopplingar
* Deltasynkronisering på alla kopplingar
* Exportera på alla kopplingar

Det kan bero på att du har en brådskande ändring som måste synkroniseras omedelbart, vilket är anledningen måste du manuellt köra en cykel. Om du vill köra manuellt en cykel sedan från PowerShell kör `Start-ADSyncSyncCycle -PolicyType Delta`.

**Fullständig synkroniseringscykel**  
Om du har gjort något av följande konfigurationsändringar, måste du köra en fullständig synkroniseringscykel (alias) Första):

* Lagt till fler objekt eller attribut som ska importeras från en källkatalog
* Gjort ändringar i synkroniseringsreglerna
* Ändra [filtrering](how-to-connect-sync-configure-filtering.md) så att ett annat antal objekt som ska inkluderas

Om du har gjort någon av dessa ändringar, måste du kör en fullständig synkroniseringscykel Synkroniseringsmotorn har möjlighet att konsolidera kopplingens utrymmen. En fullständig synkroniseringscykel omfattar följande steg:

* Fullständig Import på alla kopplingar
* Fullständig synkronisering på alla kopplingar
* Exportera på alla kopplingar

Om du vill initiera en fullständig synkroniseringscykel kör `Start-ADSyncSyncCycle -PolicyType Initial` från en PowerShell-kommandotolk. Detta kommando startar en fullständig synkroniseringscykel.

## <a name="stop-the-scheduler"></a>Stoppa Schemaläggaren
Om scheduler körs för närvarande en synkroniseringscykel, kan du behöva stoppa den. Till exempel om du startar installationsguiden och du får felet:

![SyncCycleRunningError](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

När en synkroniseringscykel körs, kan du inte göra ändringar i konfigurationen. Vänta tills scheduler har slutfört processen, men du kan också stoppa den så att du kan göra dina ändringar omedelbart. Stoppar den aktuella cykeln är inte skadliga och väntande ändringar bearbetas med nästa körning.

1. Starta genom att tala om scheduler för att stoppa aktuella cykeln med PowerShell-cmdlet `Stop-ADSyncSyncCycle`.
2. Om du använder en version före 1.1.281 stoppar scheduler inte att stoppa den aktuella anslutningen från den aktuella aktiviteten. Om du vill framtvinga anslutningen ska du vidta följande åtgärder: ![StopAConnector](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)
   * Starta **synkroniseringstjänsten** från start-menyn. Gå till **Anslutningsappar**, markera anslutningen med tillståndet **kör**, och välj **stoppa** från åtgärderna.

Scheduler är fortfarande aktiv och börjar igen på nästa tillfälle.

## <a name="custom-scheduler"></a>Anpassade scheduler
Cmdletarna som beskrivs i det här avsnittet är bara tillgängliga i build [1.1.130.0](reference-connect-version-history.md#111300) och senare.

Om inbyggda scheduler inte uppfyller dina krav, kan du schemalägga anslutningarna med hjälp av PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Du kan starta en profil för en koppling i det här sättet:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Namn för [Kopplingsnamn](how-to-connect-sync-service-manager-ui-connectors.md) och [kör Profilnamnen](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) finns i den [Synchronization Service Manager UI](how-to-connect-sync-service-manager-ui.md).

![Anropa Körningsprofil](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

Den `Invoke-ADSyncRunProfile` cmdlet är synkron, det vill säga den inte returnerar kontroll tills anslutningen har slutförts igen, antingen utan problem eller med ett fel.

När du schemalägger dina anslutningar är rekommendationen att schemalägga dem i följande ordning:

1. (Fullständig/Deltasynkronisering) Importera från lokala kataloger, till exempel Active Directory
2. (Fullständig/Deltasynkronisering) Import från Azure AD
3. (Fullständig/Deltasynkronisering) Synkronisering från lokala kataloger, till exempel Active Directory
4. (Fullständig/Deltasynkronisering) Synkronisering från Azure AD
5. Exportera till Azure AD
6. Exportera till lokala kataloger, till exempel Active Directory

Den här ordningen är hur inbyggda scheduler körs anslutningarna.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Du kan också övervaka Synkroniseringsmotorn och se om den är upptagen eller inaktiv. Denna cmdlet returnerar ett tomt resultat om Synkroniseringsmotorn är inaktiv och inte kör en koppling. Om en anslutning körs, returnerar namnet på anslutningen.

```
Get-ADSyncConnectorRunStatus
```

![Körningsstatus för anslutningen](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
I bilden ovan är den första raden från ett tillstånd där Synkroniseringsmotorn är inaktiv. Den andra raden från när du kör Azure AD Connector.

## <a name="scheduler-and-installation-wizard"></a>Guiden Scheduler och installation
Om du startar installationsguiden pausas tillfälligt scheduler. Det här beteendet är där det förutsätts att du gör konfigurationsändringar och de här inställningarna kan inte användas om Synkroniseringsmotorn körs aktivt. Därför inte lämna installationsguiden öppna eftersom Synkroniseringsmotorn från att utföra alla åtgärder som synkroniseringen stoppas.

## <a name="next-steps"></a>Nästa steg
Läs mer om den [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md) konfiguration.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
