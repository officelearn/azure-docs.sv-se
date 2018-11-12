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
ms.openlocfilehash: 673143c2bfb23bda45c4e435c3048ae61fa16902
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256909"
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

