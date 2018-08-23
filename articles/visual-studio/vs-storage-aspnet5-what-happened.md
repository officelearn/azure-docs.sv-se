---
title: Vad hände med mitt ASP.NET 5-projekt (Visual Studio connected services) | Microsoft Docs
description: Beskriver vad som händer när du ansluter till ett Azure storage-konto i en ASP.NET 5 för Visual Studio-projektet med Visual Studio-anslutna tjänster
services: storage
author: ghogen
manager: douge
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 0e38e570b5ecb2a19af943e0dde96e2ed66f3507
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055845"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Vad hände med mitt ASP.NET 5-projekt (Visual Studio Azure Storage-anslutna tjänster)?
## <a name="references-added"></a>Referenser som har lagts till
Azure Storage NuGet-paket har lagts till Visual Studio-projektet.  
Det här paketet lägger du till följande .NET-referenser:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

Dessutom NuGet-paketet **Microsoft.Framework.Configuration.Json** har lagts till.

## <a name="connection-string-for-azure-storage-added"></a>Anslutningssträng för Azure Storage har lagts till
I config.json-fil av projektet skapades ett element med anslutningssträngen och nyckeln för det valda lagringskontot.

Mer information finns i [ASP.NET 5](http://www.asp.net/vnext).

