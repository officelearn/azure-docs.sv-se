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
ms.openlocfilehash: 17fe8d9ed02156b8fe9aafd7adca946531895883
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253024"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Felsöka SSIS Integration Runtime Management i Azure Data Factory

Det här dokumentet innehåller fel söknings guider för hanterings problem i SSIS Integration Runtime (SSIS IR).

## <a name="overview"></a>Översikt

Ett fel meddelande visas i ADF-portalen eller returneras från PowerShell-cmdleten om det finns något problem med etableringen eller avetableringen av SSIS IR. Felet är alltid i formatet som en felkod med ett detaljerat fel meddelande.

Det innebär att tjänsten har några tillfälliga problem om felkoden är InternalServerError. Du kan försöka igen senare. Kontakta Azure Data Factory support team om det inte går att göra ett nytt försök.

Det finns tre huvudsakliga externa beroenden som kan orsaka fel: Azure SQL Database Server eller hanterad instans, anpassat installations skript och Virtual Network konfiguration om felkoden inte är InternalServerError.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Problem med Azure SQL Database Server eller hanterade instanser

En Azure SQL Database Server eller en hanterad instans krävs om etableringen av SSIS IR med SSIS Catalog-databasen. Azure SQL Database-servern eller den hanterade instansen bör vara tillgänglig för SSIS IR. Kontot för Azure SQL Database-servern eller den hanterade instansen måste ha behörighet att skapa SSIS-katalog databas (SSISDB). Om det uppstår något fel visas en felkod med information om SQL-undantag i ADF-portalen. Felsök fel koderna genom att följa stegen nedan.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Det här problemet kan uppstå när du konfigurerar en ny SSIS-IR eller under IR-körning.

Det kan bero på följande: om du ser felet under IR-etableringen och du kan få detalj SqlException-meddelande i fel meddelandet.

* Problem med nätverks anslutningen. Kontrol lera att det SQL Server eller den hanterade instansens värd namn är tillgängligt och att det inte finns någon brand vägg eller NSG blockerar SSIS IR för att få åtkomst till servern.
* Inloggningen misslyckades och SQL-autentisering används. Det innebär att det angivna kontot inte kan logga in på SQL Server. Kontrol lera att rätt användar konto har angetts.
* Inloggningen misslyckades och AAD-autentisering (hanterad identitet) används. Lägg till den hanterade identiteten för din fabrik i en AAD-grupp och se till att den hanterade identiteten har åtkomst behörighet till katalog databas servern.
* Timeout för anslutning, det är alltid på grund av en säkerhetsrelaterad konfiguration. Vi rekommenderar att du skapar en ny virtuell dator, gör den virtuella datorn kopplad till samma VNet för IR om IR är i ett VNet, sedan installerar du SSMS och kontrollerar den Azure SQL Database servern eller status för hanterade instanser.

Information om andra problem finns i fel meddelandet detaljerad SQL-undantag och åtgärda problemet som visas i fel meddelandet. Kontakta Azure SQL Database Server eller support teamet för hanterade instanser om du fortfarande har problem.

Det är troligt att vissa nätverks säkerhets grupper eller brand Väggs ändringar visas om felet uppstår under IR-körning, vilket leder till att SSIS IR Worker-noden inte kan komma åt Azure SQL Database-servern eller den hanterade instansen längre. Avblockera SSIS IR Worker-noden för att få åtkomst till Azure SQL Database Server eller hanterad instans.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Fel meddelandet liknar "databasen" SSISDB "har nått sin storleks kvot. Partitionera eller ta bort data, släpp index eller Läs om möjliga lösningar i dokumentationen. " Möjliga lösningar är:
* Öka storleks kvoten för din SSISDB.
* Ändra konfigurationerna för SSISDB för att minska storleken som:
   * Reducering av kvarhållningsperioden och antal projekt versioner.
   * Minskar loggens kvarhållningsperiod.
   * Ändra standard nivån för loggen och så vidare.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Det här felet innebär att Azure SQL Database-servern eller den hanterade instansen redan har en SSISDB som skapats och används av en annan IR. Du behöver antingen ange en annan Azure SQL Database Server eller en hanterad instans eller ta bort befintliga SSISDB och starta om den nya IR-filen.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Felet kan bero på följande:

* Det användar konto som har kon figurer ATS för SSIS IR har inte behörighet att skapa databasen. Du kan ge användaren behörighet att skapa databasen.
* Skapa tids gräns för databas, t. ex. tids gräns för databas, tids gräns för databas åtgärd. Du kan försöka igen senare för att se om problemet är löst. Kontakta support teamet för Azure SQL Database Server eller Managed instance om försöket inte fungerar.

För andra problem kontrollerar du fel meddelandet SQL-undantag och löser problemet som nämns i fel meddelande. Om du fortfarande har problem kontaktar du Azure SQL Database Server eller support teamet för hanterade instanser.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Fel meddelandet är som "ogiltigt objekt namn" Catalog. catalog_properties ".", det betyder att du redan har en databas med namnet SSISDB, men att den inte har skapats av SSIS IR, eller att databasen är i ett ogiltigt tillstånd som orsakas av fel under den senaste SSIS IR-etableringen. Du kan släppa den befintliga databasen med namnet SSISDB eller konfigurera en ny Azure SQL Database Server eller en hanterad instans för IR.

## <a name="custom-setup"></a>Anpassad installation

Anpassad installation tillhandahåller ett gränssnitt för att lägga till egna installations steg under etableringen eller omkonfigurationen av din SSIS IR. Mer information finns i [Anpassa installations programmet för Azure-SSIS integration runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Se till att din behållare bara innehåller de nödvändiga anpassade installationsfilerna, eftersom alla filer i behållaren kommer att laddas ned till noden SSIS IR Worker. Vi rekommenderar att du testar det anpassade installations skriptet på en lokal dator för att åtgärda eventuella skript körnings problem innan du kör skriptet i SSIS IR.

Den anpassade installations skript behållaren kontrol leras under IR-körningen för eftersom SSIS IR uppdateras regelbundet, vilket behöver åtkomst till behållaren igen för att ladda ned det anpassade installations skriptet och installera igen. Kontrollen kommer att inkludera om behållaren är tillgänglig och om filen main. cmd finns.

Fel med anpassad installation visas ett fel meddelande med koden CustomSetupScriptFailure. kontrol lera fel meddelandet som innehåller en under fel kod.  Följ stegen nedan för att felsöka under fel koderna.  

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Det innebär att SSIS IR inte kan komma åt din Azure Blob-behållare för anpassad installation. Kontrol lera att det går att hitta SAS-URI: n för behållaren och att den inte har gått ut.

Stoppa IR först om IR är i körnings läge, konfigurera om IR med en ny egen SAS-URI för installations behållaren och starta sedan IR igen.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Det innebär att SSIS IR inte kan hitta något anpassat installations skript (Main. cmd) i BLOB-behållaren. Se till att main. cmd finns i behållaren, vilket är start punkten för anpassad installation.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Det innebär att det inte går att köra anpassade installations skript (Main. cmd). du kan prova skriptet på den lokala datorn först eller kontrol lera de anpassade konfigurations körnings loggarna i BLOB-behållaren.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Det innebär att kör anpassad installations skript tids gräns. Se till att BLOB-behållaren bara innehåller nödvändiga anpassade installationsfiler. Du kan också kontrol lera körnings loggar för anpassade installationer i BLOB-behållaren. Den maximala tids perioden för anpassad konfiguration ställs in på 45 minuter innan tids gränsen uppnåddes och den längsta perioden innehåller tiden för att ladda ned alla filer från behållaren och installera dem på SSIS IR. Om en längre period krävs, Utlös ett support ärende.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Det innebär att det inte gick att överföra anpassade konfigurations körnings loggar till BLOB-behållaren, antingen på grund av att SSIS IR inte har Skriv behörighet till din BLOB-behållare, eller vissa lagrings-eller nätverks problem. Om den anpassade installationen lyckas, påverkar det här felet inte någon SSIS-funktion, men loggarna saknas. Om anpassad installation misslyckades med ett annat fel och vi inte kan ladda upp loggen, kommer vi att rapportera det här felet först så att loggen kan laddas upp för analys och när problemet har lösts, kommer vi att rapportera fler specifika problem. Kontakta Azure Data Factory support teamet om problemet inte är löst efter ett nytt försök.

## <a name="virtual-network-configuration"></a>Konfiguration av virtuellt nätverk

När du ansluter SSIS IR till en Virtual Network (VNet) använder den VNet under användar prenumerationen. Mer information finns i [ansluta en Azure-SSIS integration runtime till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Om det finns ett VNet-relaterat problem visas felet enligt nedan

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Det kan bero på variant-orsaker. Följ stegen nedan för att felsöka under fel koderna.

### <a name="forbidden"></a>Förbjudna

Fel meddelandet liknar "subnetId är inte aktiverat för det aktuella kontot. Microsoft. batch Resource Provider är inte registrerad under samma prenumeration av VNet. "

Det innebär att Azure Batch inte kan komma åt ditt VNet. Registrera Microsoft. batch Resource Provider under samma prenumeration av VNet.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Fel meddelandet liknar "antingen det angivna virtuella nätverket finns inte eller också har batch-tjänsten inte åtkomst till det" eller "det angivna under nätet XXX finns inte".

Det innebär att VNet inte finns eller att Azure Batch tjänsten inte kan komma åt det, eller att det angivna under nätet inte finns. Kontrol lera att VNet och undernät finns och att Azure Batch kan komma åt dem.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Meddelandet liknar "Det gick inte att etablera Integration Runtime i VNet. Om DNS-servern eller NSG-inställningarna konfigureras kontrollerar du att DNS-servern är tillgänglig och att NSG är korrekt konfigurerat.

Det är troligt att du har en anpassad konfiguration av DNS-serverns eller NSG inställningar, vilket gör att det inte går att matcha det Azure Server-namn som krävs av SSIS IR eller inte går att komma åt. Mer information finns i konfigurations dokument för [SSIS IR VNet](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) . Kontakta Azure Data Factory support-teamet om du fortfarande har problem.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR uppdateras regelbundet regelbundet och en ny Azure Batch-pool skapas under uppgraderingen och den gamla Azure Batchs poolen tas bort, VNet-relaterad resurs för den gamla poolen tas bort och den nya VNet-relaterade resursen skapas under din Prenumerera. Det här felet innebär att det inte gick att ta bort VNet-relaterade resurser för den gamla poolen på grund av borttagnings låset vid prenumerationen eller resurs grupp Hjälp att ta bort borttagnings låset.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

SSIS IR-etablering kan Miss lyckas på grund av olika typer av orsaker, och om ett misslyckande inträffar tas alla resurser som skapas bort. Dock kunde inte VNet-resurserna tas bort på grund av att det finns ett resurs borttagnings lås på prenumerations-eller resurs grupps nivå. Ta bort borttagnings låset och starta om IR.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

När du stoppar SSIS IR tas all resurs som är relaterad till VNet bort, men borttagningen misslyckades på grund av att det finns ett resurs borttagnings lås på prenumerations-eller resurs grupps nivå. Hjälp att ta bort borttagnings låset och försök att stoppa det igen.

### <a name="nodeunavailable"></a>NodeUnavailable

Det här felet uppstår under IR-körningen, det innebär att IR är hälso tillstånd innan och skadas, det är alltid på grund av att DNS-servern eller NSG-konfigurationen har ändrats och orsakar att SSIS IR inte kan ansluta till en beroende tjänst, hjälpa till att åtgärda DNS-serverns eller NSG konfigurations problem Mer information finns i [SSIS IR VNet Configuration](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Kontakta Azure Data Factory support-teamet om du fortfarande har problem.
