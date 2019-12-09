---
title: Appar & tjänstens huvud namn i Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om relationen mellan program-och tjänst huvud objekt i Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cf436881a74cffd0053718413ce83a148fa019d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74918210"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Objekt för program och tjänstens huvudnamn i Azure Active Directory

Ibland kan termen "program" bli feltolkad när den används i samband med Azure Active Directory (Azure AD). Den här artikeln förklarar de konceptuella och konkreta aspekterna av Azure AD Application Integration med en illustration av registrering och medgivande för ett [program med flera innehavare](developer-glossary.md#multi-tenant-application).

## <a name="overview"></a>Översikt

Ett program som har integrerats med Azure AD har konsekvenser som går utöver program varu aspekten. "Program" används ofta som en konceptuell term, som refererar till inte bara program varan, utan även dess Azure AD-registrering och-roll i autentisering/auktorisering "konversationer" vid körning.

Efter definition kan ett program fungera i följande roller:

- [Klient](developer-glossary.md#client-application) roll (konsumera en resurs)
- [Resurs Server](developer-glossary.md#resource-server) roll (exponerar API: er för klienter)
- Både klient roll och resurs server roll

Ett [OAuth 2,0-auktoriseringsarkiv](developer-glossary.md#authorization-grant) definierar konversations protokollet, vilket gör att klienten/resursen kan komma åt/skydda en resurs data.

I följande avsnitt får du se hur Azure AD-programmodellen representerar ett program vid design tillfället och kör tid.

## <a name="application-registration"></a>Programregistrering

När du registrerar ett Azure AD-program i [Azure Portal][AZURE-Portal]skapas två objekt i Azure AD-klienten:

- Ett program objekt och
- Ett huvud objekt för tjänsten

### <a name="application-object"></a>program objekt

Ett Azure AD-program definieras av sitt enda program objekt, som finns i Azure AD-klienten där programmet registrerades, vilket kallas för programmets "hem"-klient. [Entiteten Microsoft Graph program][MS-Graph-App-Entity] definierar schemat för ett program objekts egenskaper.

### <a name="service-principal-object"></a>tjänstens huvud namns objekt

För att få åtkomst till resurser som skyddas av en Azure AD-klient måste den entitet som kräver åtkomst representeras av ett säkerhets objekt. Detta gäller både för användare (användarens huvud namn) och program (tjänstens huvud namn).

Säkerhets principen definierar åtkomst principen och behörigheter för användaren/programmet i Azure AD-klienten. Detta möjliggör kärn funktioner som autentisering av användaren/programmet vid inloggning och auktorisering under resurs åtkomst.

När ett program har behörighet att komma åt resurser i en klient organisation (vid registrering eller [medgivande](developer-glossary.md#consent)) skapas ett huvud objekt för tjänsten. [Entiteten Microsoft Graph ServicePrincipal][MS-Graph-Sp-Entity] definierar schemat för ett tjänst huvud objekts egenskaper.

### <a name="application-and-service-principal-relationship"></a>Relation mellan program och tjänstens huvudnamn

Överväg programobjektet som den *globala* åter givningen av programmet för användning över alla klienter och tjänstens huvud namn som den *lokala* åter givningen för användning i en specifik klient.

Programobjektet fungerar som mallen som vanliga och standardinställda egenskaper *härleds* för användning när motsvarande objekt för tjänstens huvudnamn skapas. Ett program objekt har därför en 1:1-relation med program varu programmet och en 1: många relationer med motsvarande tjänst huvud objekt.

Ett huvud namn för tjänsten måste skapas i varje klient där programmet används, vilket gör det möjligt att upprätta en identitet för inloggning och/eller åtkomst till resurser som skyddas av klienten. Ett program för enskild klient har bara ett tjänsthuvudnamn (i dess startklientorganisation), som skapas och godkänns vid programregistrering. Ett webb program/API för flera innehavare har också ett tjänst huvud namn som skapats i varje klient organisation där en användare från den klienten har samtyckt till användningen.

> [!NOTE]
> Alla ändringar du gör i ditt program objekt visas också i dess tjänst huvud objekt i programmets hem klient (den klient där det registrerades). För program med flera klient organisationer avspeglas inte ändringar i programobjektet i någon konsument innehavares tjänst huvud objekt, tills åtkomsten tas bort via [program åtkomst panelen](https://myapps.microsoft.com) och beviljats igen.
>
> Observera också att interna program registreras som flera klienter som standard.

## <a name="example"></a>Exempel

Följande diagram illustrerar förhållandet mellan ett programs program objekt och motsvarande tjänst huvud objekt, inom ramen för ett exempel på ett program för flera innehavare som kallas **HR-app**. Det finns tre Azure AD-klienter i det här exempel scenariot:

- **Adatum** – innehavare som används av företaget som utvecklade HR- **appen**
- **Contoso** – klienten som används av Contoso-organisationen, som är konsument för HR- **appen**
- **Fabrikam** – klienten som används av Fabrikam-organisationen, som också förbrukar **HR-appen**

![Förhållandet mellan app-objektet och tjänstens huvud namns objekt](./media/app-objects-and-service-principals/application-objects-relationship.svg)

I det här exempel scenariot:

| Steg | Beskrivning |
|------|-------------|
| 1    | Är processen för att skapa program-och tjänstens huvud namns objekt i programmets hem klient organisation. |
| 2    | När contoso och Fabrikam-administratörer har slutfört sitt medgivande skapas ett huvud namn för tjänsten i företagets Azure AD-klient och tilldelas de behörigheter som administratören har beviljat. Observera också att HR-appen kan konfigureras/utformas för att tillåta medgivande från användare för enskild användning. |
| 3    | Klient organisationerna för HR-programmet (contoso och Fabrikam) har sina egna tjänst huvud objekt. Varje representerar användningen av en instans av programmet vid körning, styrs av de behörigheter som har godkänts av respektive administratör. |

## <a name="next-steps"></a>Nästa steg

- Du kan använda [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) för att fråga både program-och tjänst huvud objekt.
- Du kan komma åt ett programs program objekt med hjälp av Microsoft Graph API, [Azure Portals][AZURE-Portal] program manifest redigeraren eller [Azure AD PowerShell-cmdletar](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), som representeras av dess OData- [programentitet][MS-Graph-App-Entity].
- Du kan komma åt ett programs huvud objekt för tjänsten via Microsoft Graph API eller [Azure AD PowerShell-cmdletar](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), som representeras av dess OData [ServicePrincipal-entitet][MS-Graph-Sp-Entity].

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
