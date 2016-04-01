<properties 
    pageTitle="Erste Schritte: Azure AD-Kennwortverwaltung | Microsoft Azure" 
    description="Ermöglichen Sie es Benutzern, ihre eigenen Kennwörter zurückzusetzen, ermitteln Sie Voraussetzungen für die Kennwortzurücksetzung, und aktivieren Sie das Zurückschreiben von Kennwörtern zum Verwalten lokaler Kennwörter in Active Directory." 
    services="active-directory" 
    documentationCenter="" 
    authors="asteen" 
    manager="kbrint" 
    editor="billmath"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/16/2015" 
    ms.author="asteen"/>

# Erste Schritte mit der Kennwortverwaltung
Es sind lediglich einige einfache Schritte erforderlich, um Ihren Benutzern das Verwalten eigener Cloudkennwörter für Azure Active Directory oder lokaler Kennwörter für Active Directory zu ermöglichen. Nachdem Sie sichergestellt haben, dass einige wenige Voraussetzungen erfüllt sind, haben Sie im Handumdrehen eine Kennwortänderung und -zurücksetzung für Ihre gesamte Organisation eingerichtet. In diesem Artikel lernen Sie die folgenden Konzepte kennen:

* [**Vorgehensweise beim Zurücksetzen ihrer cloudkennwörter für Azure Active Directory-Benutzer können**](#enable-users-to-reset-their-azure-ad-passwords)
 - [Voraussetzungen für die Self-Service-Kennwortzurücksetzung](#prerequisites)
 - [Schritt 1: Konfigurieren der Richtlinie für die Kennwortzurücksetzung](#step-1-configure-password-reset-policy)
 - [Schritt 2: Hinzufügen von Kontaktdaten für Ihren Testbenutzer](#step-2-add-contact-data-for-your-test-user)
 - [Schritt 3: Zurücksetzen Ihres Kennworts als Benutzer](#step-3-reset-your-azure-ad-password-as-a-user)
* [**Gewusst wie: Zurücksetzen oder ändern die Kennwörter ihrer lokalen Active Directory-Benutzer aktivieren**](#enable-users-to-reset-or-change-their-ad-passwords)
 - [Voraussetzungen für das Zurückschreiben von Kennwörtern](#writeback-prerequisites)
 - [Schritt 1: Herunterladen der aktuellen Version von Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
 - [Schritt 2: Aktivieren der Kennwortrückschreibung in Azure AD Connect über die Benutzeroberfläche oder PowerShell mit anschließender Überprüfung](#step-2-enable-password-writeback-in-azure-ad-connect)
 - [Schritt 3: Konfigurieren der Firewall](#step-3-configure-your-firewall)
 - [Schritt 4: Einrichten der geeigneten Berechtigungen](#step-4-set-up-the-appropriate-active-directory-permissions)
 - [Schritt 5: Zurücksetzen Ihres AD-Kennworts als Benutzer mit anschließender Überprüfung](#step-5-reset-your-ad-password-as-a-user)

## Aktivieren von Benutzern für das  Zurücksetzen ihrer Azure AD-Kennwörter
In diesem Abschnitt werden Sie durch den Vorgang zum Aktivieren der Self-Service-Kennwortzurücksetzung für Ihr AAD-Cloudverzeichnis, das Registrieren von Benutzern für die Self-Service-Kennwortzurücksetzung und das Testen einer Self-Service-Kennwortzurücksetzung als ein Benutzer geleitet.

- [Voraussetzungen für die Self-Service-Kennwortzurücksetzung](#prerequisites)
- [Schritt 1: Konfigurieren der Richtlinie für die Kennwortzurücksetzung](#step-1-configure-password-reset-policy)
- [Schritt 2: Hinzufügen von Kontaktdaten für Ihren Testbenutzer](#step-2-add-contact-data-for-your-test-user)
- [Schritt 3: Zurücksetzen Ihres Kennworts als Benutzer](#step-3-reset-your-azure-ad-password-as-a-user)


###  Voraussetzungen
Bevor Sie die Self-Service-Kennwortzurücksetzung aktivieren und verwenden können, müssen Sie die folgenden Voraussetzungen erfüllen:

- Erstellen Sie einen AAD-Mandanten. Weitere Informationen finden Sie unter [Erste Schritte mit Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/)
- Schließen Sie ein Azure-Abonnement ab. Weitere Informationen finden Sie unter [Neuigkeiten von Azure AD-Mandant?](active-directory-administer.md#what-is-an-azure-ad-tenant).
- Ordnen Sie Ihren AAD-Mandanten Ihrem Azure-Abonnement zu. Weitere Informationen finden Sie unter [wie Azure-Abonnements mit Azure AD verknüpft sind](https://msdn.microsoft.com/library/azure/dn629581.aspx).
- Führen Sie ein Upgrade auf Azure AD Premium oder Basic durch. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](http://azure.microsoft.com/pricing/details/active-directory/).

  >[AZURE.NOTE] Um Self-service-kennwortzurücksetzung zu aktivieren, müssen Sie auf Azure AD Premium oder Basic Azure AD aktualisieren. Weitere Informationen finden Sie im Artikel zu den verschiedenen Azure Active Directory-Editionen. Hier finden Sie u. a. ausführliche Informationen dazu, wie Sie sich für Azure AD Premium oder Basic registrieren, wie Sie Ihren Lizenzplan und den Azure AD-Zugriff aktivieren, und wie Sie Administratoren und Benutzerkonten Zugriff gewähren.
  
- Erstellen Sie mindestens ein Administratorkonto und ein Benutzerkonto in Ihrem AAD-Verzeichnis.
- Weisen Sie dem erstellten Administrator- und Benutzerkonto eine AAD Premium- oder Basic-Lizenz zu.

### Schritt 1: Konfigurieren der Richtlinie für die Kennwortzurücksetzung
Führen Sie die folgenden Schritte aus, um die Richtlinie für das Zurücksetzen von Benutzerkennwörtern zu konfigurieren:
 
1.  Öffnen Sie einen Browser Ihrer Wahl, und wechseln Sie zu der [Azure-Verwaltungsportal](https://manage.windowsazure.com).
2.  In der [Azure-Verwaltungsportal](https://manage.windowsazure.com), suchen Sie die **Active Directory-Erweiterung** auf der Navigationsleiste auf der linken Seite.

    ![][001]

3. Unter dem **Verzeichnis** auf das Verzeichnis, in dem Sie die Richtlinie für die kennwortzurücksetzung, z. B. konfigurieren möchten, Wingtip Toys.

    ![][002]

4.  Klicken Sie auf die **konfigurieren** Registerkarte.

    ![][003]

5.  Unter den **konfigurieren** Registerkarte, führen Sie einen Bildlauf nach unten, um die **Richtlinie zum Zurücksetzen des Benutzerkennworts** Abschnitt.  Hier können Sie alle Aspekte der Richtlinie zum Zurücksetzen von Benutzerkennwörtern für ein vorgegebenes Verzeichnis konfigurieren.  

    >[AZURE.NOTE] Diese **Richtlinie gilt nur für Endbenutzer in Ihrer Organisation, die nicht Administratoren**. Aus Sicherheitsgründen steuert Microsoft die Richtlinie zum Zurücksetzen von Administratorkennwörtern. Wenn Sie diesen Abschnitt nicht sehen, stellen Sie sicher, dass Sie für die Azure Active Directory Premium oder Basic registriert haben und **eine Lizenz zugewiesen** auf das Administratorkonto, das Konfigurieren dieser Funktion. 

    ![][004]

6.  Schieben Sie zum Konfigurieren der Richtlinie für die kennwortzurücksetzung die **Benutzer, für das Zurücksetzen des Kennworts aktiviert** wechseln die **Ja** festlegen.  Es werden verschiedene weitere Steuerelemente angezeigt, mit deren Hilfe Sie die Funktionsweise dieses Features in Ihrem Verzeichnis konfigurieren können.  Passen Sie die Kennwortzurücksetzung nach Ihren Anforderungen an.  Wenn Sie mehr erfahren möchten zur Funktionsweise der einzelnen des Kennworts Steuerelemente kennwortzurücksetzungsrichtlinie, finden Sie unter [anpassen: Azure AD-Kennwortverwaltung](active-directory-passwords-customize).

    ![][005]

7.  Nach dem Konfigurieren von Richtlinien wie gewünscht für Ihren Mandanten Zurücksetzen des Benutzerkennworts klicken Sie auf die **Speichern** am unteren Bildschirmrand.

  >[AZURE.NOTE] Eine Überprüfung in zwei Schritten Kennwortrichtlinie Zurücksetzen wird empfohlen, damit Sie sehen, wie die Funktionalität in den komplexesten Fall funktioniert.

  ![][006]

### Schritt 2: Hinzufügen von Kontaktdaten für Ihren Testbenutzer
Sie haben mehrere Möglichkeiten, für Organisationsbenutzer Daten einzugeben, die für das Zurücksetzen von Kennwörtern verwendet werden.

-   Bearbeiten von Benutzern in der [Azure-Verwaltungsportal](https://manage.windowsazure.com) oder [Verwaltungsportal von Office 365](https://portal.microsoftonline.com)
-   Verwenden Sie AAD Connect, um Benutzereigenschaften aus einer lokalen Active Directory-Domäne mit Azure AD zu synchronisieren.
-   Verwenden Sie Windows PowerShell zum Bearbeiten der Benutzereigenschaften.
-   Ermöglicht es Benutzern, ihre eigenen Daten zu registrieren, auf dem registrierungsportal unter [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
-   Sollen Benutzer sich registrieren, für die kennwortzurücksetzung bei Anmeldung im Zugriffsbereich: [http://myapps.microsoft.com](http://myapps.microsoft.com) durch Festlegen der **Benutzer bei der Anmeldung im Zugriffsbereich registrieren müssen** sspr-Konfiguration auf **Ja**.

Sie möchten erfahren Sie mehr darüber, welche Daten durch das Zurücksetzen von Kennwörtern als auch formatierungsanforderungen für diese Daten verwendet wird, finden Sie unter [welche Daten durch das Zurücksetzen von Kennwörtern verwendet werden?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

#### So fügen Sie über das Benutzerregistrierungsportal Benutzerkontaktdaten hinzu
1.  Um die Registrierung des kennwortzurücksetzungsportal zu verwenden, müssen Sie die Benutzer in Ihrer Organisation mit einem Link zu dieser Seite bereitstellen ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) oder aktivieren Sie die Option zur automatischen Registrierung erforderlich sein.  Wenn die Benutzer auf diesen Link klicken, werden sie aufgefordert, sich mit ihrem Organisationskonto anzumelden.  Anschließend wird die folgende Seite angezeigt:

    ![][007]

2.  Hier können Benutzer ihre Mobiltelefonnummer, eine alternative E-Mail-Adresse oder Sicherheitsfragen angeben.  Der folgende Screenshot zeigt Überprüfung mit einem Mobiltelefon.

    ![][008]

3.  Nachdem ein Benutzer diese Informationen angegeben hat, wird die Seite aktualisiert um anzuzeigen, dass die Informationen gültig sind (nachstehend geschwärzt).  Durch Klicken auf die **Fertig stellen** oder **Abbrechen** Schaltflächen, wird der Benutzer zum Zugriffsbereich geschaltet.

    ![][009]

4.  Nachdem ein Benutzer die Informationen bestätigt hat, wird das Profil mit den bereitgestellten Daten aktualisiert.  In diesem Beispiel die **Telefon (Büro)** Anzahl angegeben wurde manuell, damit der Benutzer, die auch als Kontaktmethode für das Zurücksetzen des Kennworts verwenden kann.

    ![][010]

### Schritt 3: Zurücksetzen Ihres AD-Kennworts als Benutzer mit anschließender Überprüfung
Nachdem Sie eine Richtlinie für die Kennwortzurücksetzung konfiguriert und Kontaktdaten für Ihren Benutzer angegeben haben, kann dieser Benutzer eine Self-Service-Kennwortzurücksetzung durchführen.

#### So führen Sie eine Self-Service-Kennwortzurücksetzung durch
1.  Wenn Sie eine Website wie z. B. [**portal.microsoftonline.com**](http://portal.microsoftonline.com), sehen Sie einen Anmeldebildschirm wie der unten.  Klicken Sie auf die **kann nicht auf Ihr Konto zugreifen?** Link zum Testen der zurücksetzen Benutzeroberfläche.

    ![][011]

2.  Nach dem Klicken auf **kann nicht auf Ihr Konto zugreifen?**, Sie auf eine neue Seite, der Sie gefragt werden umgeleitet, ein **Benutzer-ID** für die ein Kennwort zurückgesetzt werden sollen.  Geben Sie Ihre **Benutzer-ID** hier die captchaprüfung aus, und klicken Sie auf **Weiter**.

    ![][012]

3.  Da der Benutzer angegeben hat eine **Telefon (Büro)**, **Mobiltelefon**, und **Alternative e-Mail-Adresse** Sie finden Sie in diesem Fall, dass er alle diese Optionen für die erste Abfrage zugewiesen wurde.

    ![][013]

4.  In diesem Fall auch **Aufrufen** die **Telefon (Büro)** erste.  Beachten Sie, dass bei der Auswahl einer telefonbasierten Methode Benutzer gebeten werden **verifizieren Sie ihre Telefonnummer** bevor sie ihre Kennwörter zurücksetzen können.  Auf diese Weise wird verhindert, dass böswillige Benutzer die Telefonnummern von Benutzern in Ihrer Organisation nicht für ein Spamming missbrauchen.

    ![][014]

5.  Nachdem der Benutzer die Telefonnummer bestätigt hat, erscheint nach Auswahl der Anrufoption ein Drehfeld, und das Telefon des Benutzers klingelt.  Eine Nachricht wird wiedergegeben, sobald Ihr Telefon zeigt an, dass er übernimmt die **Benutzer # drücken "** um sein eigenes Konto zu überprüfen.  Durch das Drücken der #-Taste wird automatisch bestätigt, dass der Benutzer den ersten Überprüfungsschritt erfolgreich ausgeführt hat, und die Benutzeroberfläche wechselt zum zweiten Überprüfungsschritt. 

    ![][015]

6.  Nachdem Sie den ersten Überprüfungsschritt erfolgreich ausgeführt haben, wird die Benutzeroberfläche automatisch aktualisiert, um die erste Überprüfungsmethode aus der Auswahlliste zu entfernen.  In diesem Fall, da Sie verwendet die **Telefon (Büro)**nur erste **Mobiltelefon** und **Alternative e-Mail-Adresse** verbleiben als gültige Optionen, die Herausforderung für den zweiten Überprüfungsschritt verwendet.  Klicken Sie auf die **E-Mail an meine alternative** e-Mail-Option.  Nachdem Sie dies getan haben, wird beim Klicken auf "E-Mail" eine E-Mail an die alternative E-Mail-Adresse gesendet.

    ![][016]

7.  Nachstehend sehen Sie ein Beispiel für eine E-Mail, die der Benutzer erhält. Beachten Sie hierbei das Branding für den Mandanten:

    ![][017]

8.  Nach dem Empfang der E-Mail wird die Seite aktualisiert, und Sie können den in der E-Mail enthaltenen Code in das nachstehend gezeigte Textfeld eingeben.  Nach der erfolgreichen Eingabe des Codes wird die Schaltfläche "Weiter" angezeigt, und Sie können den zweiten Überprüfungsschritt abschließen.

    ![][018]

9.  Nachdem Sie die Anforderungen der Organisationsrichtlinie erfüllt haben, sind Sie jetzt berechtigt, ein neues Kennwort auszuwählen.  Das Kennwort überprüft anhand der erfüllt AAD-Anforderungen für die "sichere" Kennwörter (finden Sie unter [Kennwortrichtlinie in Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx)), und eine Kennwortstärke zeigt dem Benutzer an, ob das eingegebene Kennwort dieser Richtlinie entspricht.

    ![][019]

10. Nachdem Sie übereinstimmende Kennwörter eingegeben haben, die der Organisationsrichtlinie entsprechen, wird das Kennwort zurückgesetzt, und Sie können sich sofort mit dem neuen Kennwort anmelden.

    ![][020]


## Aktivieren von Benutzern für das Zurücksetzen ihrer AD-Kennwörter

In diesem Abschnitt werden Sie durch den Vorgang zum Konfigurieren der Kennwortzurücksetzung für das Zurückschreiben von Kennwörtern in ein lokales Active Directory-Verzeichnis geleitet.

- [Voraussetzungen für das Zurückschreiben von Kennwörtern](#writeback-prerequisites)
- [Schritt 1: Herunterladen der aktuellen Version von Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
- [Schritt 2: Aktivieren der Kennwortrückschreibung in Azure AD Connect über die Benutzeroberfläche oder PowerShell mit anschließender Überprüfung](#step-2-enable-password-writeback-in-azure-ad-connect)
- [Schritt 3: Konfigurieren der Firewall](#step-3-configure-your-firewall)
- [Schritt 4: Einrichten der geeigneten Berechtigungen](#step-4-set-up-the-appropriate-active-directory-permissions)
- [Schritt 5: Zurücksetzen Ihres AD-Kennworts als Benutzer mit anschließender Überprüfung](#step-5-reset-your-ad-password-as-a-user)


### Voraussetzungen für das Zurückschreiben von Kennwörtern
Bevor Sie das Zurückschreiben von Kennwörtern aktivieren und verwenden können, müssen Sie die folgenden Voraussetzungen erfüllen:

- Sie verfügen über einen Azure AD-Mandanten, für den Azure AD Premium aktiviert ist.  Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).
- Für Ihren Mandanten wurde die Kennwortzurücksetzung konfiguriert und aktiviert.  Weitere Informationen finden Sie unter [können Benutzer ihre Azure AD-Kennwörter zurücksetzen](#enable-users-to-reset-their-azure-ad-passwords)
- Sie verfügen über mindestens ein Administratorkonto und ein Testbenutzerkonto mit einer Azure AD Premium-Lizenz zum Testen dieser Funktion.  Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).

  > [AZURE.NOTE] Stellen Sie sicher, dass das Administratorkonto, das Sie verwenden, um das Rückschreiben von Kennwörtern zu aktivieren, ein cloudadministratorkonto (erstellt in Azure AD), kein verbundkonto ist (erstellt im lokalen Active Directory und in Azure AD synchronisiert).
  
- Sie verfügen über eine lokale AD-Bereitstellung mit einer oder mehreren Gesamtstrukturen, in der Windows Server 2008, Windows Server 2008 R2, Windows Server 2012 oder Windows Server 2012 R2 mit den aktuellen Service Packs ausgeführt wird.

  > [AZURE.NOTE] Wenn Sie eine ältere Version von Windows Server 2008 oder 2008 R2 ausführen, Sie können diese Funktion verwenden, jedoch müssen [KB 2386717 herunterladen und installieren](https://support.microsoft.com/kb/2386717) damit Sie Ihre lokale AD-Kennwortrichtlinie in der Cloud zu erzwingen.
  
- Sie haben Azure AD Connect installiert und Ihre AD-Umgebung für eine Synchronisierung mit der Cloud vorbereitet.  Weitere Informationen finden Sie unter [Verwenden Ihrer lokalen Identitätsinfrastruktur in der Cloud](active-directory-aadconnect.md).

  > [AZURE.NOTE] Bevor Sie das Rückschreiben von Kennwörtern testen, stellen Sie sicher, dass Sie zunächst einen vollständigen Import und eine vollständige Synchronisierung von Active Directory und Azure AD in Azure AD Connect.

- Wenn Sie Azure AD Sync oder Azure AD Connect verwenden  **TCP 443** ausgehend (und in einigen Fällen **TCP 9350-9354**) geöffnet sein.  Finden Sie unter [Schritt 3: Konfigurieren der Firewall](#step-3-configure-your-firewall) Weitere Informationen. Die Verwendung von DirSync für dieses Szenario wird nicht mehr unterstützt.  Wenn Sie DirSync weiterhin verwenden möchten, aktualisieren Sie auf die neueste Version von Azure AD Connect,  bevor Sie das Zurückschreiben von Kennwörtern bereitstellen.

  > [AZURE.NOTE] Es wird dringend empfohlen die Azure AD Sync oder Dirsync-Upgrade auf die neueste Version von Azure AD Connect verwenden, um die bestmögliche Umgebung und die neuen Funktionen zu gewährleisten, sobald sie freigegeben werden.
  

### Schritt 1: Herunterladen der aktuellen Version von Azure AD Connect
Das Rückschreiben von Kennwörtern steht in Versionen von Azure AD Connect oder im Azure AD Sync-Tool mit Versionsnummer **1.0.0419.0911** oder höher.  Entsperren Sie das Rückschreiben von Kennwörtern mit automatischer steht in Versionen von Azure AD Connect oder im Azure AD Sync-Tool mit Versionsnummer **1.0.0485.0222** oder höher. Wenn Sie eine ältere Version verwenden, führen Sie ein Upgrade auf diese oder eine höhere Version durch, bevor Sie fortfahren. [Klicken Sie hier, um die neueste Version von Azure AD Connect herunterzuladen](active-directory-aadconnect.md#download-azure-ad-connect).

#### So überprüfen Sie die Version von Azure AD Sync
1.  Navigieren Sie zu **%ProgramFiles%\Azure Active Directory Sync\ **.
2.  Suchen Sie die **ConfigWizard.exe** ausführbare Datei.
3.  Mit der rechten Maustaste in der ausführbare Datei, und wählen Sie die **Eigenschaften** Option im Kontextmenü.
4.  Klicken Sie auf die **Details** Registerkarte.
5.  Suchen Sie die **Dateiversion** Feld.

    ![][021]

Ist diese Versionsnummer größer als oder gleich **1.0.0419.0911**, oder Sie Azure AD Connect installieren, können Sie fortfahren mit [Schritt2: Aktivieren der Kennwortrückschreibung in Azure AD Connect über die Benutzeroberfläche oder Powershell, und vergewissern Sie sich](#step-2-enable-password-writeback-in-azure-ad-connect). 

 > [AZURE.NOTE] Ist dies zum ersten Mal mit dem Azure AD Connect installieren, wird empfohlen, dass Sie einige bewährte Methoden zur Vorbereitung der Umgebung für die Verzeichnissynchronisation folgen.  Vor der Installation des Azure AD Connect-Tools müssen Sie verzeichnissynchronisierung entweder in Aktivieren der [Verwaltungsportal von Office 365](https://portal.microsoftonline.com) oder [Azure-Verwaltungsportal](https://manage.windowsazure.com).  Weitere Informationen finden Sie unter [Verwalten von Azure AD Connect](active-directory-aadconnect-whats-next.md).


### Schritt 2: Aktivieren der Kennwortrückschreibung in Azure AD Connect
Nun, da Sie das Azure AD Connect-Tool heruntergeladen haben, können Sie das Zurückschreiben von Kennwörtern aktivieren.  Hierzu stehen zwei Möglichkeiten zur Verfügung.  Sie können das Zurückschreiben von Kennwörtern entweder im Bildschirm für die optionalen Features im Setup-Assistenten für Azure AD Connect aktivieren, oder Sie verwenden zur Aktivierung Windows PowerShell.

#### So aktivieren Sie das Zurückschreiben von Kennwörtern im Konfigurations-Assistenten
1.  Auf der **verzeichnissynchronisierungscomputer**, öffnen Sie die **Azure AD Connect** Konfigurations-Assistenten.
2.  Klicken Sie auf die Schritte, bis zu der **optionale Features** Konfigurationsbildschirm.
3.  Überprüfen Sie die **Kennwort Zurückschreiben** Option.

    ![][022]

4.  Schließen Sie den Assistenten ab. Auf der letzten Seite werden die Änderungen zusammengefasst. Dort wird auch die Änderung zur Aktivierung der Kennwortrückschreibung angezeigt.

> [AZURE.NOTE] Sie können das Rückschreiben von Kennwörtern jederzeit deaktivieren, indem Sie entweder diesen Assistenten erneut ausführen und die Funktion oder durch Festlegen der **Kennwörter zurück zum lokalen Verzeichnis schreiben** auf **keine** in der **Richtlinie zum Zurücksetzen des** Teil Ihres Verzeichnisses **konfigurieren** Registerkarte der [Azure-Verwaltungsportal](https://manage.windowsazure.com).  Weitere Informationen zum Anpassen Ihrer Erfahrung zurücksetzen, Auschecken [anpassen: Azure AD-Kennwortverwaltung](active-directory-passwords-customize.md).

#### So aktivieren Sie das Zurückschreiben von Kennwörtern mithilfe von Windows PowerShell
1.  Auf der **verzeichnissynchronisierungscomputer**, öffnen Sie eine neue **Windows PowerShell-Fenster mit erhöhten Rechten**.
2.  Wenn das Modul nicht bereits geladen ist, geben Sie den `Import-Module ADSync`-Befehl ein, um die Azure AD Connect-Cmdlets in Ihre aktuelle Sitzung zu laden.
3.  Rufen Sie eine Liste der AAD-Connectors in Ihrem System ab, indem Sie das `Get-ADSyncConnector`-Cmdlet ausführen und die Ergebnisse in `$aadConnectorName` speichern.
4.  Rufen Sie den aktuellen Status der Rückschreibung für den aktuellen Connector ab, indem Sie das folgende Cmdlet ausführen: `Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName`
5.  Aktivieren Sie das Zurückschreiben von Kennwörtern, indem Sie dieses Cmdlet ausführen: `Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName –Enable $true`

> [AZURE.NOTE] Nach Anmeldeinformationen gefragt werden, stellen sicher, dass das Administratorkonto, das für AzureADCredential angegeben ist eine **cloudadministratorkonto (erstellt in Azure AD)**, kein verbundkonto (erstellt im lokalen AD und in Azure AD synchronisiert.
> [AZURE.NOTE] Sie können das Rückschreiben von Kennwörtern über PowerShell deaktivieren, indem Sie die gleichen Schritte wiederholen, oben, aber das Übergeben von `$false` oder durch Festlegen der **Kennwörter zurück zum lokalen Verzeichnis schreiben** auf **keine** in der **Richtlinie zum Zurücksetzen des Abschnitts** Ihres Verzeichnisses **konfigurieren** Registerkarte der [Azure-Verwaltungsportal](https://manage.windowsazure.com).

#### Überprüfen, ob die Konfiguration erfolgreich war
Nachdem Sie die Konfiguration abgeschlossen haben, wird im Windows PowerShell-Fenster die Meldung "Das Zurückschreiben der Kennwortzurücksetzung ist aktiviert" angezeigt, in der Konfigurationsoberfläche wird eine Erfolgsmeldung eingeblendet. 

Sie können auch überprüfen, der Dienst ordnungsgemäß installiert wurde, durch Öffnen der Ereignisanzeige, zum Anwendungsereignisprotokoll navigieren und Suchen nach Ereignis **31005 - OnboardingEventSuccess** von der Quelle **PasswordResetService**.

  ![][023]

### Schritt 3: Konfigurieren der Firewall
Nachdem Sie das Zurückschreiben von Kennwörtern in Azure AD Connect aktiviert haben, müssen Sie sicherstellen, dass der Dienst eine Verbindung mit der Cloud herstellen kann.

1.  Wenn Sie nach dem Abschluss der Installation in Ihrer Umgebung unbekannte ausgehende Verbindungen blockieren, müssen Sie außerdem Änderungen an den folgenden Regeln Ihrer Firewall vornehmen. Stellen Sie sicher, dass Sie Ihren AAD Connect-Computer nach dem Durchführen dieser Änderungen neu starten:
   - Lassen Sie ausgehende Verbindungen über TCP-Port 443 zu.
   - Ausgehende Verbindungen auf https://ssprsbprodncu-sb.accesscontrol.windows.net/ zulassen 
   - Wenn Sie einen Proxy verwenden oder allgemeine Verbindungsprobleme vorliegen, lassen Sie ausgehende Verbindungen über TCP-Port 9350-9354 zu.

### Schritt 4: Einrichten der geeigneten Active Directory-Berechtigungen
Für jede Gesamtstruktur, die Benutzer enthält, deren Kennwörter zurückgesetzt werden, wenn X das Konto ist, die für diese Gesamtstruktur im Konfigurations-Assistenten (während der Erstkonfiguration) angegeben wurde, und dann X muss angegeben werden die **Kennwort zurücksetzen**, **Kennwort ändern**, **Schreibberechtigungen** auf `lockoutTime`, und **Schreibberechtigungen** auf `pwdLastSet`, erweiterten Rechte für das Stammobjekt jeder Domäne in der Gesamtstruktur. Die Rechte müssen hierbei so festgelegt werden, dass sie an alle Benutzerobjekte vererbt werden.  

Wenn Sie nicht sicher sind wie die oben genannten Konto bezieht sich auf, öffnen die Azure Active Directory Connect-Konfigurationsbenutzeroberfläche, und klicken auf die **Überprüfung der Lösung** Option.  Das Konto, dem Sie die Berechtigung hinzufügen müssen, ist im Screenshot unten rot unterstrichen.

**<font color="red">Stellen sicher, dass dieser Berechtigungssatz für die einzelnen Domänen in jeder Gesamtstruktur in Ihrem System, andernfalls das Rückschreiben von Kennwörtern nicht ordnungsgemäß funktionieren.</font>**

  ![][032]

  Durch das Festlegen dieser Berechtigungen kann das MA-Dienstkonto für jede Gesamtstruktur Kennwörter im Namen der Benutzerkonten innerhalb dieser Gesamtstruktur verwalten. Wenn Sie diese Berechtigungen nicht gewähren, erhalten die Benutzer – obwohl das Zurückschreiben scheinbar ordnungsgemäß konfiguriert ist – Fehler bei dem Versuch, ihre lokalen Kennwörter über die Cloud verwalten. Hier sind die einzelnen Schritte zum Sie diese mit der **Active Directory-Benutzer und-Computer** Verwaltungs-Snap-in:

>[AZURE.NOTE] Es kann bis zu einer Stunde für diese Berechtigungen für alle Objekte in Ihrem Verzeichnis repliziert dauern.

#### So richten Sie die geeigneten Berechtigungen für das Zurückschreiben von Kennwörtern ein

1.  Öffnen Sie **Active Directory-Benutzer und-Computer** mit einem Konto an, die über die entsprechenden administrativen Domänenberechtigungen verfügt.
2.  In der **im Ansichtsmenü** option, stellen Sie sicher, dass **Erweiterte Funktionen** eingeschaltet ist.
3.  Klicken Sie im linken Bereich mit der rechten Maustaste auf das Objekt, das den Stamm der Domäne repräsentiert.
4.  Klicken Sie auf die **Sicherheit** Registerkarte.
5.  Klicken Sie dann auf **Erweitert**.

    ![][024]

6.  Auf der **Berechtigungen** auf **Hinzufügen**.

    ![][025]

7.  Wählen Sie das Konto aus, dem Sie Berechtigungen hinzufügen möchten (es sollte sich um dasselbe Konto handeln, das beim Einrichten der Synchronisierung für diese Gesamtstruktur angegeben wurde).
8.  Wählen Sie in der Dropdownliste am oberen Rand, **nachfolgerbenutzerobjekt**.
9.  In der **Berechtigungseintrag** (Dialogfeld), das anzeigt, aktivieren Sie das Kontrollkästchen für **Kennwort zurücksetzen**, **Kennwort ändern**, **Schreibberechtigungen** auf `lockoutTime`, und **Schreibberechtigungen** auf `pwdLastSet`.

    ![][026]
    ![][027]
    ![][028]

10. Klicken Sie dann auf **anwenden/Ok** in allen geöffneten Dialogfeldern.

### Schritt 5: Zurücksetzen Ihres AD-Kennworts als Benutzer
Jetzt, da das Zurückschreiben von Kennwörtern aktiviert ist, können Sie die ordnungsgemäße Funktion testen, indem Sie das Kennwort eines Benutzers zurücksetzen, dessen Konto mit Ihrem Cloudmandanten synchronisiert wurde.
 
#### So prüfen Sie, ob das Zurückschreiben von Kennwörtern ordnungsgemäß funktioniert
1.  Navigieren Sie zu [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) oder wechseln Sie zu einem beliebigen Organisations-ID-Anmeldebildschirm, und klicken Sie auf die **kann nicht auf Ihr Konto zugreifen?** Link.

    ![][029]

2.  Sie sollten jetzt eine neue Seite sehen, in der Sie nach der Benutzer-ID gefragt werden, für die Sie ein Kennwort zurücksetzen möchten. Geben Sie die ID des Testbenutzers ein, und führen Sie die Schritte zur Kennwortzurücksetzung aus.
3.  Nachdem Sie Ihr Kennwort zurückgesetzt haben, wird ein Bildschirm angezeigt, der dem folgenden ähnelt. Diese Anzeige weist darauf hin, dass Sie Ihr Kennwort im lokalen Verzeichnis und/oder in den Cloudverzeichnissen erfolgreich zurückgesetzt haben.

    ![][030]

4.  Um zu überprüfen, ob der Vorgang erfolgreich war oder Fehler, wechseln Sie zu Ihrer **verzeichnissynchronisierungscomputer**, Öffnen **Ereignisanzeige**, navigieren Sie zu der **Anwendungsereignisprotokoll**, und suchen Sie nach Ereignis **31002 - PasswordResetSuccess** aus der Quelle **PasswordResetService** für Ihren Testbenutzer.

    ![][031]


<br/>
<br/>
<br/>

## Links zur Dokumentation für die Kennwortzurücksetzung
Im Folgenden finden Sie Links zu allen Webseiten mit Informationen zur Kennwortzurücksetzung für Azure AD: 

* [**Ihr eigenes Kennwort zurücksetzen**](active-directory-passwords-update-your-own-password.md) -erfahren Sie mehr über zurücksetzen oder ändern Ihr eigenes Kennwort als Benutzer des Systems
* [**Funktionsweise**](active-directory-passwords-how-it-works.md) -erfahren Sie mehr über die sechs verschiedenen Komponenten der Dienst und die jeweilige
* [**Anpassen von**](active-directory-passwords-customize.md) -erfahren Sie, wie das Aussehen und Verhalten des Diensts, um den Bedürfnissen Ihres Unternehmens anpassen.
* [**Bewährte Methoden**](active-directory-passwords-best-practices.md) -erfahren Sie, wie schnell bereitstellen und effektiv verwalten von Kennwörtern in Ihrer Organisation
* [**Einblicke**](active-directory-passwords-get-insights.md) -erfahren Sie mehr über unsere integrierten Berichtsfunktionen
* [**Häufig gestellte Fragen zu**](active-directory-passwords-faq.md) – Hier erhalten Sie Antworten auf häufig gestellte Fragen
* [**Problembehandlung bei**](active-directory-passwords-troubleshoot.md) -erfahren Sie, wie Probleme mit dem Dienst schnell beheben
* [**Erfahren Sie mehr**](active-directory-passwords-learn-more.md) – erhalten Sie tiefgehende technische Details zur Funktionsweise des Diensts



[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"
[032]: ./media/active-directory-passwords-getting-started/032.jpg "Image_032.jpg"


