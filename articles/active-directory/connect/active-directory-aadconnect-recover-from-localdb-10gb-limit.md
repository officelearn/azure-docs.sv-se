---
title: "Azure AD Connect: Hur du återställer LocalDB 10 GB gränsen problemet | Microsoft Docs"
description: "Det här avsnittet beskriver hur du återställer Azure AD Connect-synkroniseringstjänsten vid LocalDB 10GB begränsa problemet."
services: active-directory
documentationcenter: 
author: cychua
manager: femila
editor: 
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: billmath
ms.openlocfilehash: 08e682c51b12d4506019d2f6b68e1eae0798b990
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: Så här återställer du från LocalDB 10 GB gränsen
Azure AD Connect kräver en SQL Server-databas för att lagra identitetsdata. Du kan antingen använda SQL Server 2012 Express LocalDB som är installerat som standard med Azure AD Connect eller använda din egen fullständiga SQL. SQL Server Express har en storleksgräns på 10 GB. När du använder LocalDB och gränsen har uppnåtts kan synkroniseringstjänsten för Azure AD Connect inte längre starta eller synkronisera korrekt. Den här artikeln innehåller steg för återställning.

## <a name="symptoms"></a>Symtom
Det finns två vanliga problem:

* Azure AD Connect-synkroniseringstjänsten **körs** men inte kan synkronisera med *”stoppats databas-disk-fullständiga”* fel.

* Azure AD Connect-synkroniseringstjänsten **går inte att starta**. När du försöker starta tjänsten misslyckas med händelse 6323 och felmeddelandet *”servern påträffade ett fel eftersom SQLServer är slut på diskutrymme”.*

## <a name="short-term-recovery-steps"></a>Kortsiktig steg för återställning
Det här avsnittet innehåller steg för att återfå DB-utrymme som krävs för Azure AD Connect-synkroniseringstjänsten att återuppta åtgärden. Stegen omfattar:
1. [Ta reda på status för synkroniseringstjänsten](#determine-the-synchronization-service-status)
2. [Minska databasen](#shrink-the-database)
3. [Ta bort kör historikdata](#delete-run-history-data)
4. [Ange ett kortare kvarhållningsperiod för körningshistorik data](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Ta reda på status för synkroniseringstjänsten
Bestäm först om synkroniseringstjänsten körs fortfarande eller inte:

1. Logga in på Azure AD Connect-servern som administratör.

2. Gå till **Service Control Manager**.

3. Kontrollera status för **Microsoft Azure AD Sync**.


4. Om den körs inte stoppa eller starta om tjänsten. Hoppa över [krympa databasen](#shrink-the-database) steget och gå till [ta bort kör historikdata](#delete-run-history-data) steg.

5. Om den inte körs, försök att starta tjänsten. Om tjänsten startar hoppa över [krympa databasen](#shrink-the-database) steget och gå till [ta bort kör historikdata](#delete-run-history-data) steg. Annars fortsätter du med [krympa databasen](#shrink-the-database) steg.

### <a name="shrink-the-database"></a>Minska databasen
Använd komprimeringsåtgärden för att frigöra tillräckligt med utrymme för DB att starta synkroniseringstjänsten. Det Frigör DB-utrymme genom att ta bort blanksteg i databasen. Det här steget är bästa eftersom det inte är säkert att du alltid återställa utrymme. Om du vill veta mer om komprimeringsåtgärden kan den här artikeln [krympa en databas](https://msdn.microsoft.com/library/ms189035.aspx).

> [!IMPORTANT]
> Hoppa över det här steget om du får synkroniseringstjänsten ska köras. Det rekommenderas inte att krympa SQL-databas som den kan leda till dåliga prestanda på grund av ökade fragmentering.

Namnet på den databas som har skapats för Azure AD Connect är **ADSync**. Om du vill utföra en komprimeringsåtgärden måste du logga in antingen som sysadmin eller DBO av databasen. Följande konton har beviljats sysadmin-rättigheter under installationen av Azure AD Connect:
* Lokala administratörer
* Det användarkonto som användes för att köra Azure AD Connect-installationen.
* Synkronisera tjänstkontot som används som kontexten av Azure AD Connect-synkroniseringstjänsten.
* Den lokala gruppen ADSyncAdmins som skapades under installationen.

1. Säkerhetskopiera databasen genom att kopiera **ADSync.mdf** och **ADSync_log.ldf** filer som finns `%ProgramFiles%\program files\Microsoft Azure AD Sync\Data` på en säker plats.

2. Starta en ny PowerShell-session.

3. Navigera till mappen `%ProgramFiles%\Program Files\Microsoft SQL Server\110\Tools\Binn`.

4. Starta **sqlcmd** utility genom att köra kommandot `./SQLCMD.EXE -S “(localdb)\.\ADSync” -U <Username> -P <Password>`, med hjälp av autentiseringsuppgifter i en sysadmin eller databasens DBO.

5. Att krympa databasen på sqlcmd-kommandotolk (1 >), ange `DBCC Shrinkdatabase(ADSync,1);`, följt av `GO` på nästa rad.

6. Om åtgärden lyckas försök starta synkroniseringstjänsten igen. Om du kan starta synkroniseringstjänsten, gå till [ta bort kör historikdata](#delete-run-history-data) steg. Om inte, kontakta supporten.

### <a name="delete-run-history-data"></a>Ta bort kör historikdata
Som standard behåller Azure AD Connect upp till sju dagar som kör historikdata. I det här steget bort vi körningshistorik data om du vill frigöra utrymme i databasen så att Azure AD Connect-synkroniseringstjänsten kan starta synkronisera igen.

1.  Starta **Synchronization Service Manager** genom att gå till START → synkroniseringstjänsten.

2.  Gå till den **Operations** fliken.

3.  Under **åtgärder**väljer **Rensa körs**...

4.  Du kan antingen välja **avmarkera alla körningar** eller **Rensa körs innan... <date>**  alternativet. Vi rekommenderar att du börjar genom att avmarkera kör historikdata som är äldre än två dagar. Om du fortsätter att köra i DB storlek problemet, välj sedan den **avmarkera alla körningar** alternativet.

### <a name="shorten-retention-period-for-run-history-data"></a>Ange ett kortare kvarhållningsperiod för körningshistorik data
Det här steget är att minska sannolikheten för att köra till 10 GB gränsen problemet efter flera synkroniseringscyklerna.

1. Öppna en ny PowerShell-session.

2. Kör `Get-ADSyncScheduler` och anteckna egenskapen PurgeRunHistoryInterval, som anger den aktuella bevarandeperioden.

3. Kör `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` att ställa in kvarhållningsperioden på två dagar. Justera kvarhållningsperioden efter behov.

## <a name="long-term-solution--migrate-to-full-sql"></a>Långsiktig lösning – migrera till en fullständig SQL
I allmänhet är problemet jämförbar 10 GB-databasens storlek är inte längre tillräckligt för Azure AD Connect att synkronisera din lokala Active Directory till Azure AD. Vi rekommenderar att du istället använda den fullständiga versionen av SQLServer. Du kan inte direkt ersätta en LocalDB i en befintlig Azure AD Connect-distribution med den fullständiga SQL-versionens databas. Istället måste du distribuera en ny Azure AD Connect-server med den fullständiga versionen av SQL. Vi rekommenderar att du gör en swingmigrering med den nya Azure AD Connect-servern (med SQL DB) distribuerad som en mellanlagringsserver bredvid den befintliga Azure AD Connect-servern (med LocalDB). 
* Instruktioner för hur du konfigurerar fjärr-SQL med Azure AD Connect finns i artikeln [Anpassad installation av Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom).
* I artikeln [Azure AD Connect: Uppgradera från en tidigare version till den senaste](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration) finns det information om swingmigrering för Azure AD Connect-uppgradering.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
