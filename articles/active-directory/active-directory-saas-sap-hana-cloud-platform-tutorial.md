<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit SAP HANA Cloud Platform | Microsoft Azure" 
    description="Erfahren Sie, wie Sie SAP HANA Cloud Platform mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Tutorial: Azure Active Directory-Integration mit SAP HANA Cloud Platform
  
In diesem Tutorial wird die Integration von Azure und SAP HANA Cloud Platform erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein SAP HANA Cloud Platform-Konto
  
Nach Abschluss dieses Lernprogramms SAP HANA Cloud Platform zugewiesene Azure AD-Benutzer werden in der Anwendung mit einmaligem Anmelden die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

>[AZURE.IMPORTANT]Sie müssen Ihre eigene Anwendung bereitstellen, oder abonnieren Sie eine Anwendung in Ihrem SAP HANA Cloud Platform-Konto für die einmalige Anmeldung zu testen. In diesem Tutorial wird eine Anwendung auf dem Konto bereitgestellt.
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für SAP HANA Cloud Platform
2.  Konfigurieren der einmaligen Anmeldung
3.  Zuweisen einer Rolle an einen Benutzer
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "Scenario")
##Aktivieren der Anwendungsintegration für SAP HANA Cloud Platform
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für SAP HANA Cloud Platform aktivieren.

###So aktivieren Sie die Anwendungsintegration für SAP HANA Cloud Platform:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **SAP HANA Cloud Platform**.

    ![Anwendungskatalog](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **SAP HANA Cloud Platform**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![SAP Hana](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "SAP Hana")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei SAP HANA Cloud Platform zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei in Ihren SAP HANA Cloud Platform-Mandanten hochladen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **SAP HANA Cloud Platform** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer anmelden bei SAP HANA Cloud Platform** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "Configure Single Sign-On")

3.  In einem Webbrowserfenster, melden Sie sich beim SAP HANA Cloud Platform Cockpit unter Https://account.\<landscape host\ >.ondemand.com/cockpit (z. B.: *https://account.hanatrial.ondemand.com/cockpit*).

4.  Klicken Sie auf die **vertrauen** Registerkarte.

    ![Vertrauen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "Trust")

5.  Führen Sie im Verwaltungsabschnitt „Vertrauen“ die folgenden Schritte aus:

    ![Metadaten abrufen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "Get Metadata")

    1.  Klicken Sie auf die **Local Service Provider** Registerkarte.
    2.  Um die SAP HANA Cloud Platform-Metadatendatei herunterzuladen, klicken Sie auf **Get Metadata**.

6.  Im Azure Active Directory-Portal auf der **App-URL konfigurieren** Seite führen die folgenden Schritte aus, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "Configure App URL")

    1.  In der **-Anmelde-URL** Textfeld die URL ein, Ihre Benutzer zum Anmelden bei Ihrem **SAP HANA Cloud Platform** Anwendung. Dies ist die kontospezifische URL einer geschützten Ressource in der SAP HANA Cloud Platform-Anwendung. Die URL basiert auf dem folgenden Muster: *https://\<applicationName\>\<accountName\>.\<landscape Host\ >.ondemand.com/\ < Path\_to\_protected\_resource\ >* (z. B.: *https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*)

        >[AZURE.NOTE]Dies ist die URL in der SAP HANA Cloud Platform-Anwendung, die Authentifizierung des Benutzers erfordert.

    2.  Öffnen Sie die heruntergeladene SAP HANA Cloud Platform-Metadatendatei, und suchen Sie die **NS3: assertionconsumerservice** Tag.
    3.  Kopieren Sie den Wert von der **Speicherort** Attribut, und fügen Sie ihn in die **SAP HANA Cloud Platform Reply URL** Textfeld.

7.  Auf der **einmaliges Anmelden bei SAP HANA Cloud Platform konfigurieren** zum Herunterladen Ihrer Metadaten klicken **Metadaten**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "Configure Single Sign-On")

8.  SAP HANA Cloud Platform Cockpit in die **Local Service Provider** führen die folgenden Schritte aus:

    ![Verwaltung von Vertrauensstellungen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "Trust Management")

    1.  Klicken Sie auf **Bearbeiten**.
    2.  Als **Konfigurationstyp**, Option **benutzerdefinierte**.
    3.  Als **Local Provider Name**, übernehmen Sie den Standardwert.
    4.  Zum Generieren einer **Signaturschlüssel** und ein **-Signaturzertifikat** Schlüsselpaar, klicken Sie auf **Generate Key Pair**.
    5.  Als **Principal Propagation**, Option **deaktiviert**.
    6.  Als **Force Authentication**, Option **deaktiviert**.
    7.  Klicken Sie auf **Speichern**.

9.  Klicken Sie auf die **Trusted Identity Provider** Registerkarte, und klicken Sie dann auf **Add Trusted Identity Provider**.

    ![Verwaltung von Vertrauensstellungen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "Trust Management")

    >[AZURE.NOTE]Um die Liste der vertrauenswürdigen Identitätsanbieter verwalten zu können, müssen Sie den Typ der benutzerdefinierten Konfiguration im Abschnitt Local Service Provider ausgewählt haben. Beim Standard-Konfigurationstyp haben Sie ein nicht-bearbeitbares und implizites Vertrauen gegenüber dem SAP ID-Dienst. Bei der Option „Keine“ haben Sie keine Vertrauenseinstellungen.

10. Klicken Sie auf die **Allgemein** Registerkarte, und klicken Sie dann auf **Durchsuchen** um die heruntergeladene Metadatendatei hochzuladen.

    ![Verwaltung von Vertrauensstellungen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "Trust Management")

    >[AZURE.NOTE] Nach dem Hochladen der Metadatendatei werden die Werte für **Single Sign-on URL**, **Single Logout URL** und **-Signaturzertifikat** automatisch aufgefüllt.

11. Klicken Sie auf die **Attribute** Registerkarte.

12. Auf der **Attribute** führen die folgenden Schritte aus:

    ![Attribute](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "Attributes")

    1.  Durch Klicken auf **based Attribut**, fügen Sie die folgenden Assertion-basierten Attribute hinzu:

        |Assertion-Attribut| Prinzipal-Attribut|
        |-------------------|--------------------|
        |http://Schemas.xmlsoap.org/ws/2005/05/Identity/Claims/givenName|   firstname|--------------------|--------------------|
        |http://Schemas.xmlsoap.org/ws/2005/05/Identity/Claims/Surname|        lastname|-----------|
        |http://Schemas.xmlsoap.org/ws/2005/05/Identity/Claims/EmailAddress|E-Mail|

    >[AZURE.NOTE]Die Konfiguration der Attribute hängt davon ab, wie die Anwendung(en) auf HPC werden entwickelt, d. h. welche Attribute in der SAML-Antwort erwarten und unter welchem Name (Prinzipalattribut) Zugriff auf dieses Attribut im Code.
    >  
    >a.  Die **Standardattribut** im Screenshot wird nur zur Veranschaulichung verwendet. Es ist nicht erforderlich, damit das Szenario funktioniert.  
    >
    >b.  Die Namen und Werte für **Prinzipalattribut** im Screenshot gezeigten abhängig, wie die Anwendung entwickelt wird. Es ist möglich, dass die Anwendung andere Zuordnungen erfordert.

13. In Azure-Portal auf der **einmaliges Anmelden bei SAP HANA Cloud Platform konfigurieren** konfigurationsbestätigung konfigurationsbestätigung für einmaliges Anmelden, und klicken Sie dann auf **vollständig**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "Configure Single Sign-On")
  
Als optionalen Schritt können Sie Assertion-Gruppen für Ihren Azure Active Directory-Identitätsanbieter konfigurieren.

>[AZURE.NOTE]Gruppen können auf SAP HANA Cloud Platform Sie eine oder mehrere Rollen in der SAP HANA Cloud Platform-Anwendung, die durch die Werte der Attribute in der SAML 2.0-Assertion bestimmt dynamisch einen oder mehrere Benutzer zuweisen. Beispielsweise, wenn die Assertion enthält das Attribut "*Vertrag = temporary*", können Sie alle betroffenen Benutzer der Gruppe hinzugefügt werden"*temporäre*". Die Gruppe "*temporäre*" enthalten eine oder mehrere Rollen aus einer oder mehreren Anwendungen, die in Ihrem SAP HANA Cloud Platform-Konto bereitgestellt.
>  
>Verwenden Sie Assertion-Gruppen, wenn Sie viele Benutzer einer oder mehreren Rolle(n) auf Ihrem SAP HANA Cloud Platform-Konto zuweisen möchten. Wenn Sie nur eine einzelne oder einer geringen Zahl von Benutzern (a) bestimmten Rollen zuweisen möchten sollten Sie direkt in der "**Autorisierungen**" Registerkarte des SAP HANA Cloud Platform Cockpit vor.

##Zuweisen einer Rolle an einen Benutzer
  
Um Azure AD-Benutzer für die Anmeldung bei SAP HANA Cloud Platform zu aktivieren, müssen Sie ihnen Rollen in der SAP HANA Cloud Platform zuweisen.

###So weisen Sie einem Benutzer eine Rolle zu:

1.  Melden Sie sich bei Ihrem **SAP HANA Cloud Platform** Cockpit an.

2.  Führen Sie die folgenden Schritte aus:

    ![Autorisierungen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "Authorizations")

    1.  Klicken Sie auf **Autorisierung**.
    2.  Klicken Sie auf die **Benutzer** Registerkarte.
    3.  In der **Benutzer** Textfeld Geben Sie die e-Mail-Adresse des Benutzers.
    4.  Klicken Sie auf **Zuweisen** den Benutzer eine Rolle zuweisen.
    5.  Klicken Sie auf **Speichern**.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie SAP HANA Cloud Platform Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der **SAP HANA Cloud Platform** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

