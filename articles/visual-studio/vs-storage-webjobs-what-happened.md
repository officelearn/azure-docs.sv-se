---
title: Vad hände med mitt jobb projekt (Visual Studio Azure Storage)?
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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 17861b7c25dfaf9bc9399e5261cdf2a5b43caf21
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298740"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Vad hände med mitt jobb projekt (Visual Studio Azure Storage Connected service)?
## <a name="references-added"></a>Tillagda referenser
Azure Storage NuGet-paketet har lagts till eller uppdaterats i ditt Visual Studio-projekt.  
Det här paketet lägger till följande .NET-referenser:

* **Microsoft. data. EDM**
* **Microsoft. data. OData**
* **Microsoft. data. Services. client**
* **Microsoft. WindowsAzure. ConfigurationManager**
* **Microsoft. WindowsAzure. Storage**
* **Newtonsoft. JSON**
* **System. data**
* **System. spatial**

## <a name="connection-string-for-azure-storage-added"></a>Anslutnings sträng för Azure Storage tillagd
I filen app. config i projektet har **AzureWebJobsStorage** -och **AzureWebJobsDashboard** -posterna uppdaterats med det valda lagrings kontots anslutnings sträng och nyckel.

Mer information finns i [Azure WebJobs dokumentations resurser](https://go.microsoft.com/fwlink/?linkid=390226).

