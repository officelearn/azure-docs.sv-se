---
title: 'Azure AD Connect: Så här återställer du från LocalDB 10GB limit issue | Microsoft-dokument'
description: I det här avsnittet beskrivs hur du återställer Azure AD Connect-synkroniseringstjänst när den stöter på problem med LocalDB 10GB-gräns.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d420c64c5834f7d3cb11d2f5f59e3ed85a54891
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60386932"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: Så här återställer du från en LocalDB med en gräns på 10 GB
Azure AD Connect kräver en SQL Server-databas för att lagra identitetsdata. Du kan antingen använda SQL Server 2012 Express LocalDB som är installerat som standard med Azure AD Connect eller använda din egen fullständiga SQL. SQL Server Express har en storleksgräns på 10 GB. När du använder LocalDB och gränsen har uppnåtts kan synkroniseringstjänsten för Azure AD Connect inte längre starta eller synkronisera korrekt. Den här artikeln innehåller återställningsstegen.

## <a name="symptoms"></a>Symtom
Det finns två vanliga symtom:

* Azure AD Connect Synchronization Service **körs** men misslyckas med att synkronisera med *felet "stoppad databas-disk-full".*

* Synkroniseringstjänsten för Azure AD Connect **kan inte starta**. NÃ¤r du fÃ¶rsÃ¶r att starta tjänsten misslyckas den med händelse 6323 och felmeddelandet *Servern påträffades ett fel eftersom SQL Server har på diskutrymme.*

## <a name="short-term-recovery-steps"></a>Kortsiktiga återhämtningssteg
Det här avsnittet innehåller stegen för att frigöra DB-utrymme som krävs för Azure AD Connect-synkroniseringstjänst för att återuppta åtgärden. Stegen omfattar:
1. [Ta reda på synkroniseringstjänstens status](#determine-the-synchronization-service-status)
2. [Förminska databasen](#shrink-the-database)
3. [Ta bort körningshistorikdata](#delete-run-history-data)
4. [Förkorta lagringsperioden för körningshistorikdata](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Ta reda på synkroniseringstjänstens status
Ta först reda på om synkroniseringstjänsten fortfarande körs eller inte:

1. Logga in på Din Azure AD Connect-server som administratör.

2. Gå till **Service Control Manager**.

3. Kontrollera status för **Microsoft Azure AD Sync**.


4. Om den körs ska du inte stoppa eller starta om tjänsten. Hoppa [för Att förminska databassteget](#shrink-the-database) och gå till [Ta bort körningsdatasteg.](#delete-run-history-data)

5. Om den inte körs försöker du starta tjänsten. Om tjänsten startar hoppar du över [Förminska databassteget](#shrink-the-database) och gå till [Ta bort körningsdatasteg.](#delete-run-history-data) Annars fortsätter du med [Krymp databassteget.](#shrink-the-database)

### <a name="shrink-the-database"></a>Förminska databasen
Använd åtgärden Krymp för att frigöra tillräckligt med DB-utrymme för att starta synkroniseringstjänsten. Det frigör DB utrymme genom att ta bort blanksteg i databasen. Detta steg är bäst insats eftersom det inte är garanterat att du alltid kan återställa utrymme. Om du vill veta mer om Shrink-åtgärden läser du den här artikeln [Förminska en databas](https://msdn.microsoft.com/library/ms189035.aspx).

> [!IMPORTANT]
> Hoppa över det här steget om du kan få synkroniseringstjänsten att köras. Det rekommenderas inte att krympa SQL DB eftersom det kan leda till dåliga prestanda på grund av ökad fragmentering.

Namnet på databasen som skapats för Azure AD Connect är **ADSync**. Om du vill utföra en Shrink-åtgärd måste du logga in antingen som databasens sysadmin eller DBO. Under Installationen av Azure AD Connect beviljas följande konton sysadmin-rättigheter:
* Lokala administratörer
* Användarkontot som användes för att köra Azure AD Connect-installation.
* Synkroniseringstjänstkontot som används som driftkontext för Azure AD Connect-synkroniseringstjänst.
* Den lokala gruppen ADSyncAdmins som skapades under installationen.

1. Säkerhetskopiera databasen genom att kopiera **ADSync.mdf-** och **ADSync_log.ldf-filer** som finns under `%ProgramFiles%\Microsoft Azure AD Sync\Data` till en säker plats.

2. Starta en ny PowerShell-session.

3. Navigera till `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn`mappen .

4. Starta **sqlcmd-verktyget** genom `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>`att köra kommandot med hjälp av autentiseringsuppgifterna för ett sysadmin eller databasen DBO.

5. Om du vill förminska databasen, vid sqlcmd-prompten (1>), anger du `DBCC Shrinkdatabase(ADSync,1);`, följt av `GO` i nästa rad.

6. Om åtgärden lyckas försöker du starta synkroniseringstjänsten igen. Om du kan starta synkroniseringstjänsten går du till [Ta bort körningsdatasteg.](#delete-run-history-data) Om inte, kontakta supporten.

### <a name="delete-run-history-data"></a>Ta bort körningshistorikdata
Som standard behåller Azure AD Connect upp till sju dagars körningshistorikdata. I det här steget tar vi bort körningshistorikdata för att frigöra DB-utrymme så att Azure AD Connect-synkroniseringstjänsten kan börja synkroniseras igen.

1. Starta **synkroniseringstjänsthanteraren** genom att gå till START → Synkroniseringstjänsten.

2. Gå till fliken **Operationer.**

3. Under **Åtgärder**väljer du **Rensa körningar**...

4. Du kan antingen välja **Rensa alla körningar** eller Rensa körningar **innan... datum \<>** alternativ. Vi rekommenderar att du börjar med att rensa körningshistorikdata som är äldre än två dagar. Om du fortsätter att stöta på db-storleksproblem väljer du alternativet **Rensa alla körningar.**

### <a name="shorten-retention-period-for-run-history-data"></a>Förkorta lagringsperioden för körningshistorikdata
Det här steget är att minska sannolikheten för att köra in i 10 GB-gränsproblemet efter flera synkroniseringscykler.

1. Öppna en ny PowerShell-session.

2. Kör `Get-ADSyncScheduler` och ta del av egenskapen PurgeRunHistoryInterval, som anger den aktuella kvarhållningsperioden.

3. Kör `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` för att ange kvarhållningsperioden till två dagar. Justera kvarhållningsperioden efter behov.

## <a name="long-term-solution--migrate-to-full-sql"></a>Långsiktig lösning – Migrera till full SQL
I allmänhet är problemet ett tecken på att 10 GB databasstorlek inte längre är tillräcklig för Azure AD Connect för att synkronisera din lokala Active Directory till Azure AD. Vi rekommenderar att du växlar till att använda den fullständiga versionen av SQL-servern. Du kan inte direkt ersätta en LocalDB i en befintlig Azure AD Connect-distribution med den fullständiga SQL-versionens databas. Istället måste du distribuera en ny Azure AD Connect-server med den fullständiga versionen av SQL. Vi rekommenderar att du gör en swingmigrering med den nya Azure AD Connect-servern (med SQL DB) distribuerad som en mellanlagringsserver bredvid den befintliga Azure AD Connect-servern (med LocalDB). 
* Instruktioner för hur du konfigurerar fjärr-SQL med Azure AD Connect finns i artikeln [Anpassad installation av Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom).
* I artikeln [Azure AD Connect: Uppgradera från en tidigare version till den senaste](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration) finns det information om swingmigrering för Azure AD Connect-uppgradering.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
