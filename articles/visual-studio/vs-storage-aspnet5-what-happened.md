---
title: Vad hände med mitt ASP.NET 5-projekt (Visual Studio Connected Services) | Microsoft Docs
description: Beskriver vad som händer när du har anslutit till ett Azure Storage-konto i ett Visual Studio ASP.NET 5-projekt med Visual Studio Connected Services
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
ms.openlocfilehash: e29c0302ecd703cb02199df95892e24917baf8e8
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510771"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Vad hände med mitt ASP.NET 5-projekt (Visual Studio Azure Storage anslutna tjänster)?
## <a name="references-added"></a>Tillagda referenser
Azure Storage NuGet-paketet har lagts till i Visual Studio-projektet.  
Det här paketet lägger till följande .NET-referenser:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

Dessutom lades NuGet-paketet **Microsoft. Framework. Configuration. JSON** till.

## <a name="connection-string-for-azure-storage-added"></a>Anslutnings sträng för Azure Storage tillagd
I filen config. json i projektet skapades ett element med det valda lagrings kontots anslutnings sträng och nyckel.

Mer information finns i [ASP.net 5](https://www.asp.net/vnext).

