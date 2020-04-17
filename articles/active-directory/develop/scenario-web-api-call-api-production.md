---
title: Flytta webb-API-anropande webb-API:er till produktion – Microsoft identity platform | Azure
description: Lär dig hur du flyttar ett webb-API som anropar webb-API:er till produktion.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 078ed3e5f3a19bfa4350f9edea858b717c69e3f8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537159"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Ett webb-API som anropar webb-API:er: Flytta till produktion

När du har skaffat en token för att anropa webb-API:er kan du flytta appen till produktion.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Läs mer

Nu när du vet grunderna i hur du anropar webb-API:er från ditt eget webb-API kan du vara intresserad av följande självstudiekurs, som beskriver koden som används för att skapa ett skyddat webb-API som anropar webb-API:er.

| Exempel | Plattform | Beskrivning |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 webb-API, Stationär dator (WPF) | ASP.NET Core 2.2 webb-API anropar Microsoft Graph, som du anropar från ett WPF-program med hjälp av Microsoft identity platform (v2.0). |
