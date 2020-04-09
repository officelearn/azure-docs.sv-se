---
title: Diagnostisera fel med Azure AD-ansluten tjänst (Visual Studio)
description: Active Directory-ansluten tjänst upptäckte en inkompatibel autentiseringstyp
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 4b39aa77ea3895a606ad34a3bc9b70dba924a23f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886100"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnostisera fel med Azure Active Directory Connected Service

När du identifierade tidigare autentiseringskod upptäckte den Anslutna Azure-tjänsten en inkompatibel autentiseringstyp.

Om du vill identifiera tidigare autentiseringskod korrekt i ett projekt måste projektet återskapas. Om det här felet visas och du inte har någon tidigare autentiseringskod i projektet återskapar och försöker du igen.

## <a name="project-types"></a>Projekttyper

Den anslutna tjänsten kontrollerar vilken typ av projekt du utvecklar så att den kan injicera rätt autentiseringslogik i projektet. Om det finns någon styrenhet `ApiController` som härstammar från i projektet betraktas projektet som ett WebAPI-projekt. Om det bara finns styrenheter som härrör från `MVC.Controller` i projektet betraktas projektet som ett MVC-projekt. Den anslutna tjänsten stöder inte någon annan projekttyp.

## <a name="compatible-authentication-code"></a>Kompatibel autentiseringskod

Den anslutna tjänsten söker också efter autentiseringsinställningar som tidigare har konfigurerats eller är kompatibla med tjänsten. Om alla inställningar finns betraktas det som ett fall för återtillverkade deltagare och den anslutna tjänsten öppnar inställningarna.  Om det bara finns några av inställningarna betraktas det som ett felfall.

I ett MVC-projekt kontrollerar den anslutna tjänsten någon av följande inställningar, som är ett resultat av tidigare användning av tjänsten:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Den anslutna tjänsten söker också efter någon av följande inställningar i ett webb-API-projekt, som är ett resultat av tidigare användning av tjänsten:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Inkompatibel autentiseringskod

Slutligen försöker den anslutna tjänsten identifiera versioner av autentiseringskod som har konfigurerats med tidigare versioner av Visual Studio. Om du fick det här felet betyder det att projektet innehåller en inkompatibel autentiseringstyp. Den anslutna tjänsten identifierar följande typer av autentisering från tidigare versioner av Visual Studio:

* Windows-autentisering
* Enskilda användarkonton
* Organisationskonton

Om du vill identifiera Windows-autentisering i ett `authentication` MVC-projekt söker den anslutna efter elementet i `web.config` filen.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Om du vill identifiera Windows-autentisering i ett `IISExpressWindowsAuthentication` webb-API-projekt `.csproj` söker den anslutna tjänsten efter elementet i projektets fil:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Om du vill identifiera autentisering av enskilda användarkonton `packages.config` söker den anslutna tjänsten efter paketelementet i filen.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Om du vill identifiera en gammal form av organisationskontoautentisering`web.config`söker den anslutna tjänsten efter följande element i:

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Om du vill ändra autentiseringstypen tar du bort den inkompatibla autentiseringstypen och försöker lägga till den anslutna tjänsten igen.

Mer information finns i [Autentiseringsscenarier för Azure AD](authentication-scenarios.md).
