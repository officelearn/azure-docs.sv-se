---
title: 'Azure AD Connect: Automatisk uppgradering | Microsoft-dokument'
description: I det här avsnittet beskrivs den inbyggda automatiska uppgraderingsfunktionen i Azure AD Connect-synkroniseringen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfd61b78ca3027ade1f2f48dec33e0a8ed508d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60349852"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: Automatisk uppgradering
Den här funktionen introducerades med build [1.1.105.0 (släpptes februari 2016)](reference-connect-version-history.md#111050).  Den här funktionen uppdaterades i [build 1.1.561](reference-connect-version-history.md#115610) och stöder nu ytterligare scenarier som tidigare inte stöddes.

## <a name="overview"></a>Översikt
Att se till att din Azure AD Connect-installation alltid är uppdaterad har aldrig varit enklare med den **automatiska** uppgraderingsfunktionen. Den här funktionen är aktiverad som standard för expressinstallationer och DirSync-uppgraderingar. När en ny version släpps uppgraderas installationen automatiskt.
Automatisk uppgradering är aktiverad som standard för följande:

* Installation av expressinställningar och DirSync-uppgraderingar.
* Använda SQL Express LocalDB, vilket är vad Express-inställningar alltid använder. DirSync med SQL Express använder också LocalDB.
* AD-kontot är standardkontot MSOL_ som skapats av Express-inställningar och DirSync.
* Har mindre än 100 000 objekt i metaversumet.

Det aktuella läget för automatisk uppgradering kan visas `Get-ADSyncAutoUpgrade`med PowerShell-cmdleten . Det har efter påstår:

| Status | Kommentar |
| --- | --- |
| Enabled |Automatisk uppgradering är aktiverad. |
| Inaktiverad |Endast inställt av systemet. Systemet är **för närvarande inte** berättigat till automatiska uppgraderingar. |
| Disabled |Automatisk uppgradering är inaktiverad. |

Du kan växla mellan **Aktiverad** och **Inaktiverad** med `Set-ADSyncAutoUpgrade`. Endast systemet bör ställa in tillståndet **Suspenderat**.  Före 1.1.750.0 skulle cmdlet set-ADSyncAutoUpgrade blockera Autoupgrade om tillståndet för automatisk uppgradering var inställt på Pausad. Den här funktionen har nu ändrats så att den inte blockerar Automatisk registrering.

Automatisk uppgradering använder Azure AD Connect Health för uppgraderingsinfrastrukturen. För automatisk uppgradering av arbetet kontrollerar du att du har öppnat url:erna i proxyservern för **Azure AD Connect Health** som dokumenterats i Office [365-URL:er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Om användargränssnittet för **synkroniseringstjänsthanteraren** körs på servern pausas uppgraderingen tills användargränssnittet stängs.

## <a name="troubleshooting"></a>Felsökning
Om connect-installationen inte uppgraderar sig själv som förväntat följer du dessa steg för att ta reda på vad som kan vara fel.

Först bör du inte förvänta dig att den automatiska uppgraderingen ska göras den första dagen som en ny version släpps. Det finns en avsiktlig slumpmässighet innan en uppgradering görs så bli inte rädd om installationen inte uppgraderas omedelbart.

Om du tror att något inte `Get-ADSyncAutoUpgrade` är rätt, sedan först köra för att säkerställa automatisk uppgradering är aktiverad.

Kontrollera sedan att du har öppnat de webbadresser som krävs i proxyn eller brandväggen. Automatisk uppdatering använder Azure AD Connect Health enligt beskrivningen i [översikten](#overview). Om du använder en proxy kontrollerar du att Hälsa har konfigurerats för att använda en [proxyserver](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Testa även [hälsoanslutningen](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) till Azure AD.

Med anslutningen till Azure AD verifierad är det dags att titta på eventlogs. Starta loggboken och titta i **programhändelseloggen.** Lägg till ett eventlogfilter för källan **Azure AD Connect Upgrade** och händelse-ID-intervallet **300-399**.  
![Händelseloggfilter för automatisk uppgradering](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

Du kan nu se de eventloggs som är associerade med status för automatisk uppgradering.  
![Händelseloggfilter för automatisk uppgradering](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

Resultatkoden har ett prefix med en översikt över tillståndet.

| Prefix för resultatkod | Beskrivning |
| --- | --- |
| Lyckades |Installationen har uppgraderats. |
| UpgradeAborted |Ett tillfälligt villkor stoppade uppgraderingen. Det kommer att prövas igen och förhoppningen är att det lyckas senare. |
| UpgradeNotSupported |Systemet har en konfiguration som blockerar systemet från att uppgraderas automatiskt. Det kommer att göras om för att se om tillståndet förändras, men förhoppningen är att systemet måste uppgraderas manuellt. |

Här är en lista över de vanligaste meddelandena du hittar. Det listar inte alla, men resultatet budskapet bör vara tydlig med vad problemet är.

| Resultatmeddelande | Beskrivning |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Det gick inte att skriva till registret. |
| UpgradeAbortedInsufficientDatabasePermissions |Den inbyggda administratörsgruppen har inte behörighet till databasen. Uppgradera manuellt till den senaste versionen av Azure AD Connect för att lösa problemet. |
| UpgradeAbortedInsufficientDiskSpace |Det finns inte tillräckligt med diskutrymme för att stödja en uppgradering. |
| UpgradeAbortedSecurityGroupsNotPresent |Det gick inte att hitta och lösa alla säkerhetsgrupper som används av synkroniseringsmotorn. |
| UpgradeAbortedServiceCanNotBeStarted |NT-tjänsten **Microsoft Azure AD Sync** kunde inte startas. |
| UpgradeAbortedServiceCanNotBeStopped |NT-tjänsten **Microsoft Azure AD Sync** kunde inte stoppas. |
| UpgradeAbortedServiceIsNotRunning |NT-tjänsten **Microsoft Azure AD Sync** körs inte. |
| UpgradeAbortedSyncCycleDisabled |Alternativet SyncCycle i [schemaläggaren](how-to-connect-sync-feature-scheduler.md) har inaktiverats. |
| UpgradeAbortedSyncExeInUse |[Användargränssnittet för synkroniseringstjänsthanteraren](how-to-connect-sync-service-manager-ui.md) är öppet på servern. |
| UpgradeAbortedSyncOrConfigurationInProgress |Installationsguiden körs eller så har en synkronisering schemalagts utanför schemaläggaren. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | Du har valt Adfs som inloggningsmetod. |
| UpgradeNotSupportedCustomizedSyncRules |Du har lagt till egna anpassade regler i konfigurationen. |
| UpgradeNotSupportedDeviceWritebackEnabled |Du har aktiverat [funktionen för tillbakaskrivning](how-to-connect-device-writeback.md) av enheten. |
| UpgradeNotSupportedGroupWritebackEnabled |Du har aktiverat [funktionen för gruppåterskrivning.](how-to-connect-preview.md#group-writeback) |
| UpgradeNotSupportedInvalidPersistedState |Installationen är inte en Express-inställningar eller en DirSync-uppgradering. |
| UpgradeNotSupportedMetaverseSizeExceeeded |Du har mer än 100 000 objekt i metaversumet. |
| UpgradeNotSupportedMultiForestSetup |Du ansluter till mer än en skog. Expressinställningen ansluter bara till en skog. |
| UpgradeNotSupportedNonLocalDbInstallera |Du använder inte en SQL Server Express LocalDB-databas. |
| UpgradeNotSupportedNonMsolAccount |[AD DS Connector-kontot](reference-connect-accounts-permissions.md#ad-ds-connector-account) är inte längre standardkontot MSOL_. |
| UpgradeNotSupportedNotConfiguredSignInMethod | När du konfigurerar AAD Connect valde du *Konfigurera inte* när du väljer inloggningsmetoden. |
| UpgradeNotSupportedPtaSignInMethod | Du har valt Direktautentisering som inloggningsmetod. |
| UpgradeNotSupportedStagingModeEnabled |Servern är inställd på att vara i [mellanlagringsläge](how-to-connect-sync-staging-server.md). |
| UpgradeNotSupportedUserWritebackEnabled |Du har aktiverat [funktionen för tillbakaskrivning](how-to-connect-preview.md#user-writeback) av användare. |

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
