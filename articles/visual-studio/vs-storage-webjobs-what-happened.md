---
title: Vad har hänt med mitt WebJob-projekt (Visual Studio Azure Storage)?
description: Beskriver vad som hände i ett Azure WebJob-projekt efter anslutning till ett lagringskonto med hjälp av Visual Studio-anslutna tjänster
services: storage
author: ghogen
manager: jillfra
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 17861b7c25dfaf9bc9399e5261cdf2a5b43caf21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298740"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Vad har hänt med mitt WebJob-projekt (Tjänsten Visual Studio Azure Storage connected)?
## <a name="references-added"></a>Tillagda referenser
Azure Storage NuGet-paketet har lagts till eller uppdaterats i visual studio-projektet.  
I det här paketet läggs följande .NET-referenser till:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data (Data)**
* **System.Rumsligt**

## <a name="connection-string-for-azure-storage-added"></a>Anslutningssträng för Azure Storage har lagts till
I filen App.config i ditt projekt uppdaterades **AzureWebJobsStorage-** och **AzureWebJobsDashboard-posterna** med det valda lagringskontots anslutningssträng och nyckel.

Mer information finns i [Azure WebJobs dokumentationsresurser](https://go.microsoft.com/fwlink/?linkid=390226).

