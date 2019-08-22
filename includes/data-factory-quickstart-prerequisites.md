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
ms.openlocfilehash: 0a2bf306ccfa3a4a4fc80d0d55493f284185bba9
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648455"
---
## <a name="prerequisites"></a>Förutsättningar

### <a name="azure-subscription"></a>Azure-prenumeration
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

### <a name="azure-roles"></a>Azure-roller
Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna *deltagare* eller *ägare*, eller vara *administratör* för Azure-prenumerationen. Om du vill visa de behörigheter som du har i prenumerationen går du till [Azure Portal](https://portal.azure.com), väljer ditt användar namn i det övre högra hörnet, väljer **fler alternativ** (...) och väljer sedan **mina behörigheter**. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration.

För att skapa och hantera underordnade resurser för Data Factory – inklusive datauppsättningar, länkade tjänster, pipelines, utlösare och integreringskörningar – gäller följande krav:

- För att kunna skapa och hantera underordnade resurser i Azure-portalen måste du tillhöra rollen **Data Factory-deltagare** på resursgruppsnivå eller högre.
- För att skapa och hantera underordnade resurser med PowerShell eller SDK räcker det att du har rollen som **deltagare** på resursnivå eller högre.

För exempel på instruktioner om hur du lägger till en användare till en roll läser du artikeln [Lägg till roller](../articles/billing/billing-add-change-azure-subscription-administrator.md).

Mer information finns i följande artiklar:

- [Rollen Data Factory-deltagare](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Roller och behörigheter för Azure Data Factory](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Azure Storage-konto
I den här snabbstarten använder du ett allmänt Azure Storage-konto (Blob Storage, för att vara specifik) som datalager för både *källa* och *destination*. Om du inte har något allmänt Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../articles/storage/common/storage-quickstart-create-account.md). 

#### <a name="get-the-storage-account-name"></a>Hämta lagrings kontots namn
Du behöver namnet på ditt Azure Storage-konto för den här snabb starten. Följande procedur innehåller steg för att hämta namnet på ditt lagrings konto: 

1. I en webbläsare går du till [Azure Portal](https://portal.azure.com) och loggar in med ditt användar namn och lösen ord för Azure.
2. Välj **alla tjänster** > **lagrings** > **lagrings konton**.
3. På sidan **lagrings konton** filtrerar du ditt lagrings konto (om det behövs) och väljer sedan ditt lagrings konto. 

#### <a name="create-a-blob-container"></a>Skapa en blobcontainer
I det här avsnittet skapar du en blobcontainer med namnet **adftutorial** i Azure Blob Storage.

1. På sidan lagrings konto väljer du **översikts** > **blobbar**.
2. På sidan -   *kontonamn>blobs-sidans\<* verktygsfält väljer du behållare.
3. I dialogrutan **Ny container** anger du **adftutorial** som namn och väljer **OK**.  -   *Sidankontonamn>blobbaruppdaterasförattinkluderaadftutorialilistan\<* över behållare.

   ![Lista över behållare](media/data-factory-quickstart-prerequisites/list-of-containers.png)

#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>Lägg till en mapp för indata och en fil för BLOB-behållaren
I det här avsnittet ska du skapa en mapp med namnet indata i den behållare som du nyss skapade och sedan ladda upp en exempel fil till mappen indata. Innan du börjar måste du öppna en text redigerare, till exempel **anteckningar**, och skapa en fil med namnet **EMP. txt** med följande innehåll:

```emp.txt
John, Doe
Jane, Doe
```

Spara filen i mappen **C:\ADFv2QuickStartPSH** (Om mappen inte redan finns skapar du den.) Gå sedan tillbaka till Azure Portal och följ de här stegen:

1. På sidan -   *kontonamn>blobbardärduslutade,väljerduadftutorialfrånden\<* uppdaterade listan över behållare. (Om du stängde fönstret eller gick till en annan sida loggar du in på [Azure Portal](https://portal.azure.com) igen, väljer **alla tjänster** > **lagrings** > **lagrings konton**, väljer ditt lagrings konto och väljer sedan **blobbar** **adftutorial.** )  > 
2. I verktygsfältet för **adftutorial** container-sidan väljer du **Ladda upp**.
3. På sidan **Ladda upp BLOB** väljer du rutan **filer** och bläddrar sedan till och väljer filen **EMP. txt** .
4. Expandera den **avancerade** rubriken. Sidan visas nu som på det sätt som visas:

   ![Välj länken Avancerat](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
5. I rutan **Ladda upp till mapp** anger du **ininformation**.
6. Välj knappen **Ladda upp**. Du bör se filen **emp.txt** och uppladdningens status i listan.
7. Välj ikonen **Stäng** (a **X**) för att stänga sidan **Ladda upp BLOB** .

Behåll sidan **adftutorial** container öppen. Du kommer att använda den för att bekräfta utdata i slutet av snabbstarten.