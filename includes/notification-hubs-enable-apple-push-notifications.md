

## <a name="generate-the-certificate-signing-request-file"></a>Generera filen för begäran om certifikatsignering
APNS (Apple Push Notification Service) använder sig av certifikat för att autentisera dina push-meddelanden. Följ de här instruktionerna för att skapa nödvändiga push-certifikat för att skicka och ta emot meddelanden. Mer information om de här koncepten finns i den officiella [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584)-dokumentationen.

Generera filen för begäran om certifikatsignering (CSR), som används av Apple för att generera ett signerat push-certifikat.

1. Kör Nyckelhanteraren på din Mac. Den kan öppnas från mappen **Verktyg** eller mappen **Annat** i startmenyn.
2. Klicka på **Nyckelhanteraren**, expandera **Certifikatassistenten** och klicka sedan på **Begär ett certifikat från en certifikatutfärdare...**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Ange en **e-postadress för användaren** och ett **vanligt namn**, se till att **Sparas till disk** är markerat och klicka sedan på **Fortsätt**. Lämna fältet **CA-e-postadress** tomt eftersom det inte behövs.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)
4. Ange ett namn för filen för begäran om certifikatsignering (CSR) i **Spara som**, ange en plats i **Var** och klicka sedan på **Spara**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)
   
      CSR-filen sparas på den valda platsen. Standardplatsen är skrivbordet. Kom ihåg den plats du valde för den här filen.

Därefter behöver du registrera din app med Apple, aktivera push-meddelanden och ladda upp den exporterade CSR-filen för att skapa ett push-certifikat.

## <a name="register-your-app-for-push-notifications"></a>Registrera din app för push-meddelanden
För att kunna skicka push-meddelanden till en iOS-app måste du registrera programmet med Apple och registrera dig för push-meddelanden.  

1. Om du inte redan har registrerat din app, navigerar du till <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> i Apple Developer Center, loggar in med ditt Apple-ID, klickar på **Identifierare**, klickar sedan på **App-ID:n** och klickar slutligen på **+** logga in för att registrera en ny app.
   
       ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)
2. Uppdatera följande tre fält för din nya app och klicka sedan på **Fortsätt**:
   
   * **Namn**: Ange ett beskrivande namn för din app i **Namn**-fältet i avsnittet **Beskrivning av App-ID**.
   * **Paketidentifierare**: Under avsnittet **Uttrycklig App-ID**, anger du en **paketidentifierare** i formuläret `<Organization Identifier>.<Product Name>` enligt [appdistributionsguiden](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Den *organisationsidentifierare* och det *produktnamn* som du använder måste matcha organisationsidentifieraren och produktnamnet som du kommer att använda när du skapar ditt XCode-projekt. I skärmbilden nedan används *NotificationHubs* som en organisationsidentifierare och *GetStarted* som produktnamnet. Kontrollera att detta matchar de värden som du kommer att använda i ditt XCode-projekt, eftersom du då kan använda rätt publiceringsprofil med XCode. 
   * **Push-meddelanden**: Markera alternativet **Push-meddelanden** i avsnittet **Apptjänster**.
     
     ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)
     
      Detta genererar ditt app-ID och ber dig att bekräfta informationen. Klicka på **Registrera** för att bekräfta det nya app-ID:et.
     
      När du klickar på **Registrera** visas skärmen **Registreringen har slutförts** enligt nedan. Klicka på **Klar**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


1. Under App-ID:n i Developer Center letar du upp den App-ID som du just skapade och klickar på dess rad.
   
       ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)
   
       Clicking on the app ID will display the app details. Click the **Edit** button at the bottom.
   
       ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)
2. Bläddra till längst ned på skärmen och klicka på knappen **Skapa certifikat...** i avsnittet **Development Push SSL-certifikat**.
   
       ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)
   
       This displays the "Add iOS Certificate" assistant.
   
   > [!NOTE]
   > Den här guiden använder ett utvecklarcertifikat. Du använder samma process när du registrerar ett driftscertifikat. Se bara till att du använder samma certifikattyp när du skickar meddelanden.
   > 
   > 
3. Klicka på **Välj fil**, bläddra till platsen där du sparade CSR-filen som du skapade i den första uppgiften och klicka sedan på **Generera**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)
4. När certifikatet har skapats av portalen klickar du på **Hämta**-knappen och klickar sedan på **Klar**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)
   
       This downloads the certificate and saves it to your computer in your Downloads folder.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)
   
   > [!NOTE]
   > Som standard kallas den hämtade filen, ett utvecklingscertifikat, **aps_development.cer**.
   > 
   > 
5. Dubbelklicka på det hämtade push-certifikatet **aps_development.cer**.
   
       This installs the new certificate in the Keychain, as shown below:
   
       ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)
   
   > [!NOTE]
   > Certifikatnamnet kan skilja sig, men prefixet kommer att vara **Apple Development iOS Push Services:**.
   > 
   > 
6. I nyckelhanteraren högerklickar du på det nya push-certifikatet som du skapade i **Certifikat**-kategorin. Klicka på **Exportera**, namnge filen, välj **.p12**-formatet och klicka sedan på **Spara**.
   
    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)
   
    Anteckna filnamnet och platsen dit .p12-certifikatet exporterats. Dessa kommer att användas för att aktivera autentisering med APNS.
   
   > [!NOTE]
   > Den här guiden skapar en QuickStart.p12-fil. Filnamnet och sökvägen kan vara olika för dig.
   > 
   > 

## <a name="create-a-provisioning-profile-for-the-app"></a>Skapa en etableringsprofil för appen
1. I <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> väljer du **Etableringsprofiler**, markerar **Alla** och klickar sedan på **+**-knappen för att skapa en ny profil. Guiden **Lägg till iOS-etableringsprofil** startas
   
       ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)
2. Välj **iOS App Development** under **Utveckling** som etableringsprofiltyp och klicka på **Fortsätt**. 
3. Därefter väljer du den App-ID som du just skapat från listrutan **App-ID** och klickar på **Fortsätt**
   
       ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)
4. På skärmen **Välj certifikat**, väljer du ditt vanliga utvecklingscertifikat som används för kodsignering och klickar på **Fortsätt**. Det här är inte push-certifikatet du nyss skapade.
   
       ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)
5. Därefter väljer du de **enheter** som ska användas för testning och klickar på **Fortsätt**
   
       ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)
6. Slutligen väljer du ett namn för profilen i **Profilnamn** och klickar på **Generera**.
   
       ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
7. När den nya etableringsprofilen har skapats klickar du och hämtar den och installerar den på din XCode-utvecklingsdator. Klicka sedan på **Klar**.
   
       ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)



<!--HONumber=Nov16_HO2-->


