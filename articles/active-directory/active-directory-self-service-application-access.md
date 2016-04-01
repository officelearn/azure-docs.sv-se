<properties
    pageTitle="Self-Service-Anwendungszugriff und delegierte Verwaltung mit Azure Active Directory | Microsoft Azure"
    description="Dieser Artikel beschreibt, wie der Self-Service-Anwendungszugriff und die delegierte Verwaltung mit Azure Active Directory aktiviert werden."
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
    ms.date="12/14/2015"
    ms.author="asmalser"/>

#Self-Service-Anwendungszugriff und delegierte Verwaltung mit Azure Active Directory

Das Aktivieren von Self-Service-Funktionen für Endbenutzer ist ein häufiges Szenario in IT-Unternehmen. Dort gibt es viele Benutzer und viele Anwendungen, und die am besten informierte Person, die über das Erteilen von Zugriffsrechten entscheidet, ist nicht unbedingt der Verzeichnisadministrator. Oft ist die Person, die am besten darüber entscheiden kann, wer Zugriff auf eine Anwendung erhält, ein Teamleiter oder ein anderer delegierter Administrator. Doch letzten Endes ist es der Benutzer, der die Anwendung benutzt, und nur er weiß genau, was er zum Ausführen seiner Aufgaben benötigt.

Self-Service-Zugriff ist ein Feature von [Azure Active Directory Premium](http://azure.microsoft.com/trial/get-started-active-directory/) Directory-Administratoren können:

* Aktivieren von Benutzern für die Anforderung des Zugriffs auf Programme, die mit "Get-Anwendung" Kachel in der [Azure AD-Zugriffsbereich](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)
* Festlegen, für welche Anwendungen Benutzer den Zugriff anfordern können.
* Festlegen, ob Benutzer eine Genehmigung benötigen, um sich selbst den Zugriff auf eine Anwendung zuweisen zu können.
* Festlegen, wer die Anforderungen genehmigt und den Zugriff für jede Anwendung verwaltet.

Derzeit wird diese Funktion unterstützt, für alle bereits integriert und benutzerdefinierte apps mit Unterstützung für Verbund oder Kennwort-basierte Single-Sign-on in den [Azure Active Directory-Anwendungskatalog](http://azure.microsoft.com/marketplace/active-directory/all/), einschließlich apps wie Salesforce, Dropbox, Google Apps und mehr.
Dieser Artikel beschreibt die Vorgehensweise zum

* den Self-Service-Anwendungszugriff für Endbenutzer konfigurieren, einschließlich der Konfiguration eines optionalen Genehmigungsworkflows. 
* die Zugriffsverwaltung für bestimmte Anwendungen auf die am besten geeigneten Personen in Ihrer Organisation delegieren und ihnen ermöglichen, den Azure AD-Zugriffsbereich zum Genehmigen von Zugriffsanforderungen, dem direkten Zuweisen von Zugriff für ausgewählte Benutzer oder (optional) zum Festlegen von Anmeldeinformationen für den Anwendungszugriff verwenden, wenn die kennwortbasierte einmalige Anwendung konfiguriert ist.


##Konfigurieren des Self-Service-Anwendungszugriffs

Um den Self-Service-Anwendungszugriff zu aktivieren und zu konfigurieren, welche Anwendungen von den Endbenutzern hinzugefügt oder angefordert werden können, befolgen Sie die nachstehenden Anweisungen.

**1:** Melden Sie sich bei der [klassischen Azure-Portal](https://manage.windowsazure.com/).

**2:**  unter der **Active Directory** Abschnitt, wählen Sie Ihr Verzeichnis, und wählen Sie dann die **Applikationen** Registerkarte. 

**3:** Wählen Sie die **Hinzufügen** Schaltfläche, und verwenden Sie die Option Katalog auswählen und eine Anwendung hinzufügen.

**4:** nach Ihrer app hinzugefügt wurde, erhalten Sie die app-Schnellstart-Seite. Klicken Sie auf **Konfigurieren Sie einmaliges Anmelden**, wählen Sie den gewünschten SSO-Modus und die Konfiguration zu speichern. 

**5:** Wählen Sie als Nächstes die **konfigurieren** Registerkarte. Legen Sie zum Aktivieren von Benutzern zum Anfordern des Zugriffs auf diese app über den Azure AD-Zugriffsbereich **Self-Service-Zugriff zulassen** zu **Ja**.

![][1]

**6:** um einen Genehmigungsworkflow für Anforderungen optional zu konfigurieren, legen **zugestimmt werden muss, bevor Zugriff gewährt** zu **Ja**. Und dann eine oder mehrere genehmigende Personen ausgewählt werden können, mit der **Genehmiger** Schaltfläche.

Eine genehmigende Person kann jeder Benutzer in der Organisation mit einem Azure AD-Konto sein. Er kann für die Kontobereitstellung, Lizenzierung oder einen beliebigen anderen Geschäftsprozess verantwortlich sein, den Ihre Organisation vor Gewähren des Zugriffs auf eine Anwendung erfordert. Die genehmigende Person kann sogar der Besitzer der Gruppe für eine oder mehrere freigegebene Kontogruppen sein und kann Benutzer einer dieser Gruppen zuweisen, um ihnen den Zugriff auf ein freigegebenes Konto zu gewähren. 

Wenn keine Genehmigung erforderlich ist, wird die Anwendung sofort zum Azure AD-Zugriffsbereich der Benutzer hinzugefügt. Diesem geeignet, wenn für die Anwendung festgelegt wurde [Automatische benutzerbereitstellung](active-directory-saas-app-provisioning.md), oder eingerichtet wurde ["Benutzer-verwaltet" Kennwort-SSO-Modus](active-directory-appssoaccess-whatis.md#password-based-single-sign-on) bei denen der Benutzer bereits ein Benutzerkonto und das Kennwort ist unbekannt.

**7:** wenn die Anwendung verwenden kennwortbasierten einmaligen Anmeldung konfiguriert wurde, und klicken Sie dann eine Option für das Zulassen des Genehmigers die SSO-Anmeldeinformationen für jeden Benutzer fest auch verfügbar ist. Weitere Informationen finden Sie im folgenden Abschnitt zum Delegieren der Zugriffsverwaltung.

**8:** schließlich die **für Benutzer mit Self-Assigned** zeigt den Namen der Gruppe, die verwendet wird, zum Speichern der Benutzer, die Zugriff auf die Anwendung zugewiesen oder gewährt wurden. Die Person, die den Zugriff genehmigt, wird zum Besitzer dieser Gruppe. Wenn der angezeigte Gruppenname nicht vorhanden ist, wird er automatisch erstellt. Optional kann der Gruppenname auf den Namen einer vorhandenen Gruppe festgelegt werden.

**9:** Klicken Sie auf **Speichern** am unteren Bildschirmrand, um die Konfiguration zu speichern. Jetzt können Benutzer den Zugriff auf diese Anwendung über den Zugriffsbereich anfordern.

**10:** Melden Sie sich zum Testen des Endbenutzers in der Organisation Azure AD-Zugriffsbereich unter https://myapps.microsoft.com, vorzugsweise mit einem anderen Konto, das eine app genehmigende Person ist. 

**11:** unter der **Applikationen** auf die **erhalten Sie weitere Apps** Kachel. Dadurch wird ein Katalog mit allen Anwendungen angezeigt, für die der Self-Service-Anwendungszugriff im Verzeichnis aktiviert wurde. Sie haben die Möglichkeit, auf der linken Seite nach Anwendungskategorie zu suchen und zu filtern. 

**12:** durch Klicken auf eine Anwendung startet den Prozess ab. Wenn kein Genehmigungsprozess erforderlich ist, wird die Anwendung sofort werden, unter hinzugefügt wird der **Applikationen** Registerkarte nach einer kurzen Bestätigung. Wenn eine Genehmigung erforderlich ist, sehen Sie ein entsprechendes Dialogfeld, und eine E-Mail-Nachricht wird an die genehmigenden Personen gesendet. (Kurzer Hinweis: Sie müssen im Zugriffsbereich als nicht-genehmigende Person angemeldet sein, damit dieser Anforderungsprozess angezeigt wird.)

**13:** die e-Mail-Adresse weist die genehmigende Person in den Azure AD-Zugriffsbereich anmelden und genehmigen Sie die Anforderung. Nach der Genehmigung der Anforderung (und Sie definieren besondere Prozesse durch die genehmigende Person ausgeführt wurde), sieht der Benutzer dann die Anwendung erscheinen ihre **Applikationen** können sie beim Anmelden, in diese, Registerkarte.

##Delegierte Verwaltung des Anwendungszugriffs

Eine genehmigende Person kann jeder Benutzer in der Organisation sein, die am besten dafür geeignet ist, den Zugriff auf die betreffende Anwendung freizugeben oder zu verweigern. Dieser Benutzer kann für die Kontobereitstellung, Lizenzierung oder einen beliebigen anderen Geschäftsprozess verantwortlich sein, den Ihre Organisation vor Gewähren des Zugriffs auf eine Anwendung erfordert.
 
Beim Konfigurieren von Self-Service-Zugriff, die oben beschriebenen zugewiesen genehmigenden Personen sehen Anwendung eine zusätzliche **Applikationen verwalten** Kachel im Zugriffsbereich Azure AD, die anzeigt, die sie der Administrator Zugriff für die Anwendungsmöglichkeiten. Durch Klicken auf die Anwendung erscheint ein Bildschirm mit mehreren Optionen.

![][2]

###Anforderungen genehmigen

Die **Anforderungen genehmigen** Kachel können Sie alle ausstehenden Genehmigungen, die spezifisch für die Anwendung finden Sie unter den genehmigenden Personen, und leitet Sie zur Registerkarte "Genehmigung", in dem die Anforderungen bestätigt oder verweigert werden können. Beachten Sie, dass die genehmigende Person außerdem automatisierte E-Mail-Nachrichten mit Anweisungen empfängt, sobald eine Anforderung erstellt wurde.

###Benutzer hinzufügen

Die **Hinzufügen von Benutzern** Kachel genehmigenden Personen direkt ausgewählten Benutzern Zugriff auf die Anwendung gewähren kann. Durch Klicken auf die Kachel wird genehmigenden Personen ein Dialogfeld angezeigt, durch das sie in ihrem Verzeichnis Benutzer anzeigen und diese suchen können. Durch Hinzufügen eines Benutzers wird die Anwendung im Azure AD-Zugriffsbereich oder im Office 365 dieses Benutzers angezeigt. Falls in der Anwendung ein manueller Prozess zur Benutzerbereitstellung erforderlich ist, bevor der Benutzer sich anmelden kann, sollte die genehmigende Person vor dem Zuweisen des Zugriffs zunächst diesen Prozess durchführen.  

###Benutzer verwalten

Die **Benutzer verwalten** Kachel genehmigenden Personen direkt zu aktualisieren oder zu entfernen, welche Benutzer auf die Anwendung zugreifen kann. 

###Kennwortbasierte SSO-Anmeldeinformationen konfigurieren (falls zutreffend)

Die **konfigurieren** Kachel wird nur angezeigt, wenn die Anwendung mit der IT-Administrator verwendet kennwortbasierte einmalige konfiguriert wurde, und der Administrator der genehmigende Person die Möglichkeit erteilt, SSO-Anmeldeinformationen festzulegen, wie zuvor beschrieben. Bei Auswahl dieser Möglichkeit erhält die genehmigende Person mehrere Optionen, wie die Anmeldeinformationen an zugewiesene Benutzer weitergegeben werden:

![][3]

* **Benutzer melden Sie sich mit ihrer eigenen Kennwörter** – In diesem Modus der zugewiesenen Benutzer wissen, was ihre Benutzernamen und Kennwörter werden für die Anwendung und werden aufgefordert, die sie bei ihrer ersten Anmeldung der Anwendung eingeben. Dies entspricht der Kennwort-SSO-Fall, in dem die [Benutzer Anmeldeinformationen verwalten](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

* **Benutzer werden automatisch abgemeldet, in separate Konten verwenden, die ich verwalte** – In diesem Modus kann die Benutzer nicht verlangt werden eingeben oder kennen ihre app-spezifische Anmeldeinformationen beim Anmelden bei der Anwendung. Die genehmigende Person legt die Anmeldeinformationen für jeden Benutzer nach dem Zuweisen der Zugriff über die **Add User** Kachel. Klickt der Benutzer im Zugriffsbereich oder in Office 365 auf die Anwendung, wird er automatisch mit den von der genehmigenden Person festgelegten Anmeldeinformationen angemeldet. Dies entspricht der Kennwort-SSO-Fall, in dem die [Administratoren verwalten von Anmeldeinformationen](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

* **Benutzer werden automatisch abgemeldet, mit einem einzelnen Konto, die ich verwalte** – Dies ist ein Sonderfall, und verwendet, wenn alle Benutzer müssen den werden zugewiesen gewährt Zugriff mithilfe eines einzelnen freigegebenen Kontos. Am häufigsten tritt dies bei Social Media-Anwendungen auf, bei denen eine Organisation ein einzelnes „Unternehmenskonto“ besitzt und mehrere Benutzer darin Aktualisierungen vornehmen. Dies entspricht auch der Kennwort-SSO-Fall, in dem die [Administratoren verwalten von Anmeldeinformationen](active-directory-appssoaccess-whatis.md#password-based-single-sign-on). Nachdem diese Option ausgewählt wurde, wird die genehmigende Person jedoch dazu aufgefordert, den Benutzernamen und das Kennwort für das einzelne freigegebene Konto einzugeben. Nach Fertigstellung werden alle zugewiesenen Benutzer mit diesem Konto angemeldet, wenn sie in ihrem Azure AD-Zugriffsbereich oder in Office 365 auf die Anwendung klicken.

<!--Image references-->
[1]: ./media/active-directory-self-service-application-access/ssaa_admin.PNG
[2]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app.PNG
[3]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app_config.PNG


