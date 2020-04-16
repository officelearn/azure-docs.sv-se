---
title: Felsöka hantering av SSIS-integreringskörning
description: Den här artikeln innehåller felsökningsvägledning för hanteringsproblem för SSIS Integration Runtime (SSIS IR)
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: 0324044d93f12f6ac6ec96ff1a31be8ee02ada41
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414701"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Felsöka hantering av SSIS-integreringskörning i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln innehåller felsökningsvägledning för hanteringsproblem i Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR), även känd som SSIS IR.

## <a name="overview"></a>Översikt

Om du stöter på problem när du etablerar eller avetableras SSIS IR visas ett felmeddelande i Microsoft Azure Data Factory-portalen eller ett fel som returneras från en PowerShell-cmdlet. Felet visas alltid i formatet för en felkod med ett detaljerat felmeddelande.

Om felkoden är InternalServerError har tjänsten tillfälliga problem och du bör försöka igen åtgärden senare. Om ett nytt försök inte hjälper kontaktar du Supportteamet för Azure Data Factory.

Annars kan tre stora externa beroenden orsaka fel: en Azure SQL Database-server eller hanterad instans, ett anpassat installationsskript och en virtuell nätverkskonfiguration.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Azure SQL Database server eller hanterade instansproblem

En Azure SQL Database-server eller en hanterad instans krävs om du etablerar SSIS IR med en SSIS-katalogdatabas. SSIS IR måste kunna komma åt Azure SQL Database-servern eller den hanterade instansen. Dessutom ska kontot för Azure SQL Database-servern eller den hanterade instansen ha behörighet att skapa en SSIS-katalogdatabas (SSISDB). Om det uppstår ett fel visas en felkod med ett detaljerat SQL-undantagsmeddelande i Data Factory-portalen. Använd informationen i följande lista för att felsöka felkoderna.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Det här felet kan uppstå när du etablerar en ny SSIS IR eller medan IR körs. Om det här felet uppstår under IR-etableringen får du kanske ett detaljerat SqlException-meddelande i felmeddelandet som anger något av följande problem:

* Ett problem med nätverksanslutning. Kontrollera om det går att komma åt värddatornamnet för SQL Server eller hanterad instans. Kontrollera även att ingen brandvägg eller nätverkssäkerhetsgrupp (NSG) blockerar SSIS IR-åtkomst till servern.
* Inloggningen misslyckades under SQL-autentisering. Det angivna kontot kan inte logga in på SQL Server-databasen. Se till att du anger rätt användarkonto.
* Inloggningen misslyckades under Microsoft Azure Active Directory-autentisering (Azure AD) (hanterad identitet). Lägg till den hanterade identiteten för din fabrik till en AAD-grupp och se till att den hanterade identiteten har åtkomstbehörighet till din katalogdatabasserver.
* Tidsgräns för anslutning. Det här felet orsakas alltid av en säkerhetsrelaterad konfiguration. Vi rekommenderar att du gör följande:
  1. Skapa en ny virtuell dator.
  1. Gå med i den virtuella datorn till samma Microsoft Azure Virtual Network of IR om IR finns i ett virtuellt nätverk.
  1. Installera SSMS och kontrollera Azure SQL Database-servern eller statusen för hanterade instanser.

För andra problem löser du det problem som visas i det detaljerade felmeddelandet för SQL-undantag. Om det fortfarande är problem kontaktar du supportteamet för Azure SQL Database-servern eller hanterad instans.

Om felet uppstår när IR körs förhindrar ändringar i en nätverkssäkerhetsgrupp eller brandvägg förmodligen SSIS IR-arbetsnoden från att komma åt Azure SQL Database-servern eller den hanterade instansen. Avblockera SSIS IR-arbetsnoden så att den kan komma åt Azure SQL Database-servern eller den hanterade instansen.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Så här kan den här typen av felmeddelande se ut: "Databasen 'SSISDB' har nått sin storlekskvot. Partitionera eller ta bort data, släpp index eller läs dokumentationen för möjliga lösningar." 

Möjliga lösningar är följande:
* Öka kvotstorleken för din SSISDB.
* Ändra konfigurationen för SSISDB för att minska storleken genom att:
   * Minska kvarhållningsperioden och antalet projektversioner.
   * Minska kvarhållningsperioden för loggen.
   * Ändra loggens standardnivå.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Det här felet innebär att Azure SQL Database-servern eller den hanterade instansen redan har en SSISDB och att den används av en annan IR. Du behöver antingen ange en annan Azure SQL Database-server eller hanterad instans eller ta bort befintlig SSISDB och sedan starta om den nya IR:en.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Felet kan uppstå på grund av någon av följande orsaker:

* Det användarkonto som har konfigurerats för SSIS IR har inte behörighet att skapa databasen. Du kan bevilja användaren behörighet att skapa databasen.
* En överskriden tidsgräns inträffar när databasen skapas, till exempel en tidsgräns för körning eller för databasåtgärd. Du bör försöka åtgärden på nytt senare. Om det nya försöket inte fungerar kontaktar du supportteamet för Azure SQL Database-servern eller hanterad instans.

För andra problem läser du felmeddelandet om SQL-undantag och åtgärdar det problem som beskrivs i felinformationen. Om det fortfarande är problem kontaktar du supportteamet för Azure SQL Database-servern eller hanterad instans.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Den här typen av felmeddelande ser ut så här: "Ogiltigt objektnamn 'catalog.catalog_properties'." I det här fallet har du antingen redan en databas med namnet SSISDB men den skapades inte av SSIS IR, eller så är databasen i ett ogiltigt tillstånd som orsakas av fel i den senaste SSIS IR-etableringen. Du kan frigöra den befintliga databasen med namnet SSISDB, eller så kan du konfigurera en ny Azure SQL Database-server eller en hanterad instans för IR.

## <a name="custom-setup-issues"></a>Problem med anpassade inställningar

Anpassad installation tillhandahåller ett gränssnitt där du kan lägga till egna installationssteg under etablering eller omkonfiguration av din SSIS IR. Mer information finns i [Anpassa installation för Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Kontrollera att din container endast innehåller nödvändiga filer för anpassad installation. Alla filer i containern laddas ned till SSIS IR-arbetsnoden. Vi rekommenderar att du testar skriptet för anpassad installation på en lokal dator för att åtgärda eventuella problem med skriptkörning innan du kör skriptet i SSIS IR.

Containern för anpassat installationsskript kontrolleras medan IR körs eftersom SSIS IR uppdateras regelbundet. Den här uppdateringen kräver åtkomst till containern för att ladda ned skriptet för anpassad installation och installera det igen. Processen kontrollerar även om containern är tillgänglig och om filen main.cmd finns.

För alla fel som innebär anpassad installation visas en CustomSetupScriptFailure-felkod med underkod som CustomSetupScriptBlobContainerInaccessible eller CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Det här felet innebär att SSIS IR inte kan komma åt din Azure-blobcontainer för anpassad installation. Kontrollera att containerns SAS-URI kan nås och inte har upphört att gälla.

Stoppa IR om den körs, konfigurera om IR med en ny SAS-URI för container för anpassad installation och starta sedan om IR.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Det här felet innebär att SSIS IR inte kan hitta ett skript för anpassad installation (main.cmd) i blobcontainern. Se till att main.cmd finns i containern, vilket är startpunkten för anpassad installation.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Detta fel innebär att körningen av skriptet för anpassad inställningar (main.cmd) misslyckades. Testa skriptet på den lokala datorn först eller kontrollera körningsloggarna för anpassad installation i blobcontainern.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Det här felet anger en överskriden tidsgräns för körningen av skriptet för anpassad installation. Kontrollera att skriptet kan köras i tysthet och att det inte behövs några interaktiva indata, och se till att blobcontainern bara innehåller nödvändiga anpassade installationsfiler. Vi rekommenderar att du först testar skriptet på den lokala datorn. Du bör även kontrollera körningsloggarna för anpassad installation i blobcontainern. Den maximala tidsperioden för anpassad installation är 45 minuter innan tidsgränsen uppnås, och i den maximala perioden ingår tiden för att ladda ned alla filer från din container och installera dem på SSIS IR. Om du behöver en längre period kan du skapa en supportbegäran.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Det här felet innebär att försöket att ladda upp körningsloggar för anpassad installation till din blobcontainer misslyckades. Det här problemet inträffar antingen på grund av att SSIS IR inte har skrivbehörighet till din blobcontainer eller på grund av problem med lagring eller nätverk. Om den anpassade installationen lyckas påverkar det här felet inte någon SSIS-funktion, men loggarna kommer att saknas. Om anpassad installation misslyckas med ett annat fel och loggen inte laddas upp kommer vi att rapportera det här felet först så att loggen kan laddas upp för analys. När det här problemet har lösts rapporterar vi eventuella mer specifika problem. Om problemet inte har lösts efter ett nytt försök kontaktar du supportteamet för Azure Data Factory.

## <a name="virtual-network-configuration"></a>Konfiguration av virtuellt nätverk

När du kopplar SSIS IR till Azure Virtual Network använder SSIS IR det virtuella nätverk som finns under användarprenumerationen. Mer information finns i [Koppla en Azure-SSIS Integration Runtime till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

När det finns ett problem som rör virtuellt nätverk visas något av följande fel.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Det här felet kan inträffa av olika orsaker. Information om hur du felsöker det finns i avsnitten [Förbjudet](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) och [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings).

### <a name="forbidden"></a>Förbjudet

Den här typen av fel kan likna detta: "SubnetId är inte aktiverat för bytesbalansen. Microsoft.Batch-resursprovidern registreras inte under samma prenumeration på virtuella nätverk."

Den här informationen innebär att Azure Batch inte kan komma åt ditt virtuella nätverk. Registrera Microsoft.Batch-resursprovidern under samma prenumeration som Virtual Network.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Den här typen av fel kan se ut på något av följande sätt: 

- "Antingen finns inte det angivna virtuella nätverket eller så har batch-tjänsten inte åtkomst till det."
- "Det angivna undernätet xxx finns inte."

Dessa fel innebär att det virtuella nätverket inte finns, att Azure Batch-tjänsten inte kan komma åt det eller att det angivna undernätet inte finns. Kontrollera att det virtuella nätverket och undernätet finns och att Azure Batch kan komma åt dem.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Den här typen av felmeddelande kan se ut så här: "Det gick inte att etablera Integration Runtime i VNet. Om DNS-servern eller NSG-inställningarna är konfigurerade kontrollerar du att DNS-servern är tillgänglig och att NSG är korrekt konfigurerat."

I den här situationen har du förmodligen en anpassad konfiguration av DNS-serverns eller NSG-inställningarna, vilket förhindrar att det Azure-servernamn som krävs av SSIS IR matchas eller nås. Mer information finns i [Konfiguration av virtuellt nätverk för SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Om det fortfarande är problem kan du kontakta supportteamet för Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR uppdateras automatiskt med jämna mellanrum. En ny Azure Batch-pool skapas under uppgraderingen, och den gamla Azure Batch-poolen tas bort. Dessutom tas Virtual Network-relaterade resurser för den gamla poolen bort, och de nya Virtual Network-relaterade resurserna skapas under din prenumeration. Det här felet innebär att borttagning av Virtual Network-relaterade resurser för den gamla poolen misslyckades på grund av ett borttagningslås på prenumerations- eller resursgruppsnivå. Eftersom kunden kontrollerar och anger borttagningslåset måste kunden ta bort borttagningslåset i den här situationen.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Om SSIS IR-etableringen misslyckas tas alla resurser som skapades bort. Men om det finns ett resursborttagningslås på prenumerations -eller resursgruppsnivå tas Virtual Network-resurser inte bort som förväntat. Du kan åtgärda det här felet genom att ta bort borttagningslåset och starta om IR.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

När du stoppar SSIS IR tas alla Virtual Network-relaterade resurser bort. Men borttagningen kan misslyckas om det finns ett resursborttagningslås på prenumerations- eller resursgruppsnivå. Även i det här fallet är det kunden som kontrollerar och anger borttagningslåset. Därför måste kunden ta bort borttagningslåset och sedan stoppa SSIS IR igen.

### <a name="nodeunavailable"></a>NodeUnavailable

Det här felet uppstår när IR körs och innebär att IR har blivit defekt. Det här felet orsakas alltid av en ändring i den DNS-server- eller NSG-konfiguration som blockerar SSIS IR från att ansluta till en nödvändig tjänst. Eftersom konfigurationen av DNS-server och NSG kontrolleras av kunden måste kunden åtgärda blockeringsproblemen på deras sida. Mer information finns i [Konfiguration av virtuellt nätverk för SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Om det fortfarande är problem kan du kontakta supportteamet för Azure Data Factory.

## <a name="static-public-ip-addresses-configuration"></a>Statisk konfiguration av offentliga IP-adresser

När du ansluter till Azure-SSIS IR till Azure Virtual Network kan du också ta med egna statiska offentliga IP-adresser för IR så att IR kan komma åt datakällor som begränsar åtkomsten till specifika IP-adresser. Mer information finns i [Koppla en Azure-SSIS Integration Runtime till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Förutom ovanstående problem med virtuella nätverk kan du också uppfylla statiska offentliga IP-adresser-relaterade problem. Kontrollera följande fel för hjälp.

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>Ogiltigt Offentligt BESpecificerat

Det här felet kan uppstå av olika anledningar när du startar Azure-SSIS IR:

| Felmeddelande | Lösning|
|:--- |:--- |
| Den angivna statiska offentliga IP-adressen används redan, ange två oanvända för din Azure-SSIS Integration Runtime. | Du bör välja två oanvända statiska offentliga IP-adresser eller ta bort aktuella referenser till den angivna offentliga IP-adressen och sedan starta om Azure-SSIS IR. |
| Den angivna statiska offentliga IP-adressen har inget DNS-namn, ange två av dem med DNS-namn för din Azure-SSIS Integration Runtime. | Du kan ställa in DNS-namnet på den offentliga IP-adressen i Azure-portalen, som bilden nedan visar. Specifika steg är följande: (1) Öppna Azure-portalen och gå till resurssidan för den här offentliga IP-adressen. (2) Välj **avsnittet Konfiguration** och konfigurera DNS-namnet och klicka sedan på Knappen **Spara.** (3) Starta om din Azure-SSIS IR. |
| De angivna virtuella nätverken och statiska offentliga IP-adresser för din Azure-SSIS-integrationskörning måste finnas på samma plats. | Enligt Azure Networks krav ska den statiska offentliga IP-adressen och det virtuella nätverket vara på samma plats och prenumeration. Ange två giltiga statiska offentliga IP-adresser och starta om Azure-SSIS IR. |
| Den angivna statiska offentliga IP-adressen är grundläggande, ange två standardadresser för din Azure-SSIS Integration Runtime. | Mer hjälp finns [i SKU:er för offentlig IP-adress.](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) |

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>Offentlig IPResourceGroupLockedUnderingStart

Om Azure-SSIS IR-etablering misslyckas tas alla resurser som har skapats bort. Men om det finns ett resursborttagningslås på prenumerations- eller resursgruppen (som innehåller din statiska offentliga IP-adress) nivå tas nätverksresurserna inte bort som förväntat. Åtgärda felet genom att ta bort borttagningslåset och starta om IR:n.

### <a name="publicipresourcegrouplockedduringstop"></a>OffentligaIPResourceGroupLockedUnderingStop

När du stoppar Azure-SSIS IR tas alla nätverksresurser som skapas i resursgruppen som innehåller din offentliga IP-adress bort. Men borttagning kan misslyckas om det finns ett resursborttagningslås på prenumerations- eller resursgruppen (som innehåller din statiska offentliga IP-adress) nivå. Ta bort borttagningslåset och starta om IR:et.

### <a name="publicipresourcegrouplockedduringupgrade"></a>OffentligaIPResourceGroupLockedUnderingUpgrade

Azure-SSIS IR uppdateras automatiskt regelbundet. Nya IR-noder skapas under uppgraderingen och de gamla noderna tas bort. Dessutom tas de skapade nätverksresurserna (t.ex. belastningsutjämnaren och nätverkssäkerhetsgruppen) för de gamla noderna bort och de nya nätverksresurserna skapas under prenumerationen. Det här felet innebär att det gick inte att ta bort nätverksresurserna för de gamla noderna på grund av ett borttagningslås på prenumerations- eller resursgruppen (som innehåller din statiska offentliga IP-adress). Ta bort borttagningslåset så att vi kan rensa de gamla noderna och släppa den statiska offentliga IP-adressen för de gamla noderna. Annars kan den statiska offentliga IP-adressen inte släppas och vi kommer inte att kunna uppgradera din IR ytterligare.

### <a name="publicipnotusableduringupgrade"></a>PublicIPAnvändbaraUnderuppgradering

När du vill ta med egna statiska offentliga IP-adresser bör två offentliga IP-adresser tillhandahållas. En av dem kommer att användas för att skapa IR-noder omedelbart och en annan kommer att användas under uppgraderingen av IR. Det här felet kan uppstå när den andra offentliga IP-adressen är oanvändbar under uppgraderingen. Se [InvalidPublicIP Angiven](#InvalidPublicIPSpecified) för möjliga orsaker.