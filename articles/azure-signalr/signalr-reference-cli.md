---
title: Azure CLI-exempel – Azure SignalR Service
description: Azure CLI-exempel – Azure SignalR Service
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 1fbe96d827bcf6bb39a6cf7a4e6811174b862d59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61071084"
---
# <a name="azure-cli-reference"></a>Azure CLI-referens

Följande tabell inkluderar länkar till bash-skript som skapats för Azure SignalR Service med Azure CLI.

| | |
|-|-|
|**Skapa**||
| [Skapa en ny SignalR Service och resursgrupp](scripts/signalr-cli-create-service.md) | Skapar en ny Azure SignalR Service-resurs i en ny resursgrupp med ett slumpmässigt namn.  |
|**Integrera**||
| [Skapa en ny SignalR Service och webbapp som är konfigurerad för att använda SignalR](scripts/signalr-cli-create-with-app-service.md) | Skapar en ny Azure SignalR Service-resurs i en ny resursgrupp med ett slumpmässigt namn. Lägger också till en ny Webbapp och App Service-plan som värd för en ASP.NET Core-Webbapp som använder SignalR Service. Webbappen konfigureras med en appinställning för att ansluta till den nya SignalR Service-resursen. |
| [Skapa en ny SignalR Service och webbapp som är konfigurerad för att använda SignalR och GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Skapar en ny Azure SignalR Service-resurs i en ny resursgrupp med ett slumpmässigt namn. Dessutom lägger till en ny Azure-Webbapp och värdplan till värd en ASP.NET Core-Webbapp som använder SignalR Service. Webbappen konfigureras med appinställningar för anslutningssträngen till den nya SignalR Service-resursen, och klienthemligheter för att stödja [GitHub-autentisering](https://developer.github.com/v3/guides/basics-of-authentication/) som det visas i [självstudien om autentisering](signalr-concept-authenticate-oauth.md). Webbappen är också konfigurerad för att använda en lokal distributionskälla för Git-lagringsplatsen. |
| | |
