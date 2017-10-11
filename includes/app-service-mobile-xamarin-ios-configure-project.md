#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Konfigurera iOS-projekt i Xamarin Studio
1. Öppna i Xamarin.Studio, **Info.plist**, och uppdatera den **Paketidentifierare** med paket-ID som du skapade tidigare med din nya app-ID.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Rulla ned till **bakgrundslägen**. Välj den **aktivera bakgrundslägen** rutan och **Remote notifications** rutan.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Dubbelklicka på ditt projekt i panelen lösning för att öppna **Projektalternativ**.
4. Under **skapa**, Välj **iOS paket signering**, och markera motsvarande identitet och provisioning-profil du just ställt in för det här projektet.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Detta säkerställer att projektet använder den nya profilen för kodsignering. Officiell Xamarin enheten etablering dokumentation finns [Xamarin Enhetsetableringen].

#### <a name="configure-the-ios-project-in-visual-studio"></a>Konfigurera iOS-projekt i Visual Studio
1. Högerklicka på projektet i Visual Studio och klicka sedan på **egenskaper**.
2. I för egenskapssidor, klickar du på den **iOS programmet** fliken och uppdatera den **identifierare** med det ID som du skapade tidigare.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. I den **iOS paket signering** markerar du motsvarande identitet och etableringsprofil som du precis har ställts in för det här projektet.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Detta säkerställer att projektet använder den nya profilen för kodsignering. Officiell Xamarin enheten etablering dokumentation finns [Xamarin Enhetsetableringen].
4. Dubbelklicka på filen Info.plist för att öppna den och sedan aktivera **RemoteNotifications** under **bakgrundslägen**.

[Xamarin Enhetsetableringen]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
