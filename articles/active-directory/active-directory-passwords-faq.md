<properties 
    pageTitle="FAQ: Azure AD-Kennwortverwaltung | Microsoft Azure" 
    description="Häufig gestellte Fragen (FAQ) zur Kennwortverwaltung in Azure AD, einschließlich Kennwortzurücksetzung, Registrierung, Berichten und Rückschreibung in das lokale Active Directory." 
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

# Häufig gestellte Fragen zur Kennwortverwaltung
Im Folgenden werden einige häufig gestellte Fragen rund um die Kennwortverwaltung aufgeführt. 

Wenn Sie eine Frage haben, die Sie nicht beantworten können, oder zu einem bestimmten Problem Hilfe benötigen, können Sie hier weiterlesen und nachsehen, ob wir das Thema bereits behandelt haben.  Falls dies nicht der Fall sein sollte: Gerne jede Frage, die hier nicht auf abgedeckt ist die [Azure AD-Foren](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD) und melden wir uns an Sie bald wie möglich.

Diese FAQ sind in folgende Abschnitte unterteilt:

- [**Fragen zur Registrierung für die Kennwortzurücksetzung**](#password-reset-registration)
- [**Fragen zum Zurücksetzen des Kennworts**](#password-reset)
- [**Fragen zu Kennwortverwaltungsberichten**](#password-management-reports)
- [**Fragen zur Kennwortrückschreibung**](#password-writeback)

## Registrieren für die Kennwortzurücksetzung
 - **F: registrieren können meine Benutzer ihre eigenen kennwortzurücksetzungsdaten?**
 
 > **A:** Ja, solange das Zurücksetzen des Kennworts aktiviert ist, und Benutzer lizenziert sind, gelangen, das Kennwort zurücksetzen registrierungsportal unter http://aka.ms/ssprsetup registrieren ihre Authentifizierungsinformationen, die beim Zurücksetzen des Kennworts verwendet werden. Benutzer können auch registrieren, um den Zugriffsbereich unter http://myapps.microsoft.com, klicken auf die profilregisterkarte, und Registrieren für die Option Kennwort zurückzusetzen. Weitere Informationen zur Konfiguration Ihrer Benutzer für die Kennwortzurücksetzung finden Sie unter "So konfigurieren Sie Benutzer für das Zurücksetzen von Kennwörtern".
 
 - **F: definieren kann ich für meine Benutzer kennwortzurücksetzungsdaten?**
 
 > **A:** Ja, können Sie hierzu mit DirSync oder PowerShell oder über die [Azure-Verwaltungsportal](https://manage.windowsazure.com) oder Office Admin Portal. Weitere Informationen zu diesem Feature finden Sie im Blogbeitrag "Improved Privacy for Azure AD MFA and Password Reset Phone Numbers" (in englischer Sprache) und unter "Verwendung von Daten für die Kennwortzurücksetzung".
 
 - **F: synchronisieren kann ich Daten für Sicherheitsfragen vom lokalen?**
 
 > **A:** Nein, dies ist heute nicht möglich, aber wir werden es berücksichtigen.
 
 - **F: registrieren können meine Benutzer Daten so, dass andere Benutzer diese Daten nicht angezeigt?**
 
 > **A:** Ja, wenn Benutzer registrieren, Daten mithilfe der Registrierung Kennwortzurücksetzungsportals wird sie gespeichert in privaten Felder, die nur für globale Administratoren und der Benutzer selbst sichtbar sind. Weitere Informationen zu diesem Feature finden Sie im Blogbeitrag "Improved Privacy for Azure AD MFA and Password Reset Phone Numbers" (in englischer Sprache) und unter "Verwendung von Daten für die Kennwortzurücksetzung".
 
 - **F: müssen meine Benutzer registriert werden, bevor das Zurücksetzen von Kennwörtern verwendet werden kann?**
 
 > **A:** nicht, wenn Sie genügend Authentifizierungsinformationen in ihrem Auftrag definieren, Benutzer keine registrieren. Das Zurücksetzen des Kennworts funktioniert, solange Sie in den entsprechenden Feldern im Verzeichnis ordnungsgemäß formatierte Daten gespeichert haben. Weitere Informationen finden Sie unter "Verwendung von Daten für die Kennwortzurücksetzung".

 - **F: kann ich synchronisieren oder Festlegen der Telefon für Authentifizierung und des E-Mail-Authentifizierung "oder" alternatives Telefon für Authentifizierung Felder im Namen meiner Benutzer?**
 
 > **A:** derzeit nicht, aber wir möchten diese Fähigkeit zu aktivieren.

 - **F: Wie weiß das registrierungsportal welche Optionen meinen Benutzern angezeigt?**
 
 > **A:** der Registrierung kennwortzurücksetzungsportal zeigt nur die Optionen, die für Ihre Benutzer unter dem Abschnitt Richtlinie zum Zurücksetzen Ihres Verzeichnisses-Registerkarte "konfigurieren" aktiviert ist. Wenn Sie also z. B. die Sicherheitsfragen nicht aktivieren, können Benutzer sich für diese Option nicht registrieren.

 - **Registriert f: wann gilt ein Benutzer als?**
 
 > **A:** ein Benutzer gilt als registriert, wenn er mindestens N Authentifizierungsdaten definiert, wobei N die Anzahl der erforderlichen Authentifizierungsmethoden ist, die Sie festgelegt haben, in der [Azure-Verwaltungsportal](https://manage.windowsazure.com). Weitere Informationen hierzu finden Sie unter "Anpassen der Richtlinie zum Zurücksetzen des Benutzerkennworts".


## Zurücksetzen des Kennworts

 - **F: wie lange muss ich warten, eine e-Mail, SMS oder Anruf von Zurücksetzen des Kennworts erhalten?**
 
 > **A:** E-Mail, SMS-Nachrichten und Telefonanrufe sollten in weniger als 1 Minute, im Normalfall 5-20 Sekunden eingehen. Wenn Sie in diesem Zeitraum keine Benachrichtigung erhalten, überprüfen Sie den Junk-Ordner, stellen Sie sicher, dass die Nummer / E-Mail-Adresse richtig ist und dass die Authentifizierungsdaten im Verzeichnis richtig formatiert sind. Weitere Informationen zur Formatierung von Telefonnummern und E-Mail-Adressen für die Kennwortzurücksetzung finden Sie unter "Verwendung von Daten für die Kennwortzurücksetzung".

 - **F: unterstützt welche Sprachen werden durch das Zurücksetzen von Kennwörtern?**
 
 > **A:** das Kennwort zurückzusetzen, UI, SMS-Nachrichten und Sprachanrufe sind in denselben 40 Sprachen, die in Office 365 unterstützt werden lokalisiert. Diese sind: Arabisch, Bulgarisch, vereinfachtes Chinesisch, traditionelles Chinesisch, Kroatisch, Tschechisch, Dänisch, Niederländisch, Englisch, Estnisch, Finnisch, Französisch, Deutsch, Griechisch, Hebräisch, Hindi, Ungarisch, Indonesisch, Italienisch, Japanisch, Kasachisch, Koreanisch, Lettisch, Litauisch, Malaiisch (Malaysia), Norwegisch (Bokmål), Polnisch, Portugiesisch (Brasilien), Portugiesisch (Portugal), Rumänisch, Russisch, Serbisch (Lateinisch), Slowakisch, Slowenisch, Spanisch, Schwedisch, Thai, Türkisch, Ukrainisch und Vietnamesisch.

 - **Konfigurieren f: welche Teile der Benutzeroberfläche für die kennwortzurücksetzung organisationsspezifisches beim Organisations-branding in Verzeichnis "Eigene" Festlegen der Registerkarte?**
 
 > **A:** kennwortzurücksetzungsportal zeigt das Logo Ihrer Organisation und erlaubt Ihnen zudem so konfigurieren Sie den Kontakt den Link "Administrator", zeigen Sie auf eine benutzerdefinierte e-Mail-Adresse oder URL. E-Mails, die von der Kennwortzurücksetzung gesendet werden, enthalten das Logo, die Farben (in diesem Fall rot) sowie den Namen Ihrer Organisation im Nachrichtentext der E-Mail. Zudem weisen sie einen angepassten "Von"-Namen auf. Unten sehen Sie ein Beispiel mit allen organisationsspezifisch angepassten Elementen. Weitere Informationen finden Sie unter "Anpassen des Aussehens und Verhaltens der Kennwortzurücksetzung".

  ![][001]

 - **F: wie Informiere ich meine Benutzer darüber, wo Sie ihre Kennwörter zurücksetzen?**
 
 > **A:** Sie können Ihre Benutzer direkt auf https://passwordreset.microsoftonline.com senden, oder Sie können anweisen, zu klicken die Ihrem Konto Link finden Sie auf der Anmeldeseite alle Schule oder Arbeit-ID kann nicht zugegriffen werden. Sie können diese Links an jeder Stelle veröffentlichen (oder URL-Umleitungen darauf erstellen), die für die Benutzer leicht zugänglich ist.

 - **F: verwenden kann ich diese Seite von einem mobilen Gerät?**
 
 > **A:** Ja, diese Seite funktioniert auf mobilen Geräten. 

 - **F: unterstützt wird Entsperren der lokalen active Directory-Konten, wenn Benutzer ihre Kennwörter zurücksetzen?**
 
 > **A:** Ja, wenn ein Benutzer sein eigenes Kennwort und das Rückschreiben von Kennwörtern wurde in allen Versionen von Azure AD Connect oder in Versionen von Azure AD Sync 1.0.0485.0222 oder höher bereitgestellt und dann das Konto des Benutzers wird automatisch entsperrt werden, wenn der Benutzer sein Kennwort zurücksetzt.

 - **F: wie kann ich die kennwortzurücksetzung direkt in meiner Benutzer desktop anmelden integrieren?**
 
 > **A:** Dies ist heute nicht möglich. Wenn Sie diese Funktion jedoch unbedingt benötigen und ein Azure AD Premium-Kunde sind, können Sie Microsoft Identity Manager ohne zusätzliche Kosten installieren und die darin enthaltene lokale Lösung zur Kennwortzurücksetzung bereitstellen, um diese Anforderung zu erfüllen.

 - **F: festlegen kann ich für verschiedene Gebietsschemas unterschiedliche Sicherheitsfragen?**
 
 > **A:** Nein, dies ist heute nicht möglich, aber wir werden es berücksichtigen.

 - **F: Konfigurieren wie viele Fragen können wir für die Authentifizierungsoption mit Sicherheitsfragen?**
 
 > **A:** können Sie bis zu 20 benutzerdefinierte Sicherheitsfragen im Konfigurieren der [Azure-Verwaltungsportal](https://manage.windowsazure.com).

 - **F: sein wie lang dürfen Sicherheitsfragen?**
 
 > **A:** Sicherheitsfragen können zwischen 3 und 200 Zeichen lang sein.

 - **F: sein wie lang dürfen Antworten auf Sicherheitsfragen?**
 
 > **A:** Antworten können 3 bis 40 Zeichen lang sein.

 - **F: werden doppelte Antworten auf Sicherheitsfragen abgelehnt?**
 
 > **A:** Ja, doppelte Antworten auf Sicherheitsfragen werden abgelehnt.

 - **F: registrieren kann ein Benutzer mehrmals die gleiche Sicherheitsfrage?**
 
 > **A:** Nein, sobald ein Benutzer eine bestimmte Frage registriert, er kann nicht registriert für diese Frage ein zweites Mal.

 - **F: ist es möglich, eine Mindestanzahl von Sicherheitsfragen für Registrierung und Zurücksetzung festzulegen?**
 
 > **A:** Ja, ein Grenzwert für Registrierung und ein anderer für die Zurücksetzung festgelegt werden kann. Drei bis fünf Fragen können für die Registrierung und drei bis fünf Fragen für die Zurücksetzung verlangt werden.

 - **F: Wenn ein Benutzer mehr als die maximale Anzahl von Fragen zum Zurücksetzen registriert hat, wie werden Sicherheitsfragen beim Zurücksetzen ausgewählt?**
 
 > **A:** N Sicherheitsfragen nach dem Zufallsprinzip ausgewählt sind, aus der Gesamtanzahl der Fragen, die für ein Benutzer registriert hat, wobei N die minimale Anzahl von Fragen, die für das Zurücksetzen des Kennworts erforderlich ist. Wenn für einen Benutzer beispielsweise fünf Sicherheitsfragen registriert wurden, aber nur drei für das Zurücksetzen erforderlich sind, werden aus diesen fünf nach dem Zufallsprinzip drei ausgewählt und dem Benutzer zum Zeitpunkt der Zurücksetzung angezeigt. Wenn der Benutzer die Fragen falsch beantwortet, wird der Auswahlvorgang erneut durchgeführt, um eine wiederholte Fragestellung zu verhindern.

 - **F: verhindern ich Sie, dass Benutzer versuchen, das Kennwort in kurzer Zeit mehrmals zurückzusetzen?**
 
 > **A:** Ja, es gibt verschiedene Sicherheitsfunktionen integriert das Zurücksetzen von Kennwörtern. Benutzer dürfen innerhalb einer Stunde nur fünfmal versuchen, das Kennwort zurückzusetzen, bevor sie für 24 Stunden gesperrt werden. Benutzer dürfen innerhalb einer Stunde nur fünfmal versuchen, eine Telefonnummer zu validieren, bevor sie für 24 Stunden gesperrt werden. Benutzer dürfen innerhalb einer Stunde nur fünfmal versuchen, die einmalige Anmeldung durchzuführen, bevor sie für 24 Stunden gesperrt werden.

 - **F: ist wie lange die e-Mail- und SMS-einmalkennung gültig?**
 
 > **A:** die Sitzungsdauer für das Zurücksetzen von Kennwörtern beträgt 105 Minuten. Dies bedeutet, dass der Benutzer vom Beginn des Vorgangs zur Kennwortzurücksetzung 105 Minuten Zeit hat, um das Kennwort zurückzusetzen. Die E-Mail- und SMS-Einmalkennungen sind nach Ablauf dieses Zeitraums ungültig.


## Berichte zur Kennwortverwaltung

 - **F: werden wie lange dauert es für die Daten in den kennwortverwaltungsberichten angezeigt?**
 
 > **A:** Daten sollten auf den kennwortverwaltungsberichten innerhalb von 5 bis 10 Minuten angezeigt. In manchen Fällen kann es bis zu einer Stunde dauern.

 - **F: wie kann ich die kennwortverwaltungsberichte filtern?**
 
 > **A:** können Sie die kennwortverwaltungsberichte durch Klicken auf das kleine Vergrößerungsglas ganz rechts neben den spaltenbeschriftungen am Anfang des Berichts Filtern (Siehe Screenshot). Wenn Sie umfangreichere Filterfunktionen benötigen, können Sie den Bericht in Excel exportieren und eine Pivottabelle erstellen. 

  ![][002]

 - **F: Wie hoch ist die maximale Anzahl der Ereignisse, die in den Kennwortverwaltungsberichten gespeichert werden?**

 > **A:** bis zu 1.000 Kennwort zurücksetzen oder Ihr Kennwort zurücksetzen Registrierung Ereignisse in den kennwortverwaltungsberichten gespeichert werden.  Eine Erweiterung dieser Anzahl der Ereignisse ist in Arbeit.

 - **F: wie weit die kennwortverwaltungsberichte zur?**
 
 > **A:** die kennwortverwaltungsberichte zeigen Vorgänge innerhalb der letzten 30 Tage. Wir untersuchen gerade, wie wir diesen Zeitraum verlängern können. Wenn Sie diese Daten archivieren möchten, können Sie die Berichte in regelmäßigen Abständen herunterladen und an einem anderen Speicherort speichern.

 - **F: Gibt es eine maximale Anzahl von Zeilen, die auf den kennwortverwaltungsberichten angezeigt werden können?**
 
 > **A:** Ja, maximal 1.000 Zeilen kann entweder der Berichte zur Kennwortverwaltung, ob sie angezeigt in der Benutzeroberfläche oder heruntergeladen werden. Wir untersuchen gerade, wie dieser Grenzwert erhöht werden kann.

 - **F: Gibt es eine API, um das Zurücksetzen des Kennworts oder Berichtsdaten Registrierung zugreifen?**

 > **A:** Ja, finden Sie unter der folgenden Dokumentation erfahren, wie Sie das Zurücksetzen des Kennworts reporting Datenstrom zugreifen können.  [Erfahren Sie, wie das Zurücksetzen von Kennwörtern programmgesteuert Melden von Ereignissen auf](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).

## Rückschreiben von Kennwörtern
 - **F: wie funktioniert das Rückschreiben von Kennwörtern hinter den Kulissen?**
 
 > **A:** finden Sie unter Rückschreiben von Kennwörtern für eine ausführliche Erläuterung dessen, was geschieht, wenn Sie das Rückschreiben von Kennwörtern, sowie zum Datenfluss durch das System wieder in Ihre lokale Umgebung aktivieren. Unter "Sicherheitsmodell für das Rückschreiben von Kennwörtern" im Abschnitt "Rückschreiben von Kennwörtern" erfahren Sie, wie wir sicherstellen, dass das Rückschreiben von Kennwörtern ein sehr sicherer Dienst ist.

 - **F: werden wie lange dauert das Rückschreiben von Kennwörtern arbeiten?  Gibt es eine Verzögerung der Synchronisierung wie bei der kennworthashsynchronisierung?**
 
 > **A:** das Rückschreiben von Kennwörtern geschieht sofort. Sie ist eine synchrone Pipeline, die grundlegend anders funktioniert als die Kennworthashsynchronisierung. Durch die Kennwortrückschreibung erhalten Benutzer in Echtzeit Feedback über den Erfolg der Kennwortzurücksetzung oder -änderung. Die durchschnittliche Zeit für das erfolgreiche Rückschreiben eines Kennworts liegt bei unter 500 ms.

 - **F: funktioniert welche Arten von Konten das Rückschreiben von Kennwörtern?**
 
 > **A:** Rückschreiben von Kennwörtern für Verbundbenutzer und Kennworthashsynchronisierung verdächtigem Benutzer.

 - **F: durchgesetzt werden für das Rückschreiben von Kennwörtern Kennwortrichtlinien meiner Domäne?**
 
 > **A:** Ja, die Kennwortrückschreibung setzt das Kennwortalter, Verlauf, Komplexität, Filter und andere Einschränkungen kann für Kennwörter in der lokalen Domäne befinden.

 - **F: ist das Rückschreiben von Kennwörtern sicher?  Wie kann ich sicher sein, dass ich nicht gehackt werde?**
 
 > **A:** Ja, das Rückschreiben von Kennwörtern ist äußerst sicher. Weitere Informationen zu den vier Sicherheitsstufen, die durch den Dienst zur Kennwortrückschreibung implementiert werden, finden Sie unter "Sicherheitsmodell für das Rückschreiben von Kennwörtern" im Abschnitt "Rückschreiben von Kennwörtern".


<br/>
<br/>
<br/>

## Links zur Dokumentation für die Kennwortzurücksetzung
Im Folgenden finden Sie Links zu allen Webseiten mit Informationen zur Kennwortzurücksetzung für Azure AD: 

* [**Ihr eigenes Kennwort zurücksetzen**](active-directory-passwords-update-your-own-password.md) -erfahren Sie mehr über zurücksetzen oder ändern Ihr eigenes Kennwort als Benutzer des Systems
* [**Funktionsweise**](active-directory-passwords-how-it-works.md) -erfahren Sie mehr über die sechs verschiedenen Komponenten der Dienst und die jeweilige
* [**Erste Schritte**](active-directory-passwords-getting-started.md) -erfahren Sie, wie Sie Benutzern das Zurücksetzen und Ändern ihrer Cloud- oder lokalen Kennwörter erlauben
* [**Anpassen von**](active-directory-passwords-customize.md) -erfahren Sie, wie das Aussehen und Verhalten des Diensts, um den Bedürfnissen Ihres Unternehmens anpassen.
* [**Bewährte Methoden**](active-directory-passwords-best-practices.md) -erfahren Sie, wie schnell bereitstellen und effektiv verwalten von Kennwörtern in Ihrer Organisation
* [**Einblicke**](active-directory-passwords-get-insights.md) -erfahren Sie mehr über unsere integrierten Berichtsfunktionen
* [**Problembehandlung bei**](active-directory-passwords-troubleshoot.md) -erfahren Sie, wie Probleme mit dem Dienst schnell beheben
* [**Erfahren Sie mehr**](active-directory-passwords-learn-more.md) – erhalten Sie tiefgehende technische Details zur Funktionsweise des Diensts


[001]: ./media/active-directory-passwords-faq/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-faq/002.jpg "Image_002.jpg"


