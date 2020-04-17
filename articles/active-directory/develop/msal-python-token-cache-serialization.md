---
title: Anpassad serialisering av tokencache (MSAL Python) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du serialiserar tokencachen för MSAL för Python
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 9c6edd0b3cfd6620f04553f9f6dfe89f1c7b7024
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536207"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Anpassad tokencacheserieisering i MSAL för Python

I MSAL Python tillhandahålls en tokencache i minnet som finns kvar under appsessionens varaktighet som standard när du skapar en instans av [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication).

Serialisering av tokencachen, så att olika sessioner i din app kan komma åt den, tillhandahålls inte "out of the box". Det beror på att MSAL Python kan användas i apptyper som inte har åtkomst till filsystemet – till exempel webbappar. Om du vill ha en beständig tokencache i en MSAL Python-app måste du tillhandahålla anpassad serialisering av tokencache.

Strategierna för serialisering av tokencachen varierar beroende på om du skriver ett offentligt klientprogram (Desktop) eller ett konfidentiellt klientprogram (webbapp, webb-API eller daemonapp).

## <a name="token-cache-for-a-public-client-application"></a>Tokencache för ett offentligt klientprogram

Offentliga klientprogram körs på en användares enhet och hanterar token för en enskild användare. I det här fallet kan du serialisera hela cacheminnet till en fil. Kom ihåg att ange fillåsning om appen eller en annan app kan komma åt cacheminnet samtidigt. Ett enkelt exempel på hur du serialiserar en tokencache till en fil utan låsning finns i exemplet i referensdokumentationen [SerializableTokenCache.](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache)

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Tokencache för en webbapp (konfidentiellt klientprogram)

För webbappar eller webb-API:er kan du använda sessionen, en Redis-cache eller en databas för att lagra tokencachen. Det bör finnas en tokencache per användare (per konto) så se till att du serialiserar tokencachen per konto.

## <a name="next-steps"></a>Nästa steg

Se [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) för ett exempel på hur du använder tokencachen för en Windows- eller Linux-webbapp eller webb-API. Exemplet är för en webbapp som anropar Microsoft Graph API.
