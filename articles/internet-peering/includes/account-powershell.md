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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774229"
---
Innan du påbörjar konfigurationen installerar och importerar du de moduler som krävs. Du behöver administratörsbehörighet för att installera moduler i PowerShell.

1. Installera och importera Az-modul
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Installera och importera Az.Peering-modul
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Kontrollera att modulerna importeras fint med kommandot nedan.
    ```powershell
    Get-Module
    ```
1. Logga in på ditt Azure-konto med följande kommando.
    ```powershell
    Connect-AzAccount
    ```
1. Kontrollera prenumerationerna för kontot och välj den prenumeration där du vill skapa en peering.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Om du inte redan har en resursgrupp måste du skapa en innan du skapar en peering. Du kan göra det genom att köra följande kommando:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Om du inte redan har associerat ditt ASN och din prenumeration följer du stegen för [Associera peer ASN](../howto-subscription-association-powershell.md). Detta krävs för att begära en peering.

> [!NOTE]
> Resursgruppens plats är oberoende av den plats där du väljer att ställa in en peering.
&nbsp;
