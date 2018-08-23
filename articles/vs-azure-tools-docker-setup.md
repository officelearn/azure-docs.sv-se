---
title: Konfigurera en Docker-värd med VirtualBox | Microsoft Docs
description: Stegvisa instruktioner för att konfigurera en standardinstans Docker med hjälp av Docker Machine och VirtualBox
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: ''
ms.assetid: 0b1335a2-7720-42a8-8260-4e06fc00c9f6
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 7c78f27fa948c15202e83df4ed42a805a414a72e
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054168"
---
# <a name="configure-a-docker-host-with-virtualbox"></a>Konfigurera en Docker-värd med VirtualBox
## <a name="overview"></a>Översikt
Den här artikeln hjälper dig att konfigurera en standardinstans Docker med hjälp av Docker Machine och VirtualBox. Om du använder den [Docker för Windows](https://www.docker.com/products/docker-desktop), den här konfigurationen är inte nödvändigt.

## <a name="prerequisites"></a>Förutsättningar
Följande verktyg måste vara installerade.

* [Docker verktygslådan](https://github.com/docker/toolbox/releases)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>Konfigurera Docker-klienten med Windows PowerShell
Om du vill konfigurera en Docker-klient helt enkelt öppna Windows PowerShell och utför följande steg:

1. Skapa en standardinstans docker-värd.
   
    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
2. Kontrollera standardinstansen är konfigurerad och körs. (Du bör se en instans med namnet ”standard” som körs.
   
    ```PowerShell
    docker-machine ls 
    ```
   
    ![docker-dator ls-utdata][0]
3. Ange standard som den aktuella värddatorn och konfigurera ditt gränssnitt.
   
    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```
4. Visa aktiva Docker-behållare. Listan får vara tomt.
   
    ```PowerShell
    docker ps
    ```
   
    ![docker ps-utdata][1]

> [!NOTE]
> Varje gång du startar om en utvecklingsdator måste måste du starta om din lokala docker-värd.
> Om du vill göra detta kör du följande kommando i Kommandotolken: `docker-machine start default`.
> 
> 

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
