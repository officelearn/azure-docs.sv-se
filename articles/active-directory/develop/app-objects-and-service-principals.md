---
title: Appar & tjänsthuvudnamn i Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om relationen mellan program- och tjänsthuvudobjekt i Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: a636ff15da09bcf1891618d65270376f26fd3239
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885607"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Objekt för program och tjänstens huvudnamn i Azure Active Directory

Ibland kan innebörden av termen "program" missförstås när den används i kontexten för Azure Active Directory (Azure AD). Den här artikeln förtydligar de begreppsmässiga och konkreta aspekterna av Azure AD-programintegrering, med en illustration av registrering och samtycke för ett program med [flera innehavare](developer-glossary.md#multi-tenant-application).

## <a name="overview"></a>Översikt

Ett program som har integrerats med Azure AD har konsekvenser som går utöver programvaruaspekten. "Application" används ofta som en begreppsmässig term, som refererar till inte bara programprogrammet, utan även dess Azure AD-registrering och roll i autentisering/auktorisering "konversationer" vid körning.

Per definition kan ett program fungera i dessa roller:

- [Klientroll](developer-glossary.md#client-application) (förbrukar en resurs)
- [Resursserverroll](developer-glossary.md#resource-server) (exponera API:er för klienter)
- Både klientroll och resursserverroll

Ett [OAuth 2.0 Auktoriseringsbidragsflöde](developer-glossary.md#authorization-grant) definierar konversationsprotokollet, vilket gör att klienten/resursen kan komma åt/skydda en resurs data.

I följande avsnitt får du se hur Azure AD-programmodellen representerar ett program vid designtid och körning.

## <a name="application-registration"></a>Programregistrering

När du registrerar ett Azure AD-program i [Azure-portalen][AZURE-Portal]skapas två objekt i din Azure AD-klient:

- Ett programobjekt och
- Ett objekt för tjänstens huvudnamn

### <a name="application-object"></a>Programobjekt

Ett Azure AD-program definieras av dess enda programobjekt, som finns i Azure AD-klienten där programmet registrerades, känd som programmets "hem"-klientorganisation. Entiteten Microsoft [Graph-program][MS-Graph-App-Entity] definierar schemat för ett programobjekts egenskaper.

### <a name="service-principal-object"></a>Huvudobjekt för tjänsten

För att komma åt resurser som skyddas av en Azure AD-klient måste entiteten som kräver åtkomst representeras av ett säkerhetsobjekt. Detta gäller både användare (användarens huvudnamn) och program (tjänstens huvudnamn).

Säkerhetsobjektet definierar åtkomstprincipen och behörigheterna för användaren/programmet i Azure AD-klienten. Detta möjliggör grundläggande funktioner som autentisering av användaren/programmet under inloggning och auktorisering under resursåtkomst.

När ett program har behörighet att komma åt resurser i en klient (vid registrering eller [medgivande)](developer-glossary.md#consent)skapas ett huvudobjekt för tjänsten. Entiteten Microsoft Graph [ServicePrincipal][MS-Graph-Sp-Entity] definierar schemat för egenskaperna för ett huvudobjekt för tjänstens huvudobjekt.

### <a name="application-and-service-principal-relationship"></a>Relation mellan program och tjänstens huvudnamn

Betrakta programobjektet som den *globala* representationen av ditt program för användning i alla klienter och tjänstens huvudnamn som *lokal* representation för användning i en viss klientorganisation.

Programobjektet fungerar som mallen som vanliga och standardinställda egenskaper *härleds* för användning när motsvarande objekt för tjänstens huvudnamn skapas. Ett programobjekt har därför en 1:1-relation med programvaran och en 1:many-relation med motsvarande huvudobjekt för tjänsten.

Ett tjänsthuvudnamn måste skapas i varje klient där programmet används, vilket gör det möjligt att upprätta en identitet för inloggning och/eller åtkomst till resurser som skyddas av klienten. Ett program för enskild klient har bara ett tjänsthuvudnamn (i dess startklientorganisation), som skapas och godkänns vid programregistrering. Ett webbprogram/API för flera innehavare har också ett tjänsthuvudnamn som skapats i varje klient där en användare från klienten har samtyckt till dess användning.

> [!NOTE]
> Alla ändringar du gör i ditt programobjekt återspeglas också i dess huvudobjekt för tjänsten i programmets hemklient (klienten där den registrerades). För program med flera innehavare återspeglas inte ändringar i programobjektet i några principobjekt för konsumentklienter, förrän åtkomsten tas bort via [programåtkomstpanelen](https://myapps.microsoft.com) och beviljas igen.
>
> Observera också att inbyggda program som standard registreras som flera innehavare.

## <a name="example"></a>Exempel

Följande diagram illustrerar förhållandet mellan ett programs programobjekt och motsvarande huvudobjekt för tjänsten, i samband med ett exempel på ett exempel på ett program med flera innehavare som kallas **HR-app**. Det finns tre Azure AD-klienter i det här exempelscenariot:

- **Adatum** - Hyresgästen som används av företaget som utvecklade **HR-appen**
- **Contoso** - Klienten som används av Contoso-organisationen, som är en konsument av **HR-appen**
- **Fabrikam** - Klienten som används av Fabrikam-organisationen, som också förbrukar **HR-appen**

![Relation mellan appobjekt och tjänsthuvudobjekt](./media/app-objects-and-service-principals/application-objects-relationship.svg)

I det här exemplet:

| Steg | Beskrivning |
|------|-------------|
| 1    | Är processen att skapa programmet och tjänstens huvudobjekt i programmets hemklient. |
| 2    | När Contoso- och Fabrikam-administratörer har slutfört medgivandet skapas ett huvudobjekt för tjänsten i företagets Azure AD-klient och tilldelas de behörigheter som administratören beviljade. Observera också att HR-appen kan konfigureras/utformas så att användarna samtycker för individuell användning. |
| 3    | Konsumentenkresgästerna för HR-programmet (Contoso och Fabrikam) har var sin tjänst huvudobjekt. Var och en representerar deras användning av en instans av programmet vid körning, som styrs av de behörigheter som godkänts av respektive administratör. |

## <a name="next-steps"></a>Nästa steg

- Du kan använda [Utforskaren](https://developer.microsoft.com/graph/graph-explorer) i Microsoft Graph för att fråga både programmet och tjänstens huvudobjekt.
- Du kan komma åt ett programs programobjekt med hjälp av Microsoft Graph API, [Azure-portalens][AZURE-Portal] programmanifestredigerare eller [Azure AD PowerShell-cmdletar](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), som representeras av dess OData-programentitet . [Application entity][MS-Graph-App-Entity]
- Du kan komma åt ett programs huvudobjekt för tjänsten via Microsoft Graph API- eller [Azure AD PowerShell-cmdletar](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), som representeras av dess OData [ServicePrincipal-entitet][MS-Graph-Sp-Entity].

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
