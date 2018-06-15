---
title: Felsökning av problem med Azure Active Directory ansluten tjänst
description: Tjänsten active directory anslutna upptäckte ett inkompatibelt autentiseringstyp
services: active-directory
author: ghogen
manager: douge
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: 8cd15c59bbe536dba9adb6f44c07844eaf030b55
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31784895"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnostisera fel med Azure Active Directory anslutna Service

Vid identifiering av föregående Autentiseringskod, Azure Active Directory connect-servern upptäckte ett inkompatibelt autentiseringstyp.

För att korrekt identifiera tidigare Autentiseringskod i ett projekt, måste du skapat projektet.  Om detta fel uppstod och du inte har en tidigare Autentiseringskod i projektet, återskapa och försök igen.

## <a name="project-types"></a>Projekttyper

Anslutna tjänsten kontrollerar vilken typ av projekt som du utvecklar så att den kan mata in rätt autentiseringslogiken i projektet. Om det inte finns några domänkontrollanter som härleds från `ApiController` i projektet projektet betraktas som ett WebAPI-projekt. Om det finns endast domänkontrollanter som härleds från `MVC.Controller` i projektet projektet betraktas som en MVC-projektet. Anslutna tjänsten stöder inte någon annan projekttyp.

## <a name="compatible-authentication-code"></a>Kompatibel Autentiseringskod

Tjänsten anslutna kontrollerar också autentiseringsinställningarna som tidigare har konfigurerats och är kompatibla med tjänsten. Om alla inställningar finns Reentrant fall är det och öppnas för anslutna service visa inställningarna.  Om endast vissa inställningar finns anses ett ärende för fel.

I ett projekt med MVC anslutna tjänsten kontrollerar för någon av följande inställningar som kommer från tidigare användning av tjänsten:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Dessutom kontrollerar anslutna tjänsten för någon av följande inställningar i ett Web API-projekt som kommer från tidigare användning av tjänsten:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Inkompatibel Autentiseringskod

Slutligen försöker anslutna tjänsten identifiera versioner av Autentiseringskod som har konfigurerats med tidigare versioner av Visual Studio. Om du har fått det här felet betyder det att projektet innehåller en inkompatibel autentiseringstyp. Tjänsten anslutna identifierar följande typer av autentisering från tidigare versioner av Visual Studio:

* Windows-autentisering
* Enskilda användarkonton
* Organisationskonton

För att identifiera Windows-autentisering i MVC-projekt som de anslutna som söker efter den `authentication` element i din `web.config` fil.

```xml
<configuration>
    <system.web>
        <span style="background-color: yellow"><authentication mode="Windows" /></span>
    </system.web>
</configuration>
```

För att identifiera Windows-autentisering i ett Web API-projekt, anslutna tjänsten söker efter den `IISExpressWindowsAuthentication` element i ditt projekt `.csproj` fil:

```xml
<Project>
    <PropertyGroup>
        <span style="background-color: yellow"><IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication></span>
    </PropertyGroup>
</Project>
```

För att identifiera enskilda användarkonton autentisering anslutna tjänsten söker efter elementet paketet i din `packages.config` fil.

```xml
<packages>
    <span style="background-color: yellow"><package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /></span>
</packages>
```

För att identifiera en gamla form av autentisering av Organisationskonto tjänsten anslutna ser ut för följande element i`web.config`:

```xml
<configuration>
    <appSettings>
        <span style="background-color: yellow"><add key="ida:Realm" value="***" /></span>
    </appSettings>
</configuration>
```

Om du vill ändra autentiseringstyp, ta bort inkompatibla autentiseringstypen och försök lägga till anslutna tjänsten igen.

Mer information finns i [Autentiseringsscenarier för Azure AD](active-directory-authentication-scenarios.md).