---
title: Vad hände med mitt ASP.NET-projekt? | Microsoft Docs
description: Beskriver vad som händer när du har lagt till Azure Storage i ett ASP.NET-projekt med hjälp av Visual Studio Connected Services
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
ms.openlocfilehash: 74db2e1e15c282f4393715c67561b2ed61bbcdfb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512065"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Vad hände med mitt ASP.NET-projekt (Visual Studio Azure Storage Connected service)?
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

## <a name="connection-string-for-azure-storage-added"></a>Anslutnings sträng för Azure Storage tillagd
I filen Web. config i projektet skapades ett element med det valda lagrings kontots anslutnings sträng och nyckel.

Mer information finns i [ASP.net](https://www.asp.net).

