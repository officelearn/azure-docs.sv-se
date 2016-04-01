<properties
   pageTitle="Anwendungsobjekte und Dienstprinzipalobjekte | Microsoft Azure"
   description="Eine Beschreibung der Beziehung zwischen Anwendungsobjekten und ServicePrincipal-Objekten in Azure Active Directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   services="active-directory"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/17/2015"
   ms.author="mbaldwin"/>


# Anwendungsobjekte und Dienstprinzipalobjekte

Dieses Diagramm veranschaulicht die Beziehung zwischen einem Anwendungsobjekt und einem dienstprinzipalobjekt im Kontext einer beispielanwendung namens **HR app**. Drei verschiedene Mandanten sind: **Adatum**, der Mandant, die app entwickelt, und **Contoso** und **Fabrikam**, die Mandanten, die nutzen die **HR app**.

![Beziehung zwischen einem Anwendungsobjekt und einem ServicePrincipal-Objekt](./media/active-directory-application-objects/application-objects-relationship.png)


Wenn Sie eine Anwendung im Azure-Verwaltungsportal registrieren, werden zwei Objekte in Ihrem Verzeichnismandanten erstellt:

- **Anwendungsobjekt**: Dieses Objekt stellt eine Definition für die app dar. Sie finden eine ausführliche Beschreibung der Eigenschaften in der **Anwendungsobjekt** unten im Abschnitt.

- **Dienstprinzipalobjekt**: Dieses Objekt stellt eine Instanz der app in Ihrem verzeichnismandanten dar. Sie können Richtlinien auf ServicePrincipal-Objekte anwenden, einschließlich des Zuweisens von Berechtigungen zum ServicePrincipal, mit deren Hilfe die App die Verzeichnisdaten des Mandanten lesen kann. Wenn Sie das Anwendungsobjekt ändern, werden die Änderungen auch auf das zugeordnete ServicePrincipal-Objekt im Mandanten übertragen.


> [AZURE.NOTE] Wenn Ihre Anwendung für den externen Zugriff konfiguriert ist, werden Änderungen auf das Anwendungsobjekt nicht im ServicePrincipal des Mandanten ein Consumer wiedergegeben, bis der Zugriff entfernt und erneut Zugriff gewährt.



Im oben aufgeführten Diagramm stellt Schritt 1 den Vorgang des Erstellens der Anwendungs- und Dienstprinzipalobjekte dar.

Wenn in Schritt 2 ein Unternehmensadministrator Zugriff erteilt, wird ein ServicePrincipal-Objekt im Azure AD-Mandanten des Unternehmens erstellt. Diesem wird die Verzeichniszugriffsebene zugewiesen, die der Unternehmensadministrator gewährt hat.

In Schritt 3 verfügen die Consumermandanten einer App (wie etwa Contoso und Fabrikam) jeweils über ihr eigenes ServicePrincipal-Objekt, das ihre Instanz der App darstellt. Im vorliegenden Beispiel besitzen sie jeweils ein ServicePrincipal-Objekt, das die HR App darstellt.





## Anwendungsobjekteigenschaften

Die folgende Tabelle listet alle Eigenschaften eines Anwendungsobjekts auf und enthält wichtige Informationen für Entwickler. Diese Eigenschaften beziehen sich auf Webanwendungen, Web-APIs und systemeigene Clientanwendungen, die in Azure AD registriert sind.


### Allgemein

Eigenschaft | Beschreibung
| ------------- | -----------
| Name | Der Anzeigename der App. Erforderliche Eigenschaft in der **-Anwendung hinzufügen** Assistenten.
| Logo | Ein App-Logo, das Ihre App oder Ihr Unternehmen repräsentiert. Dieses Logo erlaubt es externen Benutzern, der betreffenden App leichter eine "Zugriff gewähren"-Anforderung zuzuweisen. Wenn Sie ein Logo hochladen, befolgen Sie die Angaben in den **Logo hochladen** Assistenten. Wenn Sie kein Logo bereitstellen, wird ein Standardlogo angezeigt.
| Externer Zugriff | Legt fest, ob es Benutzern in externen Organisationen erlaubt ist, einer App das einmalige Anmelden und den Zugriff auf Daten im Organisationsverzeichnis zu gewähren. Dieses Steuerelement betrifft ausschließlich die Möglichkeit, den Zugriff zu gewähren. Es ändert keinen bereits erteilten Zugriff. Zugriff kann ausschließlich von Unternehmensadministratoren gewährt werden.


### Einmaliges Anmelden

Eigenschaft | Beschreibung
| ------------- | -----------
| App-ID-URI | Ein eindeutiger logischer Bezeichner für die App. Erforderliche Eigenschaft in der **-Anwendung hinzufügen** Assistenten. <br><br>Da die App-ID-URI einen logischen Bezeichner darstellt, muss er nicht in eine IP-Adresse aufgelöst werden. Er wird von der App angezeigt, wenn eine Anforderung zum einmaligen Anmelden an Azure AD gesendet wird. Azure AD identifiziert die App und sendet die Antwort bezüglich der Anmeldung (ein SAML-Token) an die Antwort-URL, die bei der Registrierung der App bereitgestellt wurde. Verwenden Sie den App-ID-URI-Wert zum Festlegen der wtrealm-Eigenschaft (für WS-Verbund) oder der Issuer-Eigenschaft (für SAML-P) bei einer Anmeldeanforderung. Die **App-ID-URI** muss ein eindeutiger Wert in Ihrer Organisation Azure AD.<br><br>**Hinweis**: Wenn Sie eine app für externe Benutzer aktivieren möchten, muss der Wert des App-ID-URI der app eine Adresse in einer der überprüften Domänen des Verzeichnisses sein. Er darf daher kein URN sein. Diese Maßnahme verhindert, dass andere Organisationen eine eindeutige Eigenschaft angeben (und übernehmen), die zu Ihrer Organisation gehört. Während der Entwicklung können Sie den App-ID-URI an einen Speicherort in der ursprünglichen Domäne Ihrer Organisation verschieben (falls Sie noch keine benutzerdefinierte/personalisiertes Domäne überprüft haben) und die App auf diesen neuen Wert aktualisieren. Die ursprüngliche Domäne ist die Domäne der Ebene 3, die Sie bei der Registrierung erstellen, z. B. "contoso.onmicrosoft.com".
| App-URL | Die Adresse einer Webseite, auf der Benutzer sich anmelden und die App verwenden können. Erforderliche Eigenschaft in der **-Anwendung hinzufügen** Assistenten.<br><BR>**Hinweis:**: der Wert für diese Eigenschaft im Assistenten Anwendung hinzufügen, auch als Wert für die Antwort-URL festgelegt ist.
| Antwort-URL | Die physische Adresse Ihrer App. Azure AD sendet ein Token mit der Antwort auf die einmalige Anmeldung an diese Adresse. Während der ersten Registrierung in der **-Anwendung hinzufügen** -Assistent, den Wert für die URL der App auch als der Wert der Antwort-URL festgelegt. Wenn eine anmeldeanforderung, verwenden Sie den Antwort-URL-Wert zum Festlegen der Wreply-Eigenschaft (für WS-Verbund) oder die **AssertionConsumerServiceURL** -Eigenschaft (für SAML-P).<br><BR>**Hinweis**: Wenn Sie eine app für externe Benutzer aktivieren, kann die Antwort-URL muss eine **https://** Adresse.
| Verbundmetadaten-URL | (Optional). Stellt die physische URL des Verbundmetadaten-Dokuments für die App dar. Sie ist erforderlich, um die SAML-P-Abmeldung zu unterstützen. Über Azure AD wird das an diesem Endpunkt gehostete Metadatendokument heruntergeladen und verwendet, um den öffentlichen Teil des Zertifikats zu ermitteln, den Sie zum Überprüfen der Signatur auf den Abmeldeanforderungen und auf der Abmelde-URL der App verwenden. Diese Eigenschaft kann nicht konfiguriert werden, wenn Sie die App erstmals hinzufügen. Sie können nur später konfiguriert werden.<br><BR>**Hinweis**: Wenn Sie SAML-P-Abmeldung unterstützen müssen, aber Sie haben keinen verbundmetadatenendpunkt für Ihre app, wenden Sie sich an den Support für weitere Optionen.


### Aufrufen der Graph-API oder der Web-APIs

Eigenschaft | Beschreibung
| ------------- | -----------
| Client-ID | Der eindeutige Bezeichner für die App. Sie müssen diesen Bezeichner in Aufrufen der Graph-API oder anderer Web-APIs verwenden, die bei Azure AD registriert sind. Azure AD generiert diesen Wert automatisch während der Registrierung der app und kann nicht geändert werden.<BR><BR>Um die app Zugriff auf das Verzeichnis (für Lese- oder Schreibzugriff) über die Graph-API zu aktivieren, benötigen Sie eine Client-ID und einen Schlüssel (in OAuth 2.0 als ein Clientschlüssel bezeichnet). Die App verwendet die Client-ID und den Schlüssel, um ein Zugriffstoken vom Azure AD OAuth 2.0-Tokenendpunkt anzufordern. (Klicken Sie zum Anzeigen aller Azure AD-Endpunkte in der Befehlsleiste **Anzeigen von Endpunkten**.) Wenn Sie die Graph-API zum Abrufen oder Festlegen (Ändern) von Verzeichnisdaten nutzen, verwendet die App das Zugriffstoken im Autorisierungsheader der Anforderung an die Graph-API.
| Schlüssel | Wenn die App Daten in Azure AD liest oder schreibt, wie etwa Daten, die über die Graph-API zur Verfügung gestellt werden, benötigt die App einen Schlüssel. Wenn Sie ein Zugriffstoken zum Aufrufen der Graph-API anfordern, Ihre app stellt seine **Client-ID** und **Schlüssel**. Der Tokenendpunkt verwendet die ID und den Schlüssel zur Authentifizierung der App, bevor das Zugriffstoken ausgegeben wird. Sie können mehrere Schlüssel erstellen, um Schlüsselrolloverszenarien zu ermöglichen. Außerdem können Sie Schlüssel löschen, die abgelaufen, gefährdet oder nicht mehr in Verwendung sind.
| Zugriff verwalten | Wählen Sie eine der drei verschiedenen Zugriffsebenen aus: "Einmaliges Anmelden (SSO)", "Einmaliges Anmelden, Verzeichnisdaten lesen" oder "Einmaliges Anmelden, Verzeichnisdaten lesen/schreiben". Sie können den Zugriff auch aufheben. <br><BR>**Hinweis**: Änderungen an der verzeichniszugriffsebene der app gelten nur für Ihr Verzeichnis. Die Änderungen gelten nicht für Kunden, die einen Zugriff auf die App gewährt haben.


### Systemeigene Clients

Eigenschaft | Beschreibung
| ------------- | -----------
| Umleitungs-URI | Der URI, zu dem Azure AD den Benutzer-Agent als Antwort auf eine OAuth 2.0-Autorisierungsanforderung umleitet. Der Wert muss kein physischer Endpunkt, aber unbedingt ein gültiger URI sein.

##


