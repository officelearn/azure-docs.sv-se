---
title: Azure Storage Service-kryptering av vilande Data | Microsoft Docs
description: "Funktionen Azure Storage Service-kryptering för att kryptera din Azure-Blobblagring på tjänstsidan när data lagrades och dekryptera den vid hämtning av data."
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: tamram
ms.openlocfilehash: d04752c92218d17b4e90bb03a2ed2ac5a0a11b93
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Azure Storage-tjänstens kryptering av vilande data
Azure Storage Service kryptering (SSE) för Data i vila hjälper dig att skydda och skydda dina data för att uppfylla din organisations säkerhet och efterlevnad åtaganden. Med den här funktionen krypterar Azure Storage automatiskt dina data för beständig lagring och dekrypterar dem före hämtning. Användarna har fullständig insyn i kryptering, dekryptering och nyckelhantering.

Följande avsnitt innehåller detaljerad information om hur du använder funktionerna Lagringstjänstens kryptering samt scenarier som stöds och användaren inträffar.

## <a name="overview"></a>Översikt
Azure Storage innehåller en omfattande uppsättning säkerhetsfunktioner som tillsammans ger utvecklare möjligheten att skapa säkra program. Data kan skyddas vid överföring mellan en program- och Azure med hjälp av [Client Side Encryption](../storage-client-side-encryption.md), HTTPs- eller SMB 3.0. Lagringstjänstens kryptering tillhandahåller kryptering i vila, hantering av kryptering, dekryptering och hantering av nycklar på ett helt transparent sätt. Krypteras alla data med hjälp av 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste blocket chiffer tillgängliga.

SSE fungerar genom att kryptera data när de skrivs till Azure Storage och kan användas för Azure Blob-, fil-, tabell- och Queue Storage. Det fungerar för följande:

* Standardlagring: Allmänna lagringskonton för BLOB storage för fil-, tabell- och kön och Blob storage-konton
* Premium Storage 
* Aktiverad som standard för klassisk och ARM Storage-konton 
* Alla redundans nivåer (LRS, ZRS, GRS och RA-GRS)
* Azure Resource Manager lagringskonton (men inte klassiska) 
* Alla regioner.

Mer information finns i vanliga frågor och svar.

Om du vill visa inställningarna för Storage Service-kryptering för ett lagringskonto, logga in på den [Azure-portalen](https://portal.azure.com) och välj ett lagringskonto. Sök efter avsnittet Blob-tjänsten som visas i den här skärmbilden och klicka på kryptering på bladet inställningar.

![Skärmbild som visar Portal krypteringsalternativet](./media/storage-service-encryption/image1.png)
<br/>*Bild 1: Aktivera SSE för Blob-tjänsten (steg 1)*

![Skärmbild som visar Portal krypteringsalternativet](./media/storage-service-encryption/image3.png)
<br/>*Bild 2: Aktivera SSE för tjänsten File (steg 1)*

När du har klickat på inställningen kryptering kan du aktivera eller inaktivera Storage Service-kryptering.

![Portalen skärmbild som visar egenskaper för kryptering](./media/storage-service-encryption/image2.png)
<br/>*Bild 3: Aktivera SSE för Blob och Filtjänst (steg 2)*

## <a name="encryption-scenarios"></a>Scenarier för kryptering
Lagringstjänstens kryptering är aktiverat på en nivå för storage-konto. Den är aktiverad för alla tjänster som standard. Den stöder följande kundscenarier:

* Kryptering av Blob-, fil-, tabell- och Queue Storage för både klassiska och Resource Manager-konton.

SSE har följande begränsningar:

* Befintliga Data - SSE krypterar endast nyskapade data efter kryptering har aktiverats. Om till exempel du skapar ett nytt lagringskonto för Resource Manager men inte aktiverar kryptering, och sedan ladda upp blobbar eller arkiverade virtuella hårddiskar till att lagringskontot och sedan aktivera SSE, krypteras inte de blobbar om de om eller kopieras.
* Stöd för Marketplace - Aktivera kryptering av virtuella datorer skapas från Marketplace med hjälp av den [Azure-portalen](https://portal.azure.com), PowerShell och Azure CLI. Grundläggande VHD-avbildningen kommer att vara okrypterad; alla skrivningar utföras när den virtuella datorn har kommer att krypteras.

## <a name="getting-started"></a>Komma igång
### <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Steg 1: [skapa ett nytt lagringskonto](../storage-create-storage-account.md).
### <a name="step-2-verify-encryption"></a>Steg 2: Kontrollera kryptering.
Du kan kontrollera kryptering använder den [Azure-portalen](https://portal.azure.com).

> [!NOTE]
> Om du vill kontrollera programmatiskt Lagringstjänstens kryptering på ett lagringskonto kan du använda den [Azure Storage Resource Provider REST API](https://msdn.microsoft.com/library/azure/mt163683.aspx), [Storage-klientbiblioteket för .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx) , [Azure PowerShell](/powershell/azureps-cmdlets-docs), eller [Azure CLI](../storage-azure-cli.md).
> 
> 

### <a name="step-3-copy-data-to-storage-account"></a>Steg 3: Kopiera data till storage-konto
När SSE har aktiverats för ett lagringskonto måste krypteras alla data som skrivs till detta lagringskonto. Alla data som redan finns i detta lagringskonto krypteras inte förrän de på nytt. Du kan kopiera data från en behållare till en annan så att tidigare data krypteras. Du kan använda någon av följande verktyg för att åstadkomma detta. Det här är samma beteende för fil-, tabell- och Queue Storage samt.

#### <a name="using-azcopy"></a>Med hjälp av AzCopy
AzCopy är en Windows-kommandoradsverktyg för att kopiera data till och från Microsoft Azure Blob-, fil- och Table storage med hjälp av enkla kommandon med optimal prestanda. Du kan använda den för att kopiera BLOB eller filer från ett lagringskonto till en annan som har SSE aktiverad. 

Mer information finns [överföra data med kommandoradsverktyget Azcopy](storage-use-azcopy.md).

#### <a name="using-smb"></a>Med hjälp av SMB
Azure Files erbjuder filresurser i molnet genom att använda SMB-standardprotokollet. Du kan montera en filresurs från en klient lokalt eller i Azure. När monterad, användas verktyg, exempelvis Robocopy för att kopiera filer över till Azure-filresurser. Mer information finns i [för Azure-filresurs på Windows](../files/storage-how-to-use-files-windows.md) och [för Azure-filresurs på Linux](../files/storage-how-to-use-files-linux.md).


#### <a name="using-the-storage-client-libraries"></a>Använda Lagringsklientbiblioteken
Du kan kopiera blob-eller till och från blobblagring eller mellan lagringskonton med hjälp av vår omfattande uppsättning Lagringsklientbiblioteken som .NET, C++, Java, Android, Node.js, PHP, Python eller Ruby.

Om du vill veta mer kan du besöka vårt [komma igång med Azure Blob storage med hjälp av .NET](../blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="using-a-storage-explorer"></a>Med en lagringsutforskare
Du kan använda en Lagringsutforskaren för att skapa storage-konton, ladda upp och hämta data, visa innehållet i blobbar och gå igenom kataloger. Du kan använda något av följande för att överföra blobbar till ditt lagringskonto med kryptering aktiverat. Med vissa lagringsutforskare kan du också kopiera data från befintliga blob storage till en annan behållare i storage-konto eller ett nytt lagringskonto som har aktiverat SSE.

Mer information finns [Azure lagringsutforskare](../storage-explorers.md).

### <a name="step-4-query-the-status-of-the-encrypted-data"></a>Steg 4: Fråga status för krypterade data
En uppdaterad version av Storage-klientbibliotek har distribuerats kan du fråga efter tillståndet för ett objekt för att avgöra om den är krypterad eller inte. Detta är för närvarande bara tillgänglig för Blob storage. Det finns stöd för lagring av filer på Översikt. 

Under tiden kan du anropa [hämta kontoegenskaperna](https://msdn.microsoft.com/library/azure/mt163553.aspx) att kontrollera att lagringskontot har kryptering aktiverat eller visa egenskaperna för storage-konto i Azure-portalen.

## <a name="encryption-and-decryption-workflow"></a>Kryptering och dekryptering arbetsflöde
Här är en kort beskrivning av arbetsflödet för kryptering/dekryptering:

* Kryptering är aktiverat för ett lagringskonto.
* När kunden skriver data (PLACERA Blob, PLACERA Block, PLACERA sidan, PLACERA filen o.s.v.) för lagring av Blob- eller fil. varje skrivning har krypterats med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste blocket chiffer tillgängliga.
* När kunden behöver åtkomst till data (hämta Blob, etc.), dekrypteras data automatiskt innan det returneras till användaren.
* Om kryptering har inaktiverats kan nya skrivningar krypteras inte längre och befintliga krypterade data förblir krypterade tills skrivas om av användaren. Även om kryptering har aktiverats, krypteras skrivningar till Blob- eller fillagring. Tillståndet för data ändras inte med användaren växla mellan aktivering/inaktivering av kryptering för lagringskontot.
* Alla krypteringsnycklar lagras, krypteras och hanteras av Microsoft.

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Vanliga frågor om Storage Service-kryptering för Data i vila
**F: Jag har ett befintligt klassiska lagringskonto. Kan jag aktivera SSE på den?**

S: SSE är aktiverad som standard för alla lagringskonton (klassiskt och Resource Manager storage-konton).

**F: hur kan jag för att kryptera data i mitt klassiska storage-konto?**

S: med kryptering aktiverat som standard, krypteras automatiskt nya data med storage-tjänst. 

Du kan också skapa ett nytt lagringskonto för hanteraren för filserverresurser och kopiera alla data med [AzCopy](storage-use-azcopy.md) från ditt befintliga klassiska storage-konto till ditt nya lagringskonto för hanteraren för filserverresurser. 

Du kan också välja att migrera klassiska storage-konto till ett lagringskonto för hanteraren för filserverresurser. Den här åtgärden är omedelbar, det ändrar typ av ditt konto men påverkar inte befintliga data. Alla nya data skrivs krypteras endast efter att aktivera kryptering. Mer information finns i [plattform stöd för migrering av IaaS-resurser från klassiska till Resource Manager](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/). Observera att detta stöds endast för Blob- och -tjänster.

**F: Jag har ett befintligt lagringskonto i hanteraren för filserverresurser. Kan jag aktivera SSE på den?**

S: SSE är aktiverat som standard på alla befintliga Resource Manager-lagringskonton. Detta stöds för Blobbar, tabeller, fil och Queue storage. 

**F: Jag vill att kryptera den aktuella informationen i ett befintligt lagringskonto i Resource Manager?**

S: med SSE aktiverad som standard för alla lagringskonton - klassisk och Resource Manager storage-konton. Data som redan fanns kommer inte krypteras. För att kryptera befintliga data, kan du kopiera dem till ett annat namn eller en annan behållare och tar bort okrypterad versioner. 

**F: Jag använder Premium-lagring. kan jag använda SSE?**

S: Ja stöds SSE både standardlagring och Premium-lagring.  Premium-lagring stöds inte för tjänsten.

**F: om jag skapar ett nytt lagringskonto med SSE aktiverats, skapar du en ny virtuell dator med hjälp av att lagringskontot, betyder att den virtuella datorn är krypterad?**

S: Ja. Alla diskar som skapats med det nya lagringskontot krypteras, förutsatt att de skapas efter SSE har aktiverats. Om den virtuella datorn har skapats med hjälp av Azure-marknadsplatsen, VHD-basavbildning kommer att vara okrypterad; alla skrivningar utföras när den virtuella datorn har kommer att krypteras.

**F: kan jag skapa ny storage-konton med SSE aktiverad med Azure PowerShell och Azure CLI?**

S: SSE aktiveras som standard vid tidpunkten för att skapa någon lagringskonto (klassiskt och Resource Manager-konton). Du kan kontrollera egenskaper med både Azure PowerShell och Azure CLI.

**F: hur mycket kostar Azure Storage om SSE aktiveras?**

S: finns utan extra kostnad.

**F: som hanterar krypteringsnycklarna?**

S: nycklarna hanteras av Microsoft.

**F: kan jag använda min egen krypteringsnycklar?**

S: Vi arbetar på att ge funktioner för kunder att ta sina egna krypteringsnycklar.

**F: kan jag återkalla åtkomst till krypteringsnycklarna?**

S: inte vid detta tillfälle. nycklarna är fullständigt hanteras av Microsoft.

**F: är SSE aktiverad som standard när jag skapar ett nytt lagringskonto?**

S: Ja är SSE med Microsoft Managed nycklar aktiverad som standard för alla lagringskonton - Azure Resource Manager och klassiska lagringskonton. Det är aktiverat för alla tjänster samt - Blob-, tabell, kö och fillagring.

**F: hur skiljer detta sig från Azure Disk Encryption?**

S: funktionen används för att kryptera data i Azure Blob storage. Azure Disk Encryption används för att kryptera Operativsystemet och datadiskarna i IaaS-VM. Mer information finns våra [säkerhetsguiden för lagring](../storage-security-guide.md).

**F: Vad händer om SSE aktiveras och sedan jag aktivera Azure Disk Encryption på diskarna?**

S: Detta fungerar sömlöst. Data krypteras med båda metoderna.

**F: mitt lagringskonto har konfigurerats som ska replikeras geo-redundant. Om SSE aktiveras min redundant kopia även krypteras?**

S: Ja, krypteras alla kopior av lagringskontot och alla redundansalternativ för – lokalt Redundant lagring (LRS), zon-Redundant lagring (ZRS), Geo-Redundant lagring (GRS) och Geo-Redundant lagring med läsbehörighet (RA-GRS) – stöds.

**Fråga: Det går inte att inaktivera kryptering för storage-konto.**

S: kryptering är aktiverat som standard och skall inte att inaktivera kryptering för ditt lagringskonto. 

**F: SSE tillåts endast i vissa regioner?**

S: SSE är tillgänglig i alla regioner för alla tjänster. 

**F: hur kan jag få någon om jag har problem eller vill ge feedback?**

A: Kontakta [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) för eventuella problem som rör Storage Service-kryptering.

## <a name="next-steps"></a>Nästa steg
Azure Storage innehåller en omfattande uppsättning säkerhetsfunktioner för, vilket tillsammans ger utvecklare möjligheten att skapa säkra program. Mer information finns i [säkerhetsguiden för lagring](../storage-security-guide.md).
