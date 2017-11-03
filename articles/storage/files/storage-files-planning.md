---
title: "Planera för distribution av en Azure-filer | Microsoft Docs"
description: "Lär dig vad du bör tänka på när du planerar för distribution av en Azure-filer."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: wgries
ms.openlocfilehash: c4f997b994bb337ad8a886d7ad09791cb587d4f9
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2017
---
# <a name="planning-for-an-azure-files-deployment"></a>Planera för distribution av Azure Files
[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via SMB standardprotokoll. Eftersom Azure Files är fullständigt hanterad är distribuera i produktion scenarier mycket enklare än att distribuera och hantera en filserver eller en NAS-enhet. Den här artikeln tar information att tänka på när du distribuerar en Azure-filresurs för produktion inom din organisation.

## <a name="management-concepts"></a>Management-begrepp
 Följande diagram illustrerar Azure Files management-konstruktioner:

![Filstruktur](./media/storage-files-introduction/files-concepts.png)

* **Lagringskonto**: All åtkomst till Azure Storage görs genom ett lagringskonto. Se [Skalbarhets- och prestandamål](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för information om kapacitet för lagringskonton.

* **Resurs**: En File Storage-resurs är en SMB-filresurs i Azure. Alla kataloger och filer måste skapas i en överordnad resurs. Ett konto kan innehålla ett obegränsat antal resurser och en resurs kan lagra ett obegränsat antal filer, upp till 5 TiB totala kapacitet filresursen.

* **Katalog:** En valfri hierarki med kataloger.

* **Fil:** En fil i resursen. En fil kan vara upp till 1 TiB i storlek.

* **Webbadressformatet**: för begäranden till en Azure-filresurs som gjorts med filen REST-protokollet, filer är adresserbara via följande URL-format:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/directories>/<file>
    ```

## <a name="data-access-method"></a>Dataåtkomstmetod
Azure filer erbjuder två inbyggda och praktiskt dataåtkomst metoder som du kan använda separat eller i kombination med varandra, åtkomst till data:

1. **Direktåtkomst molnet**: alla Azure-filresurs kan monteras av [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md), och/eller [Linux](storage-how-to-use-files-linux.md) med industry standard Server Message Block ( SMB-protokollet) eller via REST-API-fil. Med SMB görs läsning och skrivning till filer på resursen direkt på en filresurs i Azure. Om du vill montera av en virtuell dator i Azure, SMB-klienten i Operativsystemet stöder minst SMB 2.1. Montera lokalt, t.ex. på en användares arbetsstation SMB-klienten som stöds av arbetsstationen stöder minst SMB 3.0 (med kryptering). Förutom SMB, nya program eller tjänster kan direkt åtkomst till filresursen via filen vila, vilket ger ett enkelt och skalbar programmeringsgränssnitt för programutveckling.
2. **Azure filsynkronisering** (förhandsversion): med Azure filsynkronisering resurser kan replikeras till Windows-servrar på lokalt eller i Azure. Användarna kan komma åt filresursen via Windows-Server som via en SMB- eller NFS-resurs. Detta är användbart för scenarier där data komma åt och ändra långt från ett Azure-datacenter, som i en filialkontorsscenario. Data kan replikeras mellan flera Windows Server-slutpunkter som mellan flera olika avdelningskontor. Slutligen kan nivåer data till Azure Files, så att alla data är tillgängliga via servern, men servern har inte en fullständig kopia av data. Data återställs i stället sömlöst när öppnas av dina användare.

Följande tabell visar hur användare och program kan komma åt Azure-filresurs:

| | Direkt molnåtkomst | Azure filsynkronisering |
|------------------------|------------|-----------------|
| Vilka protokoll du behöver använda? | Azure Files stöder SMB 2.1 och SMB 3.0-filen REST API. | Åtkomst till din Azure-filresursen via alla protokoll som stöds på Windows Server (SMB, NFS, FTPS, etc.) |  
| Var körs din arbetsbelastning? | **I Azure**: Azure-filer har direkt åtkomst till dina data. | **Lokalt med långsamma nätverk**: Windows, Linux och macOS klienter kan montera en lokal lokala Windows-filresurs som en snabb cache på Azure-filresursen. |
| Vilken nivå av ACL: er behöver du? | Resurs- och nivå. | Resurs-, fil- och användaren nivå. |

## <a name="data-security"></a>Datasäkerhet
Azure Files har flera inbyggda alternativen för att säkerställa datasäkerheten:

* Stöd för kryptering i båda protokollen över överföring: kryptering i SMB 3.0 och File REST via HTTPS. Som standard: 
    * Klienter som stöder kryptering i SMB 3.0 skicka och ta emot data via en krypterad kanal.
    * Klienter som inte stöder SMB 3.0 kan kommunicera inom datacentret över SMB 2.1 eller SMB 3.0 utan kryptering. Observera att klienter inte kan kommunicera mellan datacenter över SMB 2.1 eller SMB 3.0 utan kryptering.
    * Klienter kan kommunicera över RESTEN av filen med HTTP eller HTTPS.
* Kryptering i vila ([Azure Storage Service-kryptering](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): Vi håller på att aktivera kryptering för lagring-tjänsten (SSE) på den underliggande Azure Storage-plattformen. Det innebär att kryptering aktiveras som standard för alla lagringskonton. Om du skapar ett nytt lagringskonto i en region med kryptering i vila på standard behöver du göra något för att aktivera. Data i vila är krypterad med fullständigt hanterade nycklar. Kryptering i vila inte öka kostnader för lagring och minska prestanda. 
* Valfria behovet av krypterade data under överföring: när du väljer Azure-filer tillåter inte åtkomst till data via okrypterat kanaler. Mer specifikt tillåts endast HTTPS och SMB 3.0 med kryptering anslutningar. 

    > [!Important]  
    > Kräver säker överföring av data kommer äldre SMB-klienter inte kan kommunicera med SMB 3.0 med kryptering misslyckas. Se [montera på Windows](storage-how-to-use-files-windows.md), [montera på Linux](storage-how-to-use-files-linux.md), [montera på macOS](storage-how-to-use-files-mac.md) för mer information.

För maximal säkerhet rekommenderar vi att aktivera både kryptering i vila och aktivera kryptering av data under överföring när du använder moderna klienter för att komma åt dina data alltid. Om du behöver ansluta till en resurs på en Windows Server 2008 R2 VM, som bara stöder SMB 2.1, måste du tillåta okrypterad trafik till ditt lagringskonto eftersom SMB 2.1 inte stöder kryptering.

Om du använder Azure filsynkronisering åtkomst till din Azure-filresurs, använder vi alltid HTTPS och SMB 3.0 med kryptering för att synkronisera dina data till din Windows-servrar, oavsett om du kräver kryptering av data i vila.

## <a name="data-redundancy"></a>Dataredundans
Azure Files stöder två alternativ för redundans: lokalt redundant lagring (LRS) och geo-redundant lagring (GRS). I följande avsnitt beskrivs skillnaderna mellan lokalt redundant lagring och geo-redundant lagring:

### <a name="locally-redundant-storage"></a>Lokalt redundant lagring
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="geo-redundant-storage"></a>Geografiskt redundant lagring.
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="data-growth-pattern"></a>Tillväxt datamönster
Idag är den maximala storleken för en Azure-filresurs 5 TiB, inklusive resurs ögonblicksbilder. På grund av den här tillfälliga begränsningen måste du tillväxt förväntade data när du distribuerar en Azure-filresurs. Observera att ett Azure Storage-konto kan lagra flera resurser med totalt 500 TiB lagras över alla resurser.

Det är möjligt att synkronisera flera Azure-filresurser på en enda Windows-filserver med synkronisering av Azure-filen. På så sätt kan du se till att äldre, mycket stora filresurser som du kan ha lokalt kan anslutas till Azure filsynkronisering. Se [planera för en distribution av Azure Sync](storage-files-planning.md) för mer information.

## <a name="data-transfer-method"></a>Metoden för dataöverföring
Det finns många enkelt alternativ att Massredigera överför data från en befintlig fil delar, till exempel en lokalt på en filresurs för till Azure-filer. Några populära format är (ofullständig lista):

* **Azure filsynkronisering**: som en del av en första synkronisering mellan en Azure-filresurs (en ”Molnslutpunkt”) och ett Windows directory namnområde (en ”Server slutpunkt”), kommer Azure filsynkronisering replikera alla data från den befintliga filresursen till Azure Files.
* **[Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: I Azure Import/Export-tjänsten kan du på ett säkert sätt överföra stora mängder data i en filresurs i Azure genom att leverera hårddiskar till ett Azure-datacenter. 
* **[ROBOCOPY](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy är ett välkänt kopiera verktyg som levereras med Windows och Windows Server. ROBOCOPY kan användas för att överföra data till Azure-filer genom att montera filresursen lokalt och sedan använda monterade platsen som mål i kommandot Robocopy.
* **[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)**: AzCopy är ett kommandoradsverktyg som utformats för att kopiera data till och från Azure-filer, samt Azure Blob storage med hjälp av enkla kommandon med optimal prestanda. AzCopy är tillgänglig för Windows och Linux.

## <a name="next-steps"></a>Nästa steg
* [Planera för distribution av en Azure-synkronisering](storage-sync-files-planning.md)
* [Distribuera Azure-filer](storage-files-deployment-guide.md)
* [Distribuera Azure filsynkronisering](storage-sync-files-deployment-guide.md)