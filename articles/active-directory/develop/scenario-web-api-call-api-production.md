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
ms.openlocfilehash: d557c70c09824f3aead41a654dc92c4ec46e9238
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885063"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Ett webb-API som anropar webb-API:er: Flytta till produktion

När du har skaffat en token för att anropa webb-API:er kan du flytta appen till produktion.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Läs mer

Nu när du vet grunderna i hur du anropar webb-API:er från ditt eget webb-API kan du vara intresserad av följande självstudiekurs, som beskriver koden som används för att skapa ett skyddat webb-API som anropar webb-API:er.

| Exempel | Plattform | Beskrivning |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 Webb-API, Stationär dator (WPF) | ASP.NET Core 2.2 Web API anropar Microsoft Graph, som du anropar från ett WPF-program med hjälp av Microsofts identitetsplattform (v2.0). |
