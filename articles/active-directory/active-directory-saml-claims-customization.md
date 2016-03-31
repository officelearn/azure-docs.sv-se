<properties
    pageTitle="Anpassen ausgestellter Ansprüche im SAML-Token für bereits in Azure Active Directory integrierte Apps | Microsoft Azure"
    description="Erfahren Sie, wie Sie im SAML-Token für bereits in Azure Active Directory integrierte Apps ausgestellte Ansprüche anpassen."
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
    ms.date="11/18/2015"
    ms.author="asmalser"/>

#Anpassen ausgestellter Ansprüche im SAML-Token für bereits in Azure Active Directory integrierte Apps

Azure Active Directory unterstützt heute Tausende von bereits integrierten Anwendungen im Azure AD-Anwendungskatalog, darunter über 150, die einmaliges Anmelden mithilfe des SAML 2.0-Protokolls unterstützen. Wenn ein Benutzer sich über Azure AD per SAML bei einer Anwendung authentifiziert, sendet Azure AD (über eine HTTP 302-Umleitung) ein Token an die Anwendung, welches die Anwendung dann überprüft und anstelle von Benutzername und Kennwort für die Anmeldung des Benutzers verwendet. Diese SAML-Token enthalten Informationen über den Benutzer, die als „Ansprüche“ bezeichnet werden.

Im technischen Jargon bezeichnet ein Anspruch (Claim) Informationen über den Benutzer, die ein Identitätsanbieter im Benutzertoken übergibt. In einem [SAML-Token](http://en.wikipedia.org/wiki/SAML_2.0), diese Daten in der Regel in der SAML-Attribut-Anweisung enthalten ist und eindeutige ID des Benutzers wird in der Regel in der SAML-Betreff dargestellt.

Standardmäßig stellt Azure AD der Anwendung ein SAML-Token aus, das einen NameIdentifier-Anspruch enthält, dessen Wert dem Benutzernamen in Azure AD entspricht (über den der Benutzer eindeutig identifiziert ist). Das SAML-Token enthält auch zusätzliche Ansprüche, die E-Mail-Adresse des Benutzers, Vorname und Nachname enthält.

Zum Anzeigen oder bearbeiten die Ansprüche, die für die Anwendung im SAML-Token ausgestellt, öffnen Sie den Anwendungsdatensatz im Azure-Portal und wählen Sie die **Attribute** Registerkarte unterhalb der Anwendung.

![][1]

Es gibt zwei mögliche Gründe, warum Sie möglicherweise die Ansprüche im SAML-Token ausgestellt bearbeiten:
• Die dienstanwendung wurde entwickelt, um erfordern einen anderen Satz von Anspruchs-URIs oder Werte von Ansprüchen 
•Your Anwendung wurde in einer Weise bereitgestellt, die den Anspruch "NameIdentifier" etwas anderes als den Benutzernamen (AKA User principal Name) in Azure Active Directory gespeichert werden muss. 

Jeder der standardmäßigen Anspruchswerte lässt sich durch Auswählen des Stiftsymbols bearbeiten, das auf der rechten Seite angezeigt wird, wenn sich der Mauszeiger über einer der Zeilen in der Tabelle der SAML-Tokenattribute befindet. Ansprüche (mit Ausnahme des NameIdentifier) mithilfe von können Sie auch Entfernen der **X** -Symbol, und fügen Sie neue Ansprüche, die mithilfe der **Benutzerattribut hinzufügen** Schaltfläche.

##Bearbeiten des NameIdentifier-Anspruchs

Falls die Anwendung mit einem anderen Benutzernamen bereitgestellt wurde, klicken Sie auf das Stiftsymbol neben dem NameIdentifier-Anspruch. Dadurch wird ein Dialogfeld mit mehreren Optionen angezeigt:

![][2]

In der **Attributwert** Klicken Sie im Menü **user.mail** festlegen den Anspruch "NameIdentifier"-e-Mail-Adresse des Benutzers im Verzeichnis, oder wählen Sie **user.onpremisessamaccountname** fest, die dem Benutzer des SAM-Kontonamen, die von einer lokalen Azure AD synchronisiert wurde. 

Die spezielle ExtractMailPrefix()-Funktion können auch um das Domänensuffix aus die e-Mail-Adresse oder den Benutzerprinzipalnamen führt nur den ersten Teil des Benutzernamens weitergegeben (z. B. "Joesmith" statt joesmith@contoso.com) zu entfernen.

![][3]

##Hinzufügen von Ansprüchen

Beim Hinzufügen eines neuen Anspruchs kann der Attributname angegeben werden (der nicht unbedingt einem URI-Muster gemäß SAML-Spezifikation entsprechen muss), und der Wert kann auf jedes im Verzeichnis gespeicherte Benutzerattribut festgelegt werden. 

![][4]

Angenommen, Sie möchten die Abteilung, zu der der Benutzer gehört in ihrer Organisation als Anspruch senden (z. B. Verkauf), geben Sie können den Anspruchswert werden von der Anwendung erwartet, und wählen Sie dann **user.department** als Wert.

Wenn für einen bestimmten Benutzer kein Wert für ein ausgewähltes Attribut gespeichert ist, dann wird dieser Anspruch nicht in dem Token ausgestellt.

**Hinweis:** der **user.onpremisesecurityidentifier** und **user.onpremisesamaccountname** werden nur unterstützt, wenn Synchronisieren von Daten aus Active Directory über die aktuelle Vorschau des AAD Connect-Tools vor Ort. Sie können die Vorschau des Connect-Tools unter folgendem Link herunterladen:  

http://Connect.Microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadId=53949
    
<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ./media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ./media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ./media/active-directory-saml-claims-customization/claimscustomization4.png


