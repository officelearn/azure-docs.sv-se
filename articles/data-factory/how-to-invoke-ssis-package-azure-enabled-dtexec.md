---
title: Kör SSIS-paket (SQL Server Integration Services) med azure-aktiverade dtexec-verktyget
description: Lär dig hur du kör SSIS-paket (SQL Server Integration Services) med azure-aktiverade dtexec-verktyget.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/21/2019
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: a5540eea91937319a6ac947b50698ccaa8b25847
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931709"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Kör SQL Server Integration Services-paket med azure-aktiverade dtexec-verktyget
I den här artikeln beskrivs kommando promptverktyget för Azure-aktiverade dtexec (AzureDTExec). Den används för att köra SSIS-paket (SQL Server Integration Services) på Azure-SSIS Integration Runtime (IR) i Azure Data Factory.

Det traditionella dtexec-verktyget levereras med SQL Server. Mer information finns i [dtexec utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Det anropas ofta av tredjepartsorkestratorer eller schemaläggare, till exempel ActiveBatch och Control-M, för att köra SSIS-paket lokalt. 

Det moderna AzureDTExec-verktyget levereras med ett SSMS-verktyg (SQL Server Management Studio). Det kan också anropas av tredjepartsorkeoratorer eller schemaläggare för att köra SSIS-paket i Azure. Det underlättar lyft och växling eller migrering av dina SSIS-paket till molnet. Efter migreringen, om du vill fortsätta använda tredjepartsorkestratorer eller schemaläggare i den dagliga verksamheten, kan de nu anropa AzureDTExec i stället för dtexec.

AzureDTExec kör dina paket som Kör SSIS-paketaktiviteter i Data Factory-pipelines. Mer information finns i [Kör SSIS-paket som Azure Data Factory-aktiviteter](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

AzureDTExec kan konfigureras via SSMS för att använda ett Azure Active Directory -program (Azure AD) som genererar pipelines i din datafabrik. Den kan också konfigureras för åtkomst till filsystem, filresurser eller Azure-filer där du lagrar dina paket. Baserat på de värden du anger för dess anropsalternativ genererar och kör AzureDTExec en unik Data Factory-pipeline med en kör SSIS-paketaktivitet i den. Anropa AzureDTExec med samma värden för sina alternativ kör den befintliga pipelinen.

## <a name="prerequisites"></a>Krav
Om du vill använda AzureDTExec hämtar och installerar du den senaste versionen av SSMS, som är version 18.3 eller senare. Ladda ner den från [denna webbplats](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="configure-the-azuredtexec-utility"></a>Konfigurera AzureDTExec-verktyget
Installera SSMS på din lokala dator installerar också AzureDTExec. Om du vill konfigurera inställningarna startar du SSMS med alternativet **Kör som administratör.** Välj sedan **Verktyg** > **migrera till Azure** > **Configure Azure-aktiverad DTExec**.

![Konfigurera Azure-aktiverad dtexec-meny](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Den här åtgärden öppnar ett **AzureDTExecConfig-fönster** som måste öppnas med administratörsbehörighet för att den ska kunna skriva in i filen *AzureDTExec.settings.* Om du inte har kört SSMS som administratör öppnas ett UAC-fönster (User Account Control). Ange ditt administratörslösenord för att höja dina privilegier.

![Konfigurera Azure-aktiverade dtexec-inställningar](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

I fönstret **AzureDTExecConfig** anger du dina konfigurationsinställningar på följande sätt:

- **ApplicationId**: Ange den unika identifieraren för Azure AD-appen som du skapar med rätt behörighet för att generera pipelines i din datafabrik. Mer information finns i [Skapa en Azure AD-app och tjänsthuvudnamn via Azure Portal](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).
- **AuthenticationKey**: Ange autentiseringsnyckeln för din Azure AD-app.
- **TenantId**: Ange den unika identifieraren för Azure AD-klienten, under vilken din Azure AD-app skapas.
- **SubscriptionId**: Ange den unika identifieraren för Azure-prenumerationen, under vilken din datafabrik skapades.
- **ResourceGroup**: Ange namnet på den Azure-resursgrupp där datafabriken skapades.
- **DataFactory**: Ange namnet på din datafabrik där unika pipelines med Kör SSIS-paketaktivitet i dem genereras baserat på värdena för alternativ som tillhandahålls när du anropar AzureDTExec.
- **IRName**: Ange namnet på Azure-SSIS IR i din datafabrik, där paketen som anges i deras UNC-sökväg (Universal Naming Convention) körs när du anropar AzureDTExec.
- **PackageAccessDomain**: Ange domänautentiseringsuppgifter för att komma åt dina paket i deras UNC-sökväg som anges när du anropar AzureDTExec.
- **PackageAccessUserName**: Ange användarnamnsautentiseringsuppgifterna för att komma åt dina paket i deras UNC-sökväg som anges när du anropar AzureDTExec.
- **PackageAccessPassword**: Ange lösenordsautentiseringsuppgifter för att komma åt dina paket i deras UNC-sökväg som anges när du anropar AzureDTExec.
- **LogPath**: Ange UNC-sökvägen för loggmappen, där loggfiler från dina paketkörningar på Azure-SSIS IR skrivs.
- **LogLevel**: Ange det valda omfånget för loggning från fördefinierade **null,** **Basic,** **Verbose**eller **Performance-alternativ** för dina paketkörningar på Azure-SSIS IR.
- **LogAccessDomain**: Ange domänautentiseringsuppgifter för att komma åt loggmappen i UNC-sökvägen när du skriver loggfiler, vilket krävs när **LogPath** har angetts och **LogLevel** inte är **null**.
- **LogAccessUserName**: Ange användarnamnsautentiseringsuppgifter för att komma åt loggmappen i UNC-sökvägen när du skriver loggfiler, vilket krävs när **LogPath** har angetts och **LogLevel** inte är **null**.
- **LogAccessPassword**: Ange lösenordsautentiseringsuppgifter för att komma åt loggmappen i unc-sökvägen när du skriver loggfiler, vilket krävs när **LogPath** har angetts och **LogLevel** inte är **null**.
- **PipelineNameHashStrLen**: Ange längden på hash-strängar som ska genereras från värdena för alternativ som du anger när du anropar AzureDTExec. Strängarna används för att skapa unika namn för Data Factory-pipelines som kör dina paket på Azure-SSIS IR. Vanligtvis räcker det med en längd på 32 tecken.

Om du vill lagra dina paket och loggfiler i filsystem eller filresurser lokalt ansluter du till din Azure-SSIS IR till ett virtuellt nätverk som är anslutet till ditt lokala nätverk så att det kan hämta dina paket och skriva dina loggfiler. Mer information finns i [Ansluta till en Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

För att undvika att visa känsliga värden som skrivits in i filen *AzureDTExec.settings* i oformaterad text kodar vi dem till strängar med Base64-kodning. När du anropar AzureDTExec avkodas alla Base64-kodade strängar tillbaka till sina ursprungliga värden. Du kan ytterligare skydda *filen AzureDTExec.settings* genom att begränsa de konton som kan komma åt den.

## <a name="invoke-the-azuredtexec-utility"></a>Anropa AzureDTExec-verktyget
Du kan anropa AzureDTExec vid kommandotolkprompten och ange relevanta värden för specifika alternativ i ditt användningsfallsscenario.

Verktyget är installerat `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`på . Du kan lägga till sökvägen till miljövariabeln "PATH" för att den ska anropas var som helst.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

Att anropa AzureDTExec erbjuder liknande alternativ som att anropa dtexec. Mer information finns i [dtexec Utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Här är de alternativ som för närvarande stöds:

- **/F[ile]**: Läser in ett paket som lagras i filsystem, filresurs eller Azure-filer. Som värde för det här alternativet kan du ange UNC-sökvägen för paketfilen i filsystem, filresurs eller Azure-filer med dtsx-tillägget. Om den angivna UNC-sökvägen innehåller något blanksteg placerar du citattecken runt hela banan.
- **/Conf[igFile]**: Anger en konfigurationsfil att extrahera värden från. Med det här alternativet kan du ställa in en körningskonfiguration för paketet som skiljer sig från den som anges vid designtillfället. Du kan lagra olika inställningar i en XML-konfigurationsfil och sedan läsa in dem innan paketkörningen. Mer information finns i [SSIS-paketkonfigurationer](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017). Om du vill ange värdet för det här alternativet använder du UNC-sökvägen för konfigurationsfilen i filsystem, filresurs eller Azure-filer med dtsConfig-tillägget. Om den angivna UNC-sökvägen innehåller något blanksteg placerar du citattecken runt hela banan.
- **/Conn[ection]**: Anger anslutningssträngar för befintliga anslutningshanterare i paketet. Med det här alternativet kan du ange körningsanslutningssträngar för befintliga anslutningshanterare i paketet som skiljer sig från de som anges vid designtillfället. Ange värdet för det här `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`alternativet enligt följande: .
- **/Set**: Åsidosätter konfigurationen av en parameter, variabel, egenskap, behållare, loggprovider, Foreach-uppräknare eller anslutning i paketet. Det här alternativet kan anges flera gånger. Ange värdet för det här `property_path;value`alternativet enligt följande: . Åsidosätter `\package.variables[counter].Value;1` till exempel variabelns värde `counter` som 1. Du kan använda guiden **Paketkonfiguration** för att söka `property_path` efter, kopiera och klistra in värdet för för objekt i paketet vars värde du vill åsidosätta. Mer information finns i [guiden Paketkonfiguration](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014).
- **/De[crypt]**: Ställer in dekrypteringslösenordet för ditt paket som är konfigurerat med **EncryptAllWithPassword**/**EncryptSensitiveWithPassword-skyddsnivå.**

> [!NOTE]
> Om du anropar AzureDTExec med nya värden för alternativen skapas en ny pipeline med undantag för alternativet **/De[cript]**.

## <a name="next-steps"></a>Nästa steg

När unika pipelines med aktiviteten Kör SSIS-paket i dem genereras och körs när du har anropat AzureDTExec kan de övervakas på Data Factory-portalen. Mer information finns i [Kör SSIS-paket som datafabriksaktiviteter](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

> [!WARNING]
> Den genererade pipelinen förväntas endast användas av AzureDTExec. Dess egenskaper eller parametrar kan ändras i framtiden, så ändra eller återanvänd inte dem för andra ändamål. Ändringar kan bryta AzureDTExec. Om detta inträffar tar du bort pipelinen. AzureDTExec genererar en ny pipeline nästa gång den anropas.
