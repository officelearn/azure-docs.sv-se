---
title: inkludera fil
description: inkludera fil
services: storage
author: twooley
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: twooley
ms.custom: include file
ms.openlocfilehash: 358de5761f3535b5f1e75d82539ee8e3ecdbd289
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665635"
---
**Azure Data Lake Storage Gen2** är inte en dedikerad tjänst eller en typ av lagrings konto. Det är den senaste versionen av funktioner som är avsedda för stor data analys.  Dessa funktioner är tillgängliga i ett General-Purpose v2-eller BlockBlobStorage-lagrings konto och du kan hämta dem genom att aktivera funktionen **hierarkiskt namn område** för kontot. För skalnings mål, se de här artiklarna. 

- [Skala mål för Blob Storage](https://docs.microsoft.com/azure/storage/blobs/scalability-targets#scale-targets-for-blob-storage).
- [Skala mål för standard lagrings konton](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json#scale-targets-for-standard-storage-accounts).

**Azure Data Lake Storage gen1** är en dedikerad tjänst. Det är en företagsomfattande storskalig lagrings plats för stora data analys arbets belastningar. Du kan använda Data Lake Storage Gen1 för att samla in data om storlek, typ och inmatnings hastighet på en enda plats för operativa och undersökande analyser. Det finns ingen gräns för mängden data som du kan lagra i ett Data Lake Storage Gen1 konto.

| **Resurs** | **Gräns** | **Kommentarer** |
| --- | --- | --- |
| Maximalt antal Data Lake Storage Gen1-konton, per prenumeration, per region |10 | Kontakta supporten om du vill begära en ökning av den här gränsen. |
| Maximalt antal åtkomst-ACL: er, per fil eller mapp |32 | Detta är en hård gräns. Använd grupper för att hantera åtkomst med färre poster. |
| Maximalt antal standard-ACL: er, per fil eller mapp |32 | Detta är en hård gräns. Använd grupper för att hantera åtkomst med färre poster. |
