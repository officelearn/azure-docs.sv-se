In diesem Schritt erstellen Sie eine Firewallregel, um den Testport für den Endpunkt mit Lastenausgleich (59999 wie bereits angegeben) zu öffnen, und eine weitere Regel, um den Port des Verfügbarkeitsgruppenlisteners zu öffnen. Da Sie den Endpunkt mit Lastenausgleich auf den Azure-VMs erstellt haben, die Verfügbarkeitsgruppenreplikate enthalten, müssen Sie den Testport und den Listenerport auf den entsprechenden Azure-VMs öffnen.

1. Starten Sie auf virtuellen Computern, die Replikate hosten, **Windows-Firewall mit erweiterter Sicherheit**.

1. Mit der rechten Maustaste **Eingehende Regeln** und klicken Sie auf **neue Regel**.

1. In der **Regeltyp** Seite **Port**, klicken Sie dann auf **Weiter**.

1. In der **Protokolle und Ports** Seite **TCP** und **59999** in den **bestimmte lokale Ports** Feld. Klicken Sie dann auf **Weiter**.

1. In der **Aktion** beibehalten, Seite **Verbindung zulassen** ausgewählt, und klicken Sie auf **Weiter**.

1. In der **Profil** Seite, akzeptieren Sie die Standardeinstellungen, und klicken Sie auf **Weiter**.

1. In den **Namen** Seite, geben Sie einen Regelnamen ein, z. B. **Testport für AlwaysOn-Listener** in den **Namen** Textfeld, und klicken Sie auf **Fertig stellen**.

1. Wiederholen Sie die obigen Schritte für die Verfügbarkeit ListenerPort (wie weiter oben in der $EndpointPort-Parameter für das Skript angegeben), und geben Sie einen geeigneten Regelnamen, z. B. **AlwaysOn-ListenerPort**.

