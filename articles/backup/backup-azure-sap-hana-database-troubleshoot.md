---
title: Felsöka fel vid säkerhetskopiering av SAP HANA-databaser med Azure Backup | Microsoft Docs
description: Den här guiden beskriver hur du felsöker vanliga fel vid försök att säkerhetskopiera SAP HANA-databaser med hjälp av Azure Backup.
services: backup
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: pullabhk
ms.openlocfilehash: 33f1b4674aad35d55ab014c45cd73753533931cb
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514184"
---
# <a name="troubleshoot-back-up-of-sap-hana-server-on-azure"></a>Felsöka säkerhetskopiering av SAP HANA-servern på Azure

Den här artikeln innehåller felsökningsinformation för skydd av SAP HANA-databaser på Azure Virtual Machines. Innan du fortsätter till felsökning, låt oss se några huvudpunkter om behörigheter och inställningar.

## <a name="understanding-pre-requisites"></a>Förstå förutsättningar

Som en del av [förutsättningar](backup-azure-sap-hana-database.md#prerequisites), före registrering-skriptet ska köras på den virtuella datorn där HANA installeras du ställer in rätt behörigheter.

### <a name="setting-up-permissions"></a>Ställa in behörigheter

Det före registrering skriptet gör:

1. Skapar AZUREWLBACKUPHANAUSER i HANA-System och lägger till nödvändiga roller och behörigheter som anges nedan:
    - DATABAS-ADMIN - skapa nya databaser under återställning
    - KATALOG viktigt – att läsa säkerhetskopieringskatalogen
    - SAP_INTERNAL_HANA_SUPPORT – åtkomst till några privata tabeller
2. Lägger till nyckeln till Hdbuserstore för HANA-plugin-programmet för alla operationer (frågor för databas, konfigurera säkerhetskopiering, säkerhetskopiering, Gör återställning)
   
   - För att bekräfta nycklarna skapas, kör du kommandot HDBSQL i HANA-dator med SIDADM autentiseringsuppgifter:

    ``` hdbsql
    hdbuserstore list
    ```
    
    Kommandoutdata ska visa nyckeln {SID} {%{DBNAME/} med användaren som ”AZUREWLBACKUPHANAUSER”.

> [!NOTE]
> Kontrollera att du har en unik uppsättning SSFS filer under sökvägen ”/ usr/sap/{SID}/home/.hdb/”. Det bör finnas bara en mapp under den här sökvägen.

### <a name="setting-up-backint-parameters"></a>Inställning av BackInt parametrar

När en databas väljs för säkerhetskopiering, konfigurerar tjänsten Azure Backup backInt parametrar på databasnivå.

- [catalog_backup_using_backint:true]
- [enable_accumulated_catalog_backup:false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout:7200]

> [!NOTE]
> Kontrollera att parametrarna inte är tillgänglig vid värdnivå. Värden på parametrar åsidosätter dessa parametrar och kan orsaka annat beteende än förväntat.

## <a name="understanding-common-user-errors"></a>Förstå vanliga användarfel

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det gick inte att ansluta till HANA system.check datorn är igång.| Azure Backup-tjänsten kan inte ansluta till HANA eftersom HANA-Databasobjekt är nere. Eller HANA som körs men tillåter inte Azure Backup-tjänsten att ansluta | Kontrollera om HANA DB/tjänsten inte är igång. Om HANA-DB /-tjänsten är igång, kontrollera om alla behörigheter ställs in som vi redan nämnt [här](#setting-up-permissions). Om nyckeln saknas, kör du skriptet före registrering för att skapa en ny nyckel. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Identifierade ogiltiga Backint konfiguration. Stoppa skyddet och konfigurera om databasen.| Parametrarna backInt är felaktigt angivna för Azure Backup. | Kontrollera parametrarna som vi redan nämnt [här](#setting-up-backint-parameters). Om backInt baserat parametrar finns i värd och sedan ta bort dem. Om parametrar finns inte på värden, men har ändrats manuellt på en databasnivå, sedan återställa dem till lämpliga värden som nämns ovan. Eller 'Avbryt skyddet med kvarhålla data, från Azure portal och 'återuppta säkerhetskopiering' igen.|
