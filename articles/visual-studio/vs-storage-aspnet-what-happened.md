---
title: Vad hände med mitt ASP.NET-projekt?
description: Beskriver vad som händer när du har lagt till Azure Storage i ett ASP.NET projekt med hjälp av Visual Studio-anslutna tjänster
services: storage
author: ghogen
manager: jillfra
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4374955d6d51c1ae44c211aaa93d0b5e8930fe5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72300034"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Vad har hänt med mitt ASP.NET-projekt (Tjänsten Visual Studio Azure Storage connected)?
## <a name="references-added"></a>Tillagda referenser
Azure Storage NuGet-paketet har lagts till i ditt Visual Studio-projekt.  
I det här paketet läggs följande .NET-referenser till:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Konfiguration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data (Data)**
* **System.Rumsligt**

## <a name="connection-string-for-azure-storage-added"></a>Anslutningssträng för Azure Storage har lagts till
I filen web.config i projektet skapades ett element med det valda lagringskontots anslutningssträng och nyckel.

Mer information finns [i ASP.NET](https://www.asp.net).

