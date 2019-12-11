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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d59a5b2a74c10e36103713725113cbe8c9cc412
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965177"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>Webb-API som anropar webb-API: er – flytta till produktion

När du har skaffat en token för att anropa webb-API: er kan du flytta din app till produktion.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Läs mer

Nu när du vet grunderna för hur du anropar webb-API: er från ditt eget webb-API kan du vara intresse rad av den här kursen, som beskriver den kod som används för att bygga ett skyddat webb-API som anropar webb-API: er.

| Exempel | Plattform | Beskrivning |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2,2 webb-API, Desktop (WPF) | ASP.NET Core 2,2 webb-API som anropar Microsoft Graph, som anropas från ett WPF-program med hjälp av Microsoft Identity Platform (v 2.0) |
