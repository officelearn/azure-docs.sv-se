---
title: Komma åt datalager och filresurser med Windows-autentisering
description: Lär dig hur du konfigurerar SSIS-katalog i Azure SQL Database och Azure-SSIS Integration Runtime i Azure Data Factory för att köra paket som får åtkomst till datalager och filresurser med Windows-autentisering.
ms.date: 3/22/2018
ms.topic: conceptual
ms.prod: sql
ms.prod_service: integration-services
ms.custom: ''
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 0c4cdc3481fb58efd8eaa4cd83e1d6167f203a4e
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81760191"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>Komma åt datalager och filresurser med Windows-autentisering från SSIS-paket i Azure

Du kan använda Windows-autentisering för att komma åt datalager, till exempel SQL-servrar, filresurser, Azure-filer osv. Dina datalager kan finnas lokalt, finnas på virtuella Azure-datorer (virtuella datorer) eller köras i Azure som hanterade tjänster. Om de är lokalt måste du ansluta till din Azure-SSIS IR till ett virtuellt nätverk (Microsoft Azure Virtual Network) som är anslutet till ditt lokala nätverk, se Gå med i [Azure-SSIS IR till ett Microsoft Azure Virtual Network](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Det finns fyra metoder för att komma åt datalager med Windows-autentisering från SSIS-paket som körs på din Azure-SSIS IR:

| Anslutningsmetod | Effektiv räckvidd | Stegvisa inställningar | Åtkomstmetod i paket | Antal autentiseringsuppgifter och anslutna resurser | Typ av anslutna resurser | 
|---|---|---|---|---|---|
| Ställa in en körningskontext på aktivitetsnivå | Aktivitet för SSIS-paket per körning | Konfigurera egenskapen **Windows-autentisering** för att ställa in en "Körning/kör som" när SSIS-paket körs som kör SSIS-paketaktiviteter i ADF-pipelines.<br/><br/> Mer information finns i [Konfigurera kör SSIS-paketaktivitet](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). | Komma åt resurser direkt i paket via UNC-sökväg, till `\\YourFileShareServerName\YourFolderName` exempel om du använder filresurser eller Azure-filer: eller`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Stöd endast en autentiseringsuppsättning för alla anslutna resurser | - Filresurser på lokala/virtuella Azure-datorer<br/><br/> - Azure-filer, se [Använda en Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - SQL-servrar på lokala/Virtuella Azure-datorer med Windows-autentisering<br/><br/> - Andra resurser med Windows-autentisering |
| Ställa in en körningskontext på katalognivå | Per Azure-SSIS IR, men åsidosätts när du konfigurerar en körningskontext på aktivitetsnivå (se ovan) | Kör SSISDB-lagrad `catalog.set_execution_credential` procedur för att ställa in en kontext för körning/körning som.<br/><br/> Mer information finns i resten av artikeln nedan. | Komma åt resurser direkt i paket via UNC-sökväg, till `\\YourFileShareServerName\YourFolderName` exempel om du använder filresurser eller Azure-filer: eller`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Stöd endast en autentiseringsuppsättning för alla anslutna resurser | - Filresurser på lokala/virtuella Azure-datorer<br/><br/> - Azure-filer, se [Använda en Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - SQL-servrar på lokala/Virtuella Azure-datorer med Windows-autentisering<br/><br/> - Andra resurser med Windows-autentisering |
| Beständiga `cmdkey` autentiseringsuppgifter via kommando | Per Azure-SSIS IR, men åsidosätts vid inställning av en körningskontext på aktivitets-/katalognivå (se ovan) | Kör `cmdkey` kommandot i ett`main.cmd`anpassat installationsskript ( ) när du etablerar din Azure-SSIS IR, `cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword` `cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey`t.ex.<br/><br/> Mer information finns i [Anpassa inställningar för Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). | Komma åt resurser direkt i paket via UNC-sökväg, till `\\YourFileShareServerName\YourFolderName` exempel om du använder filresurser eller Azure-filer: eller`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Stöd för flera autentiseringsuppgifter för olika anslutna resurser | - Filresurser på lokala/virtuella Azure-datorer<br/><br/> - Azure-filer, se [Använda en Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - SQL-servrar på lokala/Virtuella Azure-datorer med Windows-autentisering<br/><br/> - Andra resurser med Windows-autentisering |
| Monteringsenheter vid paketkörningstid (icke-beständig) | Per paket | Kör `net use` kommandot i Kör processuppgift som läggs till i början av kontrollflödet i dina paket, till exempel`net use D: \\YourFileShareServerName\YourFolderName` | Komma åt filresurser via mappade enheter | Stöd för flera enheter för olika filresurser | - Filresurser på lokala/virtuella Azure-datorer<br/><br/> - Azure-filer, se [Använda en Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) |
|||||||

> [!WARNING]
> Om du inte använder någon av ovanstående metoder för att komma åt datalager med Windows-autentisering kan dina paket som är beroende av Windows-autentisering inte komma åt dem och misslyckas vid körning. 

I resten av den här artikeln beskrivs hur du konfigurerar SSIS-katalog (SSISDB) som finns i Azure SQL Database server/Managed Instance för att köra paket på Azure-SSIS IR som använder Windows-autentisering för att komma åt datalager. 

## <a name="you-can-only-use-one-set-of-credentials"></a>Du kan bara använda en uppsättning autentiseringsuppgifter

När du använder Windows-autentisering i ett SSIS-paket kan du bara använda en uppsättning autentiseringsuppgifter. De domänautentiseringsuppgifter som du anger när du följer stegen i den här artikeln gäller för alla paketkörningar - interaktiva eller schemalagda - på din Azure-SSIS IR tills du ändrar eller tar bort dem. Om ditt paket måste ansluta till flera datalager med olika uppsättningar autentiseringsuppgifter bör du överväga ovanstående alternativa metoder.

## <a name="provide-domain-credentials-for-windows-authentication"></a>Ange domänautentiseringsuppgifter för Windows-autentisering

Så här anger du domänautentiseringsuppgifter som gör att paket kan använda Windows-autentisering för att komma åt datalager lokalt:

1. Med SQL Server Management Studio (SSMS) eller ett annat verktyg ansluter du till Azure SQL Database server/Managed Instance som är värd för SSISDB. Mer information finns i [Ansluta till SSISDB i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Öppna ett frågefönster med SSISDB som aktuell databas.

3. Kör följande lagrade procedur och ange lämpliga domänautentiseringsuppgifter:

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. Kör dina SSIS-paket. Paketen använder de autentiseringsuppgifter som du angav för att komma åt datalager lokalt med Windows-autentisering.

### <a name="view-domain-credentials"></a>Visa domänautentiseringsuppgifter

Så här visar du aktiva domänautentiseringsuppgifter:

1. Med SSMS eller ett annat verktyg ansluter du till Azure SQL Database server/Managed Instance som är värd för SSISDB. Mer information finns i [Ansluta till SSISDB i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Öppna ett frågefönster med SSISDB som aktuell databas.

3. Kör följande lagrade procedur och kontrollera utdata:

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>Rensa domänautentiseringsuppgifter
Så här tar du bort och tar bort de autentiseringsuppgifter som du angav som beskrivs i den här artikeln:

1. Med SSMS eller ett annat verktyg ansluter du till Azure SQL Database server/Managed Instance som är värd för SSISDB. Mer information finns i [Ansluta till SSISDB i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Öppna ett frågefönster med SSISDB som aktuell databas.

3. Kör följande lagrade procedur:

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>Ansluta till en SQL Server lokalt

Så här kontrollerar du om du kan ansluta till en SQL Server lokalt:

1. Om du vill köra det här testet hittar du en dator som inte är ansluten till domänen.

2. På den dator som inte är ansluten till domän kör du följande kommando för att starta SSMS med de domänautentiseringsuppgifter som du vill använda:

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. Från SSMS kontrollerar du om du kan ansluta till SQL Server lokalt.

### <a name="prerequisites"></a>Krav

Så här kommer du åt en SQL Server lokalt från paket som körs i Azure:

1.  Aktivera TCP/IP-protokoll i SQL Server Configuration Manager.

2. Tillåt åtkomst via Windows-brandväggen. Mer information finns i [Konfigurera Windows-brandväggen för åtkomst till SQL Server](https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access).

3. Gå med i din Azure-SSIS IR till ett Virtuellt Microsoft Azure-nätverk som är anslutet till SQL Server lokalt.  Mer information finns i [Gå med i Azure-SSIS IR till ett Virtuellt Nätverk i Microsoft Azure](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

4. Använd SSISDB-lagrad `catalog.set_execution_credential` procedur för att ange autentiseringsuppgifter enligt beskrivningen i den här artikeln.

## <a name="connect-to-a-file-share-on-premises"></a>Ansluta till en filresurs lokalt

Så här kontrollerar du om du kan ansluta till en filresurs lokalt:

1. Om du vill köra det här testet hittar du en dator som inte är ansluten till domänen.

2. På den dator som inte är ansluten till domän kör du följande kommandon. Dessa kommandon öppnar ett kommandotolksfönster med de domänautentiseringsuppgifter som du vill använda och testar sedan anslutningen till filresursen lokalt genom att hämta en kataloglista.

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. Kontrollera om kataloglistan returneras för filresursen lokalt.

### <a name="prerequisites"></a>Krav

Så här kommer du åt en filresurs lokalt från paket som körs i Azure:

1. Tillåt åtkomst via Windows-brandväggen.

2. Gå med i din Azure-SSIS IR till ett Virtuellt Microsoft Azure-nätverk som är anslutet till filresursen lokalt.  Mer information finns i [Gå med i Azure-SSIS IR till ett Virtuellt Nätverk i Microsoft Azure](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

3. Använd SSISDB-lagrad `catalog.set_execution_credential` procedur för att ange autentiseringsuppgifter enligt beskrivningen i den här artikeln.

## <a name="connect-to-a-file-share-on-azure-vm"></a>Ansluta till en filresurs på Azure VM

Så här kommer du åt en filresurs på Azure VM från paket som körs i Azure:

1. Med SSMS eller ett annat verktyg ansluter du till Azure SQL Database server/Managed Instance som är värd för SSISDB. Mer information finns i [Ansluta till SSISDB i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Öppna ett frågefönster med SSISDB som aktuell databas.

3. Kör följande lagrade procedur och ange lämpliga domänautentiseringsuppgifter:

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>Ansluta till en filresurs i Azure-filer

Mer information om Azure-filer finns i [Azure Files](https://azure.microsoft.com/services/storage/files/).

Så här kommer du åt en filresurs i Azure Files från paket som körs i Azure:

1. Med SSMS eller ett annat verktyg ansluter du till Azure SQL Database server/Managed Instance som är värd för SSISDB. Mer information finns i [Ansluta till SSISDB i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Öppna ett frågefönster med SSISDB som aktuell databas.

3. Kör följande lagrade procedur och ange lämpliga domänautentiseringsuppgifter:

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>Nästa steg

- Distribuera dina paket. Mer information finns i [Distribuera ett SSIS-projekt till Azure med SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms).
- Kör dina paket. Mer information finns [i Kör SSIS-paket i Azure med SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms).
- Schemalägg dina paket. Mer information finns i [Schemalägga SSIS-paket i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).