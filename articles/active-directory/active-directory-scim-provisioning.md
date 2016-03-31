<properties
    pageTitle="Verwenden von SCIM für die automatische Bereitstellung von Benutzern und Gruppen aus Azure Active Directory für Anwendungen | Microsoft Azure"
    description="Über Azure Active Directory können Benutzer und Gruppen automatisch für alle Anwendungen oder Identitätsspeicher bereitgestellt werden, denen ein Webdienst mit einer Schnittstelle vorgelagert ist, wie sie in der SCIM-Protokollspezifikation definiert ist."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/29/2015"
    ms.author="asmalser-msft"/>

#Verwenden von SCIM für die automatische Bereitstellung von Benutzern und Gruppen aus Azure Active Directory für Anwendungen

##Übersicht

Azure Active Directory können Benutzer automatisch bereitstellen und Gruppen auf jeder Anwendung oder Identität, die von einem Webdienst mit der Benutzeroberfläche vorgelagert ist definiert, der [SCIM 2.0-Protokollspezifikation](https://tools.ietf.org/html/draft-ietf-scim-api-19). Azure Active Directory kann Anforderungen senden, um Benutzer und Gruppen für diesen Webdienst zu erstellen, zu ändern und zu löschen, und dann können diese Anforderungen in Vorgänge im Zielidentitätsspeicher übersetzt werden. 

![][1]
*Abbildung: Bereitstellung von Azure Active Directory in einem Identitätsspeicher über einen Webdienst*

Diese Funktion kann verwendet werden, zusammen mit der "[bringen Sie Ihre eigenen Apps](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)" Funktion in Azure AD einmaliges Anmelden und für Anwendungen, bereitstellen oder von einem Webdienst SCIM vorgelagert sind, automatische benutzerbereitstellung zu aktivieren.

In Azure Active Directory gibt es zwei Anwendungsfälle für SCIM:

* **Bereitstellung von Benutzern und Gruppen zu Anwendungen, die SCIM unterstützen** -Programmen, die SCIM 2.0 unterstützt und sind in der Lage, ein OAuth Bearer-Token von Azure AD akzeptieren funktioniert mit Azure AD des Felds.

* **Erstellen Ihrer eigenen Bereitstellung Lösung für Anwendungen, die andere API-basierte Bereitstellung unterstützt** -nicht SCIM handelt, können Sie einen Endpunkt SCIM übersetzt zwischen Azure AD SCIM Endpunkt und den Inhalt erstellen-API der Anwendung unterstützt für Benutzer bereitstellen.  Um die Entwicklung eines SCIM-Endpunkts zu erleichtern, stellen wir Ihnen CLI-Bibliotheken (Command Line Interface, Befehlszeilenschnittstelle) sowie Codebeispiele zur Verfügung, die das Bereitstellen eines SCIM-Endpunkts und das Übersetzen von SCIM-Nachrichten veranschaulichen.  

##Bereitstellen von Benutzern und Gruppen für Anwendungen, die SCIM unterstützen

Azure Active Directory kann so konfiguriert werden, dass automatisch bereitstellen, die zugewiesenen Benutzer und Gruppen für Clientanwendungen, die implementieren ein [System zur Identitätsmanagement für die domänenübergreifende 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) -Webdienst und OAuth Bearer-Token zur Authentifizierung akzeptieren. Im Rahmen der SCIM 2.0-Spezifikation müssen Anwendungen die folgenden Anforderungen erfüllen:

* Unterstützt das Erstellen von Benutzern und/oder Gruppen gemäß Abschnitt 3.3 des SCIM-Protokolls  

* Unterstützt das Ändern von Benutzern und/oder Gruppen mit Patch-Anforderungen gemäß Abschnitt 3.5.2 des SCIM-Protokolls  

* Unterstützt das Abrufen einer bekannten Ressource gemäß Abschnitt 3.4.1 des SCIM-Protokolls  

*  Unterstützt das Abfragen von Benutzern und/oder Gruppen gemäß Abschnitt 3.4.2 des SCIM-Protokolls  In der Standardeinstellung werden Benutzer nach „externalId“ und Gruppen nach „displayName“ abgefragt.  

* Unterstützt das Abfragen von Benutzern nach ID und nach Manager gemäß Abschnitt 3.4.2 des SCIM-Protokolls  

* Unterstützt das Abfragen von Gruppen nach ID und nach Mitglied gemäß Abschnitt 3.4.2 des SCIM-Protokolls  

* Akzeptiert OAuth-Bearertoken für die Autorisierung gemäß Abschnitt 2.1 des SCIM-Protokolls  

* Unterstützt die Verwendung von Azure AD als Identitätsanbieter für das OAuth-Token (Unterstützung für externe Identitätsanbieter in Kürze verfügbar)

Überprüfen Sie beim Anbieter Ihrer Anwendung oder in der Dokumentation zu Ihrer Anwendung, ob diese Anforderungen voll erfüllt werden.
 
###Erste Schritte

Anwendungen, die das SCIM-Profil wie oben beschrieben erfüllen, können über das App-Feature „Benutzerdefiniert“ im Azure AD-Anwendungskatalog mit Azure Active Directory verbunden werden. Nachdem Herstellen der Verbindung führt Azure AD alle 5 Minuten einen Synchronisierungsprozess aus, bei dem der SCIM-Endpunkt der Anwendung auf zugewiesene Benutzer und Gruppen abgefragt wird. Entsprechend den Details der Zuweisung werden dann Benutzer erstellt oder geändert.

**So verbinden Sie eine Anwendung, die SCIM unterstützt:**

1.  Starten Sie in einem Webbrowser die Azure-Verwaltungsportal unter https://manage.windowsazure.com.
2.  Navigieren Sie zu **Active Directory > Verzeichnis > [Ihr Verzeichnis] > Applications**, und wählen Sie **Hinzufügen > Hinzufügen einer Anwendung aus dem Katalog**.
3.  Wählen Sie die **benutzerdefinierte** Links auf die Registerkarte, geben Sie einen Namen für Ihre Anwendung, und klicken Sie auf das Häkchensymbol, um ein app-Objekt zu erstellen.

![][2]

4.  Wählen Sie in der resultierenden Seite das zweite **Bereitstellung konfigurieren** Schaltfläche.
5.  Geben Sie im Dialogfeld die URL des SCIM-Endpunkts der Anwendung ein.  
6.  Klicken Sie auf **Weiter**, und klicken Sie auf der **Test starten** Schaltfläche Azure Active Directory versucht, mit dem SCIM-Endpunkt herzustellen. Wenn die Versuche fehlschlagen, werden Diagnoseinformationen angezeigt.  
7.  Wenn Sie versucht, die Verbindung mit der Anwendung erfolgreich, klicken Sie dann auf **Weiter** auf die übrigen Bildschirme und auf **abgeschlossen** um das Dialogfeld zu schließen.
8.  Wählen Sie in der resultierenden Seite das dritte **Konten zuweisen** Schaltfläche. Weisen Sie im angezeigten Abschnitt „Benutzer und Gruppen“ die Benutzer und Gruppen zu, die Sie für die Anwendung bereitstellen möchten.
9.  Nachdem der Benutzer und Gruppen zugewiesen sind, klicken Sie auf die **konfigurieren** Registerkarte im oberen Bereich des Bildschirms.
10. Klicken Sie unter **Kontobereitstellung**, vergewissern Sie sich, dass der Status auf festgelegt ist. 
11. Unter **Tools**, klicken Sie auf **Neustart Bereitstellung** zum Starten des Bereitstellungsprozesses.

Beachten Sie, dass fünf bis zehn Minuten verstreichen dürfen, bevor der Bereitstellungsprozess damit beginnt, die Anforderungen an den SCIM-Endpunkt zu senden.  Eine Zusammenfassung der Verbindungsversuche wird auf der Registerkarte „Dashboard“ der Anwendung bereitgestellt, und Sie können einen Bericht mit den Bereitstellungsaktivitäten und alle Bereitstellungsfehler über die Registerkarte „Berichte“ des Verzeichnisses herunterladen.

##Erstellen einer eigenen Bereitstellungslösung für beliebige Anwendungen

Wenn Sie einen SCIM-Webdienst mit Schnittstelle zu Azure Active Directory erstellen, können Sie das einmalige Anmelden und die automatische Benutzerbereitstellung für nahezu alle Anwendungen ermöglichen, die über eine REST- oder SOAP-API zur Benutzerbereitstellung verfügen.

So funktioniert es:

1.  Azure AD bietet eine gemeinsame Sprache Infrastrukturbibliothek namens [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Systemintegratoren und Entwickler können diese Bibliothek verwenden, um einen SCIM-basierten Webdienstendpunkt zu erstellen und bereitzustellen, über den Azure AD mit dem Identitätsspeicher einer beliebigen Anwendung verbunden werden kann.
2.  Zuordnungen werden in den Webdienst implementiert, um das standardisierte Benutzerschema dem erforderlichen Benutzerschema und Protokoll für die Anwendung zuzuordnen.
3.  Die Endpunkt-URL wird in Azure AD als Teil einer benutzerdefinierten Anwendung im Anwendungskatalog registriert.
4.  Benutzer und Gruppen werden dieser Anwendung in Azure AD zugewiesen. Bei der Zuweisung werden sie in eine Warteschlange eingereiht, um mit der Zielanwendung synchronisiert zu werden. Der Synchronisierungsvorgang für die Warteschlange wird alle fünf Minuten ausgeführt.

###Codebeispiele

Um dieses Prozesses einfacher, einen Satz von [Codebeispiele](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) werden bereitgestellt, die einen SCIM Webdienst-Endpunkt zu erstellen, und zeigen Sie die automatische Bereitstellung. In einem Beispiel wird von einem Anbieter eine Datei mit Zeilen kommagetrennter Werte verwendet, die für Benutzer und Gruppen stehen.  Im anderen Beispiel wird ein Anbieter verwendet, der auf dem Amazon Web Services-Dienst für Identitäts- und Zugriffsverwaltung basiert.  

**Voraussetzungen**

* Visual Studio 2013 oder höher
* [Azure SDK für .NET](https://azure.microsoft.com/downloads/)
* Windows-Computer, der die Verwendung von ASP.NET Framework 4.5 als SCIM-Endpunkt unterstützt. Auf diesen Computer muss aus der Cloud zugegriffen werden können.
* [Azure-Abonnement mit Testversion oder lizenzierter Version von Azure AD Premium](https://azure.microsoft.com/services/active-directory/)
* Für dieses Beispiel Amazon AWS müssen Bibliotheken aus der [AWS-Toolkit für Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html). Weitere Details finden Sie in der INFODATEI des Beispiels.

###Erste Schritte

Die einfachste Möglichkeit zum Implementieren eines SCIM-Endpunkts, der Bereitstellungsanforderungen von Azure AD akzeptiert, ist das Erstellen und Bereitstellen des Codebeispiels, bei dem die bereitgestellten Benutzer in eine Datei mit kommagetrennten Werten (CSV) ausgegeben werden.

**So erstellen Sie einen SCIM-Beispielendpunkt**

1.  Herunterladen des Pakets am Beispiel [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2.  Entpacken Sie das Paket, und platzieren Sie es auf Ihrem Windows-Computer an einem Ort wie z. B. C:\AzureAD-BYOA-Provisioning-Samples\.
3.  Starten Sie in diesem Ordner die FileProvisioningAgent-Projektmappe in Visual Studio.
4.  Wählen Sie **Tools > Library Package Manager > Paket-Manager-Konsole**, und führen Sie die nachfolgenden Befehle für das FileProvisioningAgent-Projekt der Projektmappe Verweise auflösen:

    Install-Package Microsoft.SystemForCrossDomainIdentityManagement
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    Install-Package "Microsoft.owin.Diagnostics"
    Install-Package Microsoft.Owin.Host.SystemWeb

5.  Erstellen Sie das FileProvisioningAgent-Projekt.
6.  Starten Sie die Befehlszeilen-Anwendung in Windows (als Administrator), und verwenden Sie die **cd** Befehl aus, um das Verzeichnis zu ändern, die **\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug** Ordner.
7.  Führen Sie den Befehl unter < Ip-Adresse > mit dem Namen IP-Adresse oder Domäne des Windows-Computers ersetzen.

    FileAgnt.exe http://<ip-address>:9000 TargetFile.csv

8.  In Windows unter **Windows-Einstellungen > Netzwerk und Internet-Einstellungen**, wählen die **Windows-Firewall > Erweiterte Einstellungen**, und erstellen Sie eine **Eingehende Regel** mit der eingehende Zugriff auf Port 9000.
9.  Falls sich der Windows-Computer hinter einem Router befindet, muss der Router konfiguriert werden, um die Netzwerkadressübersetzung zwischen seinem Port 9000, der gegenüber dem Internet offen ist, und Port 9000 auf dem Windows-Computer durchzuführen. Dies ist erforderlich, damit Azure AD auf diesen Endpunkt in der Cloud zugreifen kann.


**So registrieren Sie den SCIM-Beispielendpunkt in Azure AD**

1.  Starten Sie in einem Webbrowser die Azure-Verwaltungsportal unter https://manage.windowsazure.com.
2.  Navigieren Sie zu **Active Directory > Verzeichnis > [Ihr Verzeichnis] > Applications**, und wählen Sie **Hinzufügen > Hinzufügen einer Anwendung aus dem Katalog**.
3.  Wählen Sie die **benutzerdefinierte** Links auf die Registerkarte, geben Sie einen Namen wie "SCIM-Test-App", und klicken Sie auf das Häkchensymbol, um ein app-Objekt zu erstellen. Beachten Sie, dass mit dem erstellten Anwendungsobjekt die Ziel-App dargestellt werden soll, für die Sie das einmalige Anmelden bereitstellen und implementieren möchten, und nicht nur der SCIM-Endpunkt.

![][2]

4.  Wählen Sie in der resultierenden Seite das zweite **Bereitstellung konfigurieren** Schaltfläche.
5.  Geben Sie im Dialogfeld die über das Internet zugängliche URL und den Port Ihres SCIM-Endpunkts ein. Wäre dies etwas wie Http://testmachine.contoso.com:9000 oder http://<ip-address>:9000/, wobei < Ip-Adresse > im Internet ist-IP verfügbar-Adresse.  
6.  Klicken Sie auf **Weiter**, und klicken Sie auf der **Test starten** Schaltfläche Azure Active Directory versucht, mit dem SCIM-Endpunkt herzustellen. Wenn die Versuche fehlschlagen, werden Diagnoseinformationen angezeigt.  
7.  Wenn versucht, die Verbindung zu Ihrem Webdienst erfolgreich ausgeführt werden, und klicken Sie dann **Weiter** auf die übrigen Bildschirme und auf **abgeschlossen** um das Dialogfeld zu schließen.
8.  Wählen Sie in der resultierenden Seite das dritte **Konten zuweisen** Schaltfläche. Weisen Sie im angezeigten Abschnitt „Benutzer und Gruppen“ die Benutzer und Gruppen zu, die Sie für die Anwendung bereitstellen möchten.
9.  Nachdem der Benutzer und Gruppen zugewiesen sind, klicken Sie auf die **konfigurieren** Registerkarte im oberen Bereich des Bildschirms.
10. Klicken Sie unter **Kontobereitstellung**, vergewissern Sie sich, dass der Status auf festgelegt ist. 
11. Unter **Tools**, klicken Sie auf **Neustart Bereitstellung** zum Starten des Bereitstellungsprozesses.

Beachten Sie, dass fünf bis zehn Minuten verstreichen dürfen, bevor der Bereitstellungsprozess damit beginnt, die Anforderungen an den SCIM-Endpunkt zu senden.  Eine Zusammenfassung der Verbindungsversuche wird auf der Registerkarte „Dashboard“ der Anwendung bereitgestellt, und Sie können einen Bericht mit den Bereitstellungsaktivitäten und alle Bereitstellungsfehler über die Registerkarte „Berichte“ des Verzeichnisses herunterladen.

Der letzte Schritt bei der Überprüfung des Beispiels besteht darin, die Datei „TargetFile.csv“ im Ordner „\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug“ auf Ihrem Windows-Computer zu öffnen. Wenn der Bereitstellungsprozess ausgeführt wird, werden in dieser Datei die Details aller zugewiesenen und bereitgestellten Benutzer und Gruppen angezeigt.

###Entwicklungsbibliotheken

Um Ihren eigenen Webdienst zu entwickeln, der mit der SCIM-Spezifikation übereinstimmt, sollten Sie sich zuerst mit den folgenden Bibliotheken von Microsoft vertraut machen, mit denen der Entwicklungsprozess beschleunigt wird: 

**1:**  common Language Infrastructure-Bibliotheken für die Verwendung mit Sprachen auf Grundlage dieser Infrastruktur, wie z. B. c# angeboten werden.  Eine dieser Bibliotheken [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), deklariert eine Schnittstelle, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, in der folgenden Abbildung dargestellt.  Ein Entwickler, der die Bibliotheken verwendet, würde diese Schnittstelle mit einer Klasse implementieren, die generisch als „Anbieter“ bezeichnet werden kann.  Mit den Bibliotheken kann der Entwickler leicht einen Webdienst bereitstellen, der die Vorgaben der SCIM-Spezifikation erfüllt – entweder gehostet in Internetinformationsdienste oder einer beliebigen ausführbaren Common Language Infrastructure-Assembly.  Anforderungen an den Webdienst werden in Aufrufe der Anbietermethoden übersetzt, die vom Entwickler für den Betrieb über einen Identitätsspeicher programmiert werden.    

![][3]

**2:**  [Express Routenhandler](http://expressjs.com/guide/routing.html) werden zum Analysieren von node.js Anforderungsobjekte darstellt (gemäß der Spezifikation SCIM) Aufrufe zur Verfügung gestellt, node.js-Webdienst.     

###Erstellen eines benutzerdefinierten SCIM-Endpunkts

Mit den oben beschriebenen Bibliotheken können Entwickler ihre Dienste in einer ausführbaren Common Language Infrastructure-Assembly oder in Internetinformationsdienste hosten.  Hier ist Beispielcode für das Hosten eines Diensts in eine ausführbare Assembly, unter der Adresse http://localhost: 9000: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  
    
    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Es ist wichtig zu beachten, dass dieser Dienst über ein HTTP-Adress- und -Serverauthentifizierungszertifikat mit einer der folgenden Stammzertifizierungsstellen verfügen muss: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Ein Serverauthentifizierungszertifikat kann wie folgt an einen Port auf einem Windows-Host gebunden werden, indem das Netzwerk-Shell-Hilfsprogramm verwendet wird: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  
 
Hier ist der für das certhash-Argument angegebene Wert der Fingerabdruck des Zertifikats, und der für das appid-Argument bereitgestellte Wert ist ein beliebiger Globally Unique Identifier (GUID).  

Um den Dienst in Internetinformationsdienste zu hosten, würde ein Entwickler eine Common Language Infrastructure-Codebibliothekassembly mit einer Klasse mit dem Namen „Startup“ im Standardnamespace der Assembly erstellen.  Dies ist ein Beispiel für eine Klasse dieser Art: 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

###Behandeln der Endpunktauthentifizierung

Anforderungen aus Azure Active Directory enthalten ein OAuth 2.0-Bearertoken.   Alle Dienste, die die Anforderung empfangen, sollten den Aussteller als Azure Active Directory im Namen des erwarteten Azure Active Directory-Mandanten authentifizieren, was den Zugriff auf den Graph-Webdienst von Azure Active Directory betrifft.  Im Token wird der Aussteller mit einem iss-Anspruch angegeben, z. B. „iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/“.  In diesem Beispiel ist die Basisadresse der Anspruchswert https://sts.windows.net, Azure Active Directory als der Aussteller identifiziert, während die relative Adresssegment, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, ist ein eindeutiger Bezeichner für den Azure Active Directory-Mandanten dar, das Token ausgestellt wurde.  Wenn das Token zum Zugreifen auf den Graph-Webdienst von Azure Active Directory ausgestellt wurde, sollte sich der Bezeichner dieses Diensts (00000002-0000-0000-c000-000000000000) im Wert des aud-Anspruchs für das Token befinden.  

Entwickler, die die von Microsoft bereitgestellten Common Language Infrastructure-Bibliotheken zum Erstellen eines SCIM-Diensts verwenden, können Anforderungen von Azure Active Directory authentifizieren, indem sie das Microsoft.Owin.Security.ActiveDirectory-Paket verwenden. Hierzu sind folgende Schritte erforderlich: 

**1:**  in einem Anbieter implementieren Sie die Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior-Eigenschaft, indem Sie ihm eine Methode aufgerufen werden, wenn der Dienst gestartet wird zurückgegeben: 

    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }

**2:**  Fügen Sie den folgenden Code an diese Methode jede Anforderung an den Dienst-Endpunkte, die mit einem von Azure Active Directory für einen angegebenen Mandanten, für den Zugriff auf Azure Active Directory Graph-Webdienst ausgegebenen Token authentifiziert haben: 

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }

##Benutzer- und Gruppenschema

Azure Active Directory kann für SCIM-Webdienste zwei Arten von Ressourcen bereitstellen.  Diese Arten von Ressourcen sind Benutzer und Gruppen.  

Benutzerressourcen werden anhand der Schemabezeichner Urn: Ietf:params:scim:schemas:extension:enterprise:2.0:User, die in dieser Protokollspezifikation enthalten ist: http://tools.ietf.org/html/draft-ietf-scim-core-schema.  Die Standardzuordnung der Attribute von Benutzern in Azure Active Directory zu den Attributen von urn:ietf:params:scim:schemas:extension:enterprise:2.0:User-Ressourcen ist unten in Tabelle 1 angegeben.  

Ressourcen werden durch die Schema-ID identifiziert http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  Tabelle 2 unten zeigt die Standard-Zuordnung der Attribute der Gruppen in Azure Active Directory für die Attribute von http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group Ressourcen.  

###Tabelle 1: Standardzuordnung von Benutzerattributen

| Azure Active Directory-Benutzer | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User |
| ------------- | ------------- |
| IsSoftDeleted | aktiv |
| displayName | displayName |
| Facsimile-TelephoneNumber | phoneNumbers[type eq "fax"].value |
| givenName | name.givenName |
| jobTitle | title |
| mail | emails[type eq "work"].value |
| mailNickname | externalId |
| manager | manager |
| mobile | phoneNumbers[type eq "mobile"].value |
| objectId | ID |
| postalCode | addresses[type eq "work"].postalCode |
| proxy-Addresses | emails[type eq "other"].Value |
| physical-Delivery-OfficeName | addresses[type eq "other"].Formatted |
| streetAddress | addresses[type eq "work"].streetAddress |
| surname | name.familyName |
| telephone-Number | phoneNumbers[type eq "work"].value |
| user-PrincipalName | userName |


###Tabelle 2: Standardzuordnung von Gruppenattributen

| Azure Active Directory-Gruppe | http://Schemas.Microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| ------------- | ------------- |
| displayName | externalId |
| mail | emails[type eq "work"].value |
| mailNickname | displayName |
| members | members |
| objectId | ID |
| proxyAddresses | emails[type eq "other"].Value |


##Durchführen und Aufheben der Benutzerbereitstellung

In der Abbildung unten sind die Nachrichten dargestellt, die von Azure Active Directory an einen SCIM-Dienst gesendet werden, um den Lebenszyklus eines Benutzers in einem anderen Identitätsspeicher zu verwalten.  In der Abbildung ist auch zu sehen, wie ein SCIM-Dienst, der mit den Common Language Infrastructure-Bibliotheken von Microsoft zum Erstellen dieser Dienste implementiert wird, diese Anforderungen in Aufrufe der Methoden eines Anbieters übersetzt.  

![][4]
*Abbildung: Benutzer und Aufheben der Bereitstellung Sequenz*

**1:**  Azure Active Directory fragt den Dienst für einen Benutzer mit dem ExternalId Attributwert entspricht dem Wert des MailNickname-Attribut eines Benutzers in Azure Active Directory.  Die Abfrage wird als Hypertext Transfer Protocol-Anforderung wie in diesem Beispiel ausgedrückt, wobei „jyoung“ ein Beispiel für ein mailNickname-Element eines Benutzers in Azure Active Directory ist: 

    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...

Wenn der Dienst mit den Common Language Infrastructure-Bibliotheken von Microsoft zum Implementieren von SCIM-Diensten erstellt wurde, wird die Anforderung in einen Aufruf der Query-Methode des Dienstanbieters übersetzt.  Dies ist die Signatur dieser Methode: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);

Dies ist die Definition der Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters-Schnittstelle: 

    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }
    
    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }

Im vorherigen Beispiel für eine Abfrage für einen Benutzer mit einem bestimmten Wert für das externalId-Attribut lauten die Werte der Argumente, die an die Query-Methode übergeben werden, wie folgt: 

* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
* correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

**2:**  Wenn die Antwort auf eine Abfrage an den Dienst für einen Benutzer mit dem ExternalId Attributwert entspricht dem Wert des MailNickname-Attribut eines Benutzers in Azure Active Directory keine Benutzer zurückgibt, Azure Active Directory fordert an, dass einen Benutzer in Azure Active Directory für die Bereitstellung des Diensts.  Dies ist ein Beispiel für eine Anforderung dieser Art: 

    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}

Mit den Common Language Infrastructure-Bibliotheken von Microsoft zum Implementieren von SCIM-Diensten wird diese Anforderung in einen Aufruf der Create-Methode des Dienstanbieters übersetzt.  Die Create-Methode hat die folgende Signatur: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);

In diesem Fall einer Anforderung zum Bereitstellen eines Benutzers ist der Wert des Ressourcenarguments eine Instanz von Microsoft.SystemForCrossDomainIdentityManagement. Die Core2EnterpriseUser-Klasse, die in der Microsoft.SystemForCrossDomainIdentityManagement.Schemas-Bibliothek definiert ist.  Wenn die Anforderung zum Bereitstellen des Benutzers erfolgreich ist, wird von der Implementierung der Methode erwartet, dass eine Instanz von Microsoft.SystemForCrossDomainIdentityManagement zurückgegeben wird. Die Core2EnterpriseUser-Klasse, für die der Wert der Identifier-Eigenschaft auf den eindeutigen Bezeichner des neu bereitgestellten Benutzers festgelegt ist.  

**3:**  zum Aktualisieren eines Benutzers in einem Identitätsspeicher vorgelagert durch ein SCIM vorhanden ist, wird fortgesetzt, Azure Active Directory durch den aktuellen Status des Benutzers aus dem Dienst anfordern, mit der Anforderung, wie diese: 

    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...

Für einen Dienst, der mit den Common Language Infrastructure-Bibliotheken von Microsoft zum Implementieren von SCIM-Diensten erstellt wurde, wird die Anforderung in einen Aufruf der Retrieve-Methode des Dienstanbieters übersetzt.  Dies ist die Signatur der Retrieve-Methode: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);
    
    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }

Im Fall des vorherigen Beispiels für eine Anforderung zum Abrufen des aktuellen Status eines Benutzers lauten die Werte der Eigenschaften des Objekts, das als Wert des parameters-Arguments angegeben wird, wie folgt: 

* Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

**4:**  Wenn ein Verweisattribut aktualisiert werden, wird Azure Active Directory den Dienst fragt, um zu bestimmen, ob der aktuelle Wert des Attributs Verweis im Identitätsspeicher vorgelagert vom Dienst bereits den Wert dieses Attributs in Azure Active Directory übereinstimmt.  Bei Benutzern ist das einzige Attribut, für das der aktuelle Wert auf diese Weise abgefragt wird, das manager-Attribut.  Dies ist ein Beispiel für eine Anforderung, mit der ermittelt wird, ob das manager-Attribut eines bestimmten Benutzerobjekts derzeit über einen bestimmten Wert verfügt: 

    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...

Der Wert des Attributabfrageparameters (id) gibt Folgendes an: Wenn ein Benutzerobjekt vorhanden ist, das die Anforderungen des als Wert des Filterabfrageparameters angegebenen Ausdrucks erfüllt, wird vom Dienst erwartet, mit einer urn:ietf:params:scim:schemas:core:2.0:User- oder urn:ietf:params:scim:schemas:extension:enterprise:2.0:User-Ressource zu antworten (nur mit dem Wert des id-Attributs dieser Ressource).  Der Wert des id-Attributs ist dem Anforderer natürlich bekannt, da er im Wert des Filterabfrageparameters enthalten ist. Der Zweck des Nachfragens besteht darin, eine Minimaldarstellung einer Ressource anzufordern, die die Anforderungen des Filterausdrucks erfüllt und angibt, ob solch ein Objekt vorhanden ist.   

Wenn der Dienst mit den Common Language Infrastructure-Bibliotheken von Microsoft zum Implementieren von SCIM-Diensten erstellt wurde, wird die Anforderung in einen Aufruf der Query-Methode des Dienstanbieters übersetzt.  Der Wert der Eigenschaften des Objekts, das als Wert des parameters-Arguments angegeben wird, lautet wie folgt: 

* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "id"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "id"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

Hier kann der Wert von Index x „0“ und der Wert von Index y „1“ lauten, oder der Wert von x kann „1“ und der Wert von y „0“ lauten. Dies hängt von der Reihenfolge bei den Ausdrücken des Filterabfrageparameters ab.   

**5:**  hier ist ein Beispiel für eine Anforderung von Azure Active Directory in einem SCIM-Dienst Aktualisieren eines Benutzers: 

    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}

Mit den Common Language Infrastructure-Bibliotheken von Microsoft zum Implementieren von SCIM-Diensten wird die Anforderung in einen Aufruf der Update-Methode des Dienstanbieters übersetzt.  Dies ist die Signatur dieser Methode: 

    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }
    
    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }
      
      public string Value
      { get; set; }
    }



Im vorherigen Beispiel für eine Anforderung zum Aktualisieren eines Benutzers verfügt das Objekt, das als Wert des patch-Arguments angegeben wird, über diese Eigenschaftswerte: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest as PatchRequest2).Operations.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
* (PatchRequest als PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Referenz: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

**6:**  um einen Benutzer aus einem Identitätsspeicher, die von einem Dienst SCIM vorgelagert Portalschnittstelle, Azure Active Directory sendet eine Anforderung wie diese: 

    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    
Wenn der Dienst mit den Common Language Infrastructure-Bibliotheken von Microsoft zum Implementieren von SCIM-Diensten erstellt wurde, wird die Anforderung in einen Aufruf der Delete-Methode des Dienstanbieters übersetzt.   Diese Methode verfügt über folgende Signatur: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
 
Im vorherigen Beispiel für eine Anforderung zum Aufheben der Bereitstellung eines Benutzers verfügt das Objekt, das als Wert des resourceIdentifier-Arguments angegeben wird, über diese Eigenschaftswerte: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

##Durchführen und Aufheben der Gruppenbereitstellung

In der Abbildung unten sind die Nachrichten dargestellt, die von Azure Active Directory an einen SCIM-Dienst gesendet werden, um den Lebenszyklus einer Gruppe in einem anderen Identitätsspeicher zu verwalten.  Diese Nachrichten unterscheiden sich von den Nachrichten für Benutzer auf drei Arten: 

* Das Schema einer Ressource der Gruppe wird als http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group identifiziert werden.  
* Für Anforderungen zum Abrufen von Gruppen wird vorgegeben, dass das members-Attribut aus allen Ressourcen ausgeschlossen wird, die als Antwort auf die Anforderung bereitgestellt werden.  
* Bei Anforderungen für die Ermittlung, ob ein Referenzattribut einen bestimmten Wert hat, handelt es sich um Anforderungen zum members-Attribut.  

![][5]
*Abbildung: Gruppieren Sie und Aufheben der Bereitstellung Sequenz*

    
<!--Image references-->
[1]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG


