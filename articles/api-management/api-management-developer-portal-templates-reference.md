---
title: Azure API Management utvecklarportalsmallar | Microsoft Docs
description: Lär dig mer om att anpassa innehållet på utvecklarportalens sidor med en uppsättning mallar i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 5189f3d8-2a4c-4dc8-ab19-11c7df0114d4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 0f339984a9db1a337f97e6db24571004cc34f679
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657653"
---
# <a name="developer-portal-templates"></a>Utvecklarportalsmallar

Azure API Management ger dig möjlighet att anpassa innehållet på utvecklarportalens sidor med en uppsättning mallar som konfigurerar sitt innehåll. Med hjälp av [DotLiquid](http://dotliquidmarkup.org/) syntax och redigeringsprogram, till exempel [DotLiquid för Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), och en angiven uppsättning lokaliserade [sträng resurser](api-management-template-resources.md#strings), [tecken resurser](api-management-template-resources.md#glyphs), och [sidan kontroller](api-management-page-controls.md), har bra möjlighet att konfigurera innehållet i sidorna som du vill med hjälp av dessa mallar.  
  
Mer information om hur du arbetar med mallar finns i [hur du anpassar utvecklarportalen API Management med hjälp av mallar](api-management-developer-portal-templates.md).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

##  <a name="DeveloperPortalTemplates"></a> Utvecklarportalsmallar  
  
-   [API:er](api-management-api-templates.md)  
    -   [API-lista](api-management-api-templates.md#APIList)  
    -   [Åtgärd](api-management-api-templates.md#Product)  
    -   [Kodexempel](api-management-api-templates.md#CodeSamples)  
        -   [CURL](api-management-api-templates.md#Curl)  
        -   [C#](api-management-api-templates.md#CSharp)  
        -   [Java](api-management-api-templates.md#Stub)  
        -   [JavaScript](api-management-api-templates.md#JavaScript)  
        -   [Mål-C](api-management-api-templates.md#ObjectiveC)  
        -   [PHP](api-management-api-templates.md#PHP)  
        -   [Python](api-management-api-templates.md#Python)  
        -   [Ruby](api-management-api-templates.md#Ruby)  
-   [Produkter](api-management-product-templates.md)  
    -   [Produktlista](api-management-product-templates.md#ProductList)  
    -   [Produkten](api-management-product-templates.md#Product)  
-   [Program](api-management-application-templates.md)  
    -   [Lista över program](api-management-application-templates.md#ProductList)  
    -   [Programmet](api-management-application-templates.md#Application)  
-   [Problem](api-management-issue-templates.md)  
    -   [Problemet lista](api-management-issue-templates.md#IssueList)  
-   [Användarprofil](api-management-user-profile-templates.md)  
    -   [Profil](api-management-user-profile-templates.md#Profile)  
    -   [Prenumerationer](api-management-user-profile-templates.md#Subscriptions)  
    -   [Program](api-management-user-profile-templates.md#Applications)  
    -   [Uppdatera kontoinformation](api-management-user-profile-templates.md#UpdateAccountInfo)  
-   [Sidor](api-management-page-templates.md)  
    -   [Logga in](api-management-page-templates.md#SignIn)  
    -   [Registrera dig](api-management-page-templates.md#SignUp)  
    -   [Det gick inte att hitta sidan](api-management-page-templates.md#PageNotFound)

## <a name="next-steps"></a>Nästa steg  

+ [Mallreferens](api-management-developer-portal-templates-reference.md)  
+ [Referens för datamodell](api-management-template-data-model-reference.md)  
+ [Sidkontroller](api-management-page-controls.md)  
+ [Mallresurser](api-management-template-resources.md)