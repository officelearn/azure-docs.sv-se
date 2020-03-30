---
title: ta med fil
description: ta med fil
services: azure-netapp-files
author: b-juche
ms.service: azure-netapp-files
ms.topic: include
ms.date: 09/10/2019
ms.author: b-juche
ms.custom: include file
ms.openlocfilehash: 3a63fd96b09910b0cd7ee8c3ab9947b034173c8f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "71836298"
---
1. Ange den prenumeration som har vitlistats för Azure NetApp-filer:
    
    ```azurecli-interactive
    az account set --subscription <subscriptionId>
    ```

1. Registrera Azure Resource Provider: 
    
    ```azurecli-interactive
    az provider register --namespace Microsoft.NetApp --wait  
    ```