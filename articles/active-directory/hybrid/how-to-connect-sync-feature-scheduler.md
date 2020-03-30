---
title: 'Synkronisering av Azure AD Connect: Scheduler | Microsoft-dokument'
description: I det här avsnittet beskrivs den inbyggda scheduler-funktionen i Azure AD Connect-synkronisering.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 309adfbebd4f4b615ac1f4061823ca01f3d3ee15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261078"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Azure AD Connect sync: Scheduler
I det här avsnittet beskrivs den inbyggda schemaläggaren i Azure AD Connect-synkronisering (synkroniseringsmotor).

Den här funktionen introducerades med build 1.1.105.0 (släpptes februari 2016).

## <a name="overview"></a>Översikt
Azure AD Connect-synkronisering synkroniserar ändringar som sker i din lokala katalog med hjälp av en schemaläggare. Det finns två schemaläggarprocesser, en för lösenordssynkronisering och en annan för synkronisering och underhåll av objekt/attribut. Detta ämne täcker det senare.

I tidigare versioner var schemaläggaren för objekt och attribut externa till synkroniseringsmotorn. Den använde Windows schemaläggare eller en separat Windows-tjänst för att utlösa synkroniseringsprocessen. Schemaläggaren är med 1.1-utgåvorna inbyggda i synkroniseringsmotorn och tillåter viss anpassning. Den nya standardsynkroniseringsfrekvensen är 30 minuter.

Schemaläggaren ansvarar för två uppgifter:

* **Synkroniseringscykel**. Processen för att importera, synkronisera och exportera ändringar.
* **Underhållsuppgifter**. Förnya nycklar och certifikat för återställning av lösenord och en enhetsregistreringstjänst (DRS). Rensa gamla poster i operationsloggen.

Schemaläggaren själv körs alltid, men den kan konfigureras för att bara köra en eller ingen av dessa aktiviteter. Om du till exempel behöver ha en egen synkroniseringscykelprocess kan du inaktivera den här aktiviteten i schemaläggaren men ändå köra underhållsaktiviteten.

## <a name="scheduler-configuration"></a>Konfiguration av Schemaläggaren
Om du vill se dina aktuella `Get-ADSyncScheduler`konfigurationsinställningar går du till PowerShell och kör . Det visar dig något i stil med den här bilden:

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Om **synkroniseringskommandot eller cmdlet inte är tillgängligt** när du kör den här cmdleten läses inte PowerShell-modulen in. Det här problemet kan inträffa om du kör Azure AD Connect på en domänkontrollant eller på en server med högre PowerShell-begränsningsnivåer än standardinställningarna. Om det här felet `Import-Module ADSync` visas körs du för att göra cmdleten tillgänglig.

* **AllowedSyncCycleInterval**. Det kortaste tidsintervallet mellan synkroniseringscykler som tillåts av Azure AD. Du kan inte synkronisera oftare än den här inställningen och fortfarande stödjas.
* **För närvarandeeffektsynkvävningInterval**. Schemat gäller för närvarande. Den har samma värde som CustomizedSyncInterval (om den är inställd) om den inte är vanligare än AllowedSyncInterval. Om du använder en version före 1.1.281 och ändrar CustomizedSyncCycleInterval börjar den här ändringen gälla efter nästa synkroniseringscykel. Från bygget 1.1.281 träder ändringen i kraft omedelbart.
* **CustomizedSyncCycleInterval**. Om du vill att schemaläggaren ska köras med någon annan frekvens än standard 30 minuter konfigurerar du den här inställningen. I bilden ovan har schemaläggaren ställts in för att köras varje timme i stället. Om du ställer in den här inställningen på ett värde som är lägre än AllowedSyncInterval används den senare.
* **NextSyncCyclePolicyType**. Antingen Delta eller Initial. Definierar om nästa körning endast ska bearbeta deltaändringar, eller om nästa körning ska göra en fullständig import och synkronisering. Den senare skulle också omarbeta alla nya eller ändrade regler.
* **NextSyncCycleStartTimeInUTC**. Nästa gång schemaläggaren startar nästa synkroniseringscykel.
* **PurgeRunHistoryInterval**. Tidsoperationsloggarna bör behållas. Dessa loggar kan granskas i synkroniseringstjänsthanteraren. Standard är att behålla dessa loggar i 7 dagar.
* **SyncCycleEnabled**. Anger om schemaläggaren kör import-, synkroniserings- och exportprocesserna som en del av åtgärden.
* **UnderhållEnbar .** Visar om underhållsprocessen är aktiverad. Certifikaten/nycklarna uppdateras och operationsloggen rensas.
* **StagingModeEnabled**. Visar om [mellanlagringsläget](how-to-connect-sync-staging-server.md) är aktiverat. Om den här inställningen är aktiverad, så ignoreras exporten från att köras men ändå köra import och synkronisering.
* **SchedulerSuspended**. Ställ in med Connect under en uppgradering för att tillfälligt blockera schemaläggaren från att köras.

Du kan ändra några `Set-ADSyncScheduler`av dessa inställningar med . Följande parametrar kan ändras:

* AnpassadSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* UnderhållEnbar

I tidigare versioner av Azure AD Connect har **isStagingModeEnabled exponerats** i Set-ADSyncScheduler. Det **finns inte stöd** för att ange den här egenskapen. Egenskapen **SchedulerSuspended** ska endast ändras av Connect. Det stöds **inte** att ställa in detta med PowerShell direkt.

Schemaläggarkonfigurationen lagras i Azure AD. Om du har en mellanlagringsserver påverkar alla ändringar på den primära servern också mellanlagringsservern (förutom IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>AnpassadSyncCycleInterval
Syntax:`Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - dagar, HH - timmar, mm - minuter, ss - sekunder

Exempel: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Ändrar schemaläggaren så att den körs var tredje timme.

Exempel: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Ändringar ändrar schemaläggaren så att den körs dagligen.

### <a name="disable-the-scheduler"></a>Inaktivera schemaläggaren  
Om du behöver göra konfigurationsändringar vill du inaktivera schemaläggaren. När du till exempel [konfigurerar filtrering](how-to-connect-sync-configure-filtering.md) eller [gör ändringar i synkroniseringsregler](how-to-connect-sync-change-the-configuration.md).

Om du vill inaktivera `Set-ADSyncScheduler -SyncCycleEnabled $false`schemaläggaren kör du .

![Inaktivera schemaläggaren](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

När du har gjort ändringarna, glöm inte att aktivera `Set-ADSyncScheduler -SyncCycleEnabled $true`schemaläggaren igen med .

## <a name="start-the-scheduler"></a>Starta schemaläggaren
Schemaläggaren körs som standard var 30:e minut. I vissa fall kanske du vill köra en synkroniseringscykel mellan de schemalagda cyklerna eller köra en annan typ.

### <a name="delta-sync-cycle"></a>Delta-synkroniseringscykel
En deltasynkroniseringscykel innehåller följande steg:


- Delta-import på alla kopplingar
- Delta-synkronisering på alla kontakter
- Exportera på alla kopplingar

### <a name="full-sync-cycle"></a>Fullständig synkroniseringscykel
En fullständig synkroniseringscykel innehåller följande steg:

- Fullständig import på alla kopplingar
- Fullständig synkronisering på alla kontakter
- Exportera på alla kopplingar

Det kan vara så att du har en brådskande ändring som måste synkroniseras omedelbart, vilket är anledningen till att du måste köra en cykel manuellt. 

Om du behöver köra en synkroniseringscykel manuellt, `Start-ADSyncSyncCycle -PolicyType Delta`sedan från PowerShell kör .

Om du vill starta `Start-ADSyncSyncCycle -PolicyType Initial` en fullständig synkroniseringscykel körs du från en PowerShell-prompt.   

Köra en fullständig synkroniseringscykel kan vara mycket tidskrävande, läs nästa avsnitt för att läsa hur du optimerar den här processen.

### <a name="sync-steps-required-for-different-configuration-changes"></a>Synkroniseringssteg som krävs för olika konfigurationsändringar
Olika konfigurationsändringar kräver olika synkroniseringssteg för att säkerställa att ändringarna tillämpas korrekt på alla objekt.

- Lade till fler objekt eller attribut som ska importeras från en källkatalog (genom att lägga till/ändra synkroniseringsreglerna)
    - En fullständig import krävs på kopplingen för den källkatalogen
- Har gjort ändringar i synkroniseringsreglerna
    - En fullständig synkronisering krävs på kopplingen för de ändrade synkroniseringsreglerna
- Ändrad [filtrering](how-to-connect-sync-configure-filtering.md) så att ett annat antal objekt ska inkluderas
    - En fullständig import krävs på kopplingen för varje AD-anslutning om du inte använder attributbaserad filtrering baserat på attribut som redan importeras till synkroniseringsmotorn

### <a name="customizing-a-sync-cycle-run-the-right-mix-of-delta-and-full-sync-steps"></a>Anpassa en synkroniseringscykel köra rätt blandning av delta- och fullständig synkroniseringssteg
För att undvika att köra en fullständig synkroniseringscykel kan du markera specifika kopplingar för att köra ett helsteg med hjälp av följande cmdlets.

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullSyncRequired $true`

`Get-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid>` 

Exempel: Om du har gjort ändringar i synkroniseringsreglerna för Connector "AD Forest A" som inte kräver att några nya attribut importeras kör du följande cmdlets för att köra en deltasynkroniseringscykel som också gjorde ett fullständigt synkroniseringssteg för den kopplingen.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`

Exempel: Om du har gjort ändringar i synkroniseringsreglerna för Connector "AD Forest A" så att de nu kräver att ett nytt attribut importeras kör du följande cmdlets för att köra en deltasynkroniseringscykel som också gjorde ett fullständigt importsteg, fullständig synkronisering för den kopplingen.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`


## <a name="stop-the-scheduler"></a>Stoppa schemaläggaren
Om schemaläggaren för närvarande kör en synkroniseringscykel kan du behöva stoppa den. Till exempel om du startar installationsguiden och du får det här felet:

![SyncCycleRunningError](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

När en synkroniseringscykel körs kan du inte göra konfigurationsändringar. Du kan vänta tills schemaläggaren har avslutat processen, men du kan också stoppa den så att du kan göra ändringarna direkt. Att stoppa den aktuella cykeln är inte skadligt och väntande ändringar bearbetas med nästa körning.

1. Börja med att tala om för schemaläggaren att stoppa `Stop-ADSyncSyncCycle`den aktuella cykeln med PowerShell-cmdleten .
2. Om du använder en version före 1.1.281 stoppas inte den aktuella kopplingen från den aktuella aktiviteten om du stoppar schemaläggaren. För att tvinga kopplingen att stoppa, ![vidta följande åtgärder: StopAConnector](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)
   * Starta **synkroniseringstjänsten** från start-menyn. Gå till **Kopplingar**, markera kopplingen med läget **Körs**och välj **Stoppa** från åtgärderna.

Schemaläggaren är fortfarande aktiv och startar igen vid nästa tillfälle.

## <a name="custom-scheduler"></a>Anpassad schemaläggare
De cmdlets som dokumenteras i det här avsnittet är endast tillgängliga i bygget [1.1.130.0](reference-connect-version-history.md#111300) och senare.

Om den inbyggda schemaläggaren inte uppfyller dina krav kan du schemalägga kontakterna med PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Anropa-ADSyncRunProfile
Du kan starta en profil för en koppling på det här sättet:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Namnen som ska användas för [anslutningsnamn](how-to-connect-sync-service-manager-ui-connectors.md) och [Kör profilnamn](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) finns i [användargränssnittet för Synkroniseringstjänsthanteraren](how-to-connect-sync-service-manager-ui.md).

![Anropa körprofil](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

Cmdleten `Invoke-ADSyncRunProfile` är synkron, det vill än, den returnerar inte kontrollen förrän kopplingen har slutfört åtgärden, antingen framgångsrikt eller med ett fel.

När du schemalägger dina kopplingar är rekommendationen att schemalägga dem i följande ordning:

1. (Full/Delta) Importera från lokala kataloger, till exempel Active Directory
2. (Full/Delta) Importera från Azure AD
3. (Full/Delta) Synkronisering från lokala kataloger, till exempel Active Directory
4. (Full/Delta) Synkronisering från Azure AD
5. Exportera till Azure AD
6. Exportera till lokala kataloger, till exempel Active Directory

Den här ordningen är hur den inbyggda schemaläggaren kör kopplingarna.

### <a name="get-adsyncconnectorrunstatus"></a>Hämta-ADSyncConnectorRunStatus
Du kan också övervaka synkroniseringsmotorn för att se om den är upptagen eller inaktiv. Den här cmdleten returnerar ett tomt resultat om synkroniseringsmotorn är inaktiv och inte kör en koppling. Om en koppling körs returneras namnet på kopplingen.

```
Get-ADSyncConnectorRunStatus
```

![Status för kopplingskörning](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
I bilden ovan är den första raden från ett tillstånd där synkroniseringsmotorn är inaktiv. Den andra raden från när Azure AD Connector körs.

## <a name="scheduler-and-installation-wizard"></a>Guiden Schemaläggare och installation
Om du startar installationsguiden pausas schemaläggaren tillfälligt. Det här problemet beror på att det antas att du gör konfigurationsändringar och dessa inställningar kan inte tillämpas om synkroniseringsmotorn körs aktivt. Lämna därför inte installationsguiden öppen eftersom synkroniseringsmotorn inte fungerar.

## <a name="next-steps"></a>Nästa steg
Läs mer om synkroniseringskonfigurationen för [Azure AD Connect.](how-to-connect-sync-whatis.md)

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
