---
title: Azure API Management Developer-portalens mallar | Microsoft Docs
description: Lär dig hur du anpassar innehållet i Developer Portal-sidor med en uppsättning mallar i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 5189f3d8-2a4c-4dc8-ab19-11c7df0114d4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: eb06c1c5db20f3216c5c8e81580e445f9c0ba804
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072605"
---
# <a name="developer-portal-templates"></a>Mallar för utvecklare

Med Azure API Management kan du anpassa innehållet i utvecklares Portal sidor med en uppsättning mallar som konfigurerar innehållet. Om du använder [DotLiquid](http://dotliquidmarkup.org/) -syntax och valfritt redigerings program, till exempel [DotLiquid för designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)och en angiven uppsättning lokaliserade [sträng resurser](api-management-template-resources.md#strings), glyf- [resurser](api-management-template-resources.md#glyphs)och [sid kontroller](api-management-page-controls.md), har du stor flexibilitet att konfigurera innehållet på sidorna som du ser får plats med mallarna.  
  
Mer information om hur du arbetar med mallar finns i [anpassa API Management Developer-portalen med hjälp av mallar](api-management-developer-portal-templates.md).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

##  <a name="DeveloperPortalTemplates"></a>Mallar för utvecklare  
  
-   [API:er](api-management-api-templates.md)  
    -   [API-lista](api-management-api-templates.md#APIList)  
    -   [Åtgärd](api-management-api-templates.md#Product)  
    -   [Kodexempel](api-management-api-templates.md#CodeSamples)  
        -   [Klammerparentes](api-management-api-templates.md#Curl)  
        -   [C#](api-management-api-templates.md#CSharp)  
        -   [Java](api-management-api-templates.md#Stub)  
        -   [JavaScript](api-management-api-templates.md#JavaScript)  
        -   [Mål C](api-management-api-templates.md#ObjectiveC)  
        -   [PHP](api-management-api-templates.md#PHP)  
        -   [Python](api-management-api-templates.md#Python)  
        -   [Ruby](api-management-api-templates.md#Ruby)  
-   [Produkter  
    -   [Produkt lista](api-management-product-templates.md#ProductList)  
    -   [Momsproduktbokföringsmallar](api-management-product-templates.md#Product)  
-   [Program](api-management-application-templates.md)  
    -   [Program lista](api-management-application-templates.md#ProductList)  
    -   [Programmet](api-management-application-templates.md#Application)  
-   [Problem](api-management-issue-templates.md)  
    -   [Ärende lista](api-management-issue-templates.md#IssueList)  
-   [Användar profil](api-management-user-profile-templates.md)  
    -   [Profil](api-management-user-profile-templates.md#Profile)  
    -   [Prenumerationer](api-management-user-profile-templates.md#Subscriptions)  
    -   [Program](api-management-user-profile-templates.md#Applications)  
    -   [Uppdatera konto information](api-management-user-profile-templates.md#UpdateAccountInfo)  
-   [Sidor](api-management-page-templates.md)  
    -   [Logga in](api-management-page-templates.md#SignIn)  
    -   [Registrera dig](api-management-page-templates.md#SignUp)  
    -   [Sidan hittades inte](api-management-page-templates.md#PageNotFound)

## <a name="next-steps"></a>Nästa steg  

+ [Mallreferens](api-management-developer-portal-templates-reference.md)  
+ [Referens för datamodell](api-management-template-data-model-reference.md)  
+ [Sidkontroller](api-management-page-controls.md)  
+ [Mallresurser](api-management-template-resources.md)