---
title: Diagnostisera fel med Azure AD Connected service (Visual Studio)
description: Den Active Directory-anslutna tjänsten upptäckte en inkompatibel autentiseringstyp
author: ghogen
manager: jillfra
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: a6ec31f0d60c7f6e3737dc4042b05a6d8bf3dd5e
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699979"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnostisera fel med den Azure Active Directory anslutna tjänsten

Vid identifiering av tidigare autentiserings kod identifierade den Azure Active Directory anslutna tjänsten en inkompatibel autentiseringstyp.

Projektet måste återskapas för att det ska gå att identifiera tidigare autentiserings kod i ett projekt. Om du ser det här felet och du inte har en tidigare autentiseringsnyckel i projektet, återskapa du och försöker igen.

## <a name="project-types"></a>Projekt typer

Den anslutna tjänsten kontrollerar vilken typ av projekt du utvecklar så att den kan mata in rätt autentiserings logik i projektet. Om det finns en kontrollant som är härledd från `ApiController` i projektet, betraktas projektet som ett WebAPI-projekt. Om det bara finns kontrollanter som är härledda från `MVC.Controller` i projektet, betraktas projektet som ett MVC-projekt. Den anslutna tjänsten har inte stöd för andra projekt typer.

## <a name="compatible-authentication-code"></a>Kompatibel autentiseringsnyckel

Den anslutna tjänsten söker också efter autentiseringsinställningar som har kon figurer ATS tidigare eller som är kompatibla med tjänsten. Om alla inställningar finns med, betraktas det som en ny reentrant-väska och den anslutna tjänsten öppnas Visa inställningarna.  Om bara vissa av inställningarna finns, betraktas det som ett fel.

I ett MVC-projekt söker den anslutna tjänsten efter någon av följande inställningar, vilket beror på tidigare användning av tjänsten:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Dessutom kontrollerar den anslutna tjänsten om det finns någon av följande inställningar i ett Web API-projekt som kommer från tidigare användning av tjänsten:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Autentiserings kod är inkompatibel

Slutligen försöker den anslutna tjänsten identifiera versioner av autentiserings kod som har kon figurer ATS med tidigare versioner av Visual Studio. Om du har fått det här felet innebär det att ditt projekt innehåller en inkompatibel autentiseringstyp. Den anslutna tjänsten identifierar följande typer av autentisering från tidigare versioner av Visual Studio:

* Windows-autentisering
* Enskilda användar konton
* Organisations konton

För att identifiera Windows-autentisering i ett MVC-projekt söker den anslutna efter `authentication`-elementet i `web.config`-filen.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

För att identifiera Windows-autentisering i ett Web API-projekt söker den anslutna tjänsten efter `IISExpressWindowsAuthentication`-elementet i projektets `.csproj`-fil:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Den anslutna tjänsten söker efter paket elementet i `packages.config`-filen för att identifiera autentisering av enskilda användar konton.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

För att identifiera en gammal form av autentisering med organisations konto letar den anslutna tjänsten efter följande element i`web.config`:

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Om du vill ändra autentiseringstypen tar du bort den inkompatibla autentiseringstypen och försöker att lägga till den anslutna tjänsten igen.

Mer information finns i [autentiserings scenarier för Azure AD](authentication-scenarios.md).
