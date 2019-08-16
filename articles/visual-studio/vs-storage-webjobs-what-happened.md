---
title: Vad hände med mitt jobb projekt (Visual Studio Azure Storage Connected service)? | Microsoft Docs
description: Beskriver vad som hände i ett Azure-webbjobb-projekt efter anslutning till ett lagrings konto med hjälp av Visual Studio Connected Services
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
ms.openlocfilehash: c54214e0b919ddaa60403a1c986a144100143577
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510474"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Vad hände med mitt jobb projekt (Visual Studio Azure Storage Connected service)?
## <a name="references-added"></a>Tillagda referenser
Azure Storage NuGet-paketet har lagts till eller uppdaterats i ditt Visual Studio-projekt.  
Det här paketet lägger till följande .NET-referenser:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Anslutnings sträng för Azure Storage tillagd
I filen app. config i projektet har **AzureWebJobsStorage** -och **AzureWebJobsDashboard** -posterna uppdaterats med det valda lagrings kontots anslutnings sträng och nyckel.

Mer information finns i [Azure WebJobs dokumentations resurser](https://go.microsoft.com/fwlink/?linkid=390226).

