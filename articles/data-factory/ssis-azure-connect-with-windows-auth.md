---
title: Åtkomst till data lager och fil resurser med Windows-autentisering
description: Lär dig hur du konfigurerar SSIS-katalogen i Azure SQL Database och Azure-SSIS Integration Runtime i Azure Data Factory för att köra paket som har åtkomst till data lager och fil resurser med Windows-autentisering.
ms.date: 3/22/2018
ms.topic: conceptual
ms.prod: sql
ms.prod_service: integration-services
ms.custom: ''
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 6f2983b375e3eeb73a0372e123d4d2763b3c65ec
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629396"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>Komma åt datalager och filresurser med Windows-autentisering från SSIS-paket i Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Du kan använda Windows-autentisering för att komma åt data lager, till exempel SQL-servrar, fil resurser, Azure Files osv. från SSIS-paket som körs på din Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF). Dina data lager kan finnas lokalt, som finns på Azure Virtual Machines (VM) eller som körs i Azure som hanterade tjänster. Om de är lokalt måste du ansluta till din Azure-SSIS IR till en Virtual Network (Microsoft Azure Virtual Network) som är ansluten till ditt lokala nätverk, se [ansluta Azure-SSIS IR till en Microsoft Azure Virtual Network](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Det finns fyra metoder för att komma åt data lager med Windows-autentisering från SSIS-paket som körs på din Azure-SSIS IR:

| Anslutningsmetod | Gällande omfattning | Installations steg | Åtkomst metod i paket | Antal autentiseringsuppgifter och anslutna resurser | Typ av anslutna resurser | 
|---|---|---|---|---|---|
| Konfigurera en körnings kontext på aktivitets nivå | Aktivitet per kör SSIS-paket | Konfigurera **Windows-autentiseringsmetoden** för att ställa in en "körning/kör som"-kontext när du kör SSIS-paket som kör SSIS-paket aktiviteter i ADF-pipeline.<br/><br/> Mer information finns i [Konfigurera körning av SSIS-paket-aktivitet](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). | Åtkomst till resurser direkt i paket via UNC-sökväg, till exempel om du använder fil resurser eller Azure Files `\\YourFileShareServerName\YourFolderName` : eller`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Stöd endast för en autentiseringsuppgift för alla anslutna resurser | -Fil resurser lokalt/virtuella Azure-datorer<br/><br/> – Azure Files finns i [använda en Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> -SQL-servrar lokalt/virtuella Azure-datorer med Windows-autentisering<br/><br/> -Andra resurser med Windows-autentisering |
| Konfigurera en körnings kontext på katalog nivå | Per Azure-SSIS IR, men åsidosätts när du konfigurerar en körnings kontext på aktivitets nivå (se ovan) | Kör den `catalog.set_execution_credential` lagrade proceduren SSISDB för att konfigurera en kontext för körning/körning som.<br/><br/> Mer information finns i resten av den här artikeln nedan. | Åtkomst till resurser direkt i paket via UNC-sökväg, till exempel om du använder fil resurser eller Azure Files `\\YourFileShareServerName\YourFolderName` : eller`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Stöd endast för en autentiseringsuppgift för alla anslutna resurser | -Fil resurser lokalt/virtuella Azure-datorer<br/><br/> – Azure Files finns i [använda en Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> -SQL-servrar lokalt/virtuella Azure-datorer med Windows-autentisering<br/><br/> -Andra resurser med Windows-autentisering |
| Spara autentiseringsuppgifter via `cmdkey` kommando | Per Azure-SSIS IR, men åsidosätts när du konfigurerar en aktivitet/katalog nivå körnings kontext (se ovan) | Kör `cmdkey` kommandot i ett anpassat installations skript (`main.cmd`) när du konfigurerar Azure-SSIS IR, t. ex. om du använder fil resurser eller Azure Files: `cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword` eller. `cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey`<br/><br/> Mer information finns i [Anpassa installations programmet för Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). | Åtkomst till resurser direkt i paket via UNC-sökväg, till exempel om du använder fil resurser eller Azure Files `\\YourFileShareServerName\YourFolderName` : eller`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Stöd för flera uppsättningar av autentiseringsuppgifter för olika anslutna resurser | -Fil resurser lokalt/virtuella Azure-datorer<br/><br/> – Azure Files finns i [använda en Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> -SQL-servrar lokalt/virtuella Azure-datorer med Windows-autentisering<br/><br/> -Andra resurser med Windows-autentisering |
| Montera enheter i paketets körnings tid (icke-permanent) | Per paket | Kör `net use` kommandot i aktiviteten Kör process som läggs till i början av kontroll flödet i dina paket, till exempel`net use D: \\YourFileShareServerName\YourFolderName` | Åtkomst till fil resurser via mappade enheter | Stöd för flera enheter för olika fil resurser | -Fil resurser lokalt/virtuella Azure-datorer<br/><br/> – Azure Files finns i [använda en Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) |
|||||||

> [!WARNING]
> Om du inte använder någon av metoderna ovan för att komma åt data lager med Windows-autentisering, kan dina paket som är beroende av Windows-autentisering inte komma åt dem och inte köras vid körning. 

Resten av den här artikeln beskriver hur du konfigurerar SSIS-katalogen (SSISDB) som finns i Azure SQL Database Server/hanterad instans för att köra paket på Azure-SSIS IR som använder Windows-autentisering för att få åtkomst till data lager. 

## <a name="you-can-only-use-one-set-of-credentials"></a>Du kan bara använda en uppsättning autentiseringsuppgifter

När du använder Windows-autentisering i ett SSIS-paket kan du bara använda en uppsättning autentiseringsuppgifter. De domänautentiseringsuppgifter som du anger när du följer stegen i den här artikeln gäller alla paket körningar – interaktiva eller schemalagda på din Azure-SSIS IR tills du ändrar eller tar bort dem. Om ditt paket måste ansluta till flera data lager med olika uppsättningar autentiseringsuppgifter, bör du överväga ovanstående alternativa metoder.

## <a name="provide-domain-credentials-for-windows-authentication"></a>Ange domänautentiseringsuppgifter för Windows-autentisering

Gör så här för att ange domänautentiseringsuppgifter som tillåter att paket använder Windows-autentisering för att komma åt data lager lokalt:

1. Med SQL Server Management Studio (SSMS) eller något annat verktyg ansluter du till Azure SQL Database Server/hanterad instans som är värd för SSISDB. Mer information finns i [ansluta till SSISDB i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Öppna ett frågefönster med SSISDB som den aktuella databasen.

3. Kör följande lagrade procedur och ange lämpliga domänautentiseringsuppgifter:

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. Kör dina SSIS-paket. Paketen använder de autentiseringsuppgifter som du har angett för att komma åt data lager lokalt med Windows-autentisering.

### <a name="view-domain-credentials"></a>Visa domänautentiseringsuppgifter

Gör så här om du vill visa de aktiva domänautentiseringsuppgifter:

1. Med SSMS eller något annat verktyg ansluter du till Azure SQL Database Server/hanterad instans som är värd för SSISDB. Mer information finns i [ansluta till SSISDB i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Öppna ett frågefönster med SSISDB som den aktuella databasen.

3. Kör följande lagrade procedur och kontrol lera utdata:

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>Rensa domänautentiseringsuppgifter
Om du vill rensa och ta bort de autentiseringsuppgifter som du angav enligt beskrivningen i den här artikeln gör du följande:

1. Med SSMS eller något annat verktyg ansluter du till Azure SQL Database Server/hanterad instans som är värd för SSISDB. Mer information finns i [ansluta till SSISDB i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Öppna ett frågefönster med SSISDB som den aktuella databasen.

3. Kör följande lagrade procedur:

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>Ansluta till en lokal SQL Server

Om du vill kontrol lera om du kan ansluta till en SQL Server lokalt, gör du följande:

1. Om du vill köra det här testet söker du efter en dator som inte är domänansluten.

2. Kör följande kommando på den icke-domänanslutna datorn för att starta SSMS med de domänautentiseringsuppgifter som du vill använda:

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. Från SSMS kontrollerar du om du kan ansluta till SQL Server lokalt.

### <a name="prerequisites"></a>Krav

Om du vill komma åt en SQL Server lokalt från paket som körs i Azure gör du följande:

1.  I Konfigurationshanteraren för SQL Server aktiverar du TCP/IP-protokollet.

2. Tillåt åtkomst via Windows-brandväggen. Mer information finns i [Konfigurera Windows-brandväggen för att få åtkomst till SQL Server](https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access).

3. Anslut din Azure-SSIS IR till en Microsoft Azure Virtual Network som är ansluten till SQL Server lokalt.  Mer information finns i [koppla Azure-SSIS IR till en Microsoft Azure Virtual Network](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

4. Använd den `catalog.set_execution_credential` lagrade proceduren SSISDB för att ange autentiseringsuppgifter enligt beskrivningen i den här artikeln.

## <a name="connect-to-a-file-share-on-premises"></a>Anslut till en fil resurs lokalt

Om du vill kontrol lera om du kan ansluta till en fil resurs lokalt gör du följande:

1. Om du vill köra det här testet söker du efter en dator som inte är domänansluten.

2. Kör följande kommandon på den icke-domänanslutna datorn. Dessa kommandon öppnar ett kommando tolks fönster med de domänautentiseringsuppgifter som du vill använda och testar sedan anslutningen till fil resursen lokalt genom att hämta en katalog lista.

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. Kontrol lera om katalog listan returneras för fil resursen lokalt.

### <a name="prerequisites"></a>Krav

Gör så här för att få åtkomst till en fil resurs lokalt från paket som körs i Azure:

1. Tillåt åtkomst via Windows-brandväggen.

2. Anslut Azure-SSIS IR till en Microsoft Azure Virtual Network som är ansluten till fil resursen lokalt.  Mer information finns i [koppla Azure-SSIS IR till en Microsoft Azure Virtual Network](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

3. Använd den `catalog.set_execution_credential` lagrade proceduren SSISDB för att ange autentiseringsuppgifter enligt beskrivningen i den här artikeln.

## <a name="connect-to-a-file-share-on-azure-vm"></a>Anslut till en fil resurs på en virtuell Azure-dator

Gör så här för att få åtkomst till en fil resurs på en virtuell Azure-dator från paket som körs i Azure:

1. Med SSMS eller något annat verktyg ansluter du till Azure SQL Database Server/hanterad instans som är värd för SSISDB. Mer information finns i [ansluta till SSISDB i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Öppna ett frågefönster med SSISDB som den aktuella databasen.

3. Kör följande lagrade procedur och ange lämpliga domänautentiseringsuppgifter:

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>Ansluta till en fil resurs i Azure Files

Mer information om Azure Files finns i [Azure Files](https://azure.microsoft.com/services/storage/files/).

Gör så här för att få åtkomst till en fil resurs i Azure Files från paket som körs i Azure:

1. Med SSMS eller något annat verktyg ansluter du till Azure SQL Database Server/hanterad instans som är värd för SSISDB. Mer information finns i [ansluta till SSISDB i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Öppna ett frågefönster med SSISDB som den aktuella databasen.

3. Kör följande lagrade procedur och ange lämpliga domänautentiseringsuppgifter:

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>Nästa steg

- Distribuera dina paket. Mer information finns i [distribuera ett SSIS-projekt till Azure med SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms).
- Kör dina paket. Mer information finns i [köra SSIS-paket i Azure med SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms).
- Schemalägg dina paket. Mer information finns i [Schemalägga SSIS-paket i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).