<properties 
    pageTitle="Fehler während der Authentifizierungserkennung" 
    description="Der Verbindungs-Assistent für Active Directory hat einen inkompatiblen Authentifizierungstyp erkannt." 
    services="active-directory" 
    documentationCenter="" 
    authors="TomArcher" 
    manager="douge" 
    editor=""/>
  
<tags 
    ms.service="active-directory" 
    ms.workload="web" 
    ms.tgt_pltfrm="vs-getting-started" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/10/2015" 
    ms.author="tarcher"/>

# Fehler während der Authentifizierungserkennung

Beim Erkennen des vorherigen Authentifizierungscodes hat der Assistent einen nicht kompatiblen Authentifizierungstyp erkannt.   

##Was wird überprüft?

**Hinweis:** um vorherigen Authentifizierungscode in einem Projekt ordnungsgemäß erkennen zu können, muss das Projekt erstellt werden.  Wenn dieser Fehler auftritt und in Ihrem Projekt kein vorheriger Authentifizierungscode enthalten ist, erstellen Sie Ihr Projekt neu, und versuchen Sie es nochmals.

###Projekttypen

Der Assistent überprüft, welche Art von Projekt Sie entwickeln, um die richtige Authentifizierungslogik in das Projekt einzufügen.  Wenn ein Controller im Projekt von `ApiController` abgeleitet wird, gilt das Projekt als WebAPI-Projekt.  Wenn alle Controller im Projekt von `MVC.Controller` abgeleitet werden, gilt das Projekt als MVC-Projekt.  Alles andere wird vom Assistenten nicht unterstützt.  WebForms-Projekte werden derzeit nicht unterstützt.

###Kompatibler Authentifizierungscode

Der Assistent überprüft auch Authentifizierungseinstellungen, die zuvor mit dem Assistenten konfiguriert wurden oder mit dem Assistenten kompatibel sind.  Wenn alle Einstellungen vorhanden sind, gilt dies als eintrittsinvarianter Fall, und der Assistent wird geöffnet und zeigt die Einstellungen an.  Wenn nur einige der Einstellungen vorhanden sind, wird es als Fehlerfall betrachtet.

In einem MVC-Projekt überprüft der Assistent die folgenden Einstellungen, die aus der vorherigen Verwendung des Assistenten resultieren:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Außerdem überprüft der Assistent die folgenden Einstellungen in einem Web-API-Projekt, die aus der vorherigen Verwendung des Assistenten resultieren:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

###Nicht kompatibler Authentifizierungscode

Der Assistent versucht schließlich, Versionen von Authentifizierungscode zu erkennen, die mit früheren Versionen von Visual Studio konfiguriert wurden. Wenn Sie diesen Fehler erhalten, ist ein nicht kompatibler Authentifizierungstyp in Ihrem Projekt vorhanden. Der Assistent erkennt die folgenden Authentifizierungstypen aus früheren Versionen von Visual Studio:

* Windows-Authentifizierung 
* Einzelne Benutzerkonten 
* Organisationskonten 
 

Zum Erkennen der Windows-Authentifizierung in einem MVC-Projekt sucht der Assistent nach dem `authentication` Element aus der **web.config** Datei.

<pre>
    &lt;configuration&gt;
        &lt;system.web&gt;
            <span style="background-color: yellow">& Lt; Authentication Mode = "Windows" / & Gt;</span>
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

Zum Erkennen der Windows-Authentifizierung in einem Web-API-Projekt sucht der Assistent nach dem `IISExpressWindowsAuthentication` Element aus des Projekts **.csproj** Datei:

<pre>
    &lt;Project&gt;
        &lt;PropertyGroup&gt;
            <span style="background-color: yellow">& Lt; IISExpressWindowsAuthentication & Gt; aktiviert & Lt; / IISExpressWindowsAuthentication & Gt;</span>
        &lt;/PropertyGroup>
    &lt;/Project&gt;
</pre>

Zum Erkennen der Authentifizierung einzelner Benutzerkonten sucht der Assistent nach dem Paketelement aus Ihrer **Packages.config** Datei.

<pre>
    &lt;packages&gt;
        <span style="background-color: yellow">& Lt; id="Microsoft.AspNet.Identity.EntityFramework-Paket" Version = "2.1.0" TargetFramework = "net45" / & Gt;</span>
    &lt;/packages&gt;
</pre>

Zum Erkennen der alten Formulars von organisationskontoauthentifizierung sucht der Assistent nach dem folgenden Element aus **web.config**:

<pre>
    &lt;configuration&gt;
        &lt;appSettings&gt;
            <span style="background-color: yellow">& Lt; fügen Sie Key = "Ida: Bereich" Value = "***" / & Gt;</span>
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

Wenn Sie den Authentifizierungstyp ändern möchten, entfernen Sie den inkompatiblen Authentifizierungstyp, und führen Sie den Assistenten dann erneut aus.

Weitere Informationen finden Sie unter [Authentifizierungsszenarien für Azure AD](active-directory-authentication-scenarios.md).

