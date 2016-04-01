####Konfigurieren des iOS-Projekts in Xamarin Studio

1. Öffnen Sie in Xamarin.Studio **Info.plist**, und aktualisieren Sie die **Bundle-ID** mit der Paket-ID, die Sie zuvor erstellt, mit der neuen App-ID haben

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)

2. Führen Sie einen Bildlauf nach unten, um **Background Modes** und überprüfen Sie die **Enable Background Modes** Feld und die **Remote Notifications** Feld. 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)

3. Klicken Sie mit der Doppelklicken auf das Projekt im Projektmappen-Bereich zu öffnen **Projektoptionen**.

4.  Wählen Sie **iOS Bundle Signing** unter **Erstellen**, und wählen Sie das entsprechende **Identität** und **Bereitstellungsprofil** Sie gerade eingerichtet haben für dieses Projekt. 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

    Hierdurch wird sichergestellt, dass das Projekt das neue Profil für Codesignierung verwendet. Die offizielle Dokumentation zur Xamarin-Gerät, finden Sie unter [Xamarin Device Provisioning].

####Konfigurieren des iOS-Projekts in Visual Studio

1. Klicken Sie in Visual Studio mit der rechten Maustaste, und klicken Sie dann auf **Eigenschaften**.

2. Klicken Sie auf den Eigenschaftenseiten auf die **iOS-Anwendung** Registerkarte, und aktualisieren Sie die **Bezeichner** mit der ID, die Sie zuvor erstellt haben.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)

3. In der **iOS Bundle Signing** Registerkarte, wählen Sie das entsprechende **Identität** und **Bereitstellungsprofil** Sie gerade eingerichtet haben für dieses Projekt. 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Hierdurch wird sichergestellt, dass das Projekt das neue Profil für Codesignierung verwendet. Die offizielle Dokumentation zur Xamarin-Gerät, finden Sie unter [Xamarin Device Provisioning].

4. "Info.plist", um es zu öffnen, und aktivieren Sie dann einen Doppelklick **RemoteNotifications** unter Background Modes. 



[Xamarin Device Provisioning]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/

