---
title: Vad hände med mitt molntjänstprojekt? | Microsoft Docs
description: Beskriver vad som händer i en cloud services-projekt när du ansluter till ett Azure storage-konto med hjälp av Visual Studio-anslutna tjänster
services: storage
author: ghogen
manager: douge
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: b0adceb42109f07658e56cebde3fbc4401478a1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60391097"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Vad hände med mitt cloud services-projekt (Visual Studio Azure Storage ansluten service)?
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
Element har skapats med anslutningssträngen och nyckeln för det valda lagringskontot. Ändringar har gjorts till följande filer:

* **ServiceDefinition.csdef**
* **ServiceConfiguration.Cloud.cscfg**
* **ServiceConfiguration.Local.cscfg**

