---
title: Distribuera en ASP.NET-Docker-behållare till Azure Container Registry (ACR) | Microsoft Docs
description: Lär dig hur du använder Visual Studio Tools for Docker för att distribuera en ASP.NET Core-webbapp till ett behållarregister
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
ms.openlocfilehash: 58df17b17de1d93683875b68dd7c6c087bc6d16d
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972316"
---
# <a name="deploy-an-aspnet-container-to-a-container-registry-using-visual-studio"></a>Distribuera en ASP.NET-behållare till ett behållarregister med hjälp av Visual Studio
## <a name="overview"></a>Översikt
Docker är en enkel behållare motor likheter till en virtuell dator som du kan använda för att vara värd för program och tjänster.
Den här självstudiekursen beskriver hur du använder Visual Studio för att publicera program i behållare till ett [Azure Container Registry](https://azure.microsoft.com/services/container-registry).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/dotnet/?utm_source=acr-publish-doc&utm_medium=docs&utm_campaign=docs) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här självstudien behöver du:

* Installera den senaste versionen av [Visual Studio 2017](https://azure.microsoft.com/downloads/) med arbetsbelastningen ”ASP.NET och webbutveckling”
* Installera [Docker för Windows](https://docs.docker.com/docker-for-windows/install/)

## <a name="1-create-an-aspnet-core-web-app"></a>1. Skapa en ASP.NET Core-webbapp
Följande steg beskriver hur du skapar en grundläggande ASP.NET Core-app som ska användas i den här självstudien.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-publish-your-container-to-azure-container-registry"></a>2. Publicera din behållare till Azure Container Registry
1. Högerklicka på projektet i **Solution Explorer** och välj **publicera**.
2. Välj på mål-dialogrutan Publicera den **Behållarregister** fliken.
3. Välj **nya Azure Container Registry** och klicka på **publicera**.
4. Fyll i dina önskade värden i den **skapa en ny Azure Container Registry**.

    | Inställning      | Föreslaget värde  | Beskrivning                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS-Prefix** | Globalt unikt namn | Namn som identifierar ditt behållarregister. |
    | **Prenumeration** | Välj din prenumeration | Den Azure-prenumeration som ska användas. |
    | **[Resursgrupp](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Namnet på resursgruppen där du kan skapa ditt behållarregister. Skapa en ny resursgrupp genom att välja **Ny**.|
    | **[SKU: N](https://docs.microsoft.com/azure/container-registry/container-registry-skus)** | Standard | Tjänstenivå för container registry  |
    | **Registerplats** | En plats nära dig | Välj en plats i en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som använder ditt behållarregister. |
    ![Azure Container Registry dialogrutan Skapa i Visual Studio][0]
5. Klicka på **Skapa**

Du kan nu hämta behållaren från registret till valfri värd kan köra Docker-avbildningar, till exempel [Azure Container Instances](./container-instances/container-instances-tutorial-deploy-app.md).

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/vs-acr-provisioning-dialog.png
