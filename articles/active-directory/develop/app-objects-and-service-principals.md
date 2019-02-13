---
title: Program och tjänstobjekt i Azure Active Directory
description: Läs mer om relationen mellan program och tjänstobjekt i Azure Active Directory.
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
services: active-directory
editor: ''
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41eeb4c45e4ef2f04a5e53fde081a2c46093d379
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56186088"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Program och tjänstobjekt i Azure Active Directory

Ibland innebörden av termen ”program” kan vara tror många när de används i samband med Azure Active Directory (AD Azure). Den här artikeln visar de termer och konkreta delarna av integrering av Azure AD, med en illustration av registrering och medgivande för en [program med flera innehavare](developer-glossary.md#multi-tenant-application).

## <a name="overview"></a>Översikt

Ett program som har integrerats med Azure AD har effekter som sträcker sig utöver aspekten av programvara. ”Program” används ofta som en konceptuell termen avser inte bara program, men även dess Azure AD-registrering och roll i autentisering/auktorisering ”konversationer” vid körning.

Ett program kan fungera i dessa roller per definition:

- [Klienten](developer-glossary.md#client-application) roll (förbrukar en resurs)
- [Resursservern](developer-glossary.md#resource-server) roll (visar API: er på klienter)
- Både klienten roll och resurs-serverrollen

En [Auktoriseringsbeviljande för OAuth 2.0-flöde](developer-glossary.md#authorization-grant) definierar Konversations-protokollet, vilket gör att klienten/resursen åtkomst/skydda data för en resurs, respektive.

I följande avsnitt visas hur Azure AD-programmodell representerar ett program vid designtillfället och körning.

## <a name="application-registration"></a>Programregistrering

När du registrerar en Azure AD-program i den [Azure-portalen][AZURE-Portal], skapas två objekt i Azure AD-klienten:

- Ett programobjekt och
- En tjänstens huvudnamnsobjekt

### <a name="application-object"></a>programobjekt

Ett Azure AD-program har definierats med dess ett och endast programobjektet, som finns i Azure AD-klient där programmet har registrerats, kallas programmets ”startklientorganisation”. Azure AD Graph [program entitet] [ AAD-Graph-App-Entity] definierar schemat för ett programobjekt egenskaper.

### <a name="service-principal-object"></a>tjänstens huvudnamnsobjekt

För att komma åt resurser som skyddas av en Azure AD-klient, måste den entitet som kräver åtkomst representeras av ett säkerhetsobjekt. Det här gäller för både användare (användarens huvudnamn) och program (tjänstens huvudnamn).

Säkerhetsobjektet definierar åtkomstprincip och behörigheter för användare/program i Azure AD-klient. På så sätt kan kärnfunktioner som autentisering av användare/programmet under inloggning och auktorisering vid åtkomst till resurser.

När ett program ges behörighet att komma åt resurser i en klient (vid registreringen eller [godkänna](developer-glossary.md#consent)), en tjänstens huvudnamnsobjekt har skapats. Azure AD Graph [ServicePrincipal entitet] [ AAD-Graph-Sp-Entity] definierar schemat för en service principal objektets egenskaper.

### <a name="application-and-service-principal-relationship"></a>Program och tjänstens huvudnamn relation

Överväg att programobjektet som den *globala* representation av ditt program för användning på alla klienter och tjänstens huvudnamn som den *lokala* representation för användning i en specifik klient.

Programmet objektet fungerar som den mallen från vilka vanliga och standardegenskaperna är *härledda* som ska användas när motsvarande objekt för tjänstens huvudnamn. Ett programobjekt därför har en 1:1-relation med programmet och en 1:many relationer med dess motsvarande huvudnamn service-objekt.

Ett huvudnamn för tjänsten måste skapas på varje klient där programmet används, så att systemet kan upprätta en identitet för inloggning och/eller åtkomst till resurser som skyddas av klienten. En enda klient-programmet har bara ett huvudnamn för tjänsten (i dess startklientorganisation) skapas och godkänt villkoren för användning under programregistrering. En flera innehavare Webbapp/API har också ett huvudnamn för tjänsten som skapats i varje klienter där en användare från den klientorganisationen har samtyckt för dess användning. 

> [!NOTE]
> Alla ändringar du gör att din programobjektet också avspeglas i dess tjänstens huvudnamnsobjekt i programmets startklientorganisation endast (klient där det har registrerats). För program med flera klienter, programobjektet ändringar återspeglas inte i några konsument innehavare tjänsthuvudnamnsobjekt, förrän åtkomst tas bort via den [Programåtkomstpanelen](https://myapps.microsoft.com) och beviljat igen.
>
> Observera också att interna program är registrerade som flera innehavare som standard.

## <a name="example"></a>Exempel

Följande diagram illustrerar förhållandet mellan ett programs programobjektet och motsvarande tjänst huvudnamn objekt i kontexten för ett exempelprogram för flera innehavare kallad **HR app**. Det finns tre Azure AD-klienter i det här exemplet:

- **Adatum** -klienten som används av det företag som har utvecklats i **HR-app**
- **Contoso** -klienten som används av Contoso-organisationen som är konsumenter av den **HR-app**
- **Fabrikam** -klienten som används av organisationen Fabrikam som använder också den **HR-app**

![Förhållandet mellan ett programobjekt och en tjänstens huvudnamnsobjekt](./media/app-objects-and-service-principals/application-objects-relationship.png)

I det här exemplet:

| Steg | Beskrivning |
|------|-------------|
| 1    | Är processen för att skapa program och tjänstobjekt i programmets startklientorganisation. |
| 2    | När Contoso och Fabrikam administratörer Slutför medgivande, en tjänstens huvudnamnsobjekt skapas i företagets Azure AD-klient och tilldelas de behörigheter som administratören. Observera också att HR-appen kan konfigureras/utformade för att möjliggöra godkännande av användare för personligt bruk. |
| 3    | HR programmet (Contoso och Fabrikam) varje konsument-klienter har sina egna tjänstens huvudnamnsobjekt. Varje representerar deras användning av en instans av programmet vid körning, regleras av behörigheterna som godkänt av respektive administratören. |

## <a name="next-steps"></a>Nästa steg

- Du kan använda den [Azure AD Graph-testaren](https://graphexplorer.azurewebsites.net/) att fråga både programmet och objekt för tjänstens huvudnamn.
- Du kan komma åt programobjektet för ett program med hjälp av Azure AD Graph-API i [Azure-portalens] [ AZURE-Portal] redigeringsprogrammet för applikationsmanifestet, eller [Azure AD PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), som representeras av dess OData [program entitet][AAD-Graph-App-Entity].
- Du kan komma åt ett program tjänstens huvudnamnsobjekt via Azure AD Graph-API eller [Azure AD PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), som representeras av dess OData [ServicePrincipal entitet] [ AAD-Graph-Sp-Entity].

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Portal]: https://portal.azure.com
