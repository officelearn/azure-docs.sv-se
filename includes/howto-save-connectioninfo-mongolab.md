Es ist zwar möglich, einen MongoLab-URI in Ihren Code einzufügen, aber wir empfehlen zur leichteren Verwaltung, den Code in seiner Umgebung zu konfigurieren. Dies hat den Vorteil, dass Sie bei einer Änderung des URI den Code über das Azure-Portal aktualisieren können, ohne zu ihm gehen zu müssen.


1. Wählen Sie in der Azure-Verwaltungsportal **Web-Apps**.
1. Klicken Sie in der Liste der Web-Apps auf den Namen der gewünschten Web-App.  
![WebAppEntry][entry-website]  
Das Web-App-Dashboard wird angezeigt.

1. Klicken Sie auf **konfigurieren** in der Menüleiste.  
![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]

1. Scrollen Sie nach unten zum Abschnitt „Verbindungszeichenfolgen“.  
![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]

1. Für **Namen**, geben Sie mongolab_uri ein.
1. Für **Wert**, fügen Sie die Verbindungszeichenfolge, die im vorherigen Abschnitt erhalten haben.
1. Wählen Sie **benutzerdefinierte** in den **Typ** Dropdown-Liste (anstelle der standardmäßigen **SQLAzure**).
1. Klicken Sie auf **Speichern** auf der Symbolleiste.  
![SaveWebApp][button-website-save]

**Hinweis:** Azure fügt das **CUSTOMCONNSTR\_** Präfix für diese Variable, weshalb den Code oben auf **auf CUSTOMCONNSTR\_MONGOLAB_URI.**

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png


