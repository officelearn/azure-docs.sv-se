---
title: 'Azure AD Connect: Så här återställer du från LocalDB 10 GB gränsen problemet | Microsoft Docs'
description: Det här avsnittet beskriver hur du återställer Azure AD Connect-synkroniseringstjänsten när den påträffar en localdb med en 10GB begränsar problemet.
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
ms.openlocfilehash: 194f422c1567103e41f3b39f8510931b1f4762b5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58105190"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: Så här återställer du från LocalDB med en gräns på 10 GB
Azure AD Connect kräver en SQL Server-databas för att lagra identitetsdata. Du kan antingen använda SQL Server 2012 Express LocalDB som är installerat som standard med Azure AD Connect eller använda din egen fullständiga SQL. SQL Server Express har en storleksgräns på 10 GB. När du använder LocalDB och gränsen har uppnåtts kan synkroniseringstjänsten för Azure AD Connect inte längre starta eller synkronisera korrekt. Den här artikeln innehåller steg för återställning.

## <a name="symptoms"></a>Symtom
Det finns två vanliga kännetecken:

* Azure AD Connect-synkroniseringstjänsten **körs** men inte att synkronisera med *”stoppad-database-disken är full”* fel.

* Azure AD Connect-synkroniseringstjänsten **inte starta**. När du försöker starta tjänsten misslyckas med händelsen 6323 och felmeddelande *”servern påträffade ett fel eftersom SQLServer är slut på diskutrymme”.*

## <a name="short-term-recovery-steps"></a>Åtgärder för kortsiktig återställning
Det här avsnittet innehåller steg för att frigöra DB-utrymme som krävs för Azure AD Connect-synkroniseringstjänsten börja fungera igen. Stegen omfattar:
1. [Bestämma tillståndet för synkroniseringstjänsten](#determine-the-synchronization-service-status)
2. [Minska databasen](#shrink-the-database)
3. [Ta bort köra historikdata](#delete-run-history-data)
4. [Förkorta kvarhållningsperioden för körningshistorik för data](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Bestämma tillståndet för synkroniseringstjänsten
Börja med att kontrollera om synkroniseringstjänsten fortfarande körs eller inte:

1. Logga in på din Azure AD Connect-servern som administratör.

2. Gå till **Service Control Manager**.

3. Kontrollera status för **Microsoft Azure AD Sync**.


4. Om den körs inte stoppa eller starta om tjänsten. Hoppa över [krympa databasen](#shrink-the-database) steg och gå till [Delete köra historikdata](#delete-run-history-data) steg.

5. Om den inte körs, försök att starta tjänsten. Om tjänsten startar hoppa över [krympa databasen](#shrink-the-database) steg och gå till [Delete köra historikdata](#delete-run-history-data) steg. I annat fall fortsätter med [krympa databasen](#shrink-the-database) steg.

### <a name="shrink-the-database"></a>Minska databasen
Använd komprimeringsåtgärden igen för att frigöra tillräckligt med diskutrymme för DB att starta synkroniseringstjänsten. Det Frigör DB-utrymme genom att ta bort blanksteg i databasen. Det här steget är mån eftersom det inte är säkert att du alltid återställa utrymme. Om du vill veta mer om komprimeringsåtgärden kan den här artikeln [minska en databas](https://msdn.microsoft.com/library/ms189035.aspx).

> [!IMPORTANT]
> Hoppa över det här steget om du får synkroniseringstjänsten ska köras. Det rekommenderas inte att minska SQL DB, vilket kan leda till dåliga prestanda på grund av ökad fragmentering.

Namnet på den databas som har skapats för Azure AD Connect är **ADSync**. Om du vill utföra en komprimeringsåtgärden, måste du logga in antingen som sysadmin eller databasens DBO. Följande konton har beviljats sysadmin-rättigheter under Azure AD Connect-installationen:
* Lokala administratörer
* Det användarkonto som användes för att köra Azure AD Connect-installationen.
* Synkronisera tjänstkontot som används som kontexten för Azure AD Connect-synkroniseringstjänsten.
* Den lokala gruppen ADSyncAdmins som skapades under installationen.

1. Säkerhetskopiera databasen genom att kopiera **ADSync.mdf** och **ADSync_log.ldf** filer som finns `%ProgramFiles%\Microsoft Azure AD Sync\Data` på en säker plats.

2. Starta en ny PowerShell-session.

3. Navigera till mappen `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn`.

4. Starta **sqlcmd** utility genom att köra kommandot `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>`, med hjälp av autentiseringsuppgifterna för en sysadmin eller DBO-databasen.

5. Att minska databasen på sqlcmd-kommandotolk (1 >), ange `DBCC Shrinkdatabase(ADSync,1);`, följt av `GO` på nästa rad.

6. Om åtgärden lyckas försök starta synkroniseringstjänsten igen. Om du kan starta synkroniseringstjänsten, gå till [Delete köra historikdata](#delete-run-history-data) steg. Om inte, kontakta supporten.

### <a name="delete-run-history-data"></a>Ta bort köra historikdata
Som standard behåller Azure AD Connect upp till sju dagar som körningshistorik för data. I det här steget ska bort vi körningshistoriken-data och frigöra DB-utrymme så att Azure AD Connect-synkroniseringstjänsten kan börja synkronisera igen.

1. Starta **hanteraren för synkroniseringstjänsten** genom att gå till START → synkroniseringstjänsten.

2. Gå till den **Operations** fliken.

3. Under **åtgärder**väljer **Rensa körningar**...

4. Du kan antingen välja **Rensa alla körningar** eller **Clear körs före... <date>**  alternativet. Vi rekommenderar att du börjar genom att avmarkera köra historikdata som är äldre än två dagar. Om du fortfarande stöter på problem i DB storlek, väljer den **Rensa alla körningar** alternativet.

### <a name="shorten-retention-period-for-run-history-data"></a>Förkorta kvarhållningsperioden för körningshistorik för data
Det här steget är att minska sannolikheten för att köra i gräns på 10 GB-problem när du har flera synkroniseringscyklerna.

1. Öppna en ny PowerShell-session.

2. Kör `Get-ADSyncScheduler` och anteckna egenskapen PurgeRunHistoryInterval, som anger den aktuella kvarhållningsperioden.

3. Kör `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` att ställa in kvarhållningsperioden på två dagar. Justera kvarhållningsperioden efter behov.

## <a name="long-term-solution--migrate-to-full-sql"></a>Långsiktiga lösningen – migrera till fullständig SQL
Problemet är i allmänhet är vägledande 10 GB databasstorleken är inte längre räcker för Azure AD Connect att synkronisera din lokala Active Directory till Azure AD. Vi rekommenderar att du istället använda den fullständiga versionen av SQLServer. Du kan inte direkt ersätta en LocalDB i en befintlig Azure AD Connect-distribution med den fullständiga SQL-versionens databas. Istället måste du distribuera en ny Azure AD Connect-server med den fullständiga versionen av SQL. Vi rekommenderar att du gör en swingmigrering med den nya Azure AD Connect-servern (med SQL DB) distribuerad som en mellanlagringsserver bredvid den befintliga Azure AD Connect-servern (med LocalDB). 
* Instruktioner för hur du konfigurerar fjärr-SQL med Azure AD Connect finns i artikeln [Anpassad installation av Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom).
* Information om swingmigrering för Azure AD Connect-uppgradering finns i artikeln [Azure AD Connect: Uppgradera från en tidigare version till den senaste](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration).

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
