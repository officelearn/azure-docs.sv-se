---
title: "Azure AD Connect-synkronisering: Schemaläggaren | Microsoft Docs"
description: "Det här avsnittet beskriver funktionen inbyggda Schemaläggaren i Azure AD Connect-synkronisering."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: df7b16157ccb47a5463570c3ed58b9c44f0b67d0
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-scheduler"></a>Azure AD Connect-synkronisering: Schemaläggaren
Det här avsnittet beskrivs de inbyggda Schemaläggaren i Azure AD Connect-synkronisering (kallas även Synkroniseringsmotorn).

Den här funktionen introducerades med build 1.1.105.0 (utgiven februari 2016).

## <a name="overview"></a>Översikt
Azure AD Connect-synkronisering synkronisera ändringar äger rum i din lokala katalog med hjälp av en Schemaläggaren. Det finns två scheduler processer, ett för synkronisering av lösenord och ett annat objekt/attribut synkronisering och underhåll uppgifter. Det här avsnittet beskriver den senare.

I tidigare versioner kunde schemaläggare för objekt och attribut externa för Synkroniseringsmotorn. Den använda Schemaläggaren i Windows eller en separat Windows-tjänst för att utlösa synkroniseringen. Schemaläggaren är 1.1 versioner inbyggt att synkroniseringen motor och tillåter att vissa anpassning. Den nya standard synkroniseringsfrekvensen är 30 minuter.

Schemaläggaren är ansvarig för två aktiviteter:

* **Synkroniseringscykel**. Processen för att importera, synkronisering och exportera ändringar.
* **Underhållsaktiviteter**. Förnya nycklar och certifikat för återställning av lösenord och registreringstjänsten för enheter (DRS). Rensa gamla posterna i operations-loggen.

Schemaläggaren själva körs alltid, men den kan konfigureras så att endast köra en eller inga av dessa uppgifter. Om du behöver ha egna cykel synkroniseringsprocessen kan du inaktivera den här aktiviteten i Schemaläggaren men fortfarande köra underhållsaktiviteten.

## <a name="scheduler-configuration"></a>Konfiguration av Schemaläggaren
Den aktuella konfigurationen, gå till PowerShell och kör `Get-ADSyncScheduler`. Den visar ungefär som den här bilden:

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings2016.png)

Om du ser **sync kommandot eller cmdlet är inte tillgänglig** när du kör denna cmdlet sedan PowerShell-modulen har inte lästs in. Det här problemet kan inträffa om du kör Azure AD Connect på en domänkontrollant eller på en server med PowerShell begränsning högre än standardinställningarna. Om du ser felet kör `Import-Module ADSync` tillgängliggöra cmdlet.

* **AllowedSyncCycleInterval**. Kortaste tidsintervallet mellan synkronisering cykler tillåts av Azure AD. Du kan inte synkronisera oftare än den här inställningen och fortfarande stöds.
* **CurrentlyEffectiveSyncCycleInterval**. Schema för närvarande. Den har samma värde som CustomizedSyncInterval (om Ange) om den inte oftare än AllowedSyncInterval. Om du använder en version innan 1.1.281 och du ändrar CustomizedSyncCycleInterval, träder den här ändringen i kraft efter nästa synkroniseringscykel. Från build 1.1.281 träder ändringen i kraft omedelbart.
* **CustomizedSyncCycleInterval**. Om du vill att jobbschemat ska med andra frekvens än standardvärdet 30 minuter kan konfigurera du den här inställningen. I bilden ovan har Schemaläggaren angetts för att köra varje timme i stället. Om du anger den här inställningen till ett värde mindre än AllowedSyncInterval sedan används den senare.
* **NextSyncCyclePolicyType**. Delta eller inledande. Definierar om nästa körning bör endast processen deltaändringar, eller om nästa körning bör göra en fullständig importera och synkronisera. Det senare skulle Ombearbeta eventuella regler som är nya eller ändrade också.
* **NextSyncCycleStartTimeInUTC**. Nästa gång Schemaläggaren startar nästa synkronisering cykel.
* **PurgeRunHistoryInterval**. Den tid som åtgärdsloggar bör hållas. Dessa loggar kan ses i hanteraren för synkroniseringstjänsten. Standardvärdet är att hålla dessa loggar för 7 dagar.
* **SyncCycleEnabled**. Anger om Schemaläggaren körs import, synkronisering och exportera processer som en del av åtgärden.
* **MaintenanceEnabled**. Visar om underhållsprocessen är aktiverat. Den uppdaterar certifikat/nycklar och tar bort operations-loggen.
* **StagingModeEnabled**. Visar om [mellanlagringsläge](active-directory-aadconnectsync-operations.md#staging-mode) är aktiverad. Om den här inställningen är aktiverad, sedan den undertrycks exporter från att köras men fortfarande köra import och synkronisering.
* **SchedulerSuspended**. Ange genom Anslut under en uppgradering till tillfälligt block Schemaläggaren körs.

Du kan ändra vissa av inställningarna med `Set-ADSyncScheduler`. Följande parametrar kan ändras:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

I tidigare versioner av Azure AD Connect **isStagingModeEnabled** exponerats i Set-ADSyncScheduler. Det är **stöds inte** till den här egenskapen. Egenskapen **SchedulerSuspended** bör endast ändras av Connect. Det är **stöds inte** att direkt ange detta med PowerShell.

Schemaläggarkonfigurationen som lagras i Azure AD. Om du har en fristående server påverkar alla ändringar på den primära servern också testservern (utom IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Syntax: `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - dagar, HH - timmar, mm - minuter och ss - sekunder

Exempel:`Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Ändringar i Schemaläggaren att köra var 3: e timme.

Exempel:`Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Du ändrar Schemaläggaren att köras dagligen.

### <a name="disable-the-scheduler"></a>Inaktivera Schemaläggaren  
Om du behöver göra konfigurationsändringar vill inaktivera Schemaläggaren. Till exempel när du [filtreringen](active-directory-aadconnectsync-configure-filtering.md) eller [göra ändringar i Synkroniseringsregler](active-directory-aadconnectsync-change-the-configuration.md).

Om du vill inaktivera Schemaläggaren kör `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Inaktivera Schemaläggaren](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)

När du har gjort ändringarna Glöm inte att aktivera Schemaläggaren igen med `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="start-the-scheduler"></a>Starta Schemaläggaren
Schemaläggaren är som standard körs var 30: e minut. I vissa fall kanske du vill köra en synkronisering cykel between schemalagda cykler eller måste du köra en annan typ.

**Delta sync cykel**  
En cykel för synkronisering av delta omfattar följande steg:

* Deltaimport på alla kopplingar
* Deltasynkronisering för alla kopplingar
* Exportera alla kopplingar

Det kan bero på att du har en brådskande ändring som måste synkroniseras omedelbart, och därför måste du manuellt köra en cykel. Om du behöver köra manuellt en cykel sedan från PowerShell kör `Start-ADSyncSyncCycle -PolicyType Delta`.

**Fullständig synkronisering**  
Om du har gjort något av följande konfigurationsändringar, måste du köra en fullständig synkronisering cykel (kallas även Inledande):

* Lägga till fler objekt eller attribut som ska importeras från en källkatalog
* Gjort ändringar i regler för synkronisering
* Ändra [filtrering](active-directory-aadconnectsync-configure-filtering.md) så att ett annat antal objekt som ska tas med

Om du har gjort något av dessa ändringar, måste du köra en fullständig synkronisering cykel så att Synkroniseringsmotorn har du möjlighet att konsolidera kopplingens utrymmen. En fullständig synkronisering cykel omfattar följande steg:

* Fullständig Import på alla kopplingar
* Fullständig synkronisering på alla kopplingar
* Exportera alla kopplingar

Om du vill initiera en fullständig synkronisering cykel kör `Start-ADSyncSyncCycle -PolicyType Initial` från en PowerShell-kommandotolk. Detta kommando startar en fullständig synkronisering cykel.

## <a name="stop-the-scheduler"></a>Stoppa Schemaläggaren
Om Schemaläggaren körs för närvarande en synkroniseringscykel, kan du behöva stoppa den. Till exempel om du startar installationsguiden och du får detta fel:

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

När en cykel synkronisering körs, kan du inte göra ändringar i konfigurationen. Du kan vänta tills processen är klar med Schemaläggaren, men du kan också avbryta den så att du kan göra ändringarna direkt. Stoppar den aktuella cykeln är inte skadliga och väntande ändringar bearbetas med nästa körning.

1. Starta genom att ange Schemaläggaren att stoppa den aktuella cykeln med PowerShell-cmdleten `Stop-ADSyncSyncCycle`.
2. Om du använder en version innan 1.1.281 stoppar Schemaläggaren inte att stoppa den aktuella anslutningen från den aktuella aktiviteten. Om du vill tvinga kopplingen för att stoppa vidta följande åtgärder: ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
   * Starta **synkroniseringstjänsten** från start-menyn. Gå till **kopplingar**, markera anslutningen med tillståndet **kör**, och välj **stoppa** från åtgärder.

Schemaläggaren fortfarande är aktivt och påbörjas igen nästa tillfälle.

## <a name="custom-scheduler"></a>Anpassade scheduler
De cmdlets som beskrivs i det här avsnittet är bara tillgängliga i build [1.1.130.0](active-directory-aadconnect-version-history.md#111300) och senare.

Om inbyggda Schemaläggaren inte uppfyller dina krav kan du schemalägga kopplingar med hjälp av PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Du kan starta en profil för en koppling i det här sättet:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Namn för [Kopplingsnamn](active-directory-aadconnectsync-service-manager-ui-connectors.md) och [kör profilnamn](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles) finns i den [Synchronization Service Manager UI](active-directory-aadconnectsync-service-manager-ui.md).

![Starta Körningsprofilen](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

Den `Invoke-ADSyncRunProfile` cmdlet sker synkront, det vill säga den inte returnerar kontrollen tills anslutningen har slutfört åtgärden, utan eller med ett fel.

När du schemalägger kopplingar, vi rekommenderar att du schemalägger dem i följande ordning:

1. (Fullständig/Delta) Importera från lokala kataloger, till exempel Active Directory
2. (Fullständig/Delta) Import från Azure AD
3. (Fullständig/Delta) Synkronisering från lokala kataloger, till exempel Active Directory
4. (Fullständig/Delta) Synkronisering från Azure AD
5. Exportera till Azure AD
6. Exportera till lokala kataloger, till exempel Active Directory

Den här ordningen är hur inbyggda Schemaläggaren körs kopplingar.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Du kan också övervaka Synkroniseringsmotorn och se om det är upptagen eller inaktiv. Denna cmdlet returnerar ett tomt resultat om Synkroniseringsmotorn är inaktiv och inte kör en koppling. Om en koppling körs Returnerar namnet på kopplingen.

```
Get-ADSyncConnectorRunStatus
```

![Kopplingen Körstatus](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
I bilden ovan är den första raden från ett tillstånd där Synkroniseringsmotorn är inaktiv. Den andra raden från när Azure AD Connector körs.

## <a name="scheduler-and-installation-wizard"></a>Guiden Schemaläggaren och installation
Om du startar installationsguiden för inaktiverats Schemaläggaren tillfälligt. Det här beteendet är eftersom det förutsätts att du gör konfigurationsändringar och de här inställningarna kan inte användas om Synkroniseringsmotorn körs aktivt. Därför lämna installationsguiden öppna eftersom stoppas Synkroniseringsmotorn från att utföra några åtgärder för synkronisering.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om den [Azure AD Connect-synkronisering](active-directory-aadconnectsync-whatis.md) konfiguration.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
