---
title: Felsök fel vid säkerhets kopiering av SAP HANA databaser med Azure Backup | Microsoft Docs
description: Beskriver hur du felsöker vanliga fel som kan uppstå när du använder Azure Backup för att säkerhetskopiera SAP HANA-databaser.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2019
ms.author: dacurwin
ms.openlocfilehash: 00e37030417da97d2c57b0fb5872422e7048a2bc
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954458"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Felsöka säkerhets kopiering av SAP HANA databaser på Azure

Den här artikeln innehåller felsöknings information för att säkerhetskopiera SAP HANA databaser på virtuella Azure-datorer. I följande avsnitt beskrivs viktiga konceptuella data som krävs för att diagnostisera vanliga fel i SAP HANA säkerhets kopiering.

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

## <a name="restore-checks"></a>Återställnings kontroller

### <a name="single-container-database-sdc-restore"></a>Återställning av en SDC-databas (Single container Database)

Ta hand om indata när du återställer en enda behållar databas (SDC) för HANA till en annan SDC-dator. Databas namnet måste anges med gemener och "SDC" har lagts till inom hakparenteser. HANA-instansen kommer att visas med versaler.

Anta att en SDC HANA-instans "H21" säkerhets kopie ras. På sidan säkerhets kopierings objekt visas namnet **"H21 (SDC)"** för säkerhets kopierings objekt. Om du försöker återställa databasen till en annan mål SDC, säg H11, måste du ange följande indata.

![SDC Återställ indata](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Observera följande
- Som standard fylls det återställda DB-namnet på med namnet för säkerhets kopian, t. ex. H21 (SDC)
- Om du väljer målet som H11 ändras inte det återställda databas namnet automatiskt. **Den bör redige ras till H11 (SDC)** . I händelse av SDC kommer det återställda DB-namnet att vara mål instans-ID: t med gemena bokstäver och "SDC" i hakparenteser.
- Eftersom SDC bara kan ha en enda databas, måste du också klicka på kryss rutan för att tillåta åsidosättning av befintliga databas data med återställnings punkt data.
- Linux är Skift läges känsligt och säkerställer därför att du bevarar ärendet.

### <a name="multiple-container-database-mdc-restore"></a>Återställning av MDC (Multiple container Database)

I flera container-databaser för HANA är standard konfigurationen SYSTEMDB + 1 eller flera klient-databaser. Att återställa en hel SAP HANA instans innebär att återställa både SYSTEMDB-och klient databaser. En återställer SYSTEMDB först och fortsätter sedan för klient organisations databasen. System DB innebär i princip att åsidosätta system informationen på det valda målet. Detta åsidosätter även BackInt-relaterad information i mål instansen. När system databasen har återställts till en mål instans måste därför en köra skriptet för för registrering igen. Det går bara att återställa efterföljande klient databas återställningar.

## <a name="common-user-errors"></a>Vanliga användar fel

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

data| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det gick inte att ansluta till HANA-systemet. Kontrol lera att systemet är igång.| Tjänsten Azure Backup kan inte ansluta till HANA eftersom HANA-databasen är nere. Eller HANA körs men tillåter inte att Azure Backups tjänsten ansluter. | Kontrol lera om HANA-databasen eller-tjänsten är nere. Om HANA-databasen eller-tjänsten är igång kontrollerar du om [alla behörigheter är inställda](#setting-up-permissions). Om nyckeln saknas kör du om för registrerings skriptet för att skapa en ny nyckel. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| En ogiltig Backint-konfiguration identifierades. Stoppa skyddet och konfigurera om databasen.| BackInt-parametrarna har felaktigt angetts för Azure Backup. | Kontrol lera om [parametrarna har angetts](#setting-up-backint-parameters). Om det finns backInt-baserade parametrar i värden tar du bort dem. Om parametrarna inte finns på VÄRDnivå men har ändrats manuellt på en databas nivå, återställer du dem till lämpliga värden enligt beskrivningen ovan. Du kan också köra **stoppa skyddet och behålla säkerhets kopierings data** från Azure Portal och sedan välja **återuppta säkerhets kopiering**.|