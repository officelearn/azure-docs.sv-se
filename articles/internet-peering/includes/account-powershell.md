---
title: ta med fil
titleSuffix: Azure
description: ta med fil
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3a5f7157ef8f3645dd03ec93684238dd8bbc067e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774229"
---
Installera och importera de moduler som krävs innan du påbörjar konfigurationen. Du behöver administratörs behörighet för att installera moduler i PowerShell.

1. Installera och importera AZ-modul
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Installera och importera AZ. peering-modul
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Kontrol lera att modulerna har importer ATS med kommandot nedan.
    ```powershell
    Get-Module
    ```
1. Logga in på ditt Azure-konto med hjälp av följande kommando.
    ```powershell
    Connect-AzAccount
    ```
1. Kontrol lera prenumerationerna för kontot och välj den prenumeration där du vill skapa en peering.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Om du inte redan har en resurs grupp måste du skapa en innan du skapar en peering. Du kan göra det genom att köra följande kommando:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Om du inte redan har associerat ditt ASN och din prenumeration följer du stegen för [associera peer-ASN](../howto-subscription-association-powershell.md). Detta krävs för att begära en peering.

> [!NOTE]
> Platsen för resurs gruppen är oberoende av den plats där du väljer att konfigurera en peering.
&nbsp;
