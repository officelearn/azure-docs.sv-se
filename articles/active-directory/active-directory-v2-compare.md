<properties
    pageTitle="App-Modell v2.0 | Microsoft Azure"
    description="Ein Vergleich des allgemein verfügbaren Azure AD und der öffentlichen Vorschauversion des App-Modells v2.0."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/09/2015"
    ms.author="dastrock"/>

# App-Modell v2.0 (Vorschauversion): Was ist anders?

Wenn Sie mit dem allgemeinen Azure AD-Dienst vertraut sind oder in der Vergangenheit Apps in Azure AD integriert haben, gibt es möglicherweise einige Unterschiede im App-Modell v2.0, die Sie nicht erwarten.  In diesem Dokument werden die Unterschiede zu Ihrem Verständnis erläutert.

> [AZURE.NOTE]
    Diese Informationen gelten für App-Modell v2.0 (öffentliche Vorschauversion).  Anleitung zur Integration in die allgemein verfügbare Azure AD-service, finden Sie in der [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).


## Microsoft-Konten und Azure AD-Konten
Das App-Modell v2.0 ermöglicht Entwicklern das Schreiben von Apps, die eine Anmeldung sowohl von Microsoft-Konten als auch von Azure AD-Konten akzeptieren. Dazu wird ein einziger Endpunkt genutzt.  Dadurch können Sie die App so schreiben, dass sie Konten komplett ignoriert, und auch die Art des Kontos ignorieren kann, mit dem sich der Benutzer anmeldet.  Natürlich, Sie *können* Einrichten Ihrer app für den Typ des Kontos in einer bestimmten Sitzung verwendet wird, aber Sie brauchen.

Z. B. wenn die app Ruft die [Office 365-REST-APIs](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2), einige zusätzliche Funktionen und Daten werden für Enterprise-Benutzer, z. B. ihre SharePoint-Websites oder Verzeichnisdaten verfügbar sein.  Aber für viele Aktionen wie z. B. [Lesen von e-Mails eines Benutzers](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2), kann der Code geschrieben werden genau für Microsoft-Accounts und Azure AD-Konten.  

Das Integrieren Ihrer App mit Microsoft-Konten und Azure AD-Konten ist nun ein einfacher Prozess.  Sie können einen einzelnen Satz von Endpunkten, eine einzelne Bibliothek und eine einzelne App-Registrierung verwenden, um Zugriff auf die Verbraucher- und Unternehmens-Welten zu erhalten.  Weitere Informationen zu den v2. 0-app-Modell Vorschau Auschecken [Übersicht über die](active-directory-appmodel-v2-overview.md).


## Das neue Portal für die App-Registrierung
V2. 0-app-Modell erfordert, dass Sie Ihre Microsoft-apps an einem neuen Speicherort zu registrieren: [apps.dev.microsoft.com](https://apps.dev.microsoft.com).  Dies ist das Portal, in dem Sie eine Anwendungs-ID erhalten, die Darstellung der Anmeldeseite Ihrer App anpassen und vieles mehr tun können.  Wir werden im App-Registrierungsportal weiterhin mehr und mehr Funktionalitäten zur Verfügung stellen, was größtenteils vom Feedback abhängt, das wir während der Vorschauphase erhalten.  Ziel ist es, dass dieses Portal der Ort wird, an dem Sie alles rund um Ihre Microsoft-Apps verwalten können.

Und das Beste daran ist, dass Sie kein Azure- oder Office-Abonnement benötigen, um es nutzen zu können.  Sie benötigen lediglich ein persönliches Microsoft-Konto oder ein Geschäfts- oder Schulkonto.

Beachten Sie, dass heute apps, die in das neue Portal des App-Registrierung registriert nur mit app-Modell v2. 0 und *nur* apps, die in das neue Portal des App-Registrierung registriert funktionieren mit dem app-Modell v2. 0.  Wenn Sie versuchen, solche Apps mit dem allgemein verfügbaren Microsoft-Konto oder Azure AD-Diensten zu verwenden, oder versuchen, eine vorhandene App mit dem App-Modell v2.0 zu nutzen, können Inkompatibilitäten auftreten.


## Eine App-ID für alle Plattformen
Im GA Azure AD-Dienst haben Sie möglicherweise mehrere unterschiedliche Apps für ein einziges Projekt registriert.  Sie mussten separate App-Registrierungen für die systemeigenen Clients und Web-Apps verwenden:

![Benutzeroberfläche für die Registrierung der alten Anwendung](../media/active-directory-v2-flows/old_app_registration.PNG)

Wenn Sie z. B. sowohl eine Website als auch eine iOS-App erstellt haben, mussten Sie diese separat mit zwei verschiedenen Anwendungs-IDs registrieren.  Wenn Sie eine Website und eine Back-End-Web-API hatten, haben Sie diese möglicherweise als separate Apps in Azure AD registriert.  Wenn Sie eine App für iOS und Android hatten, kann dies ebenfalls der Fall sein.  

<!-- You may have even registered different apps for each of your build environments - one for dev, one for test, and one for production. -->

Nun brauchen Sie lediglich eine einzige App-Registrierung und eine einzige Anwendungs-ID für jedes Ihrer Projekte.  Sie können mehrere "Plattformen" zu einem einzelnen Projekt hinzufügen, und die entsprechenden Daten für jede Plattform, die Sie hinzufügen, angeben.  Natürlich können Sie je nach Bedarf beliebig viele Apps erstellen, aber in den meisten Fällen sollte nur eine Anwendung-ID erforderlich sein.

<!-- You can also label a particular platform as "production-ready" when it is ready to be published to the outside world, and use that same Application Id safely in your development environments. -->

Unser Ziel ist es, dass dies zu einer vereinfachten App-Verwaltung und Entwicklungserfahrung führt, und sich eine konsolidiertere Ansicht eines einzelnen Projekts ergibt, an dem Sie möglicherweise arbeiten.


## Bereiche, keine Ressourcen
In der Azure AD-Dienst allgemein verfügbar, kann eine Anwendung als Verhalten einer **Ressource**, oder ein Empfänger des Tokens.  Eine Ressource definieren kann eine Reihe von **Bereiche** oder **oAuth2Permissions** die er versteht, Client-apps-Token, die die Ressource für einen bestimmten Satz von Bereichen anfordern kann.  Betrachten Sie als Beispiel für eine Ressource die Azure AD Graph-API:

- Ressourcenbezeichner, oder `AppID URI`: `https://graph.windows.net/`
- Bereiche, oder `OAuth2Permissions`: `Directory.Read`, `Directory.Write`, usw.  

All dies gilt für das App-Modell v2.0.  Eine App kann sich immer noch als Ressource verhalten, Bereiche definieren und durch einen URI identifiziert werden.  Clientanwendungen können immer noch den Zugriff auf diese Bereiche anfordern.  Allerdings hat sich die Art und Weise geändert, auf die ein Client solche Berechtigungen anfordert.  In der Vergangenheit sah eine OAuth 2.0-Autorisierungsanforderung an Azure AD etwa wie folgt aus:

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

in dem die **Ressource** Parameter angezeigt, welche Ressource die Autorisierung für die Client-app anfordert.  Azure AD hat die von der App benötigten Berechtigungen berechnet, und zwar basierend auf einer statischen Konfiguration im Azure-Portal. Die Token wurden entsprechend ausgestellt.  Dieselbe OAuth 2.0-Autorisierungsanforderung sieht wie folgt aus:

```
GET https://login.microsoftonline.com/common/v2.0/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

wobei der **Bereich** Parameter gibt an, welche Ressourcen und Berechtigungen die Anwendung fordert eine Autorisierung für. Die gewünschte Ressource ist immer noch in der Anforderung vorhanden – sie ist nun einfach von den einzelnen Werten des Bereichsparameters umgeben.  Mithilfe des Bereichs-Parameters ist das App-Modell v2.0 auf diese Weise kompatibler mit der OAuth 2.0-Spezifikation, und richtet sich genauer an gemeinsamen Methoden der Branche.  Es kann auch apps ausführen [inkrementelle Zustimmung](#incremental-and-dynamic-consent), die im nächsten Abschnitt beschrieben wird.

## Inkrementelle und dynamische Zustimmung
Apps, die im allgemein verfügbaren Azure AD-Dienst registriert sind, mussten die erforderlichen OAuth 2.0-Berechtigungen zum Erstellungszeitpunkt der App im Azure-Portal angeben:

![Benutzeroberfläche für die Registrierung von Berechtigungen](../media/active-directory-v2-flows/app_reg_permissions.PNG)

Eine app, die erforderlich waren konfigurierten Berechtigungen **statisch**.  Während die Konfiguration der App dadurch im Azure Portal existieren kann und der Code sauber und einfach bleibt, stellt es den Entwickler vor ein paar Probleme:

- Eine App musste zum Erstellungszeitpunkt alle Berechtigungen kennen, die jemals von der App benötigt wurde.  Das Hinzufügen von Berechtigungen mit der Zeit war ein schwieriger Prozess.
- Eine App musste frühzeitig alle Ressourcen kennen, auf die sie je zugreifen wollte.  Es war schwierig, Apps zu erstellen, die auf eine beliebige Anzahl von Ressourcen zugreifen konnten.
- Eine App mussten alle Berechtigungen anfordern, die je nach der ersten Anmeldung des Benutzers benötigt wurden.  In einigen Fällen führte dies zu einer sehr langen Liste von Berechtigungen, was Endbenutzer davon abhielt, bei der ersten Anmeldung den Zugriff der App zu genehmigen.

In V2. 0-app-Modell können Sie angeben, die Berechtigungen Ihrer app muss **dynamisch**, zur Laufzeit während der normalen Nutzung Ihrer app.  Zu diesem Zweck können Sie die Bereiche angeben, die Ihre App zu einem bestimmten Zeitpunkt benötigt, indem Sie sie in den `scope`-Parameter einer Autorisierungsanforderung einschließen:

```
GET https://login.microsoftonline.com/common/v2.0/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

Mit der oben genannten Anforderungsberechtigung kann die App die Verzeichnisdaten des Benutzers in Azure AD lesen sowie Daten in das Verzeichnis schreiben.  Wenn der Benutzer diesen Berechtigungen in der Vergangenheit für diese spezielle App zugestimmt hat, gibt er einfach seine Anmeldeinformationen ein und meldet sich in der App an.  Wenn der Benutzer keiner Berechtigung zugestimmt hat, fordert der v2.0-Endpunkt ihn dazu auf, diesen Berechtigungen zuzustimmen.  Weitere Informationen können Sie bis lesen, auf [Berechtigungen, die Zustimmung und Bereiche](active-directory-v2-scopes.md).

Wenn Sie einer App erlauben, Berechtigungen dynamisch über den `scope`-Parameter anzufordern, erhalten Sie die vollständige Kontrolle über die Erfahrung des Benutzers.  Falls gewünscht, können Sie die Zustimmungserfahrung auch vorziehen und alle Berechtigungen in einer ersten Autorisierungsanforderung erfragen.  Wenn Ihre App eine große Anzahl von Berechtigungen erfordert, können Sie auch die Berechtigungen des Benutzers inkrementell erfassen, während er über die Zeit bestimmte Features Ihrer App verwendet.

## Offline-Zugriff
Das App-Modell v2.0 führt eine neue, altbekannte Berechtigung für Apps ein – den `offline_access`-Bereich.  Alle Apps müssen diese Berechtigung anfordern, wenn sie im Auftrag eines Benutzers für einen längeren Zeitraum auf Ressourcen zugreifen wollen, selbst, wenn der Benutzer die App nicht aktiv verwendet.  Der `offline_access`-Bereich wird dem Benutzer in den Zustimmungsdialogfeldern als "Auf Ihre Daten offline zugreifen" angezeigt, wofür der Benutzer seine Zustimmung gewähren muss.  Durch Anfordern der `offline_access`-Berechtigung kann Ihre Web-App OAuth 2.0-Aktualisierungstoken vom v2. 0-Endpunkt erhalten.  Aktualisierungstoken sind langlebig und können durch neue OAuth 2.0-Zugriffstoken für längere Zugriffszeiten ausgetauscht werden.  

Wenn die App den `offline_access`-Bereich nicht anfordert, werden auch keine Aktualisierungstoken empfangen.  Dies bedeutet, dass, wenn Sie eine Authorization_code in einlösen der [eines Autorisierungscodes OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), Sie erhalten nur wieder ein Zugriffstoken aus der `/oauth2/token` Endpunkt.  Dieses Zugriffstoken bleibt für einen kurzen Zeitraum (in der Regel eine Stunde) gültig, läuft aber anschließend ab.  Zu diesem Zeitpunkt muss Ihre App den Benutzer zurück auf den `/oauth2/authorize`-Endpunkt leiten, um einen neuen Autorisierungscode abzurufen.  Während dieser Umleitung muss der Benutzer möglicherweise seine Anmeldeinformationen erneut eingeben oder den Berechtigungen erneut zustimmen, je nach Art der App.

Weitere Informationen zu OAuth 2.0, Refresh_tokens und Access_tokens, sehen Sie sich die [v2. 0-app-Protokoll Modellverweis](active-directory-v2-protocols.md).

## Tokenansprüche
Die Ansprüche in den Token, die vom v2.0-Endpunkt ausgegeben werden, sind nicht identisch mit denen, die von den allgemein verfügbaren Azure AD-Enpunkten ausgegeben werden. Apps, die auf den neuen Dienst migriert werden, sollten nicht davon ausgehen, dass in ID-Token oder Zugriffstoken ein besonderer Anspruch vorhanden ist.   Vom v2.0-Endpunkt ausgestellte Token sind mit den OAuth 2.0- und OpenID Connect-Spezifikationen kompatibel, folgen aber möglicherweise einer anderen Semantik, als der allgemein verfügbare Azure AD-Dienst.

Weitere Informationen zu bestimmten Ansprüche in V2. 0-app-Modell-Token ausgegeben finden Sie unter der [v2. 0-Tokenverweis](active-directory-v2-tokens.md).


## Einschränkungen der Vorschau
Es gibt eine Reihe von Einschränkungen beim Erstellen einer App mit dem App-Modell v2. 0 in der öffentlichen Vorschau, die Sie berücksichtigen sollten.  Entnehmen Sie der [v2. 0-app-Modell Einschränkungen Doc](active-directory-v2-limitations.md) um festzustellen, ob diese Einschränkungen für Ihr spezielles Szenario geeignet.


