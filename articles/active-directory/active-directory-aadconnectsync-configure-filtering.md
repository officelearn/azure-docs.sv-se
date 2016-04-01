<properties
    pageTitle="Azure AD Connect-Synchronisierung: Konfigurieren der Filterung | Microsoft Azure"
    description="Erläutert das Konfigurieren der Filterung bei der Azure AD Connect-Synchronisierung."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2015"
    ms.author="markusvi;andkjell"/>


# Azure AD Connect-Synchronisierung: Konfigurieren der Filterung

In der Azure AD Connect-Synchronisierung können Sie die Filterung jederzeit aktivieren. Wenn Sie bereits die Standardkonfigurationen der Directory-Synchronisierung ausgeführt und dann die Filterung konfiguriert haben, werden die Objekte, die herausgefiltert werden, nicht mehr mit Azure AD synchronisiert. Dadurch werden alle Objekte in Azure AD, die zuvor synchronisiert, aber dann gefiltert wurden, in Azure AD gelöscht. Wenn Objekte aufgrund eines Filterungsfehlers versehentlich gelöscht wurden, können Sie die Objekte erneut in Azure AD erstellen, indem Sie Ihre Filterkonfigurationen entfernen und dann Ihre Verzeichnisse erneut synchronisieren.

> [AZURE.IMPORTANT]Microsoft unterstützt nicht die Änderung oder den Betrieb der Azure AD Connect-Synchronisierung außerhalb dieser formal dokumentierten Aktionen. All diese Aktionen können zu einem inkonsistenten oder nicht unterstützten Zustand der Azure AD Connect-Synchronisierung führen. Folglich kann Microsoft keinen technischen Support für derartige Bereitstellungen leisten.

Mit Ausnahme der ausgehenden attributbasierten Filterung werden die Konfigurationen beibehalten, wenn Sie eine neuere Version von Azure AD Connect installieren oder ein Upgrade durchführen. Es ist immer eine bewährte Methode, vor dem Ausführen des ersten Synchronisierungszyklus zu verifizieren, dass die Konfiguration nach einem Upgrade auf eine neuere Version nicht versehentlich geändert wurde.


## Filteroptionen

> [AZURE.NOTE]In diesem Thema wird die AD-Quelle als Namen für Ihre Active Directory-Domänendienstconnector verwendet. Wenn Sie mehrere Gesamtstrukturen besitzen, benötigen Sie einen Connector pro Gesamtstruktur, und die Konfiguration muss für jede Gesamtstruktur wiederholt werden.

Die folgenden drei Filterkonfigurationstypen können auf das AD-Synchronisierungstool angewendet werden:

- [**Domänenbasierten**] (#configure-domänenbasierten Filterung): Hiermit können Sie Filtertyp zum Verwalten der Eigenschaften des SourceAD-Connectors in Azure AD Connect-Synchronisierung. Mit diesem Typ können Sie auswählen, welche Domänen zum Synchronisieren mit Azure AD zulässig sind.

- [**Konfigurieren Organisationseinheiten basierenden Filterung**] (#configure Organisations-basierenden Filterung): Hiermit können Sie Filtertyp zum Verwalten der Eigenschaften des SourceAD-Connectors in Azure AD Connect-Synchronisierung. Mit diesem Typ können Sie auswählen, welche Organisationseinheiten zum Synchronisieren mit Azure AD zulässig sind.

- [**Attribut-basierte**](#configure-attribute-based-filtering): Hiermit können Sie Filtermethode attributbasierte Filter angeben. So können Sie steuern, welche Objekte mit der Cloud synchronisiert werden sollen.

> [AZURE.NOTE]Wenn Sie eine Änderung, um die Konfiguration vornehmen und Synchronisierung von Kennwörtern verwenden, müssen Sie eine vollständige Synchronisierung aller Kennwörter auslösen, nachdem Sie die Konfiguration abgeschlossen haben. Schritte zum Auslösen eines Kennworts vollständiger Synchronisierung finden Sie unter [eine vollständige Synchronisierung aller Kennwörter auslösen](active-directory-aadconnectsync-implement-password-synchronization.md#trigger-a-full-sync-of-all-passwords)

## Konfigurieren der domänenbasierten Filterung

Dieser Abschnitt enthält die Schritte, die Sie ausführen müssen, um den Filter für Ihre Domäne zu konfigurieren.

Wenn Sie nach der Installation von Azure AD Connect in der Gesamtstruktur Domänen hinzugefügt oder entfernt haben, müssen Sie auch die Konfiguration der Filterung aktualisieren.


### Ändern des Filters für vorhandene Domänen

> [AZURE.NOTE] Wenn Sie Ihren Domänenfilter geändert haben, müssen Sie auch Ihre Ausführungsprofile aktualisieren.


**Um den Filter für die Domäne festzulegen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei dem Computer mit Azure AD Connect-Synchronisierung mit einem Konto, das ein Mitglied der **ADSyncAdmins** Sicherheitsgruppe.

2. Auf **Start**, Tippen oder klicken Sie auf **Synchronisierungsdienst** zum Öffnen der **Synchronisierungsdienst-Manager**.

3. Klicken Sie zum Öffnen der connectorsansicht **Connectors** in die **Tools** Menü.

4. In der **Connectors** wählen Sie den Connector mit **Active Directory-Domänendienst** als **Typ**.

5. Zum Öffnen der **Eigenschaften** Dialogfeld klicken Sie auf **Eigenschaften** in die **Aktionen** Menü.

6. Klicken Sie auf **Verzeichnispartitionen konfigurieren**.

7. In der **Verzeichnispartitionen auswählen** auflisten, stellen Sie sicher, dass nur die Partitionen, die Sie synchronisieren möchten, ausgewählt sind. <br> Um eine Domäne aus dem Synchronisierungsprozess zu entfernen, deaktivieren Sie deren Kontrollkästchen.

8. Schließen der **Eigenschaften** Dialogfeld klicken Sie auf **OK**.


Wenn Sie den Domänenfilter aktualisiert haben, müssen Sie auch folgende Ausführungsprofile aktualisieren:

- Vollständiger Import
- Vollständige Synchronisierung
- Deltaimport
- Deltasynchronisierung
- Export


Wenn Sie haben **entfernt** eine Partition aus der Liste der Verzeichnispartitionen, müssen Sie sicherstellen, dass alle ausführungsprofilschritte, die diese Partition verweisen, ebenfalls entfernt werden.

**Um einen Schritt aus einem Ausführungsprofil zu entfernen, führen Sie die folgenden Schritte aus:**

1. In der **Connectors** wählen Sie den Connector mit **Active Directory-Domänendienst** als **Typ**.

2. Zum Öffnen der **Ausführungsprofile konfigurieren für** im Dialogfeld klicken Sie auf **Ausführungsprofile konfigurieren** in die **Aktionen** Menü.

3. In der **connectorausführungsprofile** wählen Sie das zu konfigurierende Ausführungsprofil

4. Führen Sie für jeden Schritt in der Schrittdetailliste die folgenden Schritte aus:

     4.1. Falls erforderlich, klicken Sie auf den Schritt, um die Schrittdetails zu erweitern.

     4.2. Wenn die **Wert** von der **Partition** Attribut eine GUID ist, klicken Sie auf Schritt löschen.

5. Schließen der **Ausführungsprofile konfigurieren für** Dialogfeld klicken Sie auf **OK**.

Wenn Sie haben **hinzugefügt** einer Partition auf der Liste der Verzeichnispartitionen, müssen Sie sicherstellen, dass ein ausführungsprofilschritt für diese Partition auf jedem der Ausführungsprofile in der obigen Liste verfügbar ist.

**Um einem Ausführungsprofil einen Schritt hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. In der **Connectors** wählen Sie den Connector mit **Active Directory-Domänendienst** als **Typ**.

2. Zum Öffnen der **Ausführungsprofile konfigurieren für** im Dialogfeld klicken Sie auf **Ausführungsprofile konfigurieren** in die **Aktionen** Menü.

3. In der **connectorausführungsprofile** wählen Sie das zu konfigurierende Ausführungsprofil

4. Zum Öffnen der **Ausführungsprofil konfigurieren** Dialogfeld klicken Sie auf **neuen Schritt**.

5. Auf der **Schritt konfigurieren** Seite in der Liste, wählen Sie den Schritttyp aus, und klicken Sie dann auf **Weiter**.

6. Auf der **Connectorkonfiguration** auf der Seite der **Partition** wählen Sie den Namen der Partition, die Sie Ihrem Domänenfilter hinzugefügt haben.

7. Schließen der **Ausführungsprofil konfigurieren** Dialogfeld klicken Sie auf **Fertig stellen**.

8. Schließen der **Ausführungsprofile konfigurieren** finden Sie im Dialogfeld **OK**.

### Ändern des Filters für neue oder entfernte Domänen
Wenn Sie der Gesamtstruktur eine neue Domäne hinzugefügt oder eine Domäne aus der Gesamtstruktur entfernt haben, muss die Konfiguration der Domänenfilterung aktualisiert werden.

**Schritte, wenn Sie eine Domäne hinzugefügt haben**

1. Melden Sie sich bei dem Computer mit Azure AD Connect-Synchronisierung mit einem Konto, das ein Mitglied der **ADSyncAdmins** Sicherheitsgruppe.

2. Auf **Start**, Tippen oder klicken Sie auf **Synchronisierungsdienst** zum Öffnen der **Synchronisierungsdienst-Manager**.

3. Klicken Sie zum Öffnen der connectorsansicht **Connectors** in die **Tools** Menü.

4. In der **Connectors** wählen Sie den Connector mit **Active Directory-Domänendienst** als **Typ**.

5. Zum Öffnen der **Eigenschaften** Dialogfeld klicken Sie auf **Eigenschaften** in die **Aktionen** Menü.

6. Klicken Sie auf **Verzeichnispartitionen konfigurieren**.

7. Klicken Sie auf die **Aktualisieren** Schaltfläche am oberen Rand der Seite.

8. In der **Verzeichnispartitionen auswählen** Wählen Sie die neue Domäne.

9. Schließen der **Eigenschaften** Dialogfeld klicken Sie auf OK.

Stellen Sie sicher, dass der gleiche Connector weiterhin ausgewählt ist. Die Ausführungsprofile für den Connector müssen nun konfiguriert werden.


- Vollständiger Import
- Vollständige Synchronisierung
- Deltaimport
- Deltasynchronisierung
- Export


10. Zum Öffnen der **Ausführungsprofile konfigurieren für** im Dialogfeld klicken Sie auf **Ausführungsprofile konfigurieren** in die **Aktionen** Menü.

11. In der **connectorausführungsprofile** wählen Sie das zu konfigurierende Ausführungsprofil

12. Zum Öffnen der **Ausführungsprofil konfigurieren** Dialogfeld klicken Sie auf **neuen Schritt**.

13. Auf der **Schritt konfigurieren** Seite in der Liste, wählen Sie den Schritttyp aus, und klicken Sie dann auf **Weiter**.

14. Auf der **Connectorkonfiguration** auf der Seite der **Partition** wählen Sie den Namen der Partition, die Sie Ihrem Domänenfilter hinzugefügt haben.

15. Schließen der **Ausführungsprofil konfigurieren** Dialogfeld klicken Sie auf **Fertig stellen**.

16. Schließen der **Ausführungsprofile konfigurieren** finden Sie im Dialogfeld **OK**.

17. Wiederholen Sie diese Schritte für alle Ausführungsprofile.

**Schritte, wenn Sie eine Domäne entfernt haben**

1. Melden Sie sich bei dem Computer mit Azure AD Connect-Synchronisierung mit einem Konto, das ein Mitglied der **ADSyncAdmins** Sicherheitsgruppe.

2. Auf **Start**, Tippen oder klicken Sie auf **Synchronisierungsdienst** zum Öffnen der **Synchronisierungsdienst-Manager**.

3. Klicken Sie zum Öffnen der connectorsansicht **Connectors** in die **Tools** Menü.

4. In der **Connectors** wählen Sie den Connector mit **Active Directory-Domänendienst** als **Typ**.

5. Zum Öffnen der **Eigenschaften** Dialogfeld klicken Sie auf **Eigenschaften** in die **Aktionen** Menü.

6. Klicken Sie auf **Verzeichnispartitionen konfigurieren**.

7. Klicken Sie auf die **Aktualisieren** Schaltfläche am oberen Rand der Seite.

8. Schließen der **Eigenschaften** Dialogfeld klicken Sie auf OK. Eine Popupmeldung informiert darüber, dass eine Domäne entfernt wurde und diese Konfiguration bereinigt wird.

## Konfigurieren der auf Organisationseinheiten basierenden Filterung

**Führen Sie zum Konfigurieren der auf Organisationseinheiten basierenden Filterung die folgenden Schritte aus:**

1. Melden Sie sich bei dem Computer mit Azure AD Connect-Synchronisierung mit einem Konto, das ein Mitglied der **ADSyncAdmins** Sicherheitsgruppe.

2. Auf **Start**, Tippen oder klicken Sie auf **Synchronisierungsdienst** zum Öffnen der **Synchronisierungsdienst-Manager**.<br>

3. In der **Synchronisierungsdienst-Manager**, klicken Sie auf **Connectors**, und doppelklicken Sie dann auf **SourceAD**.

4. Klicken Sie auf **Verzeichnispartitionen konfigurieren**, wählen Sie die Domäne zu konfigurieren, und klicken Sie dann auf **Container**.

5. Geben Sie bei Aufforderung die Domänenanmeldeinformationen für die lokale Active Directory-Gesamtstruktur.<br> > [AZURE.NOTE]Wenn das Dialogfeld Anmeldeinformationen angezeigt wird, das zum Importieren und Exportieren von AD DS verwendete Konto wird angezeigt. Wenn Sie das Kennwort für das Konto nicht kennen, können Sie ein anderes Konto eingeben. Das Konto, das Sie verwenden, muss über Leseberechtigungen für die Domäne verfügen, die gerade konfiguriert wird.

6. In der **Container auswählen** Dialogfeld deaktivieren, die nicht mit Cloud-Verzeichnis synchronisieren, und klicken Sie dann auf Organisationseinheiten **OK**.

7. Klicken Sie auf **OK** auf die **Eigenschaften von SourceAD** Seite.

8. Führen Sie einen vollständigen Import und eine Deltasynchronisierung mithilfe der folgenden Schritte aus:

     8.1. Wählen Sie in der Liste der Verbindungen **SourceAD**

     8.2. Öffnen der **Connector ausführen** Dialogfeld **Ausführen** aus der **Aktionen** Menü.

     8.3. In der **Ausführungsprofile** Liste **Vollständiger Import**, und warten Sie, bis das Ausführungsprofil abgeschlossen.

     8.4. Öffnen der **Connector ausführen** Dialogfeld **Ausführen** aus der **Aktionen** Menü.

     8.5. In der **Ausführungsprofile** Liste **Deltasynchronisierung**, und warten Sie, bis das Ausführungsprofil abgeschlossen.




## Konfigurieren der attributbasierten Filterung

Es gibt mehrere Methoden zur Konfiguration von Filtern auf Grundlage von Attributen. Die Konfiguration der eingehenden Filterung aus AD wird empfohlen, weil diese Konfigurationseinstellungen auch nach einem Upgrade auf eine neuere Version beibehalten werden. Die Konfiguration der ausgehenden Filterung zu Azure AD wird unterstützt. Diese Einstellungen werden jedoch nach einem Upgrade auf eine neuere Version nicht beibehalten. Diese Konfiguration sollte nur verwendet werden, wenn erforderlich ist, das kombinierte Objekt im Metaverse zu untersuchen, um die Filterung zu ermitteln.

### Eingehende Filterung

Die eingehende Filterung nutzt die Standardkonfiguration, in der für Objekte, die an Azure AD gesendet werden, das Metaverseattribut "cloudFiltered" nicht auf einen Wert festgelegt werden darf und das Metaverseattribut "sourceObjectType" auf „User“ oder „Contact“ festgelegt ist.

Das Attribut "cloudFiltered" sollte auf True festgelegt werden, wenn das Objekt nicht mit Azure AD synchronisiert werden und in anderen Fällen leer bleiben soll. Diese Methode wird verwendet, wenn für ein Objekt keine Synchronisierung erfolgen soll (negative Filterung).

Im folgenden Beispiel werden alle Benutzer ausgefiltert, bei denen "extensionAttribute15" den Wert "NoSync" aufweist:

1. Melden Sie sich bei dem Computer, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, mithilfe eines Kontos an, das Mitglied der Sicherheitsgruppe "ADSyncAdmins" ist.
2. Öffnen Sie die **Synchronisierung Regeln-Editor-** aus der **Startmenü**.
3. Stellen Sie sicher, dass **eingehende** ausgewählt ist, und klicken Sie auf **neue Regel hinzufügen**.
4. Geben Sie der Regel einen beschreibenden Namen, z. B. "*In von AD – Benutzer DoNotSyncFilter*", wählen Sie die richtige Gesamtstruktur **Benutzer** als die **CS-Objekttyp**, und **Person** als die **MV-Objekttyp**. Als **Linktyp**, Option **Join** und geben Sie in der Rangfolge einen Wert, der derzeit nicht von einer anderen Synchronisierungsregel verwendet (z. B.: 50), und klicken Sie dann auf **Weiter**.
5. In **Bereichsfilter**, klicken Sie auf **Gruppe hinzufügen**, klicken Sie auf **-Klausel hinzufügen** und Attribut wählen Sie unter **ExtensionAttribute15**. Stellen Sie sicher, dass der Operator Wert **gleich** und **Typ** in den Wert "nosync" in das Feld Wert ein. Klicken Sie auf **Weiter**.
6. Lassen Sie die **Join** leer, und klicken Sie dann auf **Weiter**.
7. Klicken Sie auf **Transformation hinzufügen**, wählen die **FlowType** auf **konstant**, wählen Sie das Zielattribut "cloudfiltered", und geben Sie in das Textfeld "Quelle" in "true". Klicken Sie auf "Hinzufügen", um die Regel zu speichern.
8. Führen Sie eine vollständige Synchronisierung: auf die **Connectors** Registerkarte der rechten Maustaste auf **SourceAD**, klicken Sie auf **Ausführen**, klicken Sie auf **vollständige Synchronisierung**, und klicken Sie dann auf **OK**.


Das Attribut **Quellobjekttyp** Bereitstellung einer **Benutzer** oder **Kontakt** Azure AD, wenn dieses Attribut den Wert hat **Benutzer** oder **Kontakt** bzw.. Durch Erstellen einer Synchronisierungsregel mit einer höheren Rangfolge als die integrierten Regeln kann das Standardverhalten außer Kraft gesetzt werden. Diese Methode bietet außerdem die Möglichkeit, positive und negative Regeln auszudrücken.

Im folgenden Beispiel werden Sie nur Benutzer synchronisiert, wenn das Department-Attribut ist "*Sales*" oder leer ist:

1. Melden Sie sich bei dem Computer, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, mithilfe eines Kontos an, das Mitglied der Sicherheitsgruppe "ADSyncAdmins" ist.
2. Öffnen Sie die **Synchronisierung Regeln-Editor-** aus der **Startmenü**.
3. Stellen Sie sicher, dass **eingehende** ausgewählt ist, und klicken Sie dann auf **neue Regel hinzufügen**.
4. Geben Sie der Regel einen beschreibenden Namen (z. B.: "*In von AD – Benutzer DoNotSyncFilter*"), wählen Sie die richtige Gesamtstruktur **Benutzer** als die **CS-Objekttyp**, und **Person** als die **MV-Objekttyp**. Als **Linktyp**, Option **Join** und **Rangfolge**, ein Wert, der derzeit nicht von einer anderen Synchronisierungsregel verwendet (z. B.: 60). Klicken Sie auf **Weiter**.
5. Lassen Sie die **Bereichsfilter** und **Verknüpfungsregeln** leer, und klicken Sie auf **Weiter** zweimal.
6. Klicken Sie auf **Transformation hinzufügen**, wählen die **FlowType**  **Ausdruck** und wählen Sie die **Target-Attribut** auf **Quellobjekttyp**. In der **Quelle**, geben Sie den folgenden Ausdruck:<br>`IIF(IsNullOrEmpty([department]),NULL,IIF([department]<>”Sales”,”DoNotSync”,NULL))`
7. Klicken Sie auf "Hinzufügen", um die Regel zu speichern.
8. Führen Sie eine vollständige Synchronisierung: auf die **Connectors** Registerkarte der rechten Maustaste auf **SourceAD**, klicken Sie auf **Ausführen**, klicken Sie auf **vollständige Synchronisierung**, und klicken Sie dann auf **OK**. Hier ist ein Ergebnis, die folgendermaßen aussehen würde:<br>

> [AZURE.NOTE] Beachten Sie, dass wir eine Mischung aus "cloudfiltered" und "sourceobjecttype" verwenden, um die Objekte bestimmen sollen wir mit AAD synchronisieren möchten.

Die Verwendung von Ausdrücken ermöglicht leistungsfähige Filteroptionen. Beachten Sie im Ausdruck oben, dass der Literalwert NULL angegeben ist, wenn die Abteilung nicht vorhanden ist und wenn die Abteilung "Sales" war. Dies zeigt an, dass dieses Attribut keinen Wert beigetragen hat und daher die integrierten Regeln ausgewertet werden. Wir tun dies, damit ermittelt werden kann, ist dies ein **Benutzer** oder **Kontakt** in Azure AD erstellt werden soll.


## Ausgehende Filterung

In einigen Fällen ist es erforderlich, die Filterung erst auszuführen, nachdem die Objekte dem Metaverse hinzugefügt wurden. Es kann z. B. erforderlich sein, das E-Mail-Attribut aus der Ressourcengesamtstruktur und das Attribut "userPrincipalName" aus der Kontogesamtstruktur zu untersuchen, um zu ermitteln, ob ein Objekt synchronisiert werden soll. In diesen Fällen wird die Filterung für die ausgehende Regel erstellt.

> [AZURE.NOTE] Diese Methode erfordert eine Änderung an den Out-of-Box-Synchronisierungsregeln. Das Ändern des Bereichs einer Synchronisierungsregel wird unterstützt. Die Änderung bleibt jedoch ggf. nach einem Upgrade auf eine neuere Version von Azure AD Connect nicht erhalten. Wenn Sie die ausgehende Filterung verwenden, notieren Sie sich die Änderungen, die vorgenommen werden müssen. Stellen Sie dann nach einem Upgrade sicher, dass die Filterung noch vorhanden ist, und wenden Sie sie bei Bedarf erneut an.


In diesem Beispiel wird die Filterung so geändert, dass nur Benutzer synchronisiert werden, deren Attribute "mail" und "userPrincipalName" auf "@contoso.com" enden:

1. Melden Sie sich bei dem Computer, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, mithilfe eines Kontos an, das Mitglied der Sicherheitsgruppe "ADSyncAdmins" ist.
2. Öffnen Sie den Synchronisierungsregeln-Editor im Startmenü.
3. Klicken Sie unter "Regeltypen" auf "Ausgehend".
4. Suchen Sie nach der Regel namens "Out to AAD – User Join". Klicken Sie auf "Bearbeiten".
5. Klicken Sie im linken Navigationsbereich auf "Bereichsfilter". Klicken Sie auf "Klausel hinzufügen", und wählen Sie "mail" als Attribut aus. Wählen Sie "ENDSWITH" als Operator und "@contoso.com" als Werttyp aus. Klicken Sie auf "Klausel hinzufügen", und wählen Sie "userPrincipalName" als Attribut aus. Wählen Sie "ENDSWITH" als Operator und "@contoso.com" als Werttyp aus.
6. Klicken Sie auf Speichern.
7. Führen Sie eine vollständige Synchronisierung aus: Klicken Sie auf der Registerkarte "Connectors" mit der rechten Maustaste auf "SourceAD". Klicken Sie auf "Ausführen", auf "Vollständige Synchronisierung" und dann auf "OK".


## Nächste Schritte
Erfahren Sie mehr über die [Azure AD Connect-Synchronisierung](active-directory-aadconnectsync-whatis.md) Konfiguration.

Erfahren Sie mehr über [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md).


