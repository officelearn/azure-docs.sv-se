---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 30/03/2020
ms.author: orspodek
ms.openlocfilehash: 9ae820097f8515dc44c67d95366f96c241cb77a1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80523055"
---
## <a name="select-an-ingestion-type"></a>Välj en inmatningstyp

För **inmatningstyp**väljer du något av följande alternativ:
   * **från lagring** - i fältet **Länk till lagring** lägger du till URL:en för lagringskontot. Använd [Blob SAS-URL](/azurevs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) för privata lagringskonton.
   
      ![Inmatning med ett klick från lagring](media/data-explorer-one-click-ingestion-types/from-storage-blob.png)

    * **från fil** - välj **Bläddra** för att hitta filen eller dra filen till fältet.
  
      ![Inmatning med ett klick från filen](media/data-explorer-one-click-ingestion-types/from-file.png)

    * **från behållare** - i fältet **Länk till lagring** lägger du till [SAS-URL:en](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) för behållaren och anger eventuellt exempelstorleken.

      ![Inmatning med ett klick från behållaren](media/data-explorer-one-click-ingestion-types/from-container.png)

  Ett exempel på data visas. Om du vill kan du filtrera den så att den bara visas filer som börjar med specifika tecken. När du justerar filtren uppdateras förhandsgranskningen automatiskt.
  
  Du kan till exempel filtrera efter alla filer som börjar med ordet *data* och sluta med ett *CSV.gz-tillägg.*

  ![Inmatningsfilter med ett klick](media/data-explorer-one-click-ingestion-types/from-container-with-filter.png)
