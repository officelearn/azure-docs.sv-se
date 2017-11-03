---
title: "Ta reda på när en viss användare kommer att kunna komma åt ett program | Microsoft Docs"
description: "Ta reda på när en ytterst viktigt användare komma åt ett program som du har konfigurerat för användaretablering med Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: fcefb31904cfb77022db0358e9feee6a0479db81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="find-out-when-a-specific-user-will-be-able-to-access-an-application"></a>Ta reda på när en viss användare kommer att kunna komma åt ett program
När du använder automatisk användaretablering med ett program, Azure AD automatiskt etablera och uppdatera användarkonton i en app baserat på sådant som [användar- och tilldelning](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) vid en schemalagd tid intervall, vanligtvis var 10 minuter.

## <a name="how-long-does-it-take"></a>Hur lång tid tar det?

Den tid det tar för en användare som ska etableras beror huvudsakligen på huruvida en inledande ”full” sync redan har inträffat.

Den första synkroniseringen mellan Azure AD och en app kan ta allt från 20 minuter till flera timmar, beroende på storleken på Azure AD-katalog och antalet användare i omfånget för etablering. 

Efterföljande synkroniseringar efter den första synkroniseringen vara snabbare (t.ex. inom 10 minuter) som tjänsten etablering lagrar vattenstämplar som representerar tillståndet för båda systemen efter den första synkroniseringen, förbättra prestanda för efterföljande synkronisering.

## <a name="how-to-check-the-status-of-a-user"></a>Så här kontrollerar du statusen för en användare

Om du vill se Etableringsstatus för en vald användare finns i granskningsloggarna i Azure AD.

Etablering granskningsloggarna kan nås i Azure-portalen i den **Azure Active Directory &gt; Företagsappar &gt; \[programnamn\] &gt; granskningsloggarna** fliken. Filtrera loggarna på den **Kontoetablering** kategori bara ser de etablering händelserna för appen. Du kan söka efter användare baserat på ”matchande ID” som konfigurerats för dem i attributet avbildningar. 

Om du har konfigurerat ”huvudnamn” eller ”e-postadress” som matchar attributet på Azure AD-sida och användaren inte etablering har värdet till exempel ”audrey@contoso.com”, söka granskningsloggarna för ”audrey@contoso.com” och sedan granska poster returnerade.

Etablering granskningsloggarna registrera alla åtgärder som utförs av tjänsten etablering, inklusive:

* Hämta Azure AD för tilldelade användare som ingår i omfånget för etablering
* Frågar appen mål förekomsten av användare
* Jämförelse mellan användarobjekt mellan systemet
* Lägga till, uppdatera eller inaktivera användarkontot i målsystemet baserat på jämförelsen

## <a name="next-steps"></a>Nästa steg
[Automatisera användaren etablering och avetablering för SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)''
