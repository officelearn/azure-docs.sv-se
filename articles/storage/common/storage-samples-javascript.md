---
title: Azure Storage exempel med Java Script | Microsoft Docs
description: Visa, ladda ned och kör exempel kod och program för Azure Storage. Identifiera exempel på att komma igång med blobbar, köer, tabeller och filer med hjälp av Java Script/Node. js-lagrings klient bibliotek.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/26/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 44fe68b8b04a1192c928e04c7d2a9d147f400130
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748287"
---
# <a name="azure-storage-samples-using-javascript"></a>Azure Storage exempel med hjälp av Java Script

Följande tabeller ger en översikt över våra exempel lager och scenarier som beskrivs i varje exempel. Klicka på länkarna om du vill visa motsvarande exempel kod i GitHub.

> [!NOTE]
> I de här exemplen används biblioteket Azure Storage JavaScript-v10. V12-kod finns i [exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples) i GitHub-lagringsplatsen.

## <a name="blob-samples-v10"></a>BLOB-exempel (v10)

| **Scenario** | **Exempelkod** |
|--------------|-----------------|
| Blockblob | [Komma igång med Azure Blob service i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L43) |
| Kryptering av klientsidan | [Hantera lagrings konto nycklar i Azure-nyckel värde med Java Script](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| Kopiera Blob | [Komma igång med Azure Blob service i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L73) |
| Skapa behållare | [Komma igång med Azure Blob service i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L54) |
| Ta bort Blob | [Komma igång med Azure Blob service i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L103) |
| Ta bort behållare | [Komma igång med Azure Blob service i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L110) |
| BLOB-metadata | [Komma igång med Azure Blob service i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L538) |
| BLOB-egenskaper | [Komma igång med Azure Blob service i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L478) |
| Behållar-ACL | [Komma igång med Azure Blob service i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L444) |
| Metadata för behållare | [Komma igång med Azure Blob service i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L409) |
| Egenskaper för behållare | [Komma igång med Azure Blob service i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L377) |
| Hämta sid intervall | [Komma igång med Azure Blob service i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L170) |
| Låna blob | [Komma igång med Azure Blob service i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L216) |
| Lease container | [Komma igång med Azure Blob service i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L185) |
| Lista BLOB/container | [Komma igång med Azure Blob service i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L134) |
| Sid-BLOB | [Komma igång med Azure Blob service i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L129) |
| SAS | [Signatur för delad åtkomst i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L257) |
| Tjänstegenskaper | [Komma igång med Azure Blob service i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L308) |
| Ange CORS-regler | [Komma igång med Azure Blob service i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L152) |
| Ögonblicks bilds-BLOB | [Komma igång med Azure Blob service i Java Script](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L79) |

## <a name="file-samples-v10"></a>Fil exempel (v10)

| **Scenario** | **Exempelkod** |
|--------------|-----------------|
| Skapa resurser/kataloger/filer | [Komma igång med Azure File Service i Java Script](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L97) |
| Ta bort resurser/kataloger/filer | [Komma igång med Azure File Service i Java Script](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L135) |
| Hämta filer | [Komma igång med Azure File Service i Java Script](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L128) |
| Lista över kataloger och filer | [Komma igång med Azure File Service i Java Script](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L115) |
| List resurser | [Komma igång med Azure File Service i Java Script](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L187) |

## <a name="queue-samples-v10"></a>Queue-exempel (v10)

| **Scenario** | **Exempelkod** |
|--------------|-----------------|
| Lägg till meddelande | [Komma igång med Azure Queue Service i Java Script](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L142) |
| Kryptering av klientsidan | [Hantera lagrings konto nycklar i Azure Key Vault med Java Script](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| Skapa köer | [Komma igång med Azure Queue Service i Java Script](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L57) |
| Ta bort meddelande | [Komma igång med Azure Queue Service i Java Script](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L164) |
| Ta bort kö | [Komma igång med Azure Queue Service i Java Script](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L203) |
| Lista köer | [Komma igång med Azure Queue Service i Java Script](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L111) |
| Granska meddelande | [Komma igång med Azure Queue Service i Java Script](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L170) |
| Kös-ACL | [Komma igång med Azure Queue Service i Java Script](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L192) |
| Queue CORS-regler | [Komma igång med Azure Queue Service i Java Script](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L55) |
| Köa metadata | [Komma igång med Azure Queue Service i Java Script](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L161) |
| Egenskaper för Queue Service | [Komma igång med Azure Queue Service i Java Script](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L94) |
| Köa statistik | [Komma igång med Azure Queue Service i Java Script](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L149) |
| Uppdatera meddelande | [Komma igång med Azure Queue Service i Java Script](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L176) |

## <a name="table-samples-v10"></a>Tabell exempel (v10)

| **Scenario** | **Exempelkod** |
|--------------|-----------------|
| Batch-entiteter | [Komma igång med Azure Table service i Java Script](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L87) |
| Skapa tabell | [Komma igång med Azure Table service i Java Script](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L41) |
| Ta bort entitet/tabell | [Komma igång med Azure Table service i Java Script](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L67) |
| Infoga/sammanfoga/Ersätt entitet | [Komma igång med Azure Table service i Java Script](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) |
| List tabeller | [Komma igång med Azure Table service i Java Script](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L63) |
| Fråga entiteter | [Komma igång med Azure Table service i Java Script](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L59) |
| Frågetabeller | [Komma igång med Azure Table service i Java Script](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L140) |
| Intervall fråga | [Komma igång med Azure Table service i Java Script](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L102) |
| SAS | [Signatur för delad åtkomst i Java Script](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L87) |
| Tabell-ACL | [Komma igång med Azure Table service i Java Script](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L255) |
| Tabell CORS-regler | [Komma igång med Azure Table service i Java Script](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L149) |
| Tabell egenskaper | [Komma igång med Azure Table service i Java Script](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L188) |
| Tabell statistik | [Komma igång med Azure Table service i Java Script](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L243) |
| Uppdatera entitet | [Komma igång med Azure Table service i Java Script](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) |

## <a name="azure-code-samples-library"></a>Bibliotek för Azure kod exempel

Om du vill visa det kompletta exempel biblioteket går du till biblioteket [Azure kod exempel](https://azure.microsoft.com/resources/samples/?service=storage) , som innehåller exempel för Azure Storage som du kan hämta och köra lokalt. Kod exempel biblioteket innehåller exempel kod i zip-format. Du kan också bläddra och klona GitHub-lagringsplatsen för varje exempel.

[!INCLUDE [storage-node-samples-include](../../../includes/storage-node-samples-include.md)]

## <a name="getting-started-guides"></a>Komma igång guider

Kolla in följande guider om du behöver instruktioner om hur du installerar och kommer igång med Azure Storage klient bibliotek.

* [Komma igång med Azure Blob service i Java Script](../blobs/storage-quickstart-blobs-nodejs.md)
* [Komma igång med Azure Queue Service i Java Script](../queues/storage-nodejs-how-to-use-queues.md)
* [Komma igång med Azure Table service i Java Script](../../cosmos-db/table-storage-how-to-use-nodejs.md)

## <a name="next-steps"></a>Nästa steg

Information om exempel för andra språk:

* .NET: [Azure Storage exempel med .net](storage-samples-dotnet.md)
* Java: [Azure Storage exempel med Java](storage-samples-java.md)
* Python: [Azure Storage exempel med python](storage-samples-python.md)
* Alla andra språk: [Azure Storage exempel](storage-samples.md)
