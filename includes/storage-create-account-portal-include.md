---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ea8ed75bf91850abb95ebe983923989375c0fcf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76759864"
---
Följ de här stegen för att skapa ett GPv2-konto för generell användning i Azure Portal:

1. Välj **Alla tjänster** på menyn i Azure-portalen. Skriv **lagringskonton** i listan över resurser. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Lagringskonton**.
2. På fönstret **lagringskonton** som visas, väljer du **lägg till**.
3. Välj den prenumeration där du vill skapa lagringskontot.
4. Under fältet **Resursgrupp** väljer du **Skapa ny**. Ange ett namn för din nya resursgrupp som du ser i följande bild.

    ![Skärmbild som visar hur du skapar en resursgrupp i portalen](./media/storage-create-account-portal-include/create-resource-group-for-storage.png)

5. Ange sedan ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure. Namnet måste också bestå av mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener.
6. Välj en plats för ditt lagringskonto eller använd standardplatsen.
7. Lämna dessa fält med respektive standardvärde:

   |Field  |Värde  |
   |---------|---------|
   |Distributionsmodell     |Resource Manager         |
   |Prestanda     |Standard         |
   |Typ av konto     |StorageV2 (generell användning v2)         |
   |Replikering     |Geo-redundant lagring med läsbehörighet (RA-GRS)         |
   |Åtkomstnivå     |Frekvent         |

8. Om du planerar att använda [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)väljer du fliken **Avancerat** och anger sedan **hierarkiskt namnområde** till **Aktiverad**.
9. Välj **Granska + skapa** för att granska inställningarna för ditt lagringskonto och skapa kontot.
10. Välj **Skapa**.

Mer information om typer av lagringskonton och andra inställningar för lagringskonto finns i [översikten över Azure-lagringskonton](https://docs.microsoft.com/azure/storage/common/storage-account-overview). Mer information om resursgrupper finns i [översikt över Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 
