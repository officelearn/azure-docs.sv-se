<properties
    pageTitle="Verwenden von Access Control (Java) | Microsoft Azure"
    description="Erfahren Sie, wie Sie Access Control mit Java in Azure entwickeln und verwenden."
    services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="jimbe" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/12/2015" 
    ms.author="robmcm" />

# Authentifizieren von Webbenutzern mit dem Azure Access Control Service über Eclipse

Diese Anleitung zeigt, wie der Azure Access Control Service (ACS) im Azure Toolkit für Eclipse verwendet wird. Weitere Informationen zum ACS finden Sie unter der [Nächste Schritte](#next_steps) Abschnitt.

> [AZURE.NOTE]
> Der Azure Access Control Service-Filter ist eine Technologievorschau für die Community. Als Vorabversion wird diese Software von Microsoft nicht offiziell unterstützt.

## Was ist der ACS?

Die meisten Entwickler sind keine Identitätsexperten und möchten in der Regel
keine Zeit mit dem Entwickeln von Authentifizierungs- und Autorisierungsmechanismen für
Ihre Anwendungen und Dienste verbringen. ACS ist ein Azure-Dienst, der
eine einfache Möglichkeit für die Authentifizierung von Benutzern bereitstellt, die auf Ihre Webanwendungen
und Dienste ohne komplexe Authentifizierungslogik im Code
zugreifen müssen.

Folgende Funktionen stehen im ACS zur Verfügung:

-   Integration mit Windows Identity Foundation (WIF).
-   Unterstützung beliebter Anbieter von Webidentitäten (IPs), wie beispielsweise Windows Live ID, Google, Yahoo und Facebook.
-   Unterstützung von Active Directory Federation Services (AD FS) 2.0.
-   Ein auf dem Open Data Protocol (OData) basierender Verwaltungsdienst, der den programmgesteuerten Zugriff auf die ACS-Einstellungen ermöglicht.
-   Ein Verwaltungsportal, über das Administratoren auf die ACS-Einstellungen zugreifen können.

Weitere Informationen zu ACS finden Sie unter [Access Control Service 2.0][].

## Konzepte

Azure ACS basiert auf den Prinzipien der anspruchsbasierten Identität -
ein einheitlicher Ansatz für die Erstellung von Authentifizierungsmechanismen für
Anwendungen, die lokal oder in der Cloud ausgeführt werden. Anspruchsbasierte Identität
bietet eine allgemeine Methode für Anwendungen und Dienste zum Abrufen der
Identitätsinformationen, die sie zu Benutzern in ihrer Organisation, in
anderen Organisationen und im Internet benötigen.

Damit Sie die in dieser Anleitung gestellten Aufgaben ausführen können, sollten Sie die folgenden
Konzepte verstehen:

**Client** -im Kontext dieses Leitfadens, ist dies ein Browser,
versucht, auf Ihre Webanwendung zuzugreifen.

**Anwendung vertrauenden Seite (RP)** -eine RP-Anwendung ist eine Website oder
ein Dienst, die bzw. der Authentifizierung an eine externe Stelle auslagert. Geben Sie in
Identitätskontext sagt man, dass die RP der Authentifizierungsstelle vertraut. In diesem Leitfaden wird
erklärt, wie Sie eine Anwendung so konfigurieren, dass sie dem ACS vertraut.

**Token** -ein Token ist eine Sammlung von Sicherheitsdaten, die in der Regel
bei der erfolgreichen Authentifizierung eines Benutzers ausgegeben wird. Es enthält eine Reihe von *Ansprüche*, Attribute des authentifizierten Benutzers. Ein Anspruch kann einen
Benutzernamen, eine ID für eine Rolle, der ein Benutzer angehört, das Alter eines Benutzers
und so weiter sein. Ein Token ist normalerweise digital signiert, d. h. es kann
immer wieder zum Aussteller zurückverfolgt werden, und sein Inhalt kann nicht geändert werden.
mit. Ein Benutzer erlangt Zugriff auf eine RP-Anwendung, indem er ein gültiges
Token bereitstellt, das von einer Zertifizierungsstelle ausgestellt wurde, der die RP-Anwendung vertraut.

**(Identity Provider, IP)** -ein IP ist eine Stelle,
Benutzer-Identitäten authentifiziert und Sicherheitstoken ausgibt. Die eigentliche Arbeit des Ausgebens
von Token wird über einen speziellen Dienst namens Sicherheitstokendienst
Service (STS) ausgeführt. Typische Beispiele für IPs sind Windows Live ID,
Facebook, geschäftliche Benutzerrepositorys (z. B. Active Directory) und so weiter.
Wenn ACS konfiguriert wurde, um einem IP zu vertrauen, akzeptiert und
überprüft das System Token, die von diesem IP ausgegeben wurden. ACS kann mehreren IPs gleichzeitig vertrauen,
was bedeutet, dass, wenn Ihre Anwendung ACS vertraut, Sie sofort
Ihre Anwendung allen authentifizierten Benutzern von allen IPs anbieten können,
denen ACS in Ihrem Auftrag vertraut.

**Verbundanbieter (FP)** -IPs haben direktes wissen über Benutzer
und authentifizieren Sie mit ihren Anmeldeinformationen und geben Ansprüche darüber aus, was
sie von ihnen wissen. Ein Verbundanbieter (FP) ist eine andere Art von
Berechtigung: anstatt authentifiziert Benutzer nicht direkt, sondern fungiert als ein
vermittelnde und verwaltende Authentifizierung zwischen einem RP und einem oder mehreren
IPs. IPs und FPs geben Sicherheitstokens aus, daher verwenden sie beide
den Sicherheitstokendienst (STS). Der ACS ist ein FP.

**ACS-Regelmodul** -die Logik verwendet, um eingehende Tokens von
vertrauenswürdige IPs in Tokens umzuwandeln, die vom RP verwendet werden sollen, wird in Form von
einfachen Anspruchstransformationsregeln codiert. ACS bietet ein Regelmodul, das
das Anwenden einer beliebigen Transformationslogik, die Sie für Ihren RP angegeben haben,
übernimmt.

**ACS-Namespace** -ein Namespace ist eine Partition oberster Ebene von ACS, die Sie
für das Organisieren Ihrer Einstellungen verwenden. Ein Namespace enthält eine Liste der IPs, denen Sie
vertrauen, der RP-Anwendungen, die Sie bereitstellen möchten, der Regeln, die das
Regelmodul voraussichtlich zum Verarbeiten eingehender Token verwendet, und so weiter. Ein namespace
weist verschiedene Endpunkte auf, die von der Anwendung und vom Entwickler verwendet werden,
damit ACS seine Funktion ausführen kann.

Die folgende Abbildung zeigt, wie die ACS-Authentifizierung bei einer:
Webanwendung abläuft:

![ACS-Ablaufdiagramm][acs_flow]

1.  Der Client (in diesem Fall ein Browser) fordert von der RP eine Seite an.
2.  Da die Anforderung noch nicht authentifiziert ist, verweist die RP den Benutzer an die Stelle, der sie vertraut, also an den ACS. Der ACS übermittelt dem Benutzer verschiedene IPs, die für diese RP definiert wurden. Der Benutzer wählt einen passenden IP aus.
3.  Der Client geht auf die Authentifizierungsseite des IPs und fordert den Benutzer auf, sich anzumelden.
4.  Wenn der Client authentifiziert ist (beispielsweise die Anmeldeinformationen eingegeben wurden), gibt der IP ein Sicherheitstoken aus.
5.  Nach der Ausgabe des Sicherheitstokens leitet der IP den Client an den ACS weiter und sendet das vom IP ausgegebene Sicherheitstoken an den ACS.
6.  Der ACS validiert das vom IP ausgegebene Token, gibt die in diesem Token enthaltenen Identitätsansprüche in das ACS-Regelmodul ein, berechnet die auszugebenden Identitätsansprüche und gibt ein neues Sicherheitstoken aus, das diese Ansprüche enthält.
7.  Der ACS leitet den Client an die RP weiter. Der Client sendet das vom ACS ausgegebene neue Sicherheitstoken an die RP. Die RP validiert die Signatur auf dem vom ACS ausgegebenen Sicherheitstoken, validiert die Ansprüche in diesem Token und gibt die ursprünglich angeforderte Seite zurück.

## Voraussetzungen

Damit Sie die in dieser Anleitung gestellten Aufgaben ausführen können, brauchen Sie Folgendes:

- Ein Java Developer Kit (JDK), Version 1.6 oder höher.
- Eclipse IDE für Java EE-Entwickler, Indigo oder höher. Dies kann von heruntergeladen werden <http://www.eclipse.org/downloads/>. 
- Eine Distribution eines Java-basierten Webservers oder Anwendungsservers wie Apache Tomcat, GlassFish, JBoss Application Server oder Jetty.
- ein Azure-Abonnement, das von abgerufen werden kann <http://www.microsoft.com/windowsazure/offers/>.
- Das Azure Toolkit für Eclipse, Version von April 2014 oder höher. Weitere Informationen finden Sie unter [Installation von Azure-Toolkit für Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
- Ein X.509-Zertifikat, das von Ihrer Anwendung verwendet werden kann. Dieses Zertifikat wird sowohl als öffentliches Zertifikat (.cer) als auch im Format Personal Information Exchange (.PFX) benötigt. (Optionen zur Erstellung dieses Zertifikats finden Sie weiter unten in diesem Lernprogramm).
- Vertrautheit mit dem Azure-Serveremulator und wurde am [Erstellen einer Hello World-Anwendung für Azure in Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx).

## Erstellen eines ACS-Namespace

Um mit der Verwendung von Azure Access Control Service (ACS) in Azure zu beginnen, müssen Sie
einen ACS-Namespace erstellen. Der Namespace stellt einen eindeutigen Bereich für
die Adressierung von ACS-Ressourcen innerhalb Ihrer Anwendung bereit.

1. Melden Sie sich bei der [Azure-Verwaltungsportal][].
2. Klicken Sie auf **Active Directory**. 
3. Um einen neuen Access Control-Namespace zu erstellen, klicken Sie auf **Neu**, klicken Sie auf **Anwendungsdienste**, klicken Sie auf **Access Control**, und klicken Sie dann auf **Schnellerfassung**. 
4. Geben Sie einen Namen für den Namespace ein. Azure prüft, ob der Name eindeutig ist.
5. Wählen Sie die Region aus, in der der Namespace verwendet wird. Verwenden Sie für die beste Leistung die Region, in der Sie ihre Anwendung einsetzen.
6. Wenn Sie über mehr als ein Abonnement verfügen, wählen Sie das Abonnement aus, das Sie für den ACS-Namespace verwenden möchten.
7. Klicken Sie auf **Erstellen**.

Azure erstellt und aktiviert den Namespace. Warten Sie, bis der Status des neuen Namespace **Active** bevor Sie fortfahren. 

## Hinzufügen von Identitätsanbietern

In dieser Aufgabe fügen Sie IPs hinzu, die mit Ihrer RP-Anwendung zur
Authentifizierung verwendet werden. Zu Demonstrationszwecken veranschaulicht diese Aufgabe das Hinzufügen
von Windows Live als IP, aber Sie können alle im ACS-Verwaltungsportal aufgeführten IPs
verwenden.


1.  In der [Azure-Verwaltungsportal][], klicken Sie auf **Active Directory**, wählen Sie einen Access Control-Namespace, und klicken Sie dann auf **verwalten**. Das ACS-Verwaltungsportal wird geöffnet.
2.  Klicken Sie im linken Navigationsbereich des ACS-Verwaltungsportals auf **Identitätsanbieter**.
3.  Windows Live ID wird standardmäßig aktiviert und kann nicht gelöscht werden. Für die Zwecke dieses Lernprogramms wird nur Windows Live ID verwendet. Dieser Bildschirm ist noch andere IP-Adressen, indem Sie auf Hinzufügen, jedoch die **Hinzufügen** Schaltfläche.

Windows Live ID wird nun als IP für Ihren ACS-Namespace aktiviert. Anschließend geben Sie
Ihre Java-Webanwendung (die später erstellt wird) als RP an.

## Hinzufügen einer Anwendung der vertrauenden Seite

In dieser Aufgabe Konfigurieren Sie den ACS zur Erkennung Ihrer Java-
Webanwendung als gültige RP-Anwendung.

1.  Klicken Sie auf die ACS-Verwaltungsportal auf **Relying Party Applications**.
2.  Auf der **Relying Party Applications** auf **Hinzufügen**.
3.  Auf der **Add Relying Party Application** Seite, gehen Sie folgendermaßen vor:
    1.  In **Namen**, geben Sie den Namen für die RP. Geben Sie für dieses Lernprogramm **Azure Web
        App**.
    2.  Im **Modus**, Option **geben Werte manuell**.
    3.  In **Bereich**, geben Sie den URI, der vom ACS ausgegebene Sicherheitstoken gilt. Geben Sie für diese Aufgabe **http://localhost: 8080 /**.
        ![RP-Bereich zur Verwendung im Serveremulator][relying_party_realm_emulator]
    4.  In **Rückgabe-URL** Geben Sie die URL, an der ACS das Sicherheitstoken zurückgibt. Geben Sie für diese Aufgabe **http://localhost: 8080/myacshelloworld/Index.jsp**
        ![Von RP zurückgegebene URL für die Verwendung im Serveremulator][relying_party_return_url_emulator]
    5.  Übernehmen Sie in den restlichen Feldern die Standardwerte.

4.  Klicken Sie auf **Speichern**.

Sie haben nun erfolgreich Ihre Java-Webanwendung konfiguriert, wenn sie im Azure-Serveremulator (unter
http://localhost: 8080/) als RP in Ihrem ACS-Namespace ausgeführt wird. Als Nächstes erstellen Sie
die Regeln, die der ACS zur Verarbeitung von Ansprüchen für die RP verwendet.

## Erstellen von Regeln

In dieser Aufgabe definieren Sie Regeln, die steuern, wie Ansprüche von
IPs an die RP übergeben werden. In diesem Leitfaden konfigurieren wir einfach
ACS zum Kopieren Sie die Eingabeanspruchstypen und -werte direkt im
Ausgabetoken, ohne sie zu filtern oder zu ändern.

1.  Klicken Sie auf der Hauptseite des ACS-Verwaltungsportal auf **Regelgruppen**.
2.  Auf der **Regelgruppen** auf **Default Rule Group for Azure Web App**.
3.  Auf der **Regelgruppe bearbeiten** auf **generieren**.
4.  Auf die **Regeln generieren: Default Rule Group for Azure Web App** Seite Windows Live ID aktiviert ist, und klicken Sie dann auf **generieren**.    
5.  Auf der **Regelgruppe bearbeiten** auf **Speichern**.

## Hochladen eines Zertifikats in Ihren ACS-Namespace

In dieser Aufgabe laden Sie ein .PFX-Zertifikat hoch, das verwendet wird, um von Ihrem ACS-Namespace erstellte Tokenanforderungen zu signieren.

1.  Klicken Sie auf der Hauptseite des ACS-Verwaltungsportal auf **Zertifikate und Schlüssel**.
2.  Auf der **Zertifikate und Schlüssel** auf **Hinzufügen** oben **Tokensignatur**.
3.  Auf der **Add Token-Signing Certificate or Key** Seite:
    1. In der **zum** auf **Relying Party Application** und wählen Sie **Azure Web App** (die Sie zuvor als Name der Anwendung der vertrauenden Seite festgelegt).
    2. In den **Typ** Abschnitt **x. 509-Zertifikat**.
    3. In der **Zertifikat** Abschnitt, klicken Sie auf die Schaltfläche zum Durchsuchen, und navigieren Sie zur x. 509-Zertifikat-Datei, die Sie verwenden möchten. Dies ist eine PFX-Datei. Wählen Sie die Datei, klicken Sie auf **Öffnen**,  und geben Sie dann das Kennwort des Zertifikats in den **Kennwort** Textfeld. Bitte beachten Sie, dass Sie zu Testzwecken ein selbstsigniertes Zertifikat verwenden können. Verwenden Sie zum Erstellen eines selbstsignierten Zertifikats die **Neu** Schaltfläche der **ACS Filter Library** Dialogfeld (siehe unten), oder verwenden Sie die **encutil.exe** Utility aus der [Projektwebsite][] der Azure-Starterkits für Java.
    4. Sicherstellen, dass **als Primärschlüssel** aktiviert ist. Ihre **Add Token-Signing Certificate or Key** Seite sollte etwa wie folgt aussehen.
        ![Tokensignaturzertifikat hinzufügen][add_token_signing_cert]
    5. Klicken Sie auf **Speichern** Ihre Einstellungen speichern und schließen die **Add Token-Signing Certificate or Key** Seite.

Jetzt überprüfen Sie die Informationen auf der Seite "Anwendungsintegration" und
kopieren den URI, die Sie benötigen, um Ihre Java-
Anwendung für die Verwendung von ACS zu konfigurieren.

## Überprüfen der Seite zur Anwendungsintegration

Sie finden alle Informationen und den Code, die bzw. den Sie zum Konfigurieren Ihrer
Java-Webanwendung (der RP-Anwendung) benötigen, um mit ACS zu arbeiten, auf
der Seite zur Anwendungsintegration des ACS-Verwaltungsportals. Durch
benötigen diese Informationen bei der Konfiguration Ihrer Java-Webanwendung für
die Verbundauthentifizierung.

1.  Klicken Sie auf die ACS-Verwaltungsportal auf **Anwendungsintegration**.  
2.  In der **Anwendungsintegration** auf **Anmeldeseiten**.
3.  In der **Anmeldeseitenintegration** auf **Azure Web App**.

In der **Login Page Integration: Azure Web App** Seite, die URL im **Option 1: Link zu einer ACS-hosted Login-Seite** in Ihrer Java-Anwendung verwendet wird. Sie benötigen diesen Wert, wenn Sie die Bibliothek für Azure Access Control Service Filter zu ihrer Java-Anwendung hinzufügen.

## Erstellen einer Java-Webanwendung
1. Klicken Sie auf das Menü in Eclipse **Datei**, klicken Sie auf **Neu**, und klicken Sie dann auf **Dynamic Web Project**. (Wenn Sie nicht sehen **Dynamic Web Project** als verfügbares Projekt aufgeführt ist, nach dem Klicken auf **Datei**, **New**, gehen Sie folgendermaßen vor: Klicken Sie auf **Datei**, klicken Sie auf **Neu**, klicken Sie auf **Projekt**, erweitern Sie **Web**, klicken Sie auf **Dynamic Web Project**, und klicken Sie auf **Weiter**.) Für die Zwecke dieses Lernprogramms, nennen Sie das Projekt **MyACSHelloWorld**. (Verwenden Sie diesen Namen unbedingt, da in den weiteren Schritten dieses Lernprogramms erwartet wird, dass Ihre WAR-Datei MyACSHelloWorld heißt).. Ihr Bildschirm sieht dann in etwa wie folgt aus:

    ![Erstellen eines Hallo-Welt-Projekts für den ACS (Beispiel)][create_acs_hello_world]

    Klicken Sie auf **Fertig stellen**.
2. Erweitern Sie in der Ansicht von Eclipse-Projektexplorer **MyACSHelloWorld**. Mit der rechten Maustaste **WebContent**, klicken Sie auf **Neu**, und klicken Sie dann auf **JSP-Datei**.
3. In der **neue JSP-Datei** Dialogfeld den Namen der Datei **index.jsp**. Nennen Sie den übergeordneten Ordner wie im Folgenden gezeigt MyACSHelloWorld/WebContent:

    ![Hinzufügen einer JSP-Datei für den ACS (Beispiel)][add_jsp_file_acs]

    Klicken Sie auf **Weiter**.

4. In der **JSP-Vorlage auswählen** Wählen Sie im Dialogfeld **neue JSP-Datei (html)** und klicken Sie auf **Fertig stellen**.
5. Wenn in Eclipse die Datei "Index.jsp" geöffnet wird, fügen Sie in den anzuzeigenden Text **Hello ACS World!** innerhalb des vorhandenen `<body>` Element. Der aktualisierte `<body>`-Inhalt sollte wie folgt aussehen:

        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
    
    Speichern Sie die Datei index.jsp.
  
## Hinzufügen der ACS-Filter-Bibliothek zu Ihrer Anwendung

1. Eclipse-Projektexplorer mit der Maustaste **MyACSHelloWorld**, klicken Sie auf **Buildpfad**, und klicken Sie dann auf **Buildpfad konfigurieren**.
2. In der **Java-Buildpfad** Dialogfeld klicken Sie auf die **Bibliotheken** Registerkarte.
3. Klicken Sie auf **Bibliothek hinzufügen**.
4. Klicken Sie auf **Azure Access Control Services Filter (von MS Open Tech)** und klicken Sie dann auf **Weiter**. Die **Azure Access Control Services Filter** Dialogfeld wird angezeigt.  (Die **Speicherort** Feld möglicherweise einen anderen Pfad, je nachdem, wo Sie Eclipse installiert haben und die Versionsnummer kann unterschiedlich sein, je nach Softwareupdates.)

    ![ACS-Filter-Bibliothek hinzufügen][add_acs_filter_lib]

5. Mit einem Browser geöffnet, in der **Login Page Integration** Seite des Verwaltungsportals, kopieren Sie die URL im der **Option 1: Link zu einer ACS-hosted Login-Seite** ein, und fügen Sie ihn in die **ACS Authentication Endpoint** Feld der Eclipse-Dialogfelds.
6. Öffnen Sie mit einem Browser die **Edit Relying Party Application** Seite des Verwaltungsportals, kopieren Sie die URL im der **Bereich** Felds, und fügen Sie ihn in die **Relying Party Realm** Feld der Eclipse-Dialogfelds.
7. Innerhalb der **Sicherheit** Abschnitt des Eclipse-Dialogfelds ein vorhandenes Zertifikat verwenden, klicken Sie auf Wunsch **Durchsuchen**, navigieren Sie zum Zertifikat, das Sie möchten, wählen Sie ihn, und klicken Sie auf **Öffnen**. Oder, wenn Sie ein neues Zertifikat erstellen möchten, klicken Sie auf **Neu** zum Anzeigen der **Neues Zertifikat** Dialogfeld, geben Sie das Kennwort, Name der CER-Datei und Namen der PFX-Datei für das neue Zertifikat.
8. Überprüfen Sie **das Zertifikat in die WAR-Datei einbetten**. Wenn Sie das Zertifikat auf diese Weise einbetten, wird es in Ihre Bereitstellung aufgenommen, ohne dass Sie es manuell als Komponente hinzufügen müssen. (Wenn stattdessen Sie Ihr Zertifikat extern aus Ihrer WAR-Datei speichern müssen, könnten Sie als rollenkomponente hinzufügen und deaktivieren Sie **Das Zertifikat in die WAR-Datei einbetten**.)
9. [Optional] Behalten Sie **Require HTTPS Connections** überprüft. Wenn Sie diese Option aktivieren, müssen Sie über das HTTPS-Protokoll auf Ihre Anwendung zugreifen. Wenn Sie nicht möchten, dass HTTPS-Verbindungen erforderlich sind, deaktivieren Sie diese Option.
10. Für eine Bereitstellung im Serveremulator Ihrer **Azure-ACS-Filter** Einstellungen sieht etwa wie folgt.

    ![Azure-ACS-Filter-Einstellungen für eine Bereitstellung auf dem Serveremulator][add_acs_filter_lib_emulator]

11. Klicken Sie auf **Fertig stellen**.
12. Klicken Sie auf **Ja** Wenn ein Dialogfeld mit dem Hinweis, dass eine web.xml-Datei erstellt wird.
13. Klicken Sie auf **OK** Schließen die **Java-Buildpfad** Dialogfeld.

## Bereitstellen auf dem Serveremulator

1. Eclipse-Projektexplorer mit der Maustaste **MyACSHelloWorld**, klicken Sie auf **Azure**, und klicken Sie dann auf **Paket für Azure**.
2. Für **Projektname**, Typ **MyAzureACSProject** und klicken Sie auf **Weiter**.
3. Wählen sie ein JDK und einen Anwendungsserver. (Diese Schritte werden detailliert in den [Erstellen einer Hello World-Anwendung für Azure in Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) Lernprogramm).
4. Klicken Sie auf **Fertig stellen**.
5. Klicken Sie auf die **Run in Azure Emulator** Schaltfläche.
6. Schließen Sie nach dem Start Ihrer Java-Anwendung im Serveremulator alle Instanzen Ihres Browsers, damit andere laufende Browsersitzungen keine Konflikte mit Ihrem ACS-Anmeldungstest auslösen.
7. Führen Sie die Anwendung durch Öffnen <http://localhost:8080/MyACSHelloWorld/> in Ihrem Browser (oder <https://localhost:8080/MyACSHelloWorld/> Wenn Sie aktiviert **Require HTTPS Connections**). Sie sollten nun zur Anmeldung bei Windows Live ID aufgefordert und dann zur Rückgabe-URL weitergeleitet werden, die Sie für die Anwendung der vertrauenden Seite angegeben haben.
99.  Wenn Sie die Anzeige Ihrer Anwendung abgeschlossen haben, klicken Sie auf die **Reset Azure Emulator** Schaltfläche.

## Bereitstellen in Azure

Für eine Bereitstellung in Azure müssen Sie den RP-Bereich und die Rückgabe-URL für Ihren ACS-Namespace ändern.

1. Der Azure-Verwaltungsportal in der **Edit Relying Party Application** Seite, ändern Sie **Bereich** in die URL Ihrer bereitgestellten Website. Ersetzen Sie **Beispiel** mit den DNS-Namen, die Sie für Ihre Bereitstellung angegeben haben.

    ![RP-Bereich zur Verwendung bei der Produktion][relying_party_realm_production]

2. Ändern Sie **Rückgabe-URL** in die URL der Anwendung. Ersetzen Sie **Beispiel** mit den DNS-Namen, die Sie für Ihre Bereitstellung angegeben haben.

    ![Rückgabe-URL der RP zur Verwendung bei der Produktion][relying_party_return_url_production]

3. Klicken Sie auf **Speichern** zum Speichern der aktualisierten RP-Bereich und Rückgabe-URL.
4. Behalten Sie die **Anmeldeseitenintegration** Seite in Ihrem Browser geöffnet, da Sie in Kürze etwas daraus kopieren müssen.
5. Eclipse-Projektexplorer mit der Maustaste **MyACSHelloWorld**, klicken Sie auf **Buildpfad**, und klicken Sie dann auf **Buildpfad konfigurieren**.
6. Klicken Sie auf die **Bibliotheken** auf **Azure Access Control Services Filter**, und klicken Sie dann auf **Bearbeiten**.
7. Mit einem Browser geöffnet, in der **Login Page Integration** Seite des Verwaltungsportals, kopieren Sie die URL im der **Option 1: Link zu einer ACS-hosted Login-Seite** ein, und fügen Sie ihn in die **ACS Authentication Endpoint** Feld der Eclipse-Dialogfelds.
8. Öffnen Sie mit einem Browser die **Edit Relying Party Application** Seite des Verwaltungsportals, kopieren Sie die URL im der **Bereich** Felds, und fügen Sie ihn in die **Relying Party Realm** Feld der Eclipse-Dialogfelds.
9. Innerhalb der **Sicherheit** Abschnitt des Eclipse-Dialogfelds ein vorhandenes Zertifikat verwenden, klicken Sie auf Wunsch **Durchsuchen**, navigieren Sie zum Zertifikat, das Sie möchten, wählen Sie ihn, und klicken Sie auf **Öffnen**. Oder, wenn Sie ein neues Zertifikat erstellen möchten, klicken Sie auf **Neu** zum Anzeigen der **Neues Zertifikat** Dialogfeld, geben Sie das Kennwort, Name der CER-Datei und Namen der PFX-Datei für das neue Zertifikat.
10. Behalten Sie **das Zertifikat in die WAR-Datei einbetten** aktiviert, sofern Sie das Zertifikat in die WAR-Datei einbetten möchten.
11. [Optional] Behalten Sie **Require HTTPS Connections** überprüft. Wenn Sie diese Option aktivieren, müssen Sie über das HTTPS-Protokoll auf Ihre Anwendung zugreifen. Wenn Sie nicht möchten, dass HTTPS-Verbindungen erforderlich sind, deaktivieren Sie diese Option.
12. Wenn die Bereitstellung in Azure erfolgt, sehen Ihre Einstellungen für Azure-ACS-Filter in etwa wie folgt aus:

    ![Azure-ACS-Filtereinstellungen für eine Bereitstellung in der Produktionsumgebung][add_acs_filter_lib_production]

13. Klicken Sie auf **Fertig stellen** Schließen die **Bibliothek bearbeiten** Dialogfeld.
14. Klicken Sie auf **OK** Schließen die **Properties for MyACSHelloWorld** Dialogfeld.
15. In Eclipse, klicken Sie auf die **Publish to Azure Cloud** Schaltfläche. Befolgen Sie die Anweisungen, die ähnlich wie der **zum Bereitstellen der Anwendung in Azure** Teil der [Erstellen einer Hello World-Anwendung für Azure in Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) Thema. 

Schließen Sie nach der Bereitstellung Ihrer Webanwendung alle offenen Browser-Sitzungen, und führen Sie Ihre Webanwendung aus. Sie sollten nun die Aufforderung erhalten, sich mit Ihren Anmeldeinformationen für Windows Live ID anzumelden, und danach zur Rückgabe-URL Ihrer Anwendung der vertrauenden Seite weitergeleitet werden.

Wenn Sie fertig sind mithilfe Ihrer ACS Hello World-Anwendung, denken Sie daran, löschen die Bereitstellung (erfahren Sie, wie zum Löschen einer Bereitstellung in der [Erstellen einer Hello World-Anwendung für Azure in Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) Thema).


## <a name="next_steps"></a>Nächste Schritte

Eine Prüfung von der Security Assertion Markup Language (SAML) vom ACS an Ihre Anwendung zurückgegeben wird, finden Sie unter [SAML Anzeigen von Azure Access Control Service zurückgegebenen][]. Die ACS-Funktionalität genauer erforschen und mit anspruchsvolleren Szenarien experimentieren möchten, finden Sie unter [Access Control Service 2.0][].

Auch in diesem Beispiel verwendet die **des Zertifikats in die WAR-Datei einbetten** Option. Diese Option vereinfacht die Bereitstellung des Zertifikats. Wenn Sie stattdessen Ihr Signaturzertifikat von Ihrer WAR-Datei getrennt halten möchten, gehen Sie folgendermaßen vor:

1. Innerhalb der **Sicherheit** Teil der **Azure Access Control Services Filter** Dialogfelds **${Env. JAVA_HOME}/MyCert.cer** und deaktivieren Sie **betten Sie das Zertifikat in die WAR-Datei**. (Wenn Ihre Zertifikatdatei einen anderen Namen hat, ersetzen Sie mycert.cer durch diesen.) Klicken Sie auf **Fertig stellen** um das Dialogfeld zu schließen.
2. Kopieren Sie das Zertifikat als Komponente in der Bereitstellung: Erweitern Sie im Projektexplorer von Eclipse, **MyAzureACSProject**, mit der rechten Maustaste **WorkerRole1**, klicken Sie auf **Eigenschaften**, erweitern Sie **Azure-Rolle**, und klicken Sie auf **Komponenten**.
3. Klicken Sie auf **Hinzufügen**.
4. Innerhalb der **Komponente hinzufügen** Dialogfeld:
    1. In der **Import** Abschnitt:
        1. Verwenden Sie die **Datei** um das Zertifikat zu wechseln, Sie verwenden möchten. 
        2. Für **Methode**, Option **Kopieren**.
    2. Für **als Name**, klicken Sie auf das Textfeld, und übernehmen Sie den Standardnamen.
    3. In der **Bereitstellen** Abschnitt:
        1. Für **Methode**, Option **Kopieren**.
        2. Für **Verzeichnis**, Typ **%JAVA_HOME%**.
    4. Die **Komponente hinzufügen** Dialogfeld sollte etwa wie folgt aussehen.

        ![Zertifikatkomponente hinzufügen][add_cert_component]

    5. Klicken Sie auf **OK**.

An diesem Punkt wird Ihr Zertifikat in Ihre Bereitstellung aufgenommen. Beachten Sie, dass unabhängig davon, ob Sie das Zertifikat in die WAR-Datei einbetten oder als eine Komponente Ihrer Bereitstellung hinzufügen, Sie das Zertifikat in Ihren Namespace hochladen, wie in beschrieben müssen die [Hochladen eines Zertifikats in Ihren ACS-Namespace][] Abschnitt.

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[Upload a certificate to your ACS namespace]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[project website]: http://wastarterkit4java.codeplex.com/releases/view/61026
[How to view SAML returned by the Azure Access Control Service]: /en-us/develop/java/how-to-guides/view-saml-returned-by-acs/
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Azure Management Portal]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png
 


