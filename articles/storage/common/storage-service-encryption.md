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
ms.openlocfilehash: 772c36c8310a4bf30c62def507382fe74427e0d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Azure Storage-tjänstens kryptering av vilande data
Azure Storage Service kryptering (SSE) för Data i vila hjälper dig att skydda och skydda dina data för att uppfylla din organisations säkerhet och efterlevnad åtaganden. Med den här funktionen Azure Storage krypterar dina data innan beständighet till lagring och automatiskt dekrypterar före hämtning. Kryptering, dekryptering och hantering av nycklar är helt transparent för användarna.

Följande avsnitt innehåller detaljerad information om hur du använder funktionerna Lagringstjänstens kryptering samt scenarier som stöds och användaren inträffar.

## <a name="overview"></a>Översikt
Azure Storage innehåller en omfattande uppsättning säkerhetsfunktioner som tillsammans ger utvecklare möjligheten att skapa säkra program. Data kan skyddas vid överföring mellan en program- och Azure med hjälp av [Client Side Encryption](../storage-client-side-encryption.md), HTTPs- eller SMB 3.0. Lagringstjänstens kryptering tillhandahåller kryptering i vila, hantering av kryptering, dekryptering och hantering av nycklar på ett helt transparent sätt. Krypteras alla data med hjälp av 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste blocket chiffer tillgängliga.

SSE fungerar genom att kryptera data när de skrivs till Azure Storage och kan användas för Azure Blob Storage och File Storage. Det fungerar för följande:

* Standardlagring: Allmänna lagringskonton för BLOB- och lagrings- och Blob storage-konton
* Premium Storage 
* Alla redundans nivåer (LRS, ZRS, GRS och RA-GRS)
* Azure Resource Manager lagringskonton (men inte klassiska) 
* Alla regioner.

Mer information finns i vanliga frågor och svar.

Om du vill aktivera eller inaktivera Storage Service-kryptering för ett lagringskonto, logga in på den [Azure-portalen](https://portal.azure.com) och välj ett lagringskonto. Sök efter avsnittet Blob-tjänsten som visas i den här skärmbilden och klicka på kryptering på bladet inställningar.

![Skärmbild som visar Portal krypteringsalternativet](./media/storage-service-encryption/image1.png)
<br/>*Bild 1: Aktivera SSE för Blob-tjänsten (steg 1)*

![Skärmbild som visar Portal krypteringsalternativet](./media/storage-service-encryption/image3.png)
<br/>*Bild 2: Aktivera SSE för tjänsten File (steg 1)*

När du har klickat på inställningen kryptering kan du aktivera eller inaktivera Storage Service-kryptering.

![Portalen skärmbild som visar egenskaper för kryptering](./media/storage-service-encryption/image2.png)
<br/>*Bild 3: Aktivera SSE för Blob och Filtjänst (steg 2)*

## <a name="encryption-scenarios"></a>Scenarier för kryptering
Lagringstjänstens kryptering kan aktiveras på en nivå för storage-konto. När du har aktiverat, kommer kunderna välja vilka tjänster att kryptera. Den stöder följande kundscenarier:

* Kryptering av Blob Storage och File Storage i Resource Manager-konton.
* Kryptering av Blob och tjänsten File i klassiska lagringskonton när migreras till Resource Manager storage-konton.

SSE har följande begränsningar:

* Kryptering av klassiska lagringskonton stöds inte.
* Befintliga Data - SSE krypterar endast nyskapade data efter kryptering har aktiverats. Om till exempel du skapar ett nytt lagringskonto för Resource Manager men inte aktiverar kryptering, och sedan ladda upp blobbar eller arkiverade virtuella hårddiskar till att lagringskontot och sedan aktivera SSE, krypteras inte de blobbar om de om eller kopieras.
* Stöd för Marketplace - Aktivera kryptering av virtuella datorer skapas från Marketplace med hjälp av den [Azure-portalen](https://portal.azure.com), PowerShell och Azure CLI. Grundläggande VHD-avbildningen kommer att vara okrypterad; alla skrivningar utföras när den virtuella datorn har kommer att krypteras.
* Tabellen och köer data krypteras inte.

## <a name="getting-started"></a>Komma igång
### <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Steg 1: [skapa ett nytt lagringskonto](../storage-create-storage-account.md).
### <a name="step-2-enable-encryption"></a>Steg 2: Aktivera kryptering.
Du kan aktivera kryptering med hjälp av den [Azure-portalen](https://portal.azure.com).

> [!NOTE]
> Om du vill programmässigt, aktivera eller inaktivera Storage Service-kryptering på ett lagringskonto kan du använda den [Azure Storage Resource Provider REST API](https://msdn.microsoft.com/library/azure/mt163683.aspx), [Storage-klientbiblioteket för .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx), [Azure PowerShell](/powershell/azureps-cmdlets-docs), eller [Azure CLI](../storage-azure-cli.md).
> 
> 

### <a name="step-3-copy-data-to-storage-account"></a>Steg 3: Kopiera data till storage-konto
Om du aktiverar SSE för Blob-tjänsten kommer att krypteras alla blobbar som skrivs till detta lagringskonto. Alla blobbar som redan finns i detta lagringskonto krypteras inte förrän de på nytt. Du kan kopiera data från ett lagringskonto till en med SSE krypterade eller även aktivera SSE och kopierar blobar från en behållare till en annan säker tidigare data som är krypterade. Du kan använda någon av följande verktyg för att åstadkomma detta. Det här är samma beteende för lagring av filer samt.

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

* Kunden aktiverar kryptering på lagringskontot.
* När kunden skriver data (PLACERA Blob, PLACERA Block, PLACERA sidan, PLACERA filen o.s.v.) för lagring av Blob- eller fil. varje skrivning har krypterats med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste blocket chiffer tillgängliga.
* När kunden behöver åtkomst till data (hämta Blob, etc.), dekrypteras data automatiskt innan det returneras till användaren.
* Om kryptering har inaktiverats kan nya skrivningar krypteras inte längre och befintliga krypterade data förblir krypterade tills skrivas om av användaren. Även om kryptering har aktiverats, krypteras skrivningar till Blob- eller fillagring. Tillståndet för data ändras inte med användaren växla mellan aktivering/inaktivering av kryptering för lagringskontot.
* Alla krypteringsnycklar lagras, krypteras och hanteras av Microsoft.

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Vanliga frågor om Storage Service-kryptering för Data i vila
**F: Jag har ett befintligt klassiska lagringskonto. Kan jag aktivera SSE på den?**

S: inte stöds SSE bara på Resource Manager storage-konton.

**F: hur kan jag för att kryptera data i mitt klassiska storage-konto?**

S: du kan skapa ett nytt lagringskonto för hanteraren för filserverresurser och kopiera dina data med hjälp av [AzCopy](storage-use-azcopy.md) från ditt befintliga klassiska storage-konto till ditt nya lagringskonto för hanteraren för filserverresurser. 

Om du migrerar klassiska storage-konto till ett lagringskonto för hanteraren för filserverresurser åtgärden är omedelbar, ändrar typ av ditt konto, men påverkas inte befintliga data. Alla nya data skrivs krypteras endast efter att aktivera kryptering. Mer information finns i [plattform stöd för migrering av IaaS-resurser från klassiska till Resource Manager](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/). Observera att detta stöds endast för Blob- och -tjänster.

**F: Jag har ett befintligt lagringskonto i hanteraren för filserverresurser. Kan jag aktivera SSE på den?**

S: Ja, men endast nyligen skrivs data att krypteras. Det inte gå tillbaka och kryptera data som redan finns. Detta stöds inte ännu för förhandsgranskning för lagring av filer.

**F: Jag vill att kryptera den aktuella informationen i ett befintligt lagringskonto i Resource Manager?**

S: du kan aktivera SSE när som helst i ett lagringskonto för hanteraren för filserverresurser. Data som redan fanns kommer inte krypteras. För att kryptera befintliga data, kan du kopiera dem till ett annat namn eller en annan behållare och tar bort okrypterad versioner.

**F: Jag använder Premium-lagring. kan jag använda SSE?**

S: Ja stöds SSE både standardlagring och Premium-lagring.  Premium-lagring stöds inte för tjänsten.

**F: om jag skapa ett nytt lagringskonto och aktivera SSE och sedan skapa en ny virtuell dator med hjälp av detta lagringskonto, betyder det den virtuella datorn är krypterad?**

S: Ja. Alla diskar som skapats med det nya lagringskontot krypteras, förutsatt att de skapas efter SSE har aktiverats. Om den virtuella datorn har skapats med hjälp av Azure-marknadsplatsen, VHD-basavbildning kommer att vara okrypterad; alla skrivningar utföras när den virtuella datorn har kommer att krypteras.

**F: kan jag skapa ny storage-konton med SSE aktiverad med Azure PowerShell och Azure CLI?**

S: Ja.

**F: hur mycket kostar Azure Storage om SSE aktiveras?**

S: finns utan extra kostnad.

**F: som hanterar krypteringsnycklarna?**

S: nycklarna hanteras av Microsoft.

**F: kan jag använda min egen krypteringsnycklar?**

S: Vi arbetar på att ge funktioner för kunder att ta sina egna krypteringsnycklar.

**F: kan jag återkalla åtkomst till krypteringsnycklarna?**

S: inte vid detta tillfälle. nycklarna är fullständigt hanteras av Microsoft.

**F: är SSE aktiverad som standard när jag skapar ett nytt lagringskonto?**

S: SSE är inte aktiverad som standard. Du kan använda Azure-portalen för att aktivera den. Du kan också programmässigt aktivera den här funktionen med Storage Resource Provider REST API.

**F: hur skiljer detta sig från Azure Disk Encryption?**

S: funktionen används för att kryptera data i Azure Blob storage. Azure Disk Encryption används för att kryptera Operativsystemet och datadiskarna i IaaS-VM. Mer information finns våra [säkerhetsguiden för lagring](../storage-security-guide.md).

**F: Vad händer om jag aktivera SSE, och sedan gå in i och aktivera Azure Disk Encryption på diskarna?**

S: Detta fungerar sömlöst. Data krypteras med båda metoderna.

**F: mitt lagringskonto har konfigurerats som ska replikeras geo-redundant. Om jag aktiverar SSE min redundant kopia även krypteras?**

S: Ja, krypteras alla kopior av lagringskontot och alla redundansalternativ för – lokalt Redundant lagring (LRS), zon-Redundant lagring (ZRS), Geo-Redundant lagring (GRS) och Geo-Redundant lagring med läsbehörighet (RA-GRS) – stöds.

**F: Jag kan inte aktivera kryptering på mitt lagringskonto.**

S: är det en Resource Manager storage-konto? Klassiska lagringskonton stöds inte. 

**F: SSE tillåts endast i vissa regioner?**

S: SSE är tillgänglig i alla regioner för Blob storage. Mer information finns i avsnittet för tillgänglighet för lagring av filer. 

**F: hur kan jag få någon om jag har problem eller vill ge feedback?**

S: Kontakta [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) för eventuella problem som rör Storage Service-kryptering.

## <a name="next-steps"></a>Nästa steg
Azure Storage innehåller en omfattande uppsättning säkerhetsfunktioner som tillsammans ger utvecklare möjligheten att skapa säkra program. Mer information finns i [säkerhetsguiden för lagring](../storage-security-guide.md).

