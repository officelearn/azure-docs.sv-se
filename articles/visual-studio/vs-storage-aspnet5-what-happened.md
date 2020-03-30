---
title: Vad hände med mitt ASP.NET 5-projekt (Visual Studio-anslutna tjänster)
description: Beskriver vad som händer efter anslutning till ett Azure-lagringskonto i ett Visual Studio-ASP.NET 5-projekt med hjälp av Anslutna Visual Studio-tjänster
services: storage
author: ghogen
manager: jillfra
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: be99465a48aaf680834f313e03384a9f0c211502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72300011"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Vad har hänt med mitt ASP.NET 5-projekt (Visual Studio Azure Storage-anslutna tjänster)?
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

NuGet-paketet **Microsoft.Framework.Configuration.Json** lades också till.

## <a name="connection-string-for-azure-storage-added"></a>Anslutningssträng för Azure Storage har lagts till
I config.json-filen i projektet skapades ett element med det valda lagringskontots anslutningssträng och nyckel.

Mer information finns [i ASP.NET 5](https://www.asp.net/vnext).

