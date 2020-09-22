---
title: Hämta användar namn och lösen ord för att ansluta till data styrenheten för bågen
description: Hämta användar namn och lösen ord för att ansluta till data styrenheten för bågen
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 1c33cf21be0b4b1ea39a568d6df9fd90507dd454
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90941496"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>Hämta användar namn och lösen ord för att ansluta till data styrenheten för bågen

Du kanske är i en situation där du behöver hämta användar namn och lösen ord för datakontrollanten. Detta är de kommandon du behöver när du kör. 

```console
azdata login
```

Om du är Kubernetes-administratör för klustret. Så att du har behörighet att köra kommandon för att hämta från Kubernetes-hemligheten lagrar den information som Azure-bågen sparar där.

> [!NOTE]
>  Om du har använt ett annat namn för namn området där datakontrollanten skapades, måste du ändra `-n arc` parametern i kommandona nedan för att använda namnet på det namn område som du skapade data styrenheten till.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="linux"></a>Linux

Kör följande kommando för att hämta användar namnet:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

Kör följande kommando för att hämta lösen ordet:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

Kör följande kommando för att hämta användar namnet:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

Kör följande kommando för att hämta lösen ordet:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>Nästa steg

Prova andra [scenarier](https://github.com/microsoft/Azure-data-services-on-Azure-Arc/tree/master/scenarios)
