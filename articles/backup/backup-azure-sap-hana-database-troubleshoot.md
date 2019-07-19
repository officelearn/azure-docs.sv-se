---
title: Felsök fel vid säkerhets kopiering av SAP HANA databaser med Azure Backup | Microsoft Docs
description: Beskriver hur du felsöker vanliga fel som kan uppstå när du använder Azure Backup för att säkerhetskopiera SAP HANA-databaser.
services: backup
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: pullabhk
ms.openlocfilehash: 32e814ea83f30b48af5ce507ce250f37a34390da
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249496"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Felsöka säkerhets kopiering av SAP HANA databaser på Azure

Den här artikeln innehåller felsöknings information för att säkerhetskopiera SAP HANA databaser på virtuella Azure-datorer.

## <a name="prerequisites"></a>Förutsättningar

Som en del av [förutsättningarna](backup-azure-sap-hana-database.md#prerequisites)kontrollerar du att för registrerings skriptet har körts på den virtuella datorn där Hana är installerat.

### <a name="setting-up-permissions"></a>Konfigurera behörigheter

Vad för registrerings skriptet gör:

1. Skapar AZUREWLBACKUPHANAUSER i HANA-systemet och lägger till de nödvändiga rollerna och behörigheterna:
    - DATABAS administratör: för att skapa nya databaser under återställningen.
    - Katalog läsning: för att läsa säkerhets kopierings katalogen.
    - SAP_INTERNAL_HANA_SUPPORT: för att få åtkomst till några privata tabeller.
2. Lägger till en nyckel till Hdbuserstore för HANA-plugin-programmet för att hantera alla åtgärder (databas frågor, återställnings åtgärder, konfigurera och köra säkerhets kopiering).
   
   Bekräfta att nyckeln skapas genom att köra kommandot HDBSQL på datorn HANA med SIDADM-autentiseringsuppgifter:

    ``` hdbsql
    hdbuserstore list
    ```
    
    Kommandots utdata ska Visa nyckeln {SID} {DBNAME}, där användaren visas som AZUREWLBACKUPHANAUSER.

> [!NOTE]
> Se till att du har en unik uppsättning SSFS-filer under **/usr/SAP/{sid}/Home/.HDB/** . Det får bara finnas en mapp i den här sökvägen.

### <a name="setting-up-backint-parameters"></a>Konfigurera BackInt-parametrar

När en databas har valts för säkerhets kopiering konfigurerar Azure Backups tjänsten backInt-parametrar på databas nivå:

- [catalog_backup_using_backint: true]
- [enable_accumulated_catalog_backup: false]
- [parallel_data_backup_backint_channels: 1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout:7200]

> [!NOTE]
> Se till att dessa parametrar *inte* finns på värdnivå. Parametrar på värdnivå åsidosätter dessa parametrar och kan orsaka oväntade beteenden.

## <a name="common-user-errors"></a>Vanliga användar fel

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

data| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det gick inte att ansluta till HANA-systemet. Kontrol lera att systemet är igång.| Tjänsten Azure Backup kan inte ansluta till HANA eftersom HANA-databasen är nere. Eller HANA körs men tillåter inte att Azure Backups tjänsten ansluter. | Kontrol lera om HANA-databasen eller-tjänsten är nere. Om HANA-databasen eller-tjänsten är igång kontrollerar du om [alla behörigheter är inställda](#setting-up-permissions). Om nyckeln saknas kör du om för registrerings skriptet för att skapa en ny nyckel. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| En ogiltig Backint-konfiguration identifierades. Stoppa skyddet och konfigurera om databasen.| BackInt-parametrarna har felaktigt angetts för Azure Backup. | Kontrol lera om [parametrarna har angetts](#setting-up-backint-parameters). Om det finns backInt-baserade parametrar i värden tar du bort dem. Om parametrarna inte finns på VÄRDnivå men har ändrats manuellt på en databas nivå, återställer du dem till lämpliga värden enligt beskrivningen ovan. Du kan också köra **stoppa skyddet och behålla säkerhets kopierings data** från Azure Portal och sedan välja **återuppta säkerhets kopiering**.|
