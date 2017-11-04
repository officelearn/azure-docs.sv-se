---
title: "Konfigurera en Docker-värd med VirtualBox | Microsoft Docs"
description: "Stegvisa instruktioner för att konfigurera en standardinstans Docker med Docker-datorn och VirtualBox"
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 0b1335a2-7720-42a8-8260-4e06fc00c9f6
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: e9465afb560a73d74f853c19094b3ee75b8c470c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-docker-host-with-virtualbox"></a>Konfigurera en Docker-värd med VirtualBox
## <a name="overview"></a>Översikt
Den här artikeln hjälper dig att konfigurera en standardinstans Docker med Docker-datorn och VirtualBox. Om du använder den [Docker för Windows beta](http://beta.docker.com/), behövs inte den här konfigurationen.

## <a name="prerequisites"></a>Krav
Följande verktyg måste vara installerade.

* [Docker verktygslådan](https://www.docker.com/products/overview#/docker_toolbox)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>Konfigurera Docker-klienten med Windows PowerShell
Om du vill konfigurera en Docker-klient helt enkelt öppna Windows PowerShell och utför följande steg:

1. Skapa en standardinstans docker värden.
   
    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
2. Kontrollera standardinstansen är konfigurerad och körs. (Du bör se en instans med namnet 'default' körs.
   
    ```PowerShell
    docker-machine ls 
    ```
   
    ![docker-datorn ls-utdata][0]
3. Ange standard som den aktuella värddatorn och konfigurera gränssnittet.
   
    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```
4. Visa aktiva Docker-behållare. Listan får vara tomt.
   
    ```PowerShell
    docker ps
    ```
   
    ![docker ps-utdata][1]

> [!NOTE]
> Varje gång du startar om datorn development behöver du starta om din lokala docker-värd.
> Om du vill göra det, kör du följande kommando i Kommandotolken: `docker-machine start default`.
> 
> 

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
