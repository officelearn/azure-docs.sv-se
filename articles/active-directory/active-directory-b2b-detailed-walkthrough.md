<properties
   pageTitle="Ausführliche exemplarische Vorgehensweise zur Verwendung der Vorschau der Azure Active Directory B2B-Zusammenarbeit | Microsoft Azure"
   description="Azure Active Directory B2B ermöglicht Geschäftspartnern den gezielten Zugriff auf Ihre Unternehmensanwendungen und unterstützt so Ihre unternehmensübergreifenden Beziehungen."
   services="active-directory"
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Ausführliche exemplarische Vorgehensweise zur Verwendung der Vorschau der Azure Active Directory (Azure AD) B2B-Zusammenarbeit
In dieser exemplarischen Vorgehensweise wird die Verwendung der Azure AD B2B-Zusammenarbeit beschrieben. Als IT-Administrator von Contoso möchten wir für Mitarbeiter von drei Partnerunternehmen Anwendungen freigeben. Keines der Partnerunternehmen benötigt Azure AD.

- Alice von Simple Partner Org.
- Bob von Medium Partner Org benötigt Zugriff auf eine Reihe von Apps.
- Carol von Complex Partner Org benötigt Zugriff auf eine Reihe von Apps und eine Mitgliedschaft in Gruppen von Contoso.

Nachdem Einladungen an Partnerbenutzer gesendet wurden, können wir sie in Azure AD konfigurieren, um ihnen Zugriff auf Apps und die Mitgliedschaft in Gruppen über das Azure-Portal zu gewähren. Zunächst fügen wir Alice hinzu.

## Hinzufügen von Alice zum Verzeichnis von Contoso
1. Erstellen Sie eine CSV-Datei mit den Headern, siehe Auffüllen nur von Alice **E-Mail**, **DisplayName**, und **InviteContactUsUrl**. **DisplayName** ist der Name, der in die INVITE-Nachricht angezeigt wird, und außerdem den Namen, die in Azure AD-Verzeichnis von Contoso angezeigt wird. **InviteContactUsUrl** ist eine Möglichkeit für Alice an Contoso. Im folgenden Beispiel ist das LinkedIn-Profil von Contoso angegeben. Die erste Zeile der CSV-Datei muss die Bezeichnungen in der hier dargestellten Reihenfolge und Schreibweise enthalten. Siehe Abschnitt CSV-Format.  
![Beispiel-CSV-Datei für Alice](./media/active-directory-b2b-detailed-walkthrough/AliceCSV.png)

2. Fügen Sie im Azure-Portal im Verzeichnis „Contoso“ einen Benutzer hinzu (Active Directory > Contoso > Benutzer > Benutzer hinzufügen). Wählen Sie im Dropdownmenü „Art des Benutzers“ die Option „Benutzer in Partnerunternehmen“ aus. Laden Sie die CSV-Datei hoch. Stellen Sie sicher, dass vor dem Hochladen die CSV-Datei geschlossen wird.  
![Hochladen der CSV-Datei für Alice](./media/active-directory-b2b-detailed-walkthrough/AliceUpload.png)

3. Alice wird jetzt als externer Benutzer in Azure AD-Verzeichnis von Contoso dargestellt.  
![Anzeige von Alice in Azure AD](./media/active-directory-b2b-detailed-walkthrough/AliceInAD.png)

4. Aus Sicht von Alice erhalten sie die folgende e-Mail-Adresse.  
![Einladungs-E-Mail für Alice](./media/active-directory-b2b-detailed-walkthrough/AliceEmail.png)

5. Alice klickt auf den Link und wird aufgefordert, die Einladung anzunehmen und sich mit ihren eigenen Anmeldeinformationen anzumelden. Wenn Alice nicht in Azure AD-Verzeichnis vorhanden ist, werden Alice Anmelden aufgefordert.  
![Anmeldung nach Erhalt der Einladung für Alice](./media/active-directory-b2b-detailed-walkthrough/AliceSignUp.png)

6. Alice ist die leere Zugriffsbereich App umgeleitet, bis sie apps zugreifen kann.  
![Zugriffsbereich für Alice](./media/active-directory-b2b-detailed-walkthrough/AliceAccessPanel.png)

Dies ist die einfachste Form der B2B-Zusammenarbeit. Als Benutzer im Azure AD-Verzeichnis von Contoso kann Alice Zugriff auf Anwendungen und Gruppen über das Azure-Portal gewährt werden. Jetzt fügen wir Bob hinzu, der Zugriff auf die Anwendungen Moodle und Salesforce benötigt.

## Hinzufügen von Bob zum Verzeichnis von Contoso und Gewähren des Zugriffs auf Apps
1. Verwenden Sie Windows PowerShell mit dem installierten Azure AD-Modul, um die IDs der Anwendungen Moodle und Salesforce zu ermitteln. Die IDs mit dem Cmdlet abgerufen werden können: `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` Dadurch wird eine Liste mit allen verfügbaren Anwendungen in Contoso und ihre AppPrincialIds.  
![IDs für Bob abrufen](./media/active-directory-b2b-detailed-walkthrough/BobPowerShell.png)

2. Erstellen Sie die CSV-Datei Auffüllen Bobs E-Mail, DisplayName, **InviteAppID**, **InviteAppResources**, und InviteContactUsUrl. **InviteAppResources** wird mit der AppPrincipalIds moodle kann nicht aufgefüllt und Salesforce gefunden, Windows PowerShell, getrennt durch ein Leerzeichen. Diese IDs sind im obigen PowerShell-Screenshot mit grünen bzw. blauen Rahmen hervorgehoben. **InviteAppId** wird mit den gleichen AppPrincipalId des moodle kann nicht an die e-Mail an Ihre Marke, und melden Sie sich Seiten aufgefüllt.  
![Beispiel-CSV-Datei für Bob](./media/active-directory-b2b-detailed-walkthrough/BobCSV.png)

3. Laden Sie die CSV-Datei wie zuvor die Datei für Alice über das Azure-Portal hoch. Bob ist jetzt ein externer Benutzer im Azure AD-Verzeichnis von Contoso.

4. Bob erhalten die folgenden e-Mail-Adresse.  
![Einladungs-E-Mail für Bob](./media/active-directory-b2b-detailed-walkthrough/BobEmail.png)

5. Bob klickt auf den Link und wird aufgefordert, die Einladung anzunehmen. Nachdem er angemeldet ist, kann er wird in den Zugriffsbereich weitergeleitet und bereits moodle nicht und Salesforce.  
![Zugriffsbereich für Bob](./media/active-directory-b2b-detailed-walkthrough/BobAccessPanel.png)

Als Nächstes fügen wir Carol hinzu. Sie benötigt Zugriff auf Anwendungen und eine Mitgliedschaft in Gruppen im Verzeichnis von Contoso.

## Hinzufügen von Carol zum Verzeichnis von Contoso, Gewähren des Zugriffs auf Apps und Festlegen von Gruppenmitgliedschaften

1. Verwenden Sie Windows PowerShell mit dem installierten Azure AD-Modul, um die Anwendungs-IDs und Gruppen-IDs in Contoso zu ermitteln.
 - Rufen Sie wie im Fall von Bob die AppPrincipalId mithilfe des Cmdlets `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` ab.
 - Rufen Sie die ObjectId für Gruppen mithilfe des Cmdlets `Get-MsolGroup | fl DisplayName, ObjectId` ab. Dadurch wird eine Liste mit allen Gruppen in Contoso und den zugehörigen ObjectIds angezeigt. Gruppen-IDs können auch als Objekt-ID auf der Registerkarte Eigenschaften der Gruppe im Azure-Portal abgerufen werden.  
![Abrufen von IDs und Gruppen für Frau Philips](./media/active-directory-b2b-detailed-walkthrough/CarolPowerShell.png)

2. CSV-Datei Auffüllen des Frau Philips erstellen E-Mail "," DisplayName "," InviteAppID "," InviteAppResources, **InviteGroupResources**, und InviteContactUsUrl. **InviteGroupResources** wird von der ObjectIds von Gruppen MyGroup1 und extern, getrennt durch ein Leerzeichen aufgefüllt.  
![Beispiel-CSV-Datei für Frau Philips](./media/active-directory-b2b-detailed-walkthrough/CarolCSV.png)

3. Laden Sie die CSV-Datei über das Azure-Portal hoch.

4. Frau Philips ist ein Benutzer im Verzeichnis von Contoso und ist auch ein Mitglied der Gruppen MyGroup1 und extern, wie in der Azure-Portal.  
![Anzeige von Carol in einer Gruppe in Azure AD](./media/active-directory-b2b-detailed-walkthrough/CarolGroup.png)

5. Carol erhält eine E-Mail mit einem Link zum Annehmen der Einladung. Nachdem sie sich angemeldet hat, wird sie zum App- Zugriffsbereich umgeleitet und hat Zugriff auf Moodle und Salesforce.  

So einfach ist es, Benutzer von Partnerunternehmen in Azure AD B2B-Zusammenarbeit hinzuzufügen. Diese exemplarische Vorgehensweise veranschaulicht, Hinzufügen von Alice, Bob und Carol in drei separate CSV-Dateien, aber sie können in der Tat hinzugefügt werden zusammen in eine CSV-Datei aus Gründen der Einfachheit.  
![Beispiel-CSV-Datei für Alice, Bob und Carol](./media/active-directory-b2b-detailed-walkthrough/CombinedCSV.png)

## Verwandte Artikel
Weitere Artikel zur Azure AD B2B-Zusammenarbeit:

- [Was ist die Azure AD B2B-Zusammenarbeit?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [So funktioniert's](active-directory-b2b-how-it-works.md)
- [Referenz zum CSV-Dateiformat](active-directory-b2b-references-csv-file-format.md)
- [Tokenformat für externe Benutzer](active-directory-b2b-references-external-user-token-format.md)
- [Objektattributänderungen für externe Benutzer](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Aktuelle Einschränkungen der Vorschau](active-directory-b2b-current-preview-limitations.md)


