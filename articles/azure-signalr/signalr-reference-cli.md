---
title: Azure CLI-exempel – Azure SignalR Service
description: Azure CLI-exempel – Azure SignalR Service
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: dedd9d14bafb342b0b34e3685ef3b7a46a54f9c8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555223"
---
# <a name="azure-cli-reference"></a>Azure CLI-referens

Följande tabell inkluderar länkar till bash-skript som skapats för Azure SignalR Service med Azure CLI.

| | |
|-|-|
|**Skapa**||
| [Skapa en ny SignalR Service och resursgrupp](scripts/signalr-cli-create-service.md) | Skapar en ny Azure SignalR Service-resurs i en ny resursgrupp med ett slumpmässigt namn.  |
|**Integrera**||
| [Skapa en ny SignalR Service och webbapp som är konfigurerad för att använda SignalR](scripts/signalr-cli-create-with-app-service.md) | Skapar en ny Azure SignalR Service-resurs i en ny resursgrupp med ett slumpmässigt namn. Lägger också till en ny webbapp och App Service-plan som värd för din ASP.NET Core-webbapp som använder SignalR-tjänsten. Webbappen konfigureras med en appinställning för att ansluta till den nya SignalR Service-resursen. |
| [Skapa en ny SignalR Service och webbapp som är konfigurerad för att använda SignalR och GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Skapar en ny Azure SignalR Service-resurs i en ny resursgrupp med ett slumpmässigt namn. Lägger också till en ny Azure-webbapp och värdplan som värd för din ASP.NET Core-webbapp som använder SignalR-tjänsten. Webbappen konfigureras med appinställningar för anslutningssträngen till den nya SignalR Service-resursen, och klienthemligheter för att stödja [GitHub-autentisering](https://developer.github.com/v3/guides/basics-of-authentication/) som det visas i [självstudien om autentisering](signalr-concept-authenticate-oauth.md). Webbappen är också konfigurerad för att använda en lokal distributionskälla för Git-lagringsplatsen. |
| | |