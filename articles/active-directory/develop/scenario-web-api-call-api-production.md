---
title: 'Flytta webb-API som anropar webb-API: er till produktion – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du flyttar ett webb-API som anropar webb-API: er till produktion.'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 474f771f007666179295f4502108acee88d1dc33
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701730"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Ett webb-API som anropar webb-API: er: flytta till produktion

När du har skaffat en token för att anropa webb-API: er kan du flytta din app till produktion.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Läs mer

Nu när du vet grunderna för hur du anropar webb-API: er från ditt eget webb-API kan du vara intresse rad av följande självstudie, som beskriver koden som används för att bygga ett skyddat webb-API som anropar webb-API: er.

| Exempel | Plattform | Beskrivning |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2,2 webb-API, Desktop (WPF) | ASP.NET Core 2,2 webb-API-anrop Microsoft Graph, som du anropar från ett WPF-program med hjälp av Microsoft Identity Platform (v 2.0). |
