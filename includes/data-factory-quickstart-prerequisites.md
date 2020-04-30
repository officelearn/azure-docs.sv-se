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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "78164321"
---
## <a name="prerequisites"></a>Krav

### <a name="azure-subscription"></a>Azure-prenumeration
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

### <a name="azure-roles"></a>Azure-roller
Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna *deltagare* eller *ägare*, eller vara *administratör* för Azure-prenumerationen. Om du vill visa de behörigheter som du har i prenumerationen går du till [Azure Portal](https://portal.azure.com), väljer ditt användar namn i det övre högra hörnet, väljer ikonen "**...**" för fler alternativ och väljer sedan **mina behörigheter**. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration.

För att skapa och hantera underordnade resurser för Data Factory – inklusive datauppsättningar, länkade tjänster, pipelines, utlösare och integreringskörningar – gäller följande krav:

- För att kunna skapa och hantera underordnade resurser i Azure-portalen måste du tillhöra rollen **Data Factory-deltagare** på resursgruppsnivå eller högre.
- För att skapa och hantera underordnade resurser med PowerShell eller SDK räcker det att du har rollen som **deltagare** på resursnivå eller högre.

För exempel på instruktioner om hur du lägger till en användare till en roll läser du artikeln [Lägg till roller](../articles/cost-management-billing/manage/add-change-subscription-administrator.md).

Mer information finns i följande artiklar:

- [Rollen Data Factory-deltagare](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Roller och behörigheter för Azure Data Factory](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Azure Storage-konto
Du använder ett allmänt Azure Storage konto (särskilt Blob Storage) som både *käll* -och *mål* data lager i den här snabb starten. Om du inte har ett allmänt Azure Storage konto, se [skapa ett lagrings konto](../articles/storage/common/storage-account-create.md) för att skapa ett. 

#### <a name="get-the-storage-account-name"></a>Hämta lagrings kontots namn
Du behöver namnet på ditt Azure Storage-konto för den här snabb starten. Följande procedur innehåller steg för att hämta namnet på ditt lagrings konto: 

1. I en webbläsare går du till [Azure Portal](https://portal.azure.com) och loggar in med ditt användar namn och lösen ord för Azure.
2. Välj **alla tjänster**på Azure Portal-menyn och välj **lagrings** > **lagrings konton**. Du kan också söka efter och välja *lagrings konton* från vilken sida som helst.
3. På sidan **lagrings konton** filtrerar du ditt lagrings konto (om det behövs) och väljer sedan ditt lagrings konto. 

Du kan också söka efter och välja *lagrings konton* från vilken sida som helst.

#### <a name="create-a-blob-container"></a>Skapa en blobcontainer
I det här avsnittet skapar du en blobcontainer med namnet **adftutorial** i Azure Blob Storage.

1. Välj **Översikt** > **behållare**på sidan lagrings konto.
2. På sidan * \<konto namn>*  -  **behållarens** verktygsfält väljer du **behållare**.
3. I dialogrutan **Ny container** anger du **adftutorial** som namn och väljer **OK**. Sidan * \<konto namn>*  -  **behållare** uppdateras för att inkludera **adftutorial** i listan över behållare.

   ![Lista över behållare](media/data-factory-quickstart-prerequisites/list-of-containers.png)

#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>Lägg till en mapp för indata och en fil för BLOB-behållaren
I det här avsnittet ska du skapa en mapp med namnet **indata** i den behållare som du nyss skapade och sedan ladda upp en exempel fil till mappen indata. Innan du börjar måste du öppna en text redigerare, till exempel **anteckningar**, och skapa en fil med namnet **EMP. txt** med följande innehåll:

```emp.txt
John, Doe
Jane, Doe
```

Spara filen i mappen **C:\ADFv2QuickStartPSH** (Om mappen inte redan finns skapar du den.) Gå sedan tillbaka till Azure Portal och följ de här stegen:

1. På sidan * \<konto namn>*  -  **behållare** där du slutade väljer du **adftutorial** från den uppdaterade listan över behållare.

   1. Om du stängde fönstret eller gick till en annan sida loggar du in på [Azure Portal](https://portal.azure.com) igen.
   1. Välj **alla tjänster**på Azure Portal-menyn och välj **lagrings** > **lagrings konton**. Du kan också söka efter och välja *lagrings konton* från vilken sida som helst.
   1. Välj ditt lagrings konto och välj sedan **containers** > **adftutorial**.

2. I verktygsfältet för **adftutorial** container-sidan väljer du **Ladda upp**.
3. På sidan **Ladda upp BLOB** väljer du rutan **filer** och bläddrar sedan till och väljer filen **EMP. txt** .
4. Expandera den **avancerade** rubriken. Sidan visas nu som på det sätt som visas:

   ![Välj länken Avancerat](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
5. I rutan **Ladda upp till mapp** anger du **ininformation**.
6. Välj knappen **Ladda upp**. Du bör se filen **emp.txt** och uppladdningens status i listan.
7. Välj ikonen **Stäng** (a **X**) för att stänga sidan **Ladda upp BLOB** .

Behåll sidan **adftutorial** container öppen. Du kommer att använda den för att bekräfta utdata i slutet av snabbstarten.