---
title: Felsöka SSIS Integration Runtime Management i Azure Data Factory | Microsoft Docs
description: Den här artikeln innehåller fel söknings vägledning för hanterings problem med SSIS Integration Runtime (SSIS IR)
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/08/2019
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 8abffdf443e26c03c38c12a3947a47a94157c9da
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609614"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Felsöka SSIS Integration Runtime Management i Azure Data Factory

Den här artikeln innehåller fel söknings vägledning för hanterings problem i Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR), även kallat SSIS IR.

## <a name="overview"></a>Översikt

Om du stöter på ett problem under etableringen eller avetableringen av SSIS IR visas ett fel meddelande i Microsoft Azure Data Factory portalen eller ett fel som returneras från en PowerShell-cmdlet. Felet visas alltid i formatet för en felkod med ett detaljerat fel meddelande.

Om felkoden är InternalServerError har tjänsten tillfälliga problem och du bör försöka igen senare. Kontakta Azure Data Factory support-teamet om det inte går att göra ett nytt försök.

I annat fall kan tre större externa beroenden orsaka fel: en Azure SQL Database Server eller en hanterad instans, ett anpassat installations skript och en virtuell nätverks konfiguration.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Problem med Azure SQL Database Server eller hanterade instanser

En Azure SQL Database Server eller en hanterad instans krävs om du ska tillhandahålla SSIS IR med en SSIS-katalog databas. SSIS IR måste kunna komma åt Azure SQL Database Server eller en hanterad instans. Kontot för Azure SQL Database-servern eller den hanterade instansen bör också ha behörighet att skapa en SSIS-katalog databas (SSISDB). Om det uppstår ett fel visas en felkod med ett detaljerat SQL-undantags meddelande i Data Factory portalen. Använd informationen i följande lista för att felsöka fel koderna.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Du kan se det här problemet när du konfigurerar en ny SSIS IR eller medan IR körs. Om det här felet uppstår under IR-etableringen kan du få ett detaljerat SqlException-meddelande i fel meddelandet som anger något av följande problem:

* Ett problem med nätverks anslutningen. Kontrol lera om SQL Server eller värd namnet för den hanterade instansen är tillgängligt. Kontrol lera också att ingen brand vägg eller nätverks säkerhets grupp (NSG) blockerar SSIS IR-åtkomst till servern.
* Inloggningen misslyckades under SQL-autentisering. Det angivna kontot kan inte logga in på den SQL Server databasen. Se till att du anger rätt användar konto.
* Inloggningen misslyckades under Microsoft Azure Active Directory (Azure AD)-autentisering (hanterad identitet). Lägg till den hanterade identiteten för din fabrik i en AAD-grupp och se till att den hanterade identiteten har åtkomst behörighet till katalog databas servern.
* Tids gräns för anslutning. Det här felet orsakas alltid av en säkerhetsrelaterad konfiguration. Vi rekommenderar att du:
  1. Skapa en ny virtuell dator.
  1. Anslut den virtuella datorn till samma Microsoft Azure Virtual Network av IR om IR finns i ett virtuellt nätverk.
  1. Installera SSMS och kontrol lera status för Azure SQL Database Server eller hanterad instans.

Lös problemet som visas i det detaljerade fel meddelandet om SQL-undantag för andra problem. Om du fortfarande har problem kan du kontakta Azure SQL Database Server eller support teamet för hanterade instanser.

Om du ser felet när IR körs, förhindrar nätverks säkerhets gruppen eller brand Väggs ändringar förmodligen SSIS IR Worker-noden från att komma åt Azure SQL Database Server eller en hanterad instans. Avblockera SSIS IR Worker-noden så att den kan komma åt Azure SQL Database Server eller hanterad instans.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Den här typen av fel meddelande kan se ut så här: "Databasen" SSISDB "har nått sin storleks kvot. Partitionera eller ta bort data, släpp index eller Läs om möjliga lösningar i dokumentationen. " 

Möjliga lösningar är:
* Öka kvot storleken för din SSISDB.
* Ändra konfigurationen för SSISDB för att minska storleken genom att:
   * Reducering av kvarhållningsperioden och antal projekt versioner.
   * Minskar loggens kvarhållningsperiod.
   * Ändra standard nivån för loggen.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Det här felet innebär att Azure SQL Database-servern eller den hanterade instansen redan har en SSISDB och att den används av en annan IR. Du måste antingen ange en annan Azure SQL Database Server eller en hanterad instans eller ta bort den befintliga SSISDB och sedan starta om den nya IR-filen.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Felet kan bero på någon av följande orsaker:

* Det användar konto som har kon figurer ATS för SSIS IR har inte behörighet att skapa databasen. Du kan bevilja användaren behörighet att skapa databasen.
* En timeout inträffar när databasen skapas, till exempel tids gräns för körning eller tids gräns för databas åtgärd. Försök igen senare. Om försöket inte fungerar kontaktar du Azure SQL Database Server eller support teamet för hanterade instanser.

Mer information om andra problem finns i fel meddelandet om SQL-undantag och åtgärda problemet som beskrivs i fel informationen. Om du fortfarande har problem kan du kontakta Azure SQL Database Server eller support teamet för hanterade instanser.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Den här typen av fel meddelande ser ut så här: "Ogiltigt objekt namn ' Catalog. catalog_properties '." I så fall har du redan en databas med namnet SSISDB men den skapades inte av SSIS IR, eller så är databasen i ett ogiltigt tillstånd som orsakas av fel under den senaste SSIS IR-etableringen. Du kan släppa den befintliga databasen med namnet SSISDB, eller så kan du konfigurera en ny Azure SQL Database Server eller en hanterad instans för IR.

## <a name="custom-setup-issues"></a>Anpassade installations problem

Anpassad installation tillhandahåller ett gränssnitt för att lägga till egna installations steg under etableringen eller omkonfigurationen av din SSIS IR. Mer information finns i [Anpassa installations programmet för Azure-SSIS integration runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Kontrol lera att din behållare bara innehåller nödvändiga anpassade installationsfiler. alla filer i behållaren kommer att laddas ned till noden SSIS IR Worker. Vi rekommenderar att du testar det anpassade installations skriptet på en lokal dator för att åtgärda eventuella skript körnings problem innan du kör skriptet i SSIS IR.

Den anpassade installations skript behållaren kontrol leras medan IR körs, eftersom SSIS IR uppdateras regelbundet. Den här uppdateringen kräver åtkomst till behållaren för att ladda ned det anpassade installations skriptet och installera det igen. Processen kontrollerar också om behållaren är tillgänglig och om filen main. cmd finns.

För alla fel som inbegriper anpassad installation visas en CustomSetupScriptFailure-felkod med under kod som CustomSetupScriptBlobContainerInaccessible eller CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Det här felet innebär att SSIS IR inte kan komma åt din Azure Blob-behållare för anpassad installation. Kontrol lera att det går att hitta SAS-URI: n för behållaren och att den inte har upphört att gälla.

Stoppa IR om den körs, konfigurera om IR med en ny egen SAS-URI för installations behållaren och starta sedan om IR.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Det här felet innebär att SSIS IR inte kan hitta ett anpassat installations skript (Main. cmd) i BLOB-behållaren. Se till att main. cmd finns i behållaren, vilket är start punkten för anpassad installation.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Detta fel innebär att det inte gick att köra anpassade installations skript (Main. cmd). Testa skriptet på den lokala datorn först eller kontrol lera de anpassade konfigurations körnings loggarna på BLOB-behållaren.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Det här felet anger tids gränsen för körning av anpassade installations skript. Se till att BLOB-behållaren bara innehåller nödvändiga anpassade installationsfiler. Du bör också kontrol lera de anpassade konfigurations körnings loggarna i BLOB-behållaren. Den maximala tids perioden för anpassad installation är 45 minuter innan tids gränsen uppnåddes, och den längsta perioden innehåller tiden för att ladda ned alla filer från behållaren och installera dem på SSIS IR. Om du behöver en längre period kan du generera ett support ärende.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Det här felet innebär att försöket att överföra körnings loggar för anpassade installationer till BLOB-behållaren misslyckades. Det här problemet inträffar antingen på grund av att SSIS IR inte har Skriv behörighet till din BLOB-behållare eller på grund av lagrings-eller nätverks problem. Om den anpassade installationen lyckas kommer det här felet inte att påverka någon SSIS-funktion, men loggarna saknas. Om anpassad installation Miss lyckas med ett annat fel och loggen inte överförs, kommer vi att rapportera felet först så att loggen kan laddas upp för analys. När det här problemet har lösts kommer vi också att rapportera eventuella mer specifika problem. Om problemet inte är löst efter ett nytt försök kontaktar du Azure Data Factory support-teamet.

## <a name="virtual-network-configuration"></a>Konfiguration av virtuellt nätverk

När du ansluter SSIS IR till Azure Virtual Network använder SSIS IR det virtuella nätverk som finns under användar prenumerationen. Mer information finns i [ansluta en Azure-SSIS integration runtime till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

När det finns ett Virtual Network-relaterat problem ser du något av följande fel.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Det här felet kan inträffa av olika orsaker. Information om hur du felsöker det finns i avsnitten om [förbjudna](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue)och [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings) .

### <a name="forbidden"></a>Förbjudna

Den här typen av fel kan likna detta: "SubnetId har inte Aktiver ATS för det aktuella kontot. Microsoft. batch Resource Provider är inte registrerad under samma prenumeration av VNet. "

Den här informationen innebär att Azure Batch inte kan komma åt ditt virtuella nätverk. Registrera providern Microsoft. batch-resurs under samma prenumeration som Virtual Network.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Den här typen av fel kan likna något av följande: 

- "Antingen finns inte det angivna virtuella nätverket eller också har batch-tjänsten inte åtkomst till det."
- "Det angivna under nätet XXX finns inte."

Dessa fel innebär att det virtuella nätverket inte finns, Azure Batch tjänsten inte kan komma åt det, eller att det angivna under nätet inte finns. Kontrol lera att det virtuella nätverket och under nätet finns och att Azure Batch kan komma åt dem.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Den här typen av fel meddelande kan se ut så här: "Det gick inte att etablera Integration Runtime i VNet. Om DNS-servern eller NSG-inställningarna konfigureras kontrollerar du att DNS-servern är tillgänglig och att NSG har kon figurer ATS korrekt. "

I den här situationen har du förmodligen en anpassad konfiguration av DNS-serverns eller NSG inställningar, vilket förhindrar att det Azure Server-namn som krävs av SSIS IR matchas eller nås. Mer information finns i [SSIS IR Virtual Network Configuration](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Om du fortfarande har problem kan du kontakta Azure Data Factory support-teamet.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR uppdateras regelbundet med jämna mellanrum. En ny Azure Batch pool skapas under uppgraderingen och den gamla Azure Batch-poolen tas bort. Virtual Network-relaterade resurser för den gamla poolen tas också bort och de nya Virtual Network-relaterade resurserna skapas under din prenumeration. Det här felet innebär att borttagning av Virtual Network-relaterade resurser för den gamla poolen misslyckades på grund av ett borttagnings lås på prenumerations-eller resurs grupps nivån. Eftersom kunden kontrollerar och anger borttagnings låset måste de ta bort borttagnings låset i den här situationen.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Om IR-etableringen av SSIS Miss lyckas tas alla resurser som har skapats bort. Men om det finns ett resurs borttagnings lås på prenumerations-eller resurs grupps nivån, tas Virtual Network resurser inte bort som förväntat. Åtgärda felet genom att ta bort borttagnings låset och starta om IR.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

När du stoppar SSIS IR tas alla resurser som är relaterade till Virtual Network bort. Men det går inte att ta bort om det finns ett resurs borttagnings lås på prenumerations-eller resurs grupps nivån. Här är det också kunden som kontrollerar och ställer in borttagnings låset. De måste därför ta bort borttagnings låset och sedan stoppa SSIS IR igen.

### <a name="nodeunavailable"></a>NodeUnavailable

Felet uppstår när IR körs, och det innebär att IR har blivit ohälsosamt. Det här felet orsakas alltid av en ändring i DNS-servern eller NSG-konfigurationen som blockerar SSIS IR från att ansluta till en nödvändig tjänst. Eftersom konfigurationen av DNS-server och NSG styrs av kunden måste kunden åtgärda spärrnings problemen. Mer information finns i [SSIS IR Virtual Network Configuration](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Om du fortfarande har problem kan du kontakta Azure Data Factory support-teamet.
