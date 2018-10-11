---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/18/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d59cb64e1499f703bf6913ab422d0f979caca324
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47008748"
---
Följ de här stegen för att skapa ett GPv2-konto för generell användning i Azure-portalen:

1. I Azure-portalen expanderar du menyn på vänster sida för att öppna tjänstemenyn och välja **Alla tjänster**. Rulla ned till **lagring** och välj **lagringskonton**. På fönstret **lagringskonton** som visas, väljer du **lägg till**.
1. Välj den prenumeration där du vill skapa lagringskontot.
1. Under fältet **Resursgrupp** klickar du på **Skapa ny**. Ange ett namn för din nya resursgrupp som du ser i följande bild.

    ![Skärmbild som visar hur du skapar en resursgrupp i portalen](./media/storage-create-account-portal-include/create-resource-group.png)

1. Ange sedan ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure, mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.
1. Välj en plats för ditt lagringskonto eller använd standardplatsen.
1. Lämna dessa fält med respektive standardvärde:
    - Fältet **Distributionsmodell** är inställt på **Resource Manager** som standard.
    - Fältet **Prestanda** är inställt på **Standard** som standard.
    - Fältet **Typ av konto** är inställt på **StorageV2 (generell användning v2)**.
    - Fältet **Replikering** är inställt på **Lokalt redundant lagring (LRS)** som standard.
    - **Åtkomstnivå** är inställt på **Frekvent** som standard.

1. Klicka på **Granska + skapa** för att granska inställningarna för ditt lagringskonto och skapa kontot.

Mer information om typer av lagringskonton och andra inställningar för lagringskonto finns i [översikten över Azure-lagringskonton](https://docs.microsoft.com/azure/storage/common/storage-account-overview). Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 
