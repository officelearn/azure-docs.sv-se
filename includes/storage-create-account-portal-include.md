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
ms.openlocfilehash: 3b770c3e51b17fa5d70abb03360e17e1073974d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392325"
---
Följ de här stegen för att skapa ett GPv2-konto för generell användning i Azure-portalen:

1. Välj **Alla tjänster** i Azure-portalen. Skriv **lagringskonton** i listan över resurser. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Lagringskonton**.
1. På fönstret **lagringskonton** som visas, väljer du **lägg till**.
1. Välj den prenumeration där du vill skapa lagringskontot.
1. Under fältet **Resursgrupp** väljer du **Skapa ny**. Ange ett namn för din nya resursgrupp som du ser i följande bild.

    ![Skärmbild som visar hur du skapar en resursgrupp i portalen](./media/storage-create-account-portal-include/create-resource-group.png)

1. Ange sedan ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure. Namnet måste också bestå av mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener.
1. Välj en plats för ditt lagringskonto eller använd standardplatsen.
1. Lämna dessa fält med respektive standardvärde:

   |Fält  |Värde  |
   |---------|---------|
   |Distributionsmodell     |Resource Manager         |
   |Prestanda     |Standard         |
   |Typ av konto     |StorageV2 (generell användning v2)         |
   |Replikering     |Lokalt redundant lagring (LRS)         |
   |Åtkomstnivå     |Frekvent         |

1. Välj **Granska + skapa** för att granska inställningarna för ditt lagringskonto och skapa kontot.
1. Välj **Skapa**.

Mer information om typer av lagringskonton och andra inställningar för lagringskonto finns i [översikten över Azure-lagringskonton](https://docs.microsoft.com/azure/storage/common/storage-account-overview). Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 
