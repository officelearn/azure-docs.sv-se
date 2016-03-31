<properties
    pageTitle="Arbeiten mit Ansprüche unterstützenden Apps im Anwendungsproxy"
    description="Erläutert, wie Sie den Azure AD-Anwendungsproxy betriebsbereit machen."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/08/2015"
    ms.author="kgremban"/>



# Arbeiten mit Ansprüche unterstützenden Apps im Anwendungsproxy

Ansprüche unterstützende Apps führen eine Umleitung zum Sicherheitstokendienst (STS, Security Token Service) durch, der wiederum Anmeldeinformationen vom Benutzer im Austausch gegen ein Token anfordert, bevor der Benutzer zur Anwendung umgeleitet wird. Damit der Anwendungsproxy mit diesen Umleitungen arbeiten kann, müssen die folgenden Schritte ausgeführt werden.

> [AZURE.IMPORTANT] Anwendungsproxy ist ein Feature, ist nur verfügbar, wenn Sie auf die Premium oder Basic Edition von Azure Active Directory aktualisiert. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).


## Voraussetzung

Stellen Sie vor dem Ausführen dieses Verfahrens sicher, dass der STS, zu dem die Ansprüche unterstützende App umgeleitet wird, außerhalb Ihres lokalen Netzwerks zur Verfügung steht.


### Konfiguration im klassischen Azure-Portal

1. Veröffentlichen der Anwendung gemäß der Anleitung in [Veröffentlichen von Anwendungen mit dem Anwendungsproxy](active-directory-application-proxy-publish.md).
2. Klicken Sie in der Liste der Programme, die Ansprüche beachten app auswählen, und klicken Sie auf **konfigurieren**.
3. Wenn Sie ausgewählt haben **Pass-Through-** als Ihre **Vorauthentifizierungsmethode**, stellen Sie sicher, dass **HTTPS** als Ihre **Externalurl** Schema.
4. Wenn Sie ausgewählt haben **Azure Active Directory** als Ihre **Vorauthentifizierungsmethode**, auf **keine** als Ihre **interne Authentifizierungsmethode**.


### ADFS-Konfiguration

1. Öffnen Sie die ADFS-Verwaltung.
2. Wechseln Sie zu **Vertrauensstellungen für vertrauende Seiten**, klicken Sie mit der rechten Maustaste auf die Anwendung, die Sie mit dem Anwendungsproxy veröffentlicht werden, und wählen **Eigenschaften**.

![Vertrauensstellungen der vertrauenden Seite: mit der rechten Maustaste auf den App-Namen klicken – Screenshot](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)

3. Auf die **Endpunkte** Registerkarte **Endpunkttyp** wählen **WS-Federation**.
4. Unter **Vertrauenswürdige URL**, geben Sie die URL, die Sie eingegeben, in der Anwendungsproxy unter haben **Externalurl** und klicken Sie auf **OK**.

![Hinzufügen eines Endpunkts – Wert "Vertrauenswürdige URL" festlegen – Screenshot](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)


