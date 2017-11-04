---
title: Azure skydda personliga data vilande med kryptering | Microsoft Docs
description: "Den här artikeln är en del av en serie kan du använda Azure för att skydda personliga data"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 2bb8370d23d9450fb8154f21c27817666fd7852c
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="azure-encryption-technologies-protect-personal-data-at-rest-with-encryption"></a>Azure krypteringstekniker: skydda personliga data vilande med kryptering

Den här artikeln hjälper dig att förstå och använda Azure krypteringstekniker för att skydda data i vila.

Kryptering av vilande data är mycket viktigt som bästa praxis att skydda känsliga eller personliga data och för att uppfylla efterlevnads- och sekretesskrav.
Kryptering i vila är utformat för att förhindra angripare från att komma åt den okrypterade data genom att säkerställa att data krypteras på disken.

## <a name="scenario"></a>Scenario 

Ett stort kryssning företag, sitt säte i USA utökar åtgärderna för att erbjuda färdvägar i Medelhavsområdet och baltiska havet samt Förenta staterna. För att stödja dessa ansträngningar genererade den flera mindre kryssning rader i Italien, Tyskland, Danmark och Storbritannien

Företaget använder Microsoft Azure för att lagra företagets data i molnet. Detta kan inkludera kund- och information som:

- adresser
- telefonnummer
- skatt ID-nummer
- kreditkortsinformation

Företaget måste skydda sekretessen för kund- och data när du gör data tillgängliga för de avdelningar som behöver den. (till exempel löneuppgifter och -reservationer avdelningar)

Raden kryssning har också en stor databas med medlemmar av trafik och förmåner som innehåller personuppgifter för att spåra relationer med aktuella och tidigare kunder.

### <a name="problem-statement"></a>Problembeskrivning

Företaget måste skydda kundernas och anställdas personliga data när du gör data tillgängliga för de avdelningar som behöver den (till exempel löneuppgifter och -reservationer avdelningar). Den här personliga data lagras utanför företagets kontrollerade datacentret och är inte under företagets fysisk kontroll.

### <a name="company-goal"></a>Företagets mål

Som en del av en flera lager skydd på djupet säkerhetsstrategi är det företagets mål att säkerställa att alla datakällor som innehåller personuppgifter krypteras, inklusive de som finns i molnet. Om obehöriga får tillgång till personliga data, måste den vara i ett formulär som kommer att visas inte kan läsas. Bör vara enkelt och transparent – för användare och administratörer att tillämpa kryptering.

## <a name="solutions"></a>Lösningar

Azure-tjänster ger flera verktyg och tekniker för att hjälpa dig att skydda personliga data i vila genom att kryptera den.

### <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis) tillhandahåller säker lagring för nycklar som används för att kryptera vilande data i Azure-tjänster och är den rekommenderade viktiga lagring och hantering av lösningen. Hantering av nycklar för kryptering är viktigt att skydda lagrade data.

#### <a name="how-do-i-use-azure-key-vault-to-protect-keys-that-encrypt-personal-data"></a>Hur använder Azure Key Vault för att skydda för att kryptera personliga data?

Med Azure Key Vault måste ha du en prenumeration på Azure-konto. Du måste också Azure PowerShell har installerats. Stegen omfattar med PowerShell-cmdletar för att göra följande:

1. Ansluta till dina prenumerationer

2. Skapa ett nyckelvalv

3. Lägga till en nyckel eller hemlighet till nyckelvalvet

4. Registrera program som ska använda nyckelvalvet med Azure Active Directory

5. Verifiera att programmen kan använda nyckel eller hemlighet.

Använd PowerShell-cmdleten New-AzureRmKeyVault för att skapa en nyckelvalvet. Du tilldelar en valvet namn, resursgruppens namn och geografisk plats. Vid hantering av nycklar via andra cmdletar ska du använda valvnamnet. Program som använder valvet via REST API använder valvet URI.

Azure Key Vault kan tillhandahålla en programvaruskyddad nyckel för dig, eller så kan du importera en befintlig nyckel i en. PFX-filen. Du kan också lagra hemligheter (lösenord) i valvet.

Du kan också skapa en nyckel i din lokala HSM och överföra den till HSM: er i Key Vault-tjänsten utan att lämna HSM-gräns nyckel.

För detaljerade anvisningar om hur du använder Azure Key Vault följer du stegen i [Kom igång med Azure Key Vault.](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-get-started)

En lista med PowerShell-Cmdlets som används med Azure Key Vault finns [AzureRM.KeyVault](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0).

### <a name="azure-disk-encryption-for-windows"></a>Azure Disk Encryption för Windows

[Azure Disk Encryption för Windows och Linux IaaS-VM](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption) skyddar personliga data i vila på virtuella Azure-datorer och kan integreras med Azure Key Vault. Azure Disk Encryption använder [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) i Windows och [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) i Linux för att kryptera både Operativsystemet och datadiskar. Azure Disk Encryption stöds på Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 och på Windows 8 och Windows 10-klienter.

#### <a name="how-do-i-use-azure-disk-encryption-to-protect-personal-data"></a>Hur använder Azure Disk Encryption för att skydda personliga data?

Med Azure Disk Encryption måste ha du en prenumeration på Azure-konto. Om du vill aktivera Azure Disk Encryption för Windows och Linux virtuella datorer, gör du följande:

1. Använda Azure Disk Encryption Resource Manager-mallen, PowerShell eller kommandoradsgränssnittet (CLI) för att aktivera kryptering och ange konfigurationen för kryptering. 

2. Bevilja åtkomst till Azure-plattformen läsa kryptering material från nyckelvalvet.

3. Ange en Programidentitet i Azure Active Directory (AAD) om du vill skriva kryptering nyckelmaterial till nyckelvalvet.

Azure kommer att uppdateras den virtuella datorn och konfigurationen som nyckelvalvet och konfigurera din krypterade VM.

När du ställer in nyckelvalvet som stöd för Azure Disk Encryption kan du lägga till en krypteringsnyckel nyckel (KEK) för extra säkerhet och stöd för säkerhetskopiering av krypterade virtuella datorer.

![](media/protect-personal-data-at-rest/create-key.png)

Detaljerade anvisningar för specifika distributionsscenarier och användarupplevelser ingår i [Azure Disk Encryption för Windows och Linux virtuella IaaS-datorer.](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption)

### <a name="azure-storage-service-encryption"></a>Kryptering av Azure-lagringstjänst

[Azure Storage Service kryptering (SSE) för Data i vila](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption) hjälper dig att skydda och skydda dina data för att uppfylla din organisations säkerhet och efterlevnad åtaganden. Azure Storage krypterar dina data med hjälp av 256-bitars AES-kryptering innan beständighet till lagring, och automatiskt dekrypterar före hämtning. Den här tjänsten är tillgänglig för Azure-BLOB och filer.

#### <a name="how-do-i-use-storage-service-encryption-to-protect-personal-data"></a>Hur använder Lagringstjänstens kryptering skyddar personuppgifter?

Om du vill aktivera Storage Service-kryptering, gör du följande:

1. Logga in på Azure-portalen.

2. Välj ett lagringskonto.

3. Välj kryptering i inställningar under avsnittet Blob-tjänsten.

4. Välj kryptering under avsnittet File Service.

När du har klickat på inställningen kryptering kan du aktivera eller inaktivera Storage Service-kryptering.

![](media/protect-personal-data-at-rest/storage-service-encryption.png)

Nya data att krypteras. Data i befintliga filer i det här lagringskontot kommer att vara okrypterad.

När du aktiverar kryptering, kopiera data till lagringskontot genom att använda någon av följande metoder:

1. Kopiera BLOB eller filer med den [AzCopy kommandoradsverktyget](https://docs.microsoft.com/en-us/azure/storage/storage-use-azcopy).

2. [Montera en filresurs med hjälp av SMB](https://docs.microsoft.com/en-us/azure/storage/storage-file-how-to-use-files-windows) så att du kan använda ett verktyg, exempelvis Robocopy för att kopiera filer.

3. Kopiera blob eller filen data till och från blobblagring eller mellan lagringskonton med [Lagringsklientbiblioteken, till exempel .NET](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-how-to-use-blobs).

4.  Använd en [Lagringsutforskaren](https://docs.microsoft.com/en-us/azure/storage/storage-explorers) att överföra blobbar till ditt lagringskonto med kryptering aktiverat.

### <a name="transparent-data-encryption"></a>Transparent datakryptering

Transparent Data kryptering (TDE) är en funktion i SQL Azure som du kan kryptera data på både databas- och programnivå. TDE är nu aktiverad som standard på alla nya databaser. TDE utför realtid i/o-kryptering och dekryptering av data och loggfilen filer.

#### <a name="how-do-i-use-tde-to-protect-personal-data"></a>Hur använder TDE för att skydda personliga data?

Du kan konfigurera TDE via Azure-portalen med hjälp av REST API eller med hjälp av PowerShell. Om du vill aktivera TDE i en befintlig databas i Azure Portal, gör du följande:

1. Besök den Azure-portalen på <https://portal.azure.com> och logga in med ditt Azure-administratör eller deltagare.

2. Klicka om du vill bläddra på banderollen vänster och klicka på SQL-databaser.

3. Med SQL-databaser som har valts i den vänstra rutan klickar du på databasen.

4. Klicka på alla inställningar i databasbladet.

5. I bladet inställningar klickar du på Transparent kryptering datadelen för att öppna bladet Transparent data kryptering.

6. I bladet för kryptering av Data vidare krypteringsknappen Data till och klicka på Spara (överst på sidan) för att använda inställningen. Krypteringsstatus kommer ungefär förloppet för transparent datakryptering.

![Aktivera kryptering av data](media/protect-personal-data-at-rest/turn-data-encryption-on.png)

Instruktioner om hur du aktiverar TDE och information om dekryptera TDE-skyddade databaser och mer finns i artikeln [Transparent datakryptering med Azure SQL Database.](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database)

## <a name="summary"></a>Sammanfattning

Företaget kan göra dess mål för att kryptera personliga data som lagras i Azure-molnet. De kan göra detta genom att använda Azure Disk Encryption för att skydda hela volymer. Detta kan inkludera både operativsystemets filer och datafiler som innehåller personligt identifierbar information och andra känsliga data. Azure Storage Service-kryptering kan användas för att skydda personliga data som lagras i BLOB och filer. För data som lagras i Azure SQL-databaser, skyddar Transparent datakryptering mot obehörig exponering av personlig information.

Företaget kan använda Azure Key Vault för att skydda de nycklar som används för att kryptera data i Azure. Detta förenklar nyckelhanteringen och gör att företaget behålla kontrollen över nycklar som kommer åt och krypterar personliga data.

## <a name="next-steps"></a>Nästa steg

- [Felsökningsguide för Azure Disk Encryption](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption-tsg)

- [Kryptera en virtuell Azure-dator](https://docs.microsoft.com/en-us/azure/security-center/security-center-disk-encryption?toc=%2fazure%2fsecurity%2ftoc.json)

- [Kryptering av data i Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-encryption)

- [Azure DB Cosmos databaskryptering i vila](https://docs.microsoft.com/en-us/azure/cosmos-db/database-encryption-at-rest)
