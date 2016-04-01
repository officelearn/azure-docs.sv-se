<properties
    pageTitle="Erste Schritte mit Azure Active Directory Premium"
    description="In diesem Thema wird die Registrierung für die Premium Edition von Azure Active Directory beschrieben."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="stevenpo" 
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/30/2015"
    ms.author="markvi"/>

# Erste Schritte mit Azure Active Directory Premium

Azure Active Directory ist in drei Editions verfügbar: Free, Basic und Premium. Die kostenlose Free Edition ist in einem Azure- oder Office 365-Abonnement enthalten. Die Basic und Premium Editions sind über ein [Microsoft Enterprise Agreement](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) oder [Open Volume License](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) Programm. Azure- und Office 365-Abonnenten können Active Directory Premium auch online erwerben. [Melden Sie sich hier](https://portal.office.com/Commerce/Catalog.aspx) kaufen.

> [AZURE.NOTE]
Die Azure Active Directory-Editionen Premium und Basic stehen für Kunden in China zur Verfügung, die mit der weltweit verfügbaren Instanz von Azure Active Directory arbeiten. Allerdings werden die Azure Active Directory-Editionen Premium und Basic derzeit durch den in China von 21Vianet betriebenen Microsoft Azure-Dienst nicht unterstützt. Weitere Informationen kontaktieren Sie uns die [Azure Active Directory-Forum](http://feedback.azure.com/forums/169401-azure-active-directory).

Azure Active Directory Premium ist auch im enthalten die **Enterprise Mobility Suite**. Enterprise Mobility Suite ist eine kostengünstige Lösung für Unternehmen, die Microsoft Intune, Azure Rights Management und die Active Directory Premium-Dienste gemeinsam unter einem Lizenzplan verwenden möchten. Weitere Informationen finden Sie unter der [Enterprise Mobility Suite](https://www.microsoft.com/en-us/server-cloud/enterprise-mobility/overview.aspx) Website.

Wenn Sie die Azure Active Directory Premium-Features noch heute verwenden möchten, führen Sie die folgenden Schritte aus. Die gleichen Schritte gelten auch für die Azure Active Directory Basic Edition.

## Schritt 1: Registrieren Sie sich für Active Directory Premium

Zur Registrierung finden Sie unter der [Volumenlizenzierung](http://www.microsoft.com/en-us/licensing/how-to-buy/how-to-buy.aspx) Website.

## Schritt 2: Aktivieren Sie Ihren Lizenzplan

Wenn dies das erste Mal ist, dass Sie einen Lizenzplan über das Enterprise Volume Licensing-Programm von Microsoft erwerben, müssen Sie den Lizenzplan zuerst aktivieren, bevor Sie mit der Zuweisung von Lizenzen aus Ihrem Azure Active Directory-Verzeichnis beginnen können. Dazu müssen Sie in der Bestätigungs-E-Mail, die Sie nach dem Kauf Ihres ersten Lizenzplans erhalten, auf den Anmelde- bzw. Registrierungslink klicken (siehe unten). Bei jedem nachfolgenden Kauf für dieses Verzeichnis werden die Lizenzen automatisch im gleichen Verzeichnis aktiviert.

![][1]

Wenn Sie einen vorhandenen Mandanten verwenden, wählen Sie die **Anmeldung** Link, um sich mit Ihrem vorhandenen Administratorkonto anzumelden. Die Anmeldung muss über die Anmeldeinformationen des globalen Administrators des Verzeichnisses erfolgen, in dem die Lizenzen aktiviert werden sollen.

Wenn Sie einen neuen Azure Active Directory-Mandanten mit Ihrem Lizenzierungsplan erstellen möchten, und Sie wählen die **registrieren** Link dadurch Sie zum folgenden Bildschirm gelangen.

![][2]

Sobald Sie die aus der E-Mail veranlasste Anmeldung bzw. Registrierung abgeschlossen haben, sehen Sie folgenden Bildschirm, der Ihnen bestätigt, dass der Lizenzplan für Ihren Mandanten aktiviert wurde.

![][3]

## Schritt 3: Aktivieren Sie den Zugriff auf Azure Active Directory

Nach der Bereitstellung der Lizenzen in Ihrem Verzeichnis erhalten Sie eine Begrüßungs-E-Mail (siehe unten), die bestätigt, dass Sie nun mit der Verwaltung Ihrer Azure Active Directory Premium- oder Enterprise Mobility Suite-Lizenzen und Features beginnen können. Wenn Sie Microsoft Azure bereits verwendet haben, können Sie fortfahren http://Manage.windowsazure.com, um die neuen Lizenzen zuzuweisen (Weitere Informationen finden Sie nachfolgend in Schritt 4). Wenn Sie neu bei Microsoft Azure sind, den Anmeldelink der e-Mail, oder Sie die [Zugriff auf Azure Active Directory](https://account.windowsazure.com/signup?offer=MS-AZR-0110P) gelangen Sie über eine Reihe von Schritten können Sie den Zugriff auf Ihr Verzeichnis über das Azure-Verwaltungsportal.

![][4]

Nach der erfolgreichen Anmeldung müssen Sie einen Bildschirm für eine zweistufige Authentifizierung ausfüllen (siehe unten). Hier geben Sie Ihre Mobiltelefonnummer ein und bestätigen diese. Danach werden Sie können Ihren Zugriff auf Azure Active Directory durch Auswahl aktivieren **registrieren**.

![][5]

Die Aktivierung kann einige Minuten dauern, wie unten dargestellt, sobald der Zugriff aktiv ist der braune Balken und Sie werden auf den Link "Portal" in der oberen rechten Ecke klicken, oder Navigieren zu der [Azure-Verwaltungsportal](http://manage.windowsazure.com).

![][6]

In diesem Fall ist Ihr Azure-Zugriff auf Azure Active Directory beschränkt.

![][7]

Möglicherweise hatten Sie aufgrund einer früheren Verwendung bereits Zugriff auf Azure. Außerdem können Sie Ihren Azure Active Directory-Zugriff durch Aktivierung weiterer Azure-Abonnements auf einen vollständigen Azure-Zugriff erweitern. In diesen Fällen bietet das Verwaltungsportal über die im Folgenden beschriebenen Funktionen hinaus weitere Funktionen.

![][8]

Wenn Sie versuchen, den Zugriff auf Azure Active Directory zu aktivieren, bevor Ihnen die oben erwähnte Begrüßungs-E-Mail zugestellt wurde, erhalten Sie möglicherweise folgende Fehlermeldung. Versuchen Sie die Aktivierung in diesem Fall erneut wenige Minuten nach Erhalt der E-Mail.

![][9]

Innerhalb Ihres Abonnements neue Administratoren können ihren Zugriff auf das Verwaltungsportal ebenfalls über diesen Link aktivieren.

## Schritt 4: Weisen Sie die Lizenzen Benutzerkonten zu 

Bevor Sie den erworbenen Plan verwenden können, müssen Sie die Lizenzen manuell Benutzerkonten Ihres Unternehmens zuweisen, damit diese das reichhaltige Feature-Angebot der Premium Edition nutzen können. Führen Sie zur Zuweisung von Lizenzen zu Benutzern die folgenden Schritte aus, so dass diese die Features der Premium Edition von Azure Active Directory nutzen können.

Zuweisen von Lizenzen zu Benutzern:

1. Melden Sie sich beim Verwaltungsportal als globaler Administrator des Verzeichnisses an, das Sie anpassen möchten.
2. Klicken Sie auf **Active Directory**, und wählen Sie das Verzeichnis, in dem Sie Lizenzen zuweisen möchten.
3. Wählen Sie die **Lizenzen** Registerkarte **Active Directory Premium** oder **Enterprise Mobility Suite**, und klicken Sie dann auf **Zuweisen**.

    ![][10]

4. Wählen Sie im Dialogfeld die Benutzer aus, denen Sie Lizenzen zuweisen möchten, und klicken Sie dann auf das Häkchen, um die Änderungen zu speichern.

    ![][11]

## Lizenzeinschränkungen

Es gibt Lizenzpläne, die in anderen Lizenzplänen enthalten sind oder andere Lizenzpläne enthalten. In den meisten Fällen kann einem Benutzer kein Lizenzplan zugewiesen werden, der ihm bereits zugewiesen wurde. Wenn Sie einem Benutzer einen Lizenzplan zuweisen möchten, der andere Lizenzpläne enthält, die dem Benutzer bereits zugewiesen sind, müssen Sie die bereits zugewiesenen Lizenzpläne zunächst entfernen.

## Lizenzanforderungen

Wenn Sie einem Benutzer eine Lizenz zuweisen, können Sie, wie unten gezeigt, in den Eigenschaften des Benutzerkontos einen primären Verwendungsort angeben. Wenn kein solcher Verwendungsort angegeben ist, wird dem Benutzer automatisch der Standort des Mandanten zugewiesen.

![][12]

Die Verfügbarkeit der Dienste und Features eines Microsoft Clouddiensts variieren je nach Land und Region. Ein Dienst wie Voice over IP (VoIP) kann in einem Land bzw. einer Region verfügbar sein, in einem anderen hingegen nicht. Die Features eines Diensts können aus rechtlichen Gründen in einigen Ländern oder Regionen eingeschränkt sein. Um festzustellen, ob ein Dienst oder ein Feature für Sie uneingeschränkt oder mit Einschränkungen verfügbar ist, suchen Sie Ihr Land bzw. Ihre Region auf der Site mit den Lizenzeinschränkungen des jeweiligen Diensts.

## Nächste Schritte

- [Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite](active-directory-add-company-branding.md)
- [Anzeigen von Zugriffs- und Nutzungsberichten](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png


