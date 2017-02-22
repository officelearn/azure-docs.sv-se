

Om du vill registrera appen för push-meddelanden via APNS (Apple Push Notification Service) måste du skapa ett nytt push-certifikat, app-ID och etableringsprofil för projektet på Apples utvecklarportal. App-ID:t innehåller konfigurationsinställningar som gör att din app kan skicka och ta emot push-meddelanden. De här inställningarna innehåller de push-meddelandecertifikat som behövs för att autentisera med APNS (Apple Push Notification Service) vid sändning och mottagning av push-meddelanden. Mer information om de här koncepten finns i den officiella [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584)-dokumentationen.

#### <a name="generate-the-certificate-signing-request-file-for-the-push-certificate"></a>Generera filen för begäran om certifikatsignering för push-certifikatet
Dessa steg hjälper dig att skapa begäran om certifikatsignering. Den används för att generera ett push-certifikat som ska användas med APNS.

1. Kör Nyckelhanteraren på din Mac. Den kan öppnas från mappen **Verktyg** eller mappen **Annat** i startmenyn.
2. Klicka på **Nyckelhanteraren**, expandera **Certifikatassistenten** och klicka sedan på **Begär ett certifikat från en certifikatutfärdare...**.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Ange en **e-postadress för användaren** och ett **vanligt namn**, se till att **Sparas till disk** är markerat och klicka sedan på **Fortsätt**. Lämna fältet **CA-e-postadress** tomt eftersom det inte behövs.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)
4. Ange ett namn för filen för begäran om certifikatsignering (CSR) i **Spara som**, ange en plats i **Var** och klicka sedan på **Spara**.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)
   
      CSR-filen sparas på den valda platsen. Standardplatsen är skrivbordet. Kom ihåg den plats du valde för den här filen.

#### <a name="register-your-app-for-push-notifications"></a>Registrera din app för push-meddelanden
Skapa ett nytt explicit app-ID för ditt program med Apple och konfigurera det för push-meddelanden.  

1. Navigera till [iOS-etableringsportalen](http://go.microsoft.com/fwlink/p/?LinkId=272456) i Apple Developer Center, logga in med ditt Apple-ID, klicka på **Identifierare**, klicka sedan på **App-ID:n** och klicka slutligen på **+** logga in för att registrera en ny app.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)
2. Uppdatera följande tre fält för din nya app och klicka sedan på **Fortsätt**:
   
   * **Namn**: Ange ett beskrivande namn för din app i **Namn**-fältet i avsnittet **Beskrivning av App-ID**.
   * **Paketidentifierare**: Under avsnittet **Uttrycklig App-ID**, anger du en **paketidentifierare** i formuläret `<Organization Identifier>.<Product Name>` enligt [appdistributionsguiden](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Den måste matcha den som används i XCode-, Xamarin- eller Cordova-projektet för din app.
   * **Push-meddelanden**: Markera alternativet **Push-meddelanden** i avsnittet **Apptjänster**.
     
     ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)
3. Granska inställningarna på skärmen Confirm your App ID (Bekräfta ditt app-ID) och när du är klar klickar du på **Submit** (Skicka)
4. När du har skickat det nya app-ID:t visas skärmen **Registration complete** (Registreringen är slutförd). Klicka på **Klar**.
5. Under App-ID:n i Developer Center letar du upp den App-ID som du just skapade och klickar på dess rad. Om du klickar på app-ID-raden visas appens information. Klicka på **Redigera**-knappen längst ned.
6. Bläddra till längst ned på skärmen och klicka på knappen **Skapa certifikat...** i avsnittet **Development Push SSL-certifikat**.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)
   
       This will display the "Add iOS Certificate" assistant.
   
   > [!NOTE]
   > Den här guiden använder ett utvecklarcertifikat. Du använder samma process när du registrerar ett driftscertifikat. Se bara till att du använder samma certifikattyp när du skickar meddelanden.
   > 
   > 
7. Klicka på **Välj fil** och bläddra till den plats där du sparade begäran om certifikatsignering (CSR) för ditt push-certifikat. Klicka sedan på **Generera**.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)
8. När portalen har skapat certifikatet klickar du på **Hämta**.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)
   
       This downloads the signing certificate and saves it to your computer in your Downloads folder.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)
   
   > [!NOTE]
   > Som standard kallas den hämtade filen, ett utvecklingscertifikat, **aps_development.cer**.
   > 
   > 
9. Dubbelklicka på det hämtade push-certifikatet **aps_development.cer**. Då installeras det nya certifikatet i nyckelringen enligt nedan:
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)
   
   > [!NOTE]
   > Certifikatnamnet kan skilja sig, men prefixet kommer att vara **Apple Development iOS Push Services:**.
   > 
   > 
10. I nyckelhanteraren ctrl-klickar du på det nya push-certifikatet som du precis skapade i **Certifikat**-kategorin. Klicka på **Exportera**, namnge filen, välj **.p12**-formatet och klicka sedan på **Spara**.
    
    Notera filnamnet och platsen för det exporterade push-certifikatet (.p12). Filen kommer att användas för att aktivera autentisering med APNS genom att ladda upp den på den klassiska Azure-portalen.

#### <a name="create-a-provisioning-profile-for-the-app"></a>Skapa en etableringsprofil för appen
1. I <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> väljer du **Etableringsprofiler**, markerar **Alla** och klickar sedan på **+**-knappen för att skapa en ny profil. Guiden **Lägg till iOS-etableringsprofil** startas
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)
2. Välj **iOS App Development** under **Utveckling** som etableringsprofiltyp och klicka på **Fortsätt**.
3. Därefter väljer du den App-ID som du just skapat från listrutan **App-ID** och klickar på **Fortsätt**
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)
4. På skärmen **Välj certifikat** väljer du ditt utvecklingscertifikat som används för kodsignering och klickar på **Fortsätt**. Det här är ett signeringscertiikat, inte push-certifikatet du nyss skapade.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)
5. Därefter väljer du de **enheter** som ska användas för testning och klickar på **Fortsätt**
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)
6. Slutligen väljer du ett namn för profilen i **Profilnamn** och klickar på **Generera**.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)



<!--HONumber=Feb17_HO1-->


