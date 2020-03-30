---
title: ta med fil
description: ta med fil
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 06/27/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 4d77cb8128105a40143a40e48ebe450115f7cf1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164321"
---
## <a name="prerequisites"></a>Krav

### <a name="azure-subscription"></a>Azure-prenumeration
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

### <a name="azure-roles"></a>Azure-roller
Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna *deltagare* eller *ägare*, eller vara *administratör* för Azure-prenumerationen. Om du vill visa de behörigheter som du har i prenumerationen går du till [Azure-portalen](https://portal.azure.com), väljer ditt användarnamn i det övre högra hörnet, väljer "**...**" ikon för fler alternativ och väljer sedan **Mina behörigheter**. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration.

För att skapa och hantera underordnade resurser för Data Factory – inklusive datauppsättningar, länkade tjänster, pipelines, utlösare och integreringskörningar – gäller följande krav:

- För att kunna skapa och hantera underordnade resurser i Azure-portalen måste du tillhöra rollen **Data Factory-deltagare** på resursgruppsnivå eller högre.
- För att skapa och hantera underordnade resurser med PowerShell eller SDK räcker det att du har rollen som **deltagare** på resursnivå eller högre.

För exempel på instruktioner om hur du lägger till en användare till en roll läser du artikeln [Lägg till roller](../articles/cost-management-billing/manage/add-change-subscription-administrator.md).

Mer information finns i följande artiklar:

- [Rollen Data Factory-deltagare](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Roller och behörigheter för Azure Data Factory](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Azure Storage-konto
Du använder ett allmänt Azure Storage-konto (särskilt Blob-lagring) som både *käll-* och *måldatalager* i den här snabbstarten. Om du inte har ett Azure Storage-konto för allmänt syfte läser du [Skapa ett lagringskonto](../articles/storage/common/storage-account-create.md) för att skapa ett. 

#### <a name="get-the-storage-account-name"></a>Hämta lagringskontonamnet
Du behöver namnet på ditt Azure Storage-konto för den här snabbstarten. Följande procedur innehåller steg för att få namnet på ditt lagringskonto: 

1. I en webbläsare går du till [Azure-portalen](https://portal.azure.com) och loggar in med ditt Azure-användarnamn och lösenord.
2. På Azure-portal-menyn väljer du **Alla tjänster**och väljer sedan**Lagringslagringskonton** **Storage** > . Du kan också söka efter och välja *Lagringskonton* från valfri sida.
3. På sidan **Lagringskonton** filtrerar du efter ditt lagringskonto (om det behövs) och väljer sedan ditt lagringskonto. 

Du kan också söka efter och välja *Lagringskonton* från valfri sida.

#### <a name="create-a-blob-container"></a>Skapa en blobcontainer
I det här avsnittet skapar du en blobcontainer med namnet **adftutorial** i Azure Blob Storage.

1. På sidan lagringskonto väljer du > **Översiktsbehållare**. **Overview**
2. Välj **Behållare**i  -  **Containers** * \< *verktygsfältet Kontonamn>behållare .
3. I dialogrutan **Ny container** anger du **adftutorial** som namn och väljer **OK**. Sidan * \<Kontonamn>*  -  **Behållare** uppdateras så att den innehåller **adftutorial** i listan över behållare.

   ![Lista över behållare](media/data-factory-quickstart-prerequisites/list-of-containers.png)

#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>Lägga till en indatamapp och fil för blob-behållaren
I det här avsnittet skapar du en mapp med namnet **indata** i behållaren som du just skapade och överför sedan en exempelfil till indatamappen. Innan du börjar öppnar du en textredigerare som **Anteckningar**och skapar en fil med namnet **emp.txt** med följande innehåll:

```emp.txt
John, Doe
Jane, Doe
```

Spara filen i mappen **C:\ADFv2QuickStartPSH.** (Om mappen inte redan finns skapar du den.) Gå sedan tillbaka till Azure-portalen och följ dessa steg:

1. I * \<sidan Kontonamn>*  -  **Behållare** där du slutade väljer du **adftutorial** i den uppdaterade listan med behållare.

   1. Om du stängde fönstret eller gick till en annan sida loggar du in på [Azure-portalen](https://portal.azure.com) igen.
   1. På Azure-portal-menyn väljer du **Alla tjänster**och väljer sedan**Lagringslagringskonton** **Storage** > . Du kan också söka efter och välja *Lagringskonton* från valfri sida.
   1. Välj ditt lagringskonto och välj sedan **Behållare** > **adftutorial**.

2. Välj **Ladda upp**i verktygsfältet för **adftutorial-behållarsidan** .
3. På sidan **Ladda upp blob** markerar du rutan **Filer** och bläddrar sedan till och väljer **filen emp.txt.**
4. Expandera rubriken **Avancerat.** Sidan visas nu som visad:

   ![Välj länken Avancerat](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
5. Ange **indata**i rutan **Ladda upp till** till .
6. Välj knappen **Ladda upp**. Du bör se filen **emp.txt** och uppladdningens status i listan.
7. Välj **ikonen Stäng** (ett **X)** för att stänga sidan **Ladda upp blob.**

Håll **adftutorial-behållarsidan** öppen. Du kommer att använda den för att bekräfta utdata i slutet av snabbstarten.