---
title: Vad hände med mitt ASP.NET-projekt? | Microsoft Docs
description: Beskriver vad som händer när du lägger till Azure Storage till en ASP.NET-projekt med Visual Studio-anslutna tjänster
services: storage
author: ghogen
manager: douge
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: a6e05d706d54d63695861b03cd9de0e65ebdd8bb
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057375"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Vad hände med mitt ASP.NET-projekt (Visual Studio Azure Storage ansluten service)?
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

## <a name="connection-string-for-azure-storage-added"></a>Anslutningssträng för Azure Storage har lagts till
I web.config-filen för projektet skapades ett element med anslutningssträngen och nyckeln för det valda lagringskontot.

Mer information finns i [ASP.NET](http://www.asp.net).

