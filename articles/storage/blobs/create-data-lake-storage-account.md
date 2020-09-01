---
title: Skapa ett lagrings konto för Azure Data Lake Storage Gen2
description: Lär dig hur du skapar ett lagrings konto för användning med Azure Data Lake Storage Gen2.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 08/31/2020
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 6b63933fc625079bb490942cf3a32232a484fe38
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270327"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Skapa ett lagrings konto som ska användas med Azure Data Lake Storage Gen2

Om du vill använda Data Lake Storage Gen2 funktioner skapar du ett lagrings konto med ett hierarkiskt namn område.

## <a name="choose-a-storage-account-type"></a>Välj en lagrings konto typ

Data Lake Storage funktioner stöds i följande typer av lagrings konton:

- General-purpose v2
- BlockBlobStorage

Information om hur du väljer mellan dem finns i [Översikt över lagrings konto](../common/storage-account-overview.md).

## <a name="create-a-storage-account-with-a-hierarchical-namespace"></a>Skapa ett lagrings konto med ett hierarkiskt namn område

Skapa antingen ett [Allmänt-Purpose v2-konto](../common/storage-account-create.md) eller ett [BlockBlobStorage](storage-blob-create-account-block-blob.md) -konto med inställningen **hierarkiskt namn område** aktive rad.

Lås upp Data Lake Storage-funktioner när du skapar kontot genom att aktivera inställningen **hierarkiskt namn område** på fliken **Avancerat** på sidan **skapa lagrings konto** . Du måste aktivera den här inställningen när du skapar kontot. Du kan inte aktivera det senare.

Följande bild visar den här inställningen på sidan **skapa lagrings konto** .

> [!div class="mx-imgBorder"]
> ![Inställning av hierarkiskt namn område](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Om du har ett befintligt lagrings konto som du vill använda med Data Lake Storage och inställningen hierarkiskt namn område är inaktive rad måste du migrera data till ett nytt lagrings konto där inställningen är aktive rad.

## <a name="next-steps"></a>Nästa steg

- [Översikt över lagrings konto](../common/storage-account-overview.md)
- [Använda Azure Data Lake Storage Gen2 för Big data-krav](data-lake-storage-data-scenarios.md)
- [Åtkomstkontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)