---
title: 'Flytta webb-API som anropar webb-API: er till produktion – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du flyttar ett webb-API som anropar webb-API: er till produktion.'
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537159"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Ett webb-API som anropar webb-API: er: flytta till produktion

När du har skaffat en token för att anropa webb-API: er kan du flytta din app till produktion.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Läs mer

Nu när du vet grunderna för hur du anropar webb-API: er från ditt eget webb-API kan du vara intresse rad av följande självstudie, som beskriver koden som används för att bygga ett skyddat webb-API som anropar webb-API: er.

| Exempel | Plattform | Beskrivning |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-WebAPI-självstudie-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2,2 webb-API, Desktop (WPF) | ASP.NET Core 2,2 webb-API-anrop Microsoft Graph, som du anropar från ett WPF-program med hjälp av Microsoft Identity Platform (v 2.0). |
