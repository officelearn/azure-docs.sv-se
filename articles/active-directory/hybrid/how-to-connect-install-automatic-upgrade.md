---
title: 'Azure AD Connect: Automatisk uppgradering | Microsoft Docs'
description: Det här avsnittet beskrivs de inbyggda automatiska uppgraderingsfunktionen i Azure AD Connect-synkronisering.
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
ms.openlocfilehash: b044edbc24a83aeb98ff603a43f160a6c88d940f
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193355"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: Automatisk uppgradering
Den här funktionen introducerades i version [1.1.105.0 (publicerad februari 2016)](reference-connect-version-history.md#111050).  Den här funktionen har uppdaterats i [skapa 1.1.561](reference-connect-version-history.md#115610) och har nu stöd för fler scenarier som tidigare inte stöds.

## <a name="overview"></a>Översikt
Att se till att din Azure AD Connect-installationen är alltid uppdaterad har aldrig varit enklare med den **automatisk uppgradering** funktionen. Den här funktionen är aktiverad som standard för express-installationer och uppgraderingar av DirSync. När en ny version släpps, uppgraderas automatiskt din installation.
Automatisk uppgradering är aktiverat som standard för följande:

* Express installation inställningar och DirSync-uppgraderingar.
* Använder SQL Express LocalDB, vilket är det standardinställningar alltid använder. DirSync med SQL Express kan också använda LocalDB.
* AD-kontot är det standardkonto MSOL_ som skapats av standardinställningar och DirSync.
* Har mindre än 100 000 objekt i metaversum.

Det aktuella tillståndet för automatisk uppgradering kan visas med PowerShell-cmdleten `Get-ADSyncAutoUpgrade`. Den har följande tillstånd:

| Status | Kommentar |
| --- | --- |
| Enabled |Automatisk uppgradering är aktiverat. |
| Tillfälligt avbruten |Ange endast i systemet. Systemet är **för närvarande inte** berättigade att ta emot automatiska uppgraderingar. |
| Disabled |Automatisk uppgradering har inaktiverats. |

Du kan ändra mellan **aktiverad** och **inaktiverad** med `Set-ADSyncAutoUpgrade`. Endast systemet bör ställa in tillståndet **pausad**.  Innan du 1.1.750.0 skulle cmdlet Set-ADSyncAutoUpgrade blockera Autoupgrade om automatiska uppgraderingar tillståndet var inställd på pausad. Den här funktionen har ändrats så att den inte blockerar AutoUpgrade.

Automatisk uppgradering använder Azure AD Connect Health för uppgradera infrastrukturen. För automatisk uppgradering till fungerar, se till att du har öppnat URL: er i din proxyserver för **Azure AD Connect Health** enligt beskrivningen i [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Om den **hanteraren för synkroniseringstjänsten** Användargränssnittet körs på servern och sedan uppgraderingen pausas tills Användargränssnittet stängs.

## <a name="troubleshooting"></a>Felsökning
Om Connect-installationen inte uppgraderar själva som förväntat, följer du dessa steg för att ta reda på vad kan vara fel.

Först måste du inte kan förvänta automatisk uppgradering till den första dagen som en ny version släpps göras. Det finns en avsiktlig slumpmässighet innan uppgraderingen fortsätter så att inte vara tom om installationen inte uppgraderats omedelbart.

Om du tror att något inte har rätt, först kör `Get-ADSyncAutoUpgrade` Kontrollera automatisk uppgradering är aktiverad.

Kontrollera sedan att du har öppnat de URL: erna i proxyservern eller brandväggen. Automatisk uppdatering använder Azure AD Connect Health enligt beskrivningen i den [översikt](#overview). Om du använder en proxyserver, kontrollera hälsotillstånd har konfigurerats för att använda en [proxyserver](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Också testa den [hälsotillstånd anslutning](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) till Azure AD.

Med anslutningen till Azure AD som har verifierats, är det dags att titta på händelseloggar. Starta Loggboken och titta i den **program** eventlog. Lägg till en händelselogg-filter för källan **Azure AD Connect uppgradera** och händelse-id-intervallet **300-399**.  
![Eventlog-filter för automatisk uppgradering](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

Du kan nu se händelseloggar som är associerade med statusen för automatisk uppgradering.  
![Eventlog-filter för automatisk uppgradering](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

Resultatkoden har ett prefix med en översikt över tillstånd.

| Prefixet resultat | Beskrivning |
| --- | --- |
| Lyckades |Installationen har uppgraderats. |
| UpgradeAborted |Ett tillfälligt tillstånd stoppas uppgraderingen. Det kommer att göras igen och förutsätts att det lyckas senare. |
| UpgradeNotSupported |Systemet har en konfiguration som blockerar systemet från uppgraderas automatiskt. Det görs för att se om tillståndet ändras, men förutsätts att systemet måste uppgraderas manuellt. |

Här är en lista över de vanligaste meddelanden som du hittar. Visar alla inte, men resultatet meddelandet ska vara Rensa med vad problemet är.

| Meddelande om aktiveringsresultat | Beskrivning |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Det gick inte att skriva till registret. |
| UpgradeAbortedInsufficientDatabasePermissions |Den inbyggda administratörsgruppen har inte behörighet till databasen. Uppgradera manuellt till den senaste versionen av Azure AD Connect för att lösa problemet. |
| UpgradeAbortedInsufficientDiskSpace |Det finns inte tillräckligt med diskutrymme för en uppgradering. |
| UpgradeAbortedSecurityGroupsNotPresent |Det gick inte att hitta och åtgärda alla säkerhetsgrupper som används av Synkroniseringsmotorn. |
| UpgradeAbortedServiceCanNotBeStarted |NT-tjänsten **Microsoft Azure AD Sync** kunde inte starta. |
| UpgradeAbortedServiceCanNotBeStopped |NT-tjänsten **Microsoft Azure AD Sync** gick inte att stoppa. |
| UpgradeAbortedServiceIsNotRunning |NT-tjänsten **Microsoft Azure AD Sync** körs inte. |
| UpgradeAbortedSyncCycleDisabled |Alternativet SyncCycle i den [scheduler](how-to-connect-sync-feature-scheduler.md) har inaktiverats. |
| UpgradeAbortedSyncExeInUse |Den [hanteraren för synkroniseringstjänsten Användargränssnittet](how-to-connect-sync-service-manager-ui.md) är öppen på servern. |
| UpgradeAbortedSyncOrConfigurationInProgress |Installationsguiden körs eller en synkronisering var schemalagt utanför scheduler. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | Du har valt AD FS som metod för inloggning. | 
| UpgradeNotSupportedCustomizedSyncRules |Du har lagt till dina egna anpassade regler i konfigurationen. |
| UpgradeNotSupportedDeviceWritebackEnabled |Du har aktiverat den [tillbakaskrivning av enhet](how-to-connect-device-writeback.md) funktion. |
| UpgradeNotSupportedGroupWritebackEnabled |Du har aktiverat den [tillbakaskrivning av grupp](how-to-connect-preview.md#group-writeback) funktionen. |
| UpgradeNotSupportedInvalidPersistedState |Installationen är inte en standardinställningar eller en DirSync-uppgradering. |
| UpgradeNotSupportedMetaverseSizeExceeeded |Du har fler än 100 000 objekt i metaversum. |
| UpgradeNotSupportedMultiForestSetup |Du ansluter till mer än en skog. Snabbinstallation av ansluter bara till en skog. |
| UpgradeNotSupportedNonLocalDbInstall |Du använder inte en SQL Server Express LocalDB-databas. |d
| UpgradeNotSupportedNonMsolAccount |Den [AD DS-anslutningskontot](reference-connect-accounts-permissions.md#ad-ds-connector-account) inte är standardkontot för MSOL_ längre. |
| UpgradeNotSupportedNotConfiguredSignInMethod | När du konfigurerar AAD Connect som du har valt *inte konfigurerar* vid val av inloggnings-metod. | 
| UpgradeNotSupportedPtaSignInMethod | Du har valt direktautentisering som metod för inloggning. |
| UpgradeNotSupportedStagingModeEnabled |Servern har angetts i [mellanlagringsläge](how-to-connect-sync-staging-server.md). |
| UpgradeNotSupportedUserWritebackEnabled |Du har aktiverat den [tillbakaskrivning av användare](how-to-connect-preview.md#user-writeback) funktionen. |

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
