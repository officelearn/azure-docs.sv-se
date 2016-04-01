<properties
    pageTitle="Tutorial: Azure Active Directory-Integration mit Evidence.com | Microsoft Azure"
    description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Evidence.com konfigurieren."
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
    ms.date="12/10/2015"
    ms.author="asmalser"/>


# Tutorial: Azure Active Directory-Integration mit Evidence.com

In diesem Tutorial wird erläutert, wie einmaliges Anmelden für Azure Active Directory (AAD) und Evidence.com eingerichtet wird. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:
    
* Ein gültiges Microsoft Azure-Abonnement
* Ein Abonnement Evidence.com mit einmaligem Anmelden aktiviert (e-Mail-earlyaccess@evidence.com Wenn SAML-basierten für einmaliges Anmelden nicht aktiviert ist)

Nach Abschluss dieses Tutorials können sich die AAD-Benutzer, denen Sie Evidence.com-Zugriff zugewiesen haben, mit dem AAD-Zugriffsbereich per einmaligem Anmelden an der Anwendung anmelden.

## Hinzufügen von Evidence.com zu Ihrem Verzeichnis

In diesem Abschnitt wird beschrieben, wie Sie Evidence.com als integrierte Anwendung in Azure Active Directory hinzufügen.

**So aktivieren Sie die Anwendungsintegration für Evidence**

1.  In der [klassischen Azure-Portal](https://manage.windowsazure.com), klicken Sie im linken Navigationsbereich auf **Active Directory**.

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

4.  Klicken Sie zum Öffnen der Galerie **Hinzufügen**, und klicken Sie dann auf **eine Anwendung aus dem Katalog hinzufügen**.

5.  Geben Sie in das Suchfeld **Evidence.com**.

6.  Wählen Sie im Ergebnisbereich **Evidence.com**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.


## Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Konto in Azure Active Directory bei Evidence.com zu authentifizieren.

**So konfigurieren Sie einmaliges Anmelden**

1.  Nachdem die Evidence.com im klassischen Azure-Portal hinzufügen, klicken Sie auf **Konfigurieren Sie einmaliges Anmelden**. 
 
2.  Wählen Sie auf dem nächsten Bildschirm **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

3.  Geben Sie im Bildschirm App-URL konfigurieren die URL ein, in dem Benutzer in die URL Ihres Evidence.com-Mandanten anmelden (Beispiel: https://yourtenant.evidence.com, und klicken Sie dann auf **Weiter**. 

4.  Klicken Sie auf die **Download des Zertifikats** verknüpfen, und speichern Sie es auf dem lokalen Laufwerk. Dieses Zertifikat und die Metadaten-URLs (Entitäts-ID, SSO-Anmelde-URL und Abmelde-URL) werden zum Einrichten von SSO auf Evidence.com Website verwendet werden. 

5.  In separaten Webbrowserfenster bei Ihrer Evidence.com Mandanten als Administrator aus, und navigieren Sie zu **Admin** Registerkarte
      
6.  Klicken Sie auf **Agentur für einmaliges Anmelden**
 
7.  Wählen Sie **SAML Single Sign Grundlage**
 
8.  Kopieren der **Aussteller-URL**, **Single Sign On** und **einmaliges Abmelden** Werte im klassischen Azure-Portal und den entsprechenden Feldern im Evidence.com.

9.  Öffnen Sie das Zertifikat heruntergeladen, die in Schritt 4 mit einem Texteditor wie z. B. Notepad.exe, und kopieren Sie den Inhalt in die **Sicherheitszertifikat** Feld. 

10. Speichern Sie die Konfiguration in Evidence.com.
 
11. Überprüfen Sie in der klassischen Azure-Portal **bestätigen, dass Sie einmaliges Anmelden wie oben beschrieben konfiguriert haben**. Bei Aktivierung kann das aktuelle Zertifikat für diese Anwendung arbeiten.
 
12. Klicken Sie auf der Bestätigungsseite für einmaliges Anmelden auf **vollständig**.  


## Erstellen eines Evidence.com-Testbenutzers

Damit sich Azure AD-Benutzer anmelden können, müssen sie für den Zugriff innerhalb der Evidence.com-Anwendung bereitgestellt werden. In diesem Abschnitt wird beschrieben, wie in Evidence.com Azure AD-Benutzerkonten erstellt werden.

**So stellen Sie in Evidence.com ein Benutzerkonto bereit**

1.  Melden Sie sich in einem Webbrowserfenster bei der Evidence.com-Unternehmenswebsite als Administrator an.

2.  Navigieren Sie zu **Admin** Registerkarte.

3.  Klicken Sie auf **Benutzer hinzufügen**.

4.  Klicken Sie auf die **Hinzufügen** Schaltfläche.

5.  Die **e-Mail-Adresse** der hinzugefügte Benutzer muss entsprechen den Benutzernamen des Benutzers in Azure AD, die Sie Zugriff gewähren möchten. Wenn der Benutzername und e-Mail-Adresse nicht den gleichen Wert in Ihrer Organisation sind, können Sie die **Evidence.com > Attribute > Single Sign-On** Abschnitt des klassischen Azure-Portal so ändern Sie die Nameidenitifer an Evidence.com gesendet, um die e-Mail-Adresse sein.


## Zuweisen von Benutzern zu Evidence.com

Damit bereitgestellten AAD-Benutzern Evidence.com im Zugriffsbereich angezeigt wird, muss ihnen im klassischen Azure-Portal der Zugriff zugewiesen werden.

**So weisen Sie Evidence.com Benutzer zu**

1.  Klicken Sie auf der Seite "Schnellstart" für Evidence.com im klassischen Azure-Portal, auf **Zuweisen von Benutzern zu Evidence.com**.
 
2.  In der **anzeigen** Menü wählen, ob Sie Evidence.com einen Benutzer oder eine Gruppe zuweisen, und klicken Sie auf das Häkchen klicken möchten.
 
3.  In der **Benutzer** wählen Sie die Benutzer auf die Gruppe, zu denen Sie Evidence.com zuweisen möchten.
 
4.  Klicken Sie in der Fußzeile der Seite auf die **Zuweisen** Schaltfläche.



