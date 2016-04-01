<properties
    pageTitle="Verwenden von Access Control (.NET) | Microsoft Azure"
    description="Erfahren Sie, wie Sie Access Control Service (ACS) in Ihrer Azure-Anwendung zur Authentifizierung von Benutzern beim Zugriff auf eine Webanwendung verwenden können."
    services="active-directory"
    documentationCenter=".net"
    authors="msmbaldwin"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="12/05/2015" 
    ms.author="mbaldwin"/>




# Authentifizieren von Webbenutzern mit der Azure Active Directory-Zugriffssteuerung


Diese Anleitung zeigt, wie Sie mit der Azure Active Directory-Zugriffssteuerung (auch Access Control Service oder ACS genannt) Benutzer von Identitätsanbietern wie Microsoft, Google, Yahoo und Facebook authentifizieren, wenn sie versuchen, auf eine Webanwendung zuzugreifen.


## Was ist der ACS?

Die meisten Entwickler sind keine Identitätsexperten und auch nicht bereit, ihre Zeit für die Entwicklung von Authentifizierungs- und Autorisierungsmechanismen für ihre Anwendungen und Dienste zu verschwenden. ACS ist ein Azure-Dienst, der Ihnen einfache Möglichkeiten bietet, Benutzer für den Zugriff auf Ihre Webanwendungen und -dienste zu authentifizieren, ohne eine komplexe Authentifizierungslogik in Ihren Code aufnehmen zu müssen.

Folgende Funktionen stehen im ACS zur Verfügung:

-   Integration mit Windows Identity Foundation (WIF).
-   Unterstützung beliebter Anbieter von Webidentitäten (IPs) wie beispielsweise Microsoft-Konto (vormals Windows Live ID), Google, Yahoo und Facebook.
-   Unterstützung von Active Directory Federation Services (AD FS) 2.0.
-   Ein Open Data Protocol (OData)-basierten Verwaltungsdienst, bereitstellt
    Programmgesteuerter Zugriff auf ACS-Einstellungen.
-   Ein Verwaltungsportal, können administrativen Zugriff auf die ACS
    löschen.

Weitere Informationen zu ACS finden Sie unter [Access Control Service 2.0][].

## Konzepte

ACS basiert auf den Prinzipien der anspruchsbasierten Identität -- einem konsistenten Ansatz zur Erstellung von Authentifizierungsmechanismen für Anwendungen, die entweder lokal oder in der Cloud ausgeführt werden. Die anspruchsbasierte Identität ist eine von Anwendungen und Diensten häufig genutzte Möglichkeit, sich benötigte Identitätsinformationen über Benutzer in ihren Unternehmen, in anderen Unternehmen und auf dem Internet zu beschaffen.

Damit Sie die in dieser Anleitung gestellten Aufgaben durchführen können, müssen Sie folgende Begriffe und Konzepte kennen und wissen, wie sie in dieser Anleitung verwendet werden:


**Client** -ein Browser, der versucht, auf Ihre Anwendung zugreifen.

**Anwendung vertrauenden Seite (RP)** -Ihrer Web-app. Eine RP-Anwendung ist eine Website oder ein Dienst, die/der die Authentifizierung an eine externe Stelle auslagert. Im Identitätskontext sagt man, dass die RP der Authentifizierungsstelle vertraut. In dieser Anleitung wird erklärt, wie Sie eine Anwendung so konfigurieren, dass sie dem ACS vertraut.

**Token** -ein Benutzer erlangt Zugriff auf eine RP-Anwendung, indem er ein gültiges Token, die von einer Zertifizierungsstelle ausgestellt wurde, die RP-Anwendung vertraut. Eine Sammlung von Sicherheitsdaten, die bei Authentifizierung eines Clients ausgegeben werden. Sie enthält eine Reihe von Ansprüchen, bei denen es sich um Attribute des authentifizierten Benutzers handelt, beispielsweise den Namen oder das Alter eines Benutzers oder einen Bezeichner einer Benutzerrolle. Ein Token ist digital signiert, sodass der Aussteller identifiziert und der Inhalt nicht geändert werden kann.

**(Identity Provider, IP)** - eine Stelle, die Benutzeridentitäten authentifiziert und Sicherheitstoken ausgibt, wie z. B. Microsoft-Konto (Windows Live ID), Facebook, Google, Twitter und Active Directory. Wenn zwischen ACS und einem IP ein Vertrauensverhältnis konfiguriert worden ist, akzeptiert und validiert ACS die von diesem IP vergebenen Token. Da ACS mehreren IPs gleichzeitig, vertrauen kann, wenn Ihre Anwendung ACS vertraut, können Sie Ihre Anwendung bietet Benutzern die Möglichkeit, die von einem der IPs authentifiziert werden, denen ACS in Ihrem Auftrag vertraut.

**Verbundanbieter (FP)** -Identitätsanbieter (IPs) kennen die Benutzer, authentifizieren Benutzer anhand ihrer Anmeldeinformationen und geben Ansprüche zu Benutzern. Ein Verbundanbieter (FP) ist eine andere Art von Authentifizierungsstelle. Ein FP authentifiziert Benutzer nicht direkt, sondern fungiert als Authentifizierungsbroker. Er übernimmt die Funktion eines Vermittlers zwischen einer RP-Anwendung und einem oder mehreren IPs. ACS ist ein Verbundanbieter (FP).

**ACS-Regelmodul** -Anspruchstransformationsregeln konvertieren die Ansprüche in Tokens vertrauenswürdiger IPS, sodass sie von einer RP verwendet werden können. ACS verfügt unter anderem ein Regelmodul, das die anspruchstransformationsregeln anwendet, die Sie für Ihre RP festlegen.

**Access Control-Namespace** -stellt einen eindeutigen Bereich für die Adressierung von ACS-Ressourcen innerhalb Ihrer Anwendung. Der Namespace enthält Ihre Einstellungen, beispielsweise die IPs, denen Sie vertrauen, die RP-Anwendungen, die Sie bereitstellen wollen, und die Regeln, die Sie auf eingehende Tokens anwenden. Außerdem zeigt er die Endpunkte, über die die Anwendung und der Entwickler mit ACS kommunizieren.

Die folgende Abbildung zeigt, wie die ACS-Authentifizierung bei einer Webanwendung abläuft:

![][0]

1.  Der Client (in diesem Fall ein Browser) fordert von der RP eine Seite an.
2.  Da die Anforderung noch nicht authentifiziert ist, verweist die RP den
    die Benutzer an die Stelle, die sie vertraut, also an den ACS. Der ACS übermittelt
    der Benutzer durch die Auswahl von IP-Adressen, die für diese RP definiert wurden. Die
    Benutzer wählt einen passenden IP aus.
3.  Der Client wechselt zur Seite "Authentifizierung" die IP-Adresse, und fordert die
    Benutzer anmelden.
4.  Wenn der Client (z. B. die Identität authentifiziert wird
    Anmeldeinformationen eingegeben wurden), die IP-Adresse ein Sicherheitstoken ausstellt.
5.  Nach der Ausgabe des Sicherheitstokens weist der IP den Client an, dieses Token an ACS zu senden.
6.  ACS validiert das Token ausgestellt von Eingaben die IP-Adresse der
    Identitätsansprüche in diesem Token in der ACS-Regelmodul berechnet
    die Ausgabe Identität Ansprüche und gibt einem neues Sicherheitstoken, die
    Diese Ansprüche enthält.
7.  ACS weist den Client an, das von ACS ausgegebene Token an die RP zu senden. Die RP validiert die Signatur auf dem Sicherheitstoken, extrahiert die für die Geschäftslogik der Anwendung bestimmten Ansprüche und gibt die ursprünglich angeforderte Seite zurück.

## Voraussetzungen


Damit Sie die in dieser Anleitung gestellten Aufgaben ausführen können, brauchen Sie Folgendes:

-   Azure-Abonnement
-   Microsoft Visual Studio 2012
-   Identitäts- und Zugriffs-Tool für Visual Studio 2012 (download Siehe [Identitäts- und Zugriffs-Tool][])


## Erstellen eines Access Control-Namespace

Damit Sie den Active Directory-Zugriffssteuerungsdienst in Azure verwenden können, müssen Sie einen Access Control-Namespace anlegen. Der Namespace stellt einen eindeutigen Bereich für
Adressierung von ACS-Ressourcen innerhalb Ihrer Anwendung.

1.  Melden Sie sich bei der [Azure-Verwaltungsportal][] (https://manage.WindowsAzure.com).

2.  Klicken Sie auf **Active Directory**.  

    ![][1]

3.  Um einen neuen Access Control-Namespace zu erstellen, klicken Sie auf **Neu**. **App-Dienste** und **Access Control** ausgewählt werden. Klicken Sie auf **Schnellerfassung**.

    ![][2]

4.  Geben Sie einen Namen für den Namespace ein. Azure prüft, ob der Name eindeutig ist.

5.  Wählen Sie die Region aus, in der der Namespace verwendet wird. Verwenden Sie für eine optimale Leistung die Region, in dem Sie Ihre Anwendung bereitstellen möchten, und klicken Sie dann auf **Erstellen**.

Azure erstellt und aktiviert den Namespace.

## Erstellen einer ASP.NET MVC-Anwendung

In diesem Schritt erstellen Sie eine ASP.NET MVC-Anwendung. In nachfolgenden Schritten wird diese einfache Anwendung für Webformulare mit ACS integriert.

1.  Starten Sie Visual Studio 2012 oder Visual Studio Express für Web 2012 (Frühere Versionen von Visual Studio können mit diesem Lernprogramm nicht verwendet werden).
1.  Klicken Sie auf **Datei**, und klicken Sie dann auf **Neues Projekt**.
1.  Wählen Sie den Visual c# / Web-Vorlage, und wählen Sie dann **ASP.NET MVC 4 Web Application**.

    In dieser Anleitung arbeiten wir mit einer MVC-Anwendung. Sie können für diese Aufgabe allerdings eine beliebige Webanwendung verwenden.

    ![][3]

1. In **Namen**, Typ **MvcACS**, und klicken Sie dann auf **OK**.
1. Wählen Sie im nächsten Dialogfeld **Internet Application**, und klicken Sie dann auf **OK**.
1. Bearbeiten der *Views\Shared\_LoginPartial.cshtml* Datei, und Ersetzen Sie den Inhalt durch den folgenden Code:

        @if (Request.IsAuthenticated)
        {
            string name = "Null ID";
            if (!String.IsNullOrEmpty(User.Identity.Name))
            {
                name = User.Identity.Name;
            }
            <text>
            Hello, @Html.ActionLink(name, "Manage", "Account", routeValues: null, htmlAttributes: new { @class = "username", title = "Manage" })!
                    @using (Html.BeginForm("LogOff", "Account", FormMethod.Post, new { id = "logoutForm" }))
                    {
                        @Html.AntiForgeryToken()
                        <a href="javascript:document.getElementById('logoutForm').submit()">Log off</a>
                    }
            </text>
        }
        else
        {
            <ul>
                <li>@Html.ActionLink("Register", "Register", "Account", routeValues: null, htmlAttributes: new { id = "registerLink" })</li>
                <li>@Html.ActionLink("Log in", "Login", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
            </ul>
        }

Derzeit fügt ACS "User.Identity.Name" noch nicht ein, deshalb ist die obige Änderung notwendig.

1. Drücken Sie die Taste F5, um die Anwendung auszuführen. Die Standardanwendung ASP.NET MVC erscheint in Ihrem Webbrowser.

## Integrieren der Webanwendung mit ACS

In dieser Aufgabe werden Sie Ihre ASP.NET-Webanwendung mit ACS integrieren.

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste des Projekts MvcACS, und wählen Sie dann **Identitäts- und**.

    Wenn die **Identitäts- und** Option wird nicht angezeigt im Kontextmenü das Identitäts- und Zugriffs-Tool zu installieren. Weitere Informationen finden Sie unter [Identity and Access Tool].

    ![][4]

2.  Auf der **Anbieter** Registerkarte **Azure Access Control Service verwenden**.

    ![][44]

3.  Klicken Sie auf die **konfigurieren** Link.

    ![][444]

    Visual Studio fordert Informationen über den Access Control-Namespace an. Geben Sie den Namen des Namespace ein, den Sie zuvor angelegt haben ("Test" in dem Beispiel oben, aber der Name Ihres Namespace wird anders lauten). Wechseln Sie zurück zum Azure-Verwaltungsportal, um den symmetrischen Schlüssel zu beschaffen.

    ![][17]

4.  In der Azure-Verwaltungsportal auf den Access Control-Namespace, und klicken Sie dann auf **verwalten**.

    ![][8]

5.  Klicken Sie auf **Verwaltungsdienst** und klicken Sie dann auf **Verwaltungsclient**.

    ![][18]

6.  Klicken Sie auf **symmetrischen Schlüssel**, klicken Sie auf **Schlüssel anzeigen**, und kopieren Sie den Schlüsselwert. Klicken Sie dann auf **Abbrechen** auf die Seite Edit Management Client zu beenden, ohne Änderungen vorzunehmen.

    ![][19]

7.  Fügen Sie in Visual Studio den Schlüssel in der **Geben Sie den Management-Schlüssel für den Namespace** ein, und klicken Sie auf **Save Management Key**, und klicken Sie dann auf **OK**.

    ![][20]

    Visual Studio verwendet die Information über den Namespace, um die Verbindung zum ACS-Verwaltungsportal herzustellen und die Einstellungen für Ihren Namespace abzurufen, unter anderem Identitätsanbieter, Bereich und Rückgabe-URL.

8.  Wählen Sie **Windows Live ID** (Microsoft-Konto), und klicken Sie auf OK.

    ![][5]

## Testen der Integration mit ACS

In dieser Aufgabe wird erläutert, wie Sie die Integration Ihrer RP-Anwendung mit ACS testen.

-   Drücken Sie in Visual Studio die Taste F5, um die Anwendung auszuführen.

Wenn Ihre Anwendung mit ACS integriert ist und Sie "Windows Live ID" (Microsoft-Konto) ausgewählt haben, wird nicht die standardmäßige ASP.NET Web Forms-Anwendung geöffnet, sondern Ihr Browser auf die Anmeldeseite für Microsoft-Konten umgeleitet. Wenn Sie sich mit einem gültigen Benutzernamen und Kennwort anmelden, werden Sie zu der Anwendung MvcACS weitergeleitet.

![][6]

Glückwunsch! Sie haben ACS mit Ihrer ASP.NET-Webanwendung integriert. ACS authentifiziert Benutzer nun anhand der Anmeldeinformationen ihres Microsoft-Kontos.

## Anzeigen der von ACS gesendeten Ansprüche

In diesem Abschnitt werden wir die Anwendung ändern, sodass die von ACS gesendeten Ansprüche angezeigt werden.  Das Identitäts- und Zugriffstool hat eine Regelgruppe angelegt, die alle Ansprüche vom IP zu Ihrer Anwendung durchleitet.  Beachten Sie, dass unterschiedliche Anbieter unterschiedliche Ansprüche senden.

1. Öffnen Sie die *Controllers\HomeController. cs* Datei. Hinzufügen einer **mit** -Anweisung für **System.Threading**:

    using System.Threading;

1. In der HomeController-Klasse hinzufügen der *Ansprüche* Methode:

    Public ActionResult Claims()
    {
        ViewBag.Message = "Ihre Ansprüche Seite.";

        ViewBag.ClaimsIdentity = Thread.CurrentPrincipal.Identity;

        return View();
    }

1. Klicken Sie mit der rechten Maustaste auf die *Ansprüche* Methode, und wählen **Ansicht hinzufügen**.

![][66]

1. Klicken Sie auf **Hinzufügen**.

1. Ersetzen Sie den Inhalt der *Views\Home\Claims.cshtml* Datei durch den folgenden Code:

        @{
            ViewBag.Title = "Claims";
        }
        <hgroup class="title">
            <h1>@ViewBag.Title.</h1>
            <h2>@ViewBag.Message</h2>
        </hgroup>
        <h3>Values from Identity</h3>
        <table>
            <tr>
                <td>
                    IsAuthenticated:
                </td>
                <td>
                    @ViewBag.ClaimsIdentity.IsAuthenticated
                </td>
            </tr>
            <tr>
                <td>
                    Name:
                </td>
                <td>
                    @ViewBag.ClaimsIdentity.Name
                </td>
            </tr>
        </table>
        <h3>Claims from ClaimsIdentity</h3>
        <table>
            <tr>
                <th>
                    Claim Type
                </th>
                <th>
                    Claim Value
                </th>
            </tr>
                @foreach (System.Security.Claims.Claim claim in ViewBag.ClaimsIdentity.Claims ) {
            <tr>
                <td>
                    @claim.Type
                </td>
                <td>
                    @claim.Value
                </td>
            </tr>
        }
        </table>

1. Führen Sie die Anwendung, und navigieren Sie zu der *Ansprüche* Methode:

![][666]

Weitere Informationen zur Verwendung von Ansprüchen in Ihrer Anwendung finden Sie unter der [Windows Identity Foundation-Bibliothek](http://msdn.microsoft.com/library/hh377151.aspx).

## Anzeigen der Anwendung im ACS-Verwaltungsportal

Das Identitäts- und Zugriffstool in Visual Studio integriert Ihre Anwendung automatisch mit ACS.

Wenn Sie die Option "Use Azure Access Control" auswählen und dann Ihre Anwendung ausführen, fügt das Identitäts- und Zugriffstool Ihre Anwendung als vertrauende Seite (RP) hinzu, konfiguriert sie für die Verwendung der ausgewählten Identitätsanbieter, erzeugt die standardmäßigen Anspruchstransformationsregeln und wählt diese für die Anwendung aus.

Diese Konfigurationseinstellungen können Sie im ACS-Verwaltungsportal prüfen und ändern. Gehen Sie folgendermaßen vor, um die Änderungen im Portal zu überprüfen.

1.  Melden Sie sich bei Windows [Azure-Verwaltungsportal](http://manage.WindowsAzure.com).

2.  Klicken Sie auf **Active Directory**.

    ![][8]

3.  Wählen Sie einen Access Control-Namespace, und klicken Sie dann auf **verwalten**. Daraufhin öffnet sich das ACS-Verwaltungsportal.

    ![][9]


4.  Klicken Sie auf **Relying Party Applications**.

    Die neue MvcACS-Anwendung wird in der Liste der Anwendungen der vertrauenden Seite aufgeführt. Unter "Bereich" wird automatisch die Hauptseite der Anwendung angezeigt.

    ![][10]


5.  Klicken Sie auf **MvcACS**.

    Die Seite "Edit Relying Party Application" enthält Konfigurationseinstellungen für die MvcACS-Webanwendung. Wenn Sie die Einstellungen auf dieser Seite ändern und speichern, werden diese sofort in der Anwendung wirksam.

    ![][11]

6.  Führen Sie auf der Seite einen Bildlauf nach unten aus, damit die übrigen Konfigurationseinstellungen für die MvcACS-Anwendung, also unter anderem die Identitätsanbieter und die Anspruchstransformationsregeln, eingeblendet werden.

    ![][12]

Im nächsten Abschnitt werden wir mit den Funktionen des ACS-Verwaltungsportals eine Änderung in der Webanwendung vornehmen -- nur, um einmal zu zeigen, wie einfach das ist.

## Hinzufügen eines Identitätsanbieters

Auf dem ACS-Verwaltungsportal ändern wir nun die Authentifizierung der MvcACS-Anwendung. In diesem Beispiel werden wir Google als Identitätsanbieter für MvcACS hinzuzufügen.

1.  Klicken Sie auf **Identitätsanbieter** (im Navigationsmenü) und klicken Sie dann auf **Hinzufügen**.

    ![][13]

2.  Klicken Sie auf **Google** und klicken Sie dann auf **Weiter**. Das Kontrollkästchen der MvcACS-App ist standardmäßig aktiviert.

    ![][14]

3. Klicken Sie auf "Speichern".

    ![][15]


Das war's! Wenn Sie zu Visual Studio zurückzukehren, öffnen Sie das Projekt für die MvcACS-app, und klicken Sie auf **Identitäts- und**, listet das Tools sowohl Windows Live ID als auch Google-Identitätsanbieter.  

![][16]

Wenn Sie dann die Anwendung ausführen, sehen Sie, was sich verändert hat. Wenn eine Anwendung mehr als einen Identitätsanbieter unterstützt, wird der Browser des Benutzers zuerst an eine von ACS gehostete Seite geleitet, die den Benutzer dann auffordert, einen Identitätsanbieter auszuwählen.

![][7]

Sobald der Benutzer einen Identitätsanbieter ausgewählt hat, geht der Browser auf die Anmeldeseite dieses Anbieters.

## Nächste Schritte

Sie haben nun eine Webanwendung erstellt, die mit ACS integriert ist. Aber das ist erst der Anfang. Dieses Szenario können Sie noch weiter ausführen.

Sie können beispielsweise weitere Identitätsanbieter für diese vertrauende Seite hinzufügen oder Benutzern, die in Unternehmensverzeichnissen wie beispielsweise Active Directory-Domänendiensten registriert sind, die Anmeldung bei der Webanwendung gestatten.

Außerdem können Sie mit zusätzlichen Regeln in Ihrem Namespace festlegen, welche Ansprüche an eine Anwendung gesendet und in der Geschäftslogik der Anwendung verarbeitet werden.

ACS-Funktionalität genauer erforschen und mit anderen Szenarien experimentieren möchten, finden Sie unter [Access Control Service 2.0].



  [What is ACS?]: #what-is
  [Concepts]: #concepts
  [Prerequisites]: #pre
  [Create an ASP.NET MVC Application]: #create-web-app
  [Create an Access Control Namespace]: #create-namespace
  [Integrate your Web Application with ACS]: #Identity-Access
  [Test the Integration with ACS]: #Test-ACS
  [View the Application in the ACS Management Portal]: acs-portal
  [Add an Identity Provider]: #add-IP
  [What's Next]: #whats-next
  [vcsb]: #bkmk_viewClaims
  [vpp]: #bkmk_VP

  [Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
  [Identity and Access Tool]: http://go.microsoft.com/fwlink/?LinkID=245849
  [Azure Management Portal]: http://manage.WindowsAzure.com

  [0]: ./media/active-directory-dotnet-how-to-use-access-control/acs-01.png
  [1]: ./media/active-directory-dotnet-how-to-use-access-control/acsCreateNamespace.png
  [2]: ./media/active-directory-dotnet-how-to-use-access-control/acsQuickCreate.png
  [3]: ./media/active-directory-dotnet-how-to-use-access-control/rzMvc.png
  [4]: ./media/active-directory-dotnet-how-to-use-access-control/rzIA.png
[44]: ./media/active-directory-dotnet-how-to-use-access-control/rzPT.png
 [444]: ./media/active-directory-dotnet-how-to-use-access-control/rzC.png
  [5]: ./media/active-directory-dotnet-how-to-use-access-control/acsIdAndAccess1.png
  [6]: ./media/active-directory-dotnet-how-to-use-access-control/acsMSFTAcct.png
  [66]: ./media/active-directory-dotnet-how-to-use-access-control/rzAv.png
  [666]: ./media/active-directory-dotnet-how-to-use-access-control/rzCl.png
  [7]: ./media/active-directory-dotnet-how-to-use-access-control/acsSignIn.png
  [8]: ./media/active-directory-dotnet-how-to-use-access-control/acsClickManage.png
  [9]: ./media/active-directory-dotnet-how-to-use-access-control/acsACSPortal.png
  [10]: ./media/active-directory-dotnet-how-to-use-access-control/acsRPPage.png
  [11]: ./media/active-directory-dotnet-how-to-use-access-control/acsEdit-RP.png
  [12]: ./media/active-directory-dotnet-how-to-use-access-control/acsEdit-RP2.png
  [13]: ./media/active-directory-dotnet-how-to-use-access-control/acsAdd-Idp.png
  [14]: ./media/active-directory-dotnet-how-to-use-access-control/acsAdd-Google.png
  [15]: ./media/active-directory-dotnet-how-to-use-access-control/acsSave-Google.png
  [16]: ./media/active-directory-dotnet-how-to-use-access-control/acsIdAndA-after.png
  [17]: ./media/active-directory-dotnet-how-to-use-access-control/acsConfigAcsNamespace.png
  [18]: ./media/active-directory-dotnet-how-to-use-access-control/acsManagementService.png
  [19]: ./media/active-directory-dotnet-how-to-use-access-control/acsShowKey.png
  [20]: ./media/active-directory-dotnet-how-to-use-access-control/acsConfigAcsNamespace2.png


