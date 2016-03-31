<properties
    pageTitle="Arbeiten mit benutzerdefinierten Domänen im Azure AD-Anwendungsproxy | Microsoft Azure"
    description="In diesem Artikel wird erläutert, wie Sie mit benutzerdefinierten Domänen im Azure AD-Anwendungsproxy arbeiten."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="StevenPo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2015"
    ms.author="kgremban"/>

# Arbeiten mit benutzerdefinierten Domänen im Azure AD-Anwendungsproxy
> [AZURE.NOTE] Anwendungsproxy ist ein Feature, ist nur verfügbar, wenn Sie auf die Premium oder Basic Edition von Azure Active Directory aktualisiert. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).

Durch das Verwenden von Standarddomänen können Sie eine einzige URL als interne und externe URL für den Zugriff auf eine Anwendung festlegen, sodass Ihre Benutzer sich nur eine URL für den Zugriff merken müssen – unabhängig davon, von wo sie auf die Anwendung zugreifen. Außerdem können Sie im Zugriffsbereich eine einzige Verknüpfung für die Anwendung erstellen. Wenn Sie die von Azure AD-Anwendungsproxy bereitgestellte Standarddomäne verwenden, ist keine weitere Konfiguration zum Aktivieren Ihrer Domäne erforderlich. Falls Sie eine benutzerdefinierte Domäne einsetzen, müssen Sie verschiedene Punkte berücksichtigen, damit der Anwendungsproxy Ihre Domäne erkennt und die zugehörigen Zertifikate überprüft.

## Auswählen Ihrer benutzerdefinierten Domäne

1. Veröffentlichen Sie Ihre Anwendung, indem Sie den Anweisungen im Abschnitt "Veröffentlichen von Anwendungen mit dem Anwendungsproxy" folgen. 
2. Nachdem die Anwendung in der Liste der Programme angezeigt wird, wählen Sie ihn, und klicken Sie auf **konfigurieren**.
3. Unter **Externalurl**, geben Sie Ihre benutzerdefinierte Domäne.
4. Wenn Sie eine externe HTTPS-URL verwenden, werden Sie aufgefordert, ein Zertifikat hochzuladen, damit Azure die Gültigkeit der Anwendungs-URL überprüfen kann. Sie haben auch die Möglichkeit, ein Platzhalterzertifikat hochzuladen, das der externen URL der Anwendung entspricht. Diese Domäne muss in der Liste der Ihrer [Azure überprüft Domänen](https://msdn.microsoft.com/library/azure/jj151788.aspx). Azure muss über ein Zertifikat für die Domänen-URL der Anwendung oder ein Platzhalterzertifikat verfügen, das der externen URL der Anwendung entspricht.
5. Stellen Sie sicher, dass ein DNS-Eintrag hinzugefügt wird, der die interne URL an die Anwendung weiterleitet. Auf diese Weise können Sie dieselbe URL für den internen und den externen Zugriff und eine einzige Verknüpfung mit der Anwendung in der Liste der Benutzeranwendungen verwenden.

## Häufig gestellte Fragen zur Arbeit mit benutzerdefinierten Domänen

F: Kann ich ein bereits hochgeladenes Zertifikat auswählen, ohne es erneut hochzuladen? <br>
A: zuvor hochgeladenen Zertifikate werden automatisch an eine Anwendung gebunden, und genau ein Zertifikat mit Hostnamen für die Anwendung vorhanden ist. <br>
…<br>
F: Wie füge ich ein Zertifikat hinzu, und in welchem Format sollte das exportierte Zertifikat hochgeladen werden? <br>
A: das Zertifikat sollte dann die Konfigurationsseite der Anwendung hochgeladen werden. Das Zertifikat muss es sich um eine PFX-Datei sein. <br>
…<br>
F: Können ECC-Zertifikate verwendet werden? <br>
A: Es ist keine explizite Einschränkung für SAS-Methoden. <br>
…<br>
F: Können SAN-Zertifikate verwendet werden? <br>
A: Ja.<br>
…<br>
F: Können Platzhalterzertifikate verwendet werden? <br>
A: Ja. <br>
…<br>
F: Können für jede Anwendung unterschiedliche Zertifikate verwendet werden? <br>
A: Ja, sofern nicht gegenseitige denselben externen Server freigeben. <br>
…..<br>
F: Wenn ich eine neue Domäne registriere, kann ich diese Domäne verwenden? <br>
A: Ja, dient die Liste der Domänen des Mandanten überprüft Domänenliste stammt. <br>
…<br>
F: Was geschieht, wenn ein Zertifikat abläuft? <br>
A: Auf der Seite für die Anwendungskonfiguration wird im Abschnitt für die Zertifikate eine Warnung angezeigt. Wenn ein Benutzer versucht, auf die Anwendung zugreifen, wird eine sicherheitswarnung angezeigt. <br>
…<br>
F: Was tun kann ich, wenn ich ein Zertifikat für einen bestimmten app ersetzen möchten? <br>
A: Hochladen Sie die Konfigurationsseite der Anwendung ein neues Zertifikat<br>
…<br>
F: Kann ich ein Zertifikat löschen und ersetzen? <br>
A: Wenn Sie ein neues Zertifikat hochladen, wenn das alte Zertifikat nicht von einer anderen Anwendung verwendet wird, wird sie automatisch gelöscht werden<br>
…<br>
F: Was geschieht, wenn ein Zertifikat gesperrt wird<br>
A: Sperrprüfungen werden für Zertifikate nicht durchgeführt. Wenn ein Benutzer versucht, Zugriff auf die Anwendung, je nach Browser möglicherweise eine sicherheitswarnung angezeigt.<br>
…<br>
F: Kann ich ein selbstsigniertes Zertifikat verwenden? <br>
A: Ja, selbstsignierte Zertifikate sind zulässig. Beachten Sie, dass bei Verwendung eine privaten Zertifizierungsstelle, Sperrlisten-Verteilungspunkt (Certificate Revocation Point-Verteilungspunkt) für das Zertifikat öffentlich sein soll. <br>
...<br>
F: Gibt es eine Ansicht, über die alle Zertifikate für einen Mandanten angezeigt werden können? <br>
A: Diese Funktion wird in der aktuellen Version nicht unterstützt.<br>



## Weitere Informationen
Der Anwendungsproxy bietet Ihnen noch viele weitere Möglichkeiten:

- [Veröffentlichen von Anwendungen mit dem Anwendungsproxy](active-directory-application-proxy-publish.md)
- [Aktivieren der einmaligen Anmeldung](active-directory-application-proxy-sso-using-kcd.md)
- [Aktivieren des bedingten Zugriffs](active-directory-application-proxy-conditional-access.md)
- [Arbeiten mit Anwendungen, die Ansprüche unterstützen](active-directory-application-proxy-claims-aware-apps.md)- [Problembehandlung bei Anwendungsproxy aufgetreten sind](active-directory-application-proxy-troubleshoot.md)

## Weitere Informationen zum Anwendungsproxy
- [Onlinehilfe anzeigen](active-directory-application-proxy-enable.md)
- [Blog zum Anwendungsproxy aufrufen](http://blogs.technet.com/b/applicationproxyblog/)
- [Sehen Sie sich unsere Videos auf Channel 9 an!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Zusätzliche Ressourcen

* [Anmelden bei Azure als Organisation](sign-up-organization.md)
* [Azure-Identität](fundamentals-identity.md)


