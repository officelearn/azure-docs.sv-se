---
title: 'Azure AD Connect: Automatisk uppgradering | Microsoft Docs'
description: "Det här avsnittet beskriver den inbyggda automatiska uppgradering funktionen i Azure AD Connect-synkronisering."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: ddf23193e7b9c2fb7a69045be539841dbb6e6249
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: Automatisk uppgradering
Den här funktionen introducerades med build 1.1.105.0 (utgiven februari 2016).

## <a name="overview"></a>Översikt
Kontrollera att installationen av Azure AD Connect alltid är uppdaterad har aldrig varit enklare med den **automatisk uppgradering** funktion. Den här funktionen är aktiverad som standard för snabb installationer och uppgraderingar av DirSync. När en ny version släpps, uppgraderas automatiskt din installation.
Automatisk uppgradering är aktiverad som standard för följande:

* Express installation inställningar och DirSync uppgraderingar.
* Använda SQL Express LocalDB är vad standardinställningar alltid använda. Också använda DirSync med SQL Express LocalDB.
* AD-kontot är det standardkonto MSOL_ som skapats av standardinställningar och DirSync.
* Har mindre än 100 000 objekt i metaversumet.

Det aktuella tillståndet för automatisk uppgradering kan visas med PowerShell-cmdleten `Get-ADSyncAutoUpgrade`. Det har följande tillstånd:

| Status | Kommentera |
| --- | --- |
| Enabled |Automatisk uppgradering är aktiverat. |
| Tillfälligt avbruten |Ange endast i systemet. Systemet är inte längre behörig att ta emot automatiska uppgraderingar. |
| Disabled |Automatisk uppgradering är inaktiverad. |

Du kan ändra mellan **aktiverad** och **inaktiverad** med `Set-ADSyncAutoUpgrade`. Endast systemet ska ställa in tillståndet **pausad**.

Automatisk uppgradering använder Azure AD Connect Health för uppgradera infrastrukturen. För automatisk uppgradering att fungera, kontrollera att du har öppnat URL: er i din proxyserver för **Azure AD Connect Health** enligt beskrivningen i [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Om den **Synchronization Service Manager** UI körs på servern och sedan uppgraderingen pausas tills Användargränssnittet stängs.

## <a name="troubleshooting"></a>Felsökning
Om Connect-installationen inte uppgraderar sig själv som förväntat, följer du dessa steg för att ta reda på vad kan vara fel.

Du bör först inte automatisk uppgradering till den första dagen som en ny version släpps testas. Det finns en avsiktlig slumpmässighet innan en uppgradering görs så inte vara tom om installationen inte uppgraderat omedelbart.

Om du tycker att något inte är rätt först kör `Get-ADSyncAutoUpgrade` Kontrollera automatisk uppgradering är aktiverad.

Kontrollera sedan att du har öppnat de URL: erna i proxyservern eller brandväggen. Automatisk uppdatering använder Azure AD Connect Health enligt beskrivningen i den [översikt](#overview). Om du använder en proxyserver, kontrollera hälsotillstånd har konfigurerats för att använda en [proxyserver](../connect-health/active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Också testa den [hälsa anslutningen](../connect-health/active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) till Azure AD.

Med anslutningen till Azure AD som verifierats, är det dags att studera händelseloggar. Starta Loggboken och titta i den **programmet** eventlog. Lägga till ett eventlog filter för källan **Azure AD Connect uppgradera** och händelse-id-intervall **300 399**.  
![Eventlog filter för automatisk uppgradering](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)  

Du kan nu se eventlogs som är associerade med status för automatisk uppgradering.  
![Eventlog filter för automatisk uppgradering](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)  

Resultatkod har ett prefix med en översikt över tillståndet.

| Prefixet resultat | Beskrivning |
| --- | --- |
| Lyckades |Installationen har uppgraderats. |
| UpgradeAborted |Ett tillfälligt tillstånd har stoppats uppgraderingen. Den provas igen och förväntningen är det lyckas senare. |
| UpgradeNotSupported |Systemet har en konfiguration som blockerar systemet från uppgraderas automatiskt. Det görs för att se om tillståndet ändras, men förväntningen är att systemet måste uppgraderas manuellt. |

Här är en lista över de vanligaste meddelanden som du hittar. Lista alla inte, men meddelandet resultatet bör vara avmarkera vad problemet är.

| Resultatmeddelande | Beskrivning |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Det gick inte att skriva till registret. |
| UpgradeAbortedInsufficientDatabasePermissions |Den inbyggda administratörsgruppen har inte behörighet till databasen. Uppgradera till den senaste versionen av Azure AD Connect för det här problemet manuellt. |
| UpgradeAbortedInsufficientDiskSpace |Det finns inte tillräckligt med diskutrymme för att stödja en uppgradering. |
| UpgradeAbortedSecurityGroupsNotPresent |Det gick inte att hitta och lösa alla säkerhetsgrupper som används av Synkroniseringsmotorn. |
| UpgradeAbortedServiceCanNotBeStarted |NT-tjänsten **Microsoft Azure AD Sync** kunde inte startas. |
| UpgradeAbortedServiceCanNotBeStopped |NT-tjänsten **Microsoft Azure AD Sync** gick inte att stoppa. |
| UpgradeAbortedServiceIsNotRunning |NT-tjänsten **Microsoft Azure AD Sync** körs inte. |
| UpgradeAbortedSyncCycleDisabled |Alternativet SyncCycle i den [scheduler](active-directory-aadconnectsync-feature-scheduler.md) har inaktiverats. |
| UpgradeAbortedSyncExeInUse |Den [Synkroniseringshanteraren service UI](active-directory-aadconnectsync-service-manager-ui.md) är öppen på servern. |
| UpgradeAbortedSyncOrConfigurationInProgress |Installationsguiden körs eller en synkronisering har schemalagts utanför Schemaläggaren. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | Du har valt AD FS som metod för inloggning. | 
| UpgradeNotSupportedCustomizedSyncRules |Du har lagt till dina egna anpassade regler i konfigurationen. |
| UpgradeNotSupportedDeviceWritebackEnabled |Du har aktiverat den [tillbakaskrivning av enheter](active-directory-aadconnect-feature-device-writeback.md) funktion. |
| UpgradeNotSupportedGroupWritebackEnabled |Du har aktiverat den [tillbakaskrivning av grupp](active-directory-aadconnect-feature-preview.md#group-writeback) funktion. |
| UpgradeNotSupportedInvalidPersistedState |Installationen är inte en standardinställningar eller en uppgradering av DirSync. |
| UpgradeNotSupportedMetaverseSizeExceeeded |Du har mer än 100 000 objekt i metaversumet. |
| UpgradeNotSupportedMultiForestSetup |Du ansluter till mer än en skog. Expressinstallationen ansluter bara till en skog. |
| UpgradeNotSupportedNonLocalDbInstall |Du använder inte en SQL Server Express LocalDB-databas. |
| UpgradeNotSupportedNonMsolAccount |Den [AD-koppling konto](active-directory-aadconnect-accounts-permissions.md#active-directory-account) inte är MSOL_ standardkontot längre. |
| UpgradeNotSupportedNotConfiguredSignInMethod | När du ställer in AAD Connect, som du har valt *konfigurerar inte* när du väljer metod för inloggning. | 
| UpgradeNotSupportedPtaSignInMethod | Du har valt direkt-autentisering som metod för inloggning. |
| UpgradeNotSupportedStagingModeEnabled |Servern är inställd på i [mellanlagringsläge](active-directory-aadconnectsync-operations.md#staging-mode). |
| UpgradeNotSupportedUserWritebackEnabled |Du har aktiverat den [tillbakaskrivning av användare](active-directory-aadconnect-feature-preview.md#user-writeback) funktion. |

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
