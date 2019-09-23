---
title: Köra SQL Server Integration Services-paket (SSIS) med Azure-aktiverat Dtexec-verktyg | Microsoft Docs
description: Lär dig hur du kör SQL Server Integration Services-paket (SSIS) med Azure-aktiverat Dtexec-verktyget.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/21/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 80d4bd2f4f9ea4c451f312f05fd42fbc1ba433ab
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71181332"
---
# <a name="run-sql-server-integration-services-ssis-packages-with-azure-enabled-dtexec-utility"></a>Köra SQL Server Integration Services-paket (SSIS) med Azure-aktiverat Dtexec-verktyget
Den här artikeln beskriver kommando tolks verktyget för Azure-aktiverad **Dtexec** (**AzureDTExec**).  Den används för att köra SSIS-paket på Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF).

Det traditionella **Dtexec** -verktyget innehåller SQL Server finns i dokumentationen till [Dtexec-verktyget](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017) för mer information.  De anropas ofta av Orchestration-och Scheduler-scheman i tredje part, till exempel aktiv batch, kontroll-M osv., för att köra SSIS-paket lokalt.  Verktyget modern **AzureDTExec** levereras med SQL Server Management Studio (SSMS).  Den kan också anropas av Orchestration-/Scheduler-program från tredje part för att köra SSIS-paket i Azure.  Det underlättar lyftet & Shift/migrering av dina SSIS-paket till molnet.  Efter migreringen, om du vill fortsätta att använda tredjepartsleverantörer/schedulers från tredje part i din dagliga verksamhet, kan de nu anropa **AzureDTExec** i stället för **Dtexec**.

**AzureDTExec** kommer att köra dina paket som kör SSIS-paket aktiviteter i ADF-pipeliner, se [köra SSIS-paket som ADF-aktiviteter](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) artikel för mer information.  Den kan konfigureras via SSMS till att använda ett Azure Active Directory-program (AAD) som genererar pipelines i din ADF.  Den kan också konfigureras för att komma åt fil system/fil resurser/Azure Files där du lagrar dina paket.  Baserat på de värden som du anger för sina anrops alternativ genererar **AzureDTExec** och kör en unik ADF-pipeline med AKTIVITETEN kör SSIS-paket.  Om du anropar **AzureDTExec** med samma värden för dess alternativ kommer den befintliga pipeline att köras igen.  Om du anropar **AzureDTExec** med nya värden för dess alternativ skapas en ny pipeline.

## <a name="prerequisites"></a>Förutsättningar
Om du vill använda **AzureDTExec**hämtar och installerar du den senaste SSMS (version 18,3 eller senare) [härifrån.](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)

## <a name="configure-azuredtexec-utility"></a>Konfigurera AzureDTExec-verktyget
När du installerar SSMS på den lokala datorn installeras även **AzureDTExec**.  Om du vill konfigurera dess inställningar startar du SSMS med alternativet **Kör som administratör** och väljer alternativet för överlappande meny objekt **verktyg-> migrera till Azure-> Konfigurera Azure-aktiverad Dtexec**.

![Konfigurera Azure-aktiverad Dtexec-menyn](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Den här åtgärden öppnar **AzureDTExecConfig** -fönstret som måste öppnas med administratörs behörighet för att den ska kunna skriva till filen **AzureDTExec. Settings** .  Om du inte har kört SSMS som administratör visas ett User Account Control (UAC)-fönster som du kan använda för att ange ditt administratörs lösen ord för att öka dina privilegier.

![Konfigurera Azure-aktiverade Dtexec-inställningar](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

I fönstret **AzureDTExecConfig** kan du ange konfigurations inställningarna enligt följande:

- **ApplicationId**: Ange den unika identifieraren för AAD-appen som du skapar med rätt behörigheter för att generera pipelines i din ADF, se [skapa en AAD-app och tjänstens huvud namn via Azure Portal](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) artikel för mer information.

- **AuthenticationKey**: Ange autentiseringsnyckel för din AAD-app.

- **TenantId**: Ange den unika identifieraren för AAD-klienten, under vilken AAD-appen skapas.

- **SubscriptionId**: Ange den unika identifieraren för Azure-prenumerationen, under vilken din ADF skapades.

- **ResourceGroup**: Ange namnet på Azure-resurs gruppen där din ADF skapades.

- **DataFactory**:  Ange namnet på din ADF, där unika pipelines med kör SSIS-paket-aktivitet genereras baserat på värdena för de alternativ som anges vid anrop av **AzureDTExec**.

- **IRName**: Ange namnet på Azure-SSIS IR i din ADF, där de paket som anges i deras Universal Naming Convention (UNC)-sökväg när du anropar **AzureDTExec** ska köras.

- **PackageAccessDomain**: Ange domänens autentiseringsuppgifter för att komma åt dina paket i UNC-sökvägen som anges när du anropar **AzureDTExec**.

- **PackageAccessUserName**:  Ange autentiseringsuppgifterna för användar namn för att komma åt dina paket i UNC-sökvägen som anges när du anropar **AzureDTExec**.

- **PackageAccessPassword**: Ange lösen ordets autentiseringsuppgifter för att komma åt dina paket i UNC-sökvägen som anges när du anropar **AzureDTExec**.

- **LogPath**:  Ange UNC-sökvägen till loggmappen där loggfiler från paket körningarna på Azure-SSIS IR ska skrivas.

- **LogLevel**:  Ange det valda omfånget för loggning **från fördefinierade**/,**grundläggande**/**utförliga**/**prestanda** alternativ för dina paket körningar på Azure-SSIS IR.

- **LogAccessDomain**: Ange domänens autentiseringsuppgifter för att komma åt loggmappen i UNC-sökvägen när du skriver loggfiler, krävs när **LogPath** har angetts och **LogLevel** inte är **Null**.

- **LogAccessUserName**: Ange inloggnings uppgifter för att komma åt loggmappen i UNC-sökvägen när du skriver loggfiler, krävs när **LogPath** anges och **LogLevel** inte är **Null**.

- **LogAccessPassword**: Ange lösen ordets autentiseringsuppgifter för att komma åt loggmappen i UNC-sökvägen när du skriver loggfiler, krävs när **LogPath** anges och **LogLevel** inte är **Null**.

- **PipelineNameHashStrLen**: Ange längden på hash-strängarna som ska genereras från värdena för de alternativ som du anger när du anropar **AzureDTExec**.  Strängarna används för att skapa unika namn för ADF-pipeliner som kör dina paket på Azure-SSIS IR.  Det räcker med en längd på 32 tecken.

Om du planerar att lagra dina paket och loggfiler i fil system/fil resurser lokalt, bör du ansluta dina Azure-SSIS IR till ett virtuellt nätverk som är anslutet till ditt lokala nätverk, så att du kan hämta dina paket och skriva dina loggfiler, se [ansluta Azure-SSIS IR till ett VNet](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) artikeln för mer information.

För att undvika att Visa känsliga värden som skrivs i **AzureDTExec. Settings** -filen i oformaterad text kodas de till strängar av base64-kodning.  När du anropar **AzureDTExec**, avkodas alla base64-kodade strängar tillbaka till sina ursprungliga värden.  Du kan skydda **AzureDTExec. Settings** -filen ytterligare genom att begränsa de konton som har åtkomst till den.

## <a name="invoke-azuredtexec-utility"></a>Anropa AzureDTExec-verktyget
Du kan anropa **AzureDTExec** i kommando tolken och ange relevanta värden för specifika alternativ i scenariot för användnings fall, till exempel:

`AzureDTExec.exe
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"
  /Set \package.variables[MyVariable].Value;MyValue
  /De MyEncryptionPassword`

När du anropar **AzureDTExec** finns liknande alternativ som när du anropar **Dtexec**. mer information finns i dokumentationen för [Dtexec-verktyget](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017) .  Här följer de alternativ som stöds för närvarande:

- **/F [Il]** : Läser in ett paket som lagras i fil system/fil resurs/Azure Files.  Som värde för det här alternativet kan du ange UNC-sökvägen för paket filen i fil system/fil resurs/Azure Files med dess dtsx-tillägg.  Om UNC-sökvägen som anges innehåller ett blank steg måste du ange citat tecken runt hela sökvägen.

- **/Conf [igFile]** : Anger en konfigurations fil som värden ska extraheras från.  Med det här alternativet kan du ange en körnings konfiguration för ditt paket som skiljer sig från det som anges i design läge.  Du kan lagra olika inställningar i en XML-konfigurationsfil och sedan läsa in dem innan du kör paketet.  Mer information finns i artikeln [SSIS Package Configurations](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017) .  Som värde för det här alternativet kan du ange UNC-sökvägen för konfigurations filen i fil system/fil resurs/Azure Files med dess dtsConfig-tillägg.  Om UNC-sökvägen som anges innehåller ett blank steg måste du ange citat tecken runt hela sökvägen.

- **/Conn [et]** : Anger anslutnings strängar för befintliga anslutnings hanterare i paketet.  Med det här alternativet kan du ange körnings anslutnings strängar för befintliga anslutnings hanterare i ditt paket som skiljer sig från de som anges i design läge.  Som värde för det här alternativet kan du ange det på följande sätt: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`.

- **/Set**: Åsidosätter konfigurationen av en parameter, variabel, egenskap, behållare, loggpost, uppräknare eller anslutning i ditt paket.  Det här alternativet kan anges flera gånger.  Som värde för det här alternativet kan du ange det enligt följande: `property_path;value`, till exempel `\package.variables[counter].Value;1` åsidosätter värdet för `counter` variabeln som 1.  Du kan använda guiden paket konfiguration för att hitta, kopiera och klistra in värdet `property_path` för objekt i ditt paket vars värde du vill åsidosätta. mer information finns i dokumentationen för [paket konfigurations guiden](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014) .

- **/De [crypt]** : Ställer in dekrypterings lösen ordet för ditt paket som har kon figurer ATS med **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** -skydds nivå.

## <a name="next-steps"></a>Nästa steg
När unika pipeliner med kör SSIS-paket-aktivitet genereras och körs efter att ha anropat **AzureDTExec**, kan de redige ras och eller köras på ADF-portalen, se [köra SSIS-paket som ADF-aktiviteter](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) artikel för mer information.
