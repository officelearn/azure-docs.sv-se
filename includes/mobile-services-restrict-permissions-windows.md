
1. Erweitern Sie im Server-Explorer in Visual Studio die **Azure** Knoten **Mobile Services**, und den mobilen Dienst.

2. Mit der rechten Maustaste die **TodoItem** auf **Berechtigungen bearbeiten**, legen Sie alle Berechtigungen **nur authentifizierte Benutzer**, und klicken Sie dann auf **Übernehmen**. Dadurch wird sichergestellt, dass alle Vorgänge für die _TodoItem_ Tabelle einen authentifizierten Benutzer erfordern.

3. Mit der rechten Maustaste in des Client-app-Projekts, klicken Sie auf **Debuggen**, dann **neue Instanz starten**; Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) ausgelöst wird, nachdem die app gestartet wurde.

    Dies geschieht, weil die app versucht, den mobilen Dienst als nicht authentifizierter Benutzer, aber die *TodoItem* Tabelle nun eine Authentifizierung verlangt.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.


