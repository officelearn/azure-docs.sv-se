---
title: 'Webb-API att anrop web API: er (flytta till produktion) - Microsoft identity-plattformen'
description: 'Lär dig hur du skapar ett webb-API att anrop nedströms webb-API: er (flytta till produktion).'
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
ms.openlocfilehash: 7de892a0851e556c5046fe4b3691f39b42a9d237
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074762"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>Webb-API som anropar webb-API: er – flytta till produktion

När du har skaffat en token för att anropa webb-API: er, kan du flytta din app till produktion.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Läs mer

Nu när du vet grunderna för hur du anropar webb-API: er från din egen webb-API, kanske du vill den här självstudien, som beskriver den kod som har använt för att skapa ett skyddat webb-API anropar webb-API: er.

| Exempel | Plattform | Beskrivning |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 webb-API, Desktop (WPF) | ASP.NET Core 2.2 webb-API att anropa Microsoft Graph, anropas själva från en WPF-program med Microsoft identity-plattformen (v2.0) |
