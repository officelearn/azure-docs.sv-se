
Dieses Lernprogramm baut auf den [GetStartedWithMobileServices app](http://go.microsoft.com/fwlink/p/?LinkID=510826), also eine universelle Windows-app-Projekt in Visual Studio 2013. Die Benutzeroberfläche dieser App und die vom Mobile Services-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden. 

1. Laden Sie die C#-Version der GetStartedWithMobileServices-Beispiel-App von der [Website mit den Codebeispielen für Entwickler] herunter. 

2. Öffnen Sie in Visual Studio 2013 das heruntergeladene Projekt, und sehen Sie sich die im Projektordner "GetStartedWithData.Shared" befindliche Datei "MainPage.xaml.cs" an.

    Beachten Sie, dass hinzugefügt **TodoItem** Objekte befinden sich in einem speicherinternen **ObservableCollection & Lt; TodoItem & Gt;**.

3. Drücken Sie die **F5** um das Projekt neu erstellen und die app zu starten.

4. Geben Sie in der app Text in **Insert a TodoItem**, klicken Sie dann auf **Speichern**.

    ![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup.png) 

    Beachten Sie, dass der gespeicherte Text angezeigt wird.

5. Mit der rechten Maustaste in des Windows Phone 8.1-Projekts, klicken Sie auf **als Startprojekt festlegen**, drücken Sie dann die **F5** Windows Phone Store-app zu starten.  

    ![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup-wp8.png)

6. Wiederholen Sie die Schritte 3 und 4, um sicherzustellen, dass sich die Beispiel-App gleichermaßen verhält.

