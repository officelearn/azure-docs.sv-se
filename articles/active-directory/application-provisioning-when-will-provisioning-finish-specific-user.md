---
title: Ta reda på när en viss användare kommer att kunna komma åt ett program | Microsoft Docs
description: Hur du tar reda på när en ytterst viktiga användare kunna komma åt ett program som du har konfigurerat för etableringen av användare med Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 94b7ade4452f760940d545829c71d3b81426467a
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363068"
---
# <a name="find-out-when-a-specific-user-will-be-able-to-access-an-application"></a>Ta reda på när en viss användare kommer att kunna komma åt ett program
När du använder automatisk användaretablering med ett program, Azure AD automatiskt etablera och uppdatera användarkonton i en app baserat på saker som [användar- och grupptilldelningar](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) vid en schemalagd tid intervall, vanligtvis varje 10 minuter.

## <a name="how-long-does-it-take"></a>Hur lång tid tar det?

Den tid det tar för en viss användare som ska etableras beror huvudsakligen på om huruvida en inledande ”full” synkronisering redan har inträffat.

Den första synkroniseringen mellan Azure AD och en app kan ta allt från 20 minuter till flera timmar beroende på storleken på Azure AD-katalog och antalet användare inom omfånget för etablering. 

Efterföljande synkroniseringar efter den första synkroniseringen vara snabbare (t.ex. inom 10 minuter), som lagrar vattenstämplar som representerar tillståndet för båda systemen efter den inledande synkroniseringen, förbättra prestanda för efterföljande synkroniseringar för etableringstjänsten.

## <a name="how-to-check-the-status-of-a-user"></a>Så här kontrollerar du statusen för en användare

Om du vill se Etableringsstatus för en vald användare läser du granskningsloggarna i Azure AD.

Etablering granskningsloggarna är tillgängliga i Azure-portalen i den **Azure Active Directory &gt; Företagsappar &gt; \[programnamn\] &gt; granskningsloggarna** fliken. Filtrera loggarna på den **Kontoetablering** kategori för att bara se händelserna etablering för appen. Du kan söka efter användare baserat på ”matchande ID” som har konfigurerats för dem i attributmappningarna. 

Exempel: Om du har konfigurerat ”user principal name” eller ”e-postadress” som det matchande attributet på Azure AD-sida och användaren inte etablera har värdet ”audrey@contoso.com”, sedan Sök i granskningsloggarna för ”audrey@contoso.com” och gå sedan igenom poster returnerade.

Etablering granskningsloggarna registrera alla åtgärder som utförs av etableringstjänsten, inklusive:

* Ställa frågor till Azure AD för tilldelade användare som är inom omfånget för etablering
* Fråga målappen förekomsten av dessa användare
* Jämföra användarobjekt mellan systemet
* Att lägga till, uppdatera eller inaktivera användarkontot i målsystemet baserat på en jämförelse

## <a name="next-steps"></a>Nästa steg
[Automatisera etablering och avetablering för SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)''
