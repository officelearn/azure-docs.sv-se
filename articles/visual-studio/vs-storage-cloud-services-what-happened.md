---
title: Vad har hänt med mitt molntjänstprojekt?
description: Beskriver vad som händer i ett molntjänstprojekt efter anslutning till ett Azure-lagringskonto med hjälp av Visual Studio-anslutna tjänster
services: storage
author: ghogen
manager: jillfra
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ac5a635b687e51ddd34899717497548296c2fc23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298762"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Vad hände med mitt molntjänstprojekt (Tjänsten Visual Studio Azure Storage connected)?
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

## <a name="connection-string-for-azure-storage-added"></a>Anslutningssträng för Azure Storage har lagts till
Element skapades med det valda lagringskontots anslutningssträng och nyckel. Ändringar har gjorts i följande filer:

* **ServiceDefinition.csdef**
* **ServiceConfiguration.Cloud.cscfg**
* **ServiceConfiguration.Local.cscfg**

