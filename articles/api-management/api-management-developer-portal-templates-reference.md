---
title: Azure API Management Developer portal mallar | Microsoft Docs
description: "Lär dig hur du anpassar innehållet i developer portalens sidor med en uppsättning mallar i Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 5189f3d8-2a4c-4dc8-ab19-11c7df0114d4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 540e9804d2ee84c050531f5287d4c4b8922ae999
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="developer-portal-templates"></a>Developer portal mallar

Azure API Management ger dig möjlighet att anpassa innehållet i developer portalens sidor med hjälp av en uppsättning mallar som konfigurerar deras innehåll. Med hjälp av [DotLiquid](http://dotliquidmarkup.org/) syntax och redigeringsprogram, t.ex [DotLiquid för Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), och en angiven uppsättning lokaliserade [String resurser](api-management-template-resources.md#strings), [glyf resurser](api-management-template-resources.md#glyphs), och [sidan kontroller](api-management-page-controls.md), du har stor flexibilitet för att konfigurera innehåll för sidorna som du vill använda dessa mallar.  
  
Mer information om hur du arbetar med mallar finns [hur du anpassar API Management developer-portalen med hjälp av mallar](api-management-developer-portal-templates.md).  

##  <a name="DeveloperPortalTemplates"></a>Developer portal mallar  
  
-   [API:er](api-management-api-templates.md)  
    -   [API-lista](api-management-api-templates.md#APIList)  
    -   [Åtgärd](api-management-api-templates.md#Product)  
    -   [Kodexempel](api-management-api-templates.md#CodeSamples)  
        -   [CURL](api-management-api-templates.md#Curl)  
        -   [C#](api-management-api-templates.md#CSharp)  
        -   [Java](api-management-api-templates.md#Stub)  
        -   [JavaScript](api-management-api-templates.md#JavaScript)  
        -   [Objective C](api-management-api-templates.md#ObjectiveC)  
        -   [PHP](api-management-api-templates.md#PHP)  
        -   [Python](api-management-api-templates.md#Python)  
        -   [Ruby](api-management-api-templates.md#Ruby)  
-   [Produkter](api-management-product-templates.md)  
    -   [Produktlista](api-management-product-templates.md#ProductList)  
    -   [Produkten](api-management-product-templates.md#Product)  
-   [Program](api-management-application-templates.md)  
    -   [Programlista](api-management-application-templates.md#ProductList)  
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
    -   [Registrera sig](api-management-page-templates.md#SignUp)  
    -   [Det gick inte att hitta sidan](api-management-page-templates.md#PageNotFound)

## <a name="next-steps"></a>Nästa steg  

+ [Mallreferensen](api-management-developer-portal-templates-reference.md)  
+ [Referens för datamodell](api-management-template-data-model-reference.md)  
+ [Sidkontroller](api-management-page-controls.md)  
+ [Mallresurser](api-management-template-resources.md)