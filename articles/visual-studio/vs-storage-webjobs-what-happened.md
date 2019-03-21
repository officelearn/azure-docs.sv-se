---
title: Vad hände med mitt WebJob-projekt (Visual Studio Azure Storage ansluten service)? | Microsoft Docs
description: Beskriver vad som hände i ett Azure WebJob-projekt när du ansluter till ett lagringskonto med hjälp av Visual Studio-anslutna tjänster
services: storage
author: ghogen
manager: douge
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: fa152d8b88254a35d00b91537bf1001ea1130e57
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884650"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Vad hände med mitt WebJob-projekt (Visual Studio Azure Storage ansluten service)?
## <a name="references-added"></a>Referenser som har lagts till
Azure Storage NuGet-paket har lagts till eller uppdateras i Visual Studio-projektet.  
Det här paketet lägger du till följande .NET-referenser:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Anslutningssträng för Azure Storage har lagts till
I filen App.config i ditt projekt i **AzureWebJobsStorage** och **AzureWebJobsDashboard** poster har uppdaterats med anslutningssträngen och nyckeln för det valda lagringskontot.

Mer information finns i [Azure WebJobs dokumentationsresurser](https://go.microsoft.com/fwlink/?linkid=390226).

