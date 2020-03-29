---
title: Kör behållare för avvikelsedetektor i Azure-behållarinstanser
titleSuffix: Azure Cognitive Services
description: Distribuera behållaren för avvikelsedetektor till en Azure Container-instans och testa den i en webbläsare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 2fba0a0d64502a30b6dfbc9f4f109bca65cca8b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76716362"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Distribuera en avvikelsedetektorbehållare till Azure Container-instanser

Lär dig hur du distribuerar behållaren för [avvikelsedetektor för](../anomaly-detector-container-howto.md) Cognitive Services till Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Den här proceduren visar att en avvikelsedetektorresurs skapas. Sedan diskuterar vi att dra den tillhörande behållarbilden. Slutligen belyser vi möjligheten att utöva orkestrering av de två från en webbläsare. Med hjälp av behållare kan flytta utvecklarnas uppmärksamhet från att hantera infrastruktur till att istället fokusera på applikationsutveckling.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Begär åtkomst till det privata behållarregistret

Du måste först fylla i och skicka formuläret för begäran om begäran om [avvikelsedetektorbehållare](https://aka.ms/adcontainer) för att begära åtkomst till behållaren.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Nästa steg

* Granska [Installera och kör behållare](../anomaly-detector-container-configuration.md) för att dra behållaravbildningen och köra behållaren
* Granska [Konfigurera behållare](../anomaly-detector-container-configuration.md) för konfigurationsinställningar
* [Läs mer om API-tjänsten för avvikelsedetektorn](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
