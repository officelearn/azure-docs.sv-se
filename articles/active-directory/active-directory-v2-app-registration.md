<properties
    pageTitle="App-Modell v2.0 | Microsoft Azure"
    description="Wie Sie eine app mit Microsoft zu registrieren, für die Anmeldung aktivieren und die Integration von apps mit app-Modell v2. 0."
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

# App-Modell v2.0 (Vorschauversion): Registrieren einer App bei Microsoft

Um eine App zu erstellen, die sowohl eine MSA- als auch eine Azure AD-Anmeldung zulässt, müssen Sie zunächst eine Anwendung bei Microsoft registrieren.  Sie können keine möglicherweise in Azure AD oder MSA vorhandenen Apps nutzen – es ist Zeit, eine brandneue App zu erstellen.

> [AZURE.NOTE]
    Diese Informationen gelten für App-Modell v2.0 (öffentliche Vorschauversion).  Anleitung zur Integration in die allgemein verfügbare Azure AD-service, finden Sie in der [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

## Besuchen Sie das Verwaltungsportal Microsoft App-Registrierung
Wichtigste zuerst – navigieren Sie zu [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com).  Dies ist das neue App-Registrierungportal, in dem Sie alles mögliche im Zusammenhang mit Ihren Microsoft-Apps verwalten können.

Melden Sie sich entweder mit einem persönlichen Microsoft-Konto oder einem Geschäfts- oder Schulkonto an.  Wenn Sie noch kein Konto besitzen, melden Sie sich für ein neues persönliches Konto an. Legen Sie los, es dauert nicht lange – wir warten hier aus Sie.

Fertig? Jetzt sollten Sie Ihre Liste der Microsoft-Apps sehen, die wahrscheinlich noch leer ist.  Ändern wir das.

<!-- TODO: Verify strings here -->
Klicken Sie auf **Hinzufügen einer Anwendung**, und geben sie einen Namen.  Das Portal weist Ihre Anwendung ein
Global eindeutige Anwendung-Id, die Sie später in Ihrem Code verwenden.  Wenn Ihre Anwendung eine serverseitige Komponente umfasst benötigt, Zugriffstoken für das Aufrufen von APIs
(denken: Office, Azure oder 3rd Party), sollten Sie zum Erstellen einer ** Anwendung
Geheimer ** auch hier.
<!-- TODO: Link for app secrets -->

Fügen Sie dann die Plattformen hinzu, auf denen Ihre App verwendet wird.
- Für Web-basierte apps bieten eine **Redirect URI** in denen-in Nachrichten gesendet werden können.
- Notieren Sie sich den standardmäßigen Umleitungs-URI für mobile Apps, der automatisch erstellt wurde.

Optional können Sie das Aussehen und Verhalten der Anmeldeseite im Profilabschnitt anpassen.  Achten Sie darauf zu **Speichern** bevor Sie fortfahren.

## Erstellen Sie eine Schnellstart-App
Nun, da Sie eine Microsoft-app haben, können eines unserer Schnellstart ausführen
Lernprogramme mit app-Modell v2. 0 bis & ausgeführt abgerufen.  Hier sind einige
Empfehlung:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]


