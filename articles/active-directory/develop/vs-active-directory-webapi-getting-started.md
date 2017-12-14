---
title: "Kom igång med Azure AD i Visual Studio-WebApi projekt | Microsoft Docs"
description: "Hur du kommer igång med Azure Active Directory i WebApi projekt när du ansluter till eller skapa en Azure AD med hjälp av Visual Studio anslutna tjänster"
services: active-directory
documentationcenter: 
author: kraigb
manager: mtillman
editor: 
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/19/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 3d510c193ab8c7e65340275017cb2dcd4c76def0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-azure-active-directory-and-visual-studio-connected-services-webapi-projects"></a>Komma igång med Azure Active Directory och Visual Studio anslutna tjänster (WebApi-projekt)
> [!div class="op_single_selector"]
> * [Komma igång](vs-active-directory-webapi-getting-started.md)
> * [Vad hände](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="requiring-authentication-to-access-controllers"></a>Kräva autentisering till access-domänkontrollanter
Alla domänkontrollanter i ditt projekt har adorned med den **auktorisera** attribut. Det här attributet kräver att användaren autentiseras innan du använder API: er som definieras av dessa domänkontrollanter. Ta bort skrivskyddsattributet från styrenheten för att tillåta att kontrollanten kan användas anonymt. Om du vill ange behörigheter på en mer detaljerad nivå gäller attributet för varje metod som kräver tillstånd i stället för att tillämpas på klassen domänkontrollant.

## <a name="next-steps"></a>Nästa steg
[Lär dig mer om Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

