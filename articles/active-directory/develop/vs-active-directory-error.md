---
title: "Felsökning av problem med Azure guiden Active Directory-anslutning"
description: "Guiden active directory-anslutning upptäckte ett inkompatibelt autentiseringstyp"
services: active-directory
documentationcenter: 
author: kraigb
manager: mtillman
editor: 
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/05/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 186bb1ede11c869b1352906b7ebafe57025f4f09
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connection-wizard"></a>Diagnostisera fel med Azure guiden Active Directory-anslutning
Guiden upptäckte en inkompatibel autentiseringstyp vid identifiering av föregående Autentiseringskod.   

## <a name="what-is-being-checked"></a>Vad är kontrolleras?
**Obs:** för att korrekt identifiera tidigare Autentiseringskod i ett projekt, projektet måste skapas.  Om detta fel uppstod och du inte har en tidigare Autentiseringskod i projektet, återskapa och försök igen.

### <a name="project-types"></a>Projekttyper
Guiden kontrollerar vilken typ av projekt som du utvecklar så att den kan mata in rätt autentiseringslogiken i projektet.  Om det inte finns några domänkontrollanter som härleds från `ApiController` i projektet projektet betraktas som ett WebAPI-projekt.  Om det finns endast domänkontrollanter som härleds från `MVC.Controller` i projektet projektet betraktas som en MVC-projektet.  Något annat stöds inte av guiden.

### <a name="compatible-authentication-code"></a>Kompatibel Autentiseringskod
Guiden kontrollerar också autentiseringsinställningar som tidigare har konfigurerats med hjälp av guiden eller som är kompatibel med hjälp av guiden.  Visa inställningarna om alla inställningar finns, anses det vara ett Reentrant ärende och guiden öppnas.  Om endast vissa inställningar finns anses ett ärende för fel.

Guiden söker efter någon av följande inställningar som kommer från tidigare användning av guiden i MVC-projektet:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Dessutom kan söker guiden efter någon av följande inställningar i ett Web API-projekt som kommer från tidigare användning av guiden:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

### <a name="incompatible-authentication-code"></a>Inkompatibel Autentiseringskod
Slutligen görs ett försök att identifiera versioner av Autentiseringskod som har konfigurerats med tidigare versioner av Visual Studio. Om du har fått det här felet betyder det att projektet innehåller en inkompatibel autentiseringstyp. Guiden identifierar följande typer av autentisering från tidigare versioner av Visual Studio:

* Windows-autentisering 
* Enskilda användarkonton 
* Organisationskonton 

För att identifiera Windows-autentisering i ett projekt med MVC guiden söker efter den `authentication` element från din **web.config** fil.

<pre>
    &lt;configuration&gt;
        &lt;system.web&gt;
            <span style="background-color: yellow">&lt;authentication mode="Windows" /&gt;</span>
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

För att identifiera Windows-autentisering i ett Web API-projekt, guiden söker efter den `IISExpressWindowsAuthentication` element från ditt projekt **.csproj** fil:

<pre>
    &lt;Project&gt;
        &lt;PropertyGroup&gt;
            <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;</span>
        &lt;/PropertyGroup>
    &lt;/Project&gt;
</pre>

För att identifiera enskilda användarkonton autentisering guiden söker efter paket-elementet från din **Packages.config-fil** fil.

<pre>
    &lt;packages&gt;
        <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /&gt;</span>
    &lt;/packages&gt;
</pre>

För att identifiera en gamla form av autentisering av Organisationskonto guiden söker efter följande element från **web.config**:

<pre>
    &lt;configuration&gt;
        &lt;appSettings&gt;
            <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /&gt;</span>
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

Ta bort inkompatibla autentiseringstyp och kör guiden igen om du vill ändra autentiseringstyp.

Mer information finns i [Autentiseringsscenarier för Azure AD](active-directory-authentication-scenarios.md).

#<a name="next-steps"></a>Nästa steg
- [Autentiseringsscenarier för Azure AD](active-directory-authentication-scenarios.md)