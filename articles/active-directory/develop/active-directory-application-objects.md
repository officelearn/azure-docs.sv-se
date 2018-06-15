---
title: Azure Active Directory-program och tjänstens huvudnamn objekt
description: En beskrivning av relationen mellan program och tjänstens huvudnamn objekt i Azure Active Directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
services: active-directory
editor: ''
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/19/2017
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: e8e693355fb9b30e1a69b49f20d5044c531e2fcd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34155627"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory-azure-ad"></a>Programmet och service principal objekt i Azure Active Directory (AD Azure)
Ibland innebörden av termen ”program” kan vara tror många när de används i samband med Azure AD. Målet med den här artikeln är att klargöra konceptuell och konkreta aspekter av Azure AD-integrering för programmet, med en illustration av registrering och medgivande för en [flera innehavare programmet](active-directory-dev-glossary.md#multi-tenant-application).

## <a name="overview"></a>Översikt
Ett program som har integrerats med Azure AD har effekter som sträcker sig utöver programvara aspekt. ”Program” används ofta som en konceptuell term hänvisar till inte bara program, men även dess Azure AD-registrering och roll i autentisering/auktorisering ”konversationer” vid körning. Per definition erbjuder ett program kan fungera i en [klienten](active-directory-dev-glossary.md#client-application) roll (förbrukar en resurs), en [resursservern](active-directory-dev-glossary.md#resource-server) roll medför API: er (klienter) eller till och med båda. Protokollet konversation definieras av ett [flödet för OAuth 2.0 Authorization Grant](active-directory-dev-glossary.md#authorization-grant), vilket gör att klienten/resurs åtkomst/skydda en resurs data respektive. Nu går vi en djupare nivå och se hur Azure AD-programmodell representerar ett program vid designtillfället och körning. 

## <a name="application-registration"></a>Programregistrering
När du registrerar en Azure AD-program i den [Azure-portalen][AZURE-Portal], skapas två objekt i Azure AD-klienten: programobjekt och ett service principal-objekt.

#### <a name="application-object"></a>Programobjektet
Ett Azure AD-program har definierats med dess ett och endast programobjektet, som finns i Azure AD-klient där programmet registrerades kallas programmets ”hem” klient. Azure AD Graph [programmet entiteten] [ AAD-Graph-App-Entity] definierar schemat för ett program-objektets egenskaper. 

#### <a name="service-principal-object"></a>Tjänstens huvudnamn objekt
För att komma åt resurser som skyddas av en Azure AD-klient, måste den entitet som kräver åtkomst representeras av ett säkerhetsobjekt. Detta gäller för både användare (användarens huvudnamn) och program (tjänstens huvudnamn). Säkerhetsobjektet definierar åtkomstprincip och behörigheter för användare/application i den klienten. Detta gör att grundläggande funktioner, till exempel autentisering av användare/application under inloggning och auktorisering under åtkomst till företagsresurser.

När ett program ges åtkomst till resurser i en klient (vid registreringen eller [medgivande](active-directory-dev-glossary.md#consent)), ett huvudnamn tjänstobjekt skapas. Azure AD Graph [ServicePrincipal entiteten] [ AAD-Graph-Sp-Entity] definierar schemat för en service principal objektets egenskaper. 

#### <a name="application-and-service-principal-relationship"></a>Program och tjänstens huvudnamn relation
Överväg att programobjektet som den *globala* representation av ditt program för användning på alla klienter och tjänstens huvudnamn som den *lokala* representation för användning i en viss klient. Programmet objektet fungerar som den mallen från vilka vanliga och standardegenskaperna är *härledda* som ska användas när motsvarande service principal objekt. Ett programobjekt måste därför en 1:1-relation med programmet och en 1:many relationer med dess motsvarande service principal objekt.

Ett huvudnamn för tjänsten måste skapas för varje klient där programmet används för att upprätta en identitet för inloggning och/eller åtkomst till resurser som skyddas av klienten. En enskild klient-programmet har bara ett huvudnamn för tjänsten (i hemmet klient), skapas och godkänt för användning under registreringen av program. En flera innehavare Web application/API har också ett huvudnamn för tjänsten som skapats i varje klient där en användare från att klienten har godkänt för dess användning. 

> [!NOTE]
> Alla ändringar du gör att objekt för program visas också i dess huvudsakliga serviceobjektet i programmets hem klienten endast (klient där den registrerades). För program med flera klienter, ändringar i programobjektet återspeglas inte i några konsumenten innehavare service principal objekt förrän åtkomsten tas bort den [programmet åtkomstpanelen](https://myapps.microsoft.com) och beviljas igen.
><br>  
> Observera också att interna program registreras som flera innehavare som standard.
> 
> 

## <a name="example"></a>Exempel
Följande diagram illustrerar förhållandet mellan ett program programobjektet och motsvarande service principal objekt i kontexten för ett exempelprogram för flera innehavare kallad **HR app**. Det finns tre Azure AD-klienter i det här scenariot: 

* **Adatum** -klienten som används av företaget som utvecklat den **HR-app**
* **Contoso** -klienten som används av Contoso-organisationen som är konsumenter av den **HR-app**
* **Fabrikam** -klienten som används av organisationen Fabrikam, vilket använder också den **HR-app**

![Förhållandet mellan ett programobjekt och en tjänst för huvudobjekt](./media/active-directory-application-objects/application-objects-relationship.png)

Steg 1 är processen att skapa programmet och service principal objekt i programmets hem klient i föregående diagram.

I steg 2 när Contoso och Fabrikam administratörer Slutför samtycke, ett huvudnamn tjänstobjekt skapas i företagets Azure AD-klient och tilldelas de behörigheter som administratören. Observera också att appen HR kunde konfigureras/utformade för att möjliggöra medgivande av användare för personligt bruk.

HR programmet (Contoso och Fabrikam) varje konsument-klienter har sina egna huvudnamn serviceobjektet i steg3. Varje representerar deras användning av en instans av programmet vid körning, regleras av behörigheter som har godkänt av respektive administratören.

## <a name="next-steps"></a>Nästa steg
Programobjektet för ett program kan nås via Azure AD Graph-API i [Azure portal] [ AZURE-Portal] manifestet Redigeraren för programmet, eller [Azure AD PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), som representeras av dess OData [programmet entiteten][AAD-Graph-App-Entity].

Programmets huvudsakliga webbtjänstobjektet kan nås via Azure AD Graph API eller [Azure AD PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), som representeras av dess OData [ServicePrincipal entiteten][AAD-Graph-Sp-Entity].

Den [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) är användbart för att fråga efter både program- och service principal objekt.

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Portal]: https://portal.azure.com
