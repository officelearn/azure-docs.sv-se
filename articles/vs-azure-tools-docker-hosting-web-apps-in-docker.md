---
title: Distribuera ett ASP.NET-dockerbehållare till Azure Container registret (ACR) | Microsoft Docs
description: Lär dig hur du använder Visual Studio Tools för Docker för att distribuera ett ASP.NET Core-webbapp till en behållare registret
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: ''
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/21/2018
ms.author: mlearned
ms.openlocfilehash: 4442c1d763f4ed21a5efeedbe957727254e2a0b8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658479"
---
# <a name="deploy-an-aspnet-container-to-a-container-registry-using-visual-studio"></a>Distribuera en ASP.NET-behållare till en behållare registret med hjälp av Visual Studio
## <a name="overview"></a>Översikt
Docker är en förenklad behållaren motor likheter till en virtuell dator som du kan använda till värd-program och tjänster.
Den här självstudiekursen vägleder dig genom med Visual Studio för att publicera av programmet till en [Azure Container registret](https://azure.microsoft.com/en-us/services/container-registry).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/dotnet/?utm_source=acr-publish-doc&utm_medium=docs&utm_campaign=docs) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här självstudien behöver du:

* Installera den senaste versionen av [Visual Studio 2017](https://azure.microsoft.com/en-us/downloads/) med ”ASP.NET och web development” arbetsbelastning
* Installera [Docker för Windows](https://docs.docker.com/docker-for-windows/install/)

## <a name="1-create-an-aspnet-core-web-app"></a>1. Skapa en ASP.NET Core-webbapp
Följande steg hur du skapar en grundläggande app i ASP.NET Core som ska användas i den här självstudiekursen.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-publish-your-container-to-azure-container-registry"></a>2. Publicera din behållare till registret för Azure-behållare
1. Högerklicka på projektet i **Solution Explorer** och välj **publicera**.
2. I dialogrutan Publicera mål väljer den **behållare registret** fliken.
3. Välj **nya Azure-behållare registret** och på **publicera**.
4. Fyll i din önskade värden i den **skapa en ny Azure-behållare registernyckel**.

    | Inställning      | Föreslaget värde  | Beskrivning                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS-Prefix** | Globalt unikt namn | Namnet som unikt identifierar behållaren registret. |
    | **Prenumeration** | Välj din prenumeration | Den Azure-prenumeration som ska användas. |
    | **[Resursgrupp](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Namnet på resursgruppen där du kan skapa behållaren registret. Välj **ny** att skapa en ny resursgrupp.|
    | **[SKU](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-skus)** | Standard | Tjänstnivån för behållaren registret  |
    | **Plats i registret** | En plats i närheten av du | Välj en plats i en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som använder behållaren registret. |
    ![Azure Container registret dialogrutan Skapa i Visual Studio][0]
5. Klicka på **Skapa**

Du kan nu dra behållaren från registret till valfri värd kan köra Docker bilder, till exempel [Azure Behållarinstanser](./container-instances/container-instances-tutorial-deploy-app.md).

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/vs-acr-provisioning-dialog.png
