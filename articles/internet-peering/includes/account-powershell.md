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
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678500"
---
Installera och importera de moduler som krävs innan du börjar konfigurera. Du behöver administratörsbehörighet för att kunna installera moduler i PowerShell.

1. Installera och importera Az-modulen.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Installera och importera modulen Az.Peering.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Kontrollera att modulerna har importerats på rätt sätt med det här kommandot:
    ```powershell
    Get-Module
    ```
1. Logga in på ditt Azure-konto med det här kommandot:
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
> Om du inte redan har associerat ditt ASN och din prenumeration följer du stegen i [Associera peer ASN](../howto-subscription-association-powershell.md). Den här åtgärden krävs för att begära en peering.

> [!NOTE]
> Platsen för en resursgrupp är oberoende av den plats där du väljer att ställa in en peering.
&nbsp;
