### <a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Bevilja Mobile Engagement åtkomst till din GCM API-nyckel
Om du vill låta Mobile Engagement skicka push-meddelanden för dig, måste du bevilja Mobile Engagement åtkomst till din API-nyckel. Detta görs genom att konfigurera och ange nyckeln i Mobile Engagement-portalen.

1. Kontrollera via din klassiska Azure-portal att du befinner dig i appen som vi använder för det här projektet och klicka sedan på **Starta**-knappen längst ned:
   
    ![](./media/mobile-engagement-android-send-push/engage-button.png)
2. Klicka sedan på **Inställningar** -> **Systemspecifik push-avisering**-avsnittet för att ange din GCM-nyckel:
   
    ![](./media/mobile-engagement-android-send-push/engagement-portal.png)
3. Klicka på **Redigera**-ikonen framför **API-nyckel** i avsnittet **GCM-inställningar** enligt nedan:
   
    ![](./media/mobile-engagement-android-send-push/native-push-settings.png)
4. I popup-fönstret klistrar du in GCM-servernyckeln som du erhållit tidigare och klickar sedan på **OK**.
   
    ![](./media/mobile-engagement-android-send-push/api-key.png)

## <a name="a-idsendasend-a-notification-to-your-app"></a><a id="send"></a>Skicka ett meddelande till din app
Nu ska vi skapa en enkel push-meddelandekampanj som skickar ett push-meddelande till vår app.

1. Gå till **REACH**-fliken i din Mobile Engagement-portal.
2. Klicka på **Nytt meddelande** för att skapa din push-meddelandekampanj.
   
    ![](./media/mobile-engagement-android-send-push/new-announcement.png)
3. Ställ in det första fältet i din kampanj med följande steg:
   
    ![](./media/mobile-engagement-android-send-push/campaign-first-params.png)
   
    a. Namnge din kampanj.
   
    b. Ställ in **Leveranstyp** som *Systemmeddelande -> enkelt*: Det här är den enkla push-meddelandetypen i Android som innehåller en rubrik och en kort textrad.
   
    c. Ställ in **Leveranstid** som *När som helst* så att appen tar emot ett meddelande vare sig den är startad eller inte.
   
    d. Som meddelandetexttyp anger du **Rubrik** som kommer att vara i fetstil i push-meddelandet.
   
    e. Sedan skriver du ditt **meddelande**
4. Bläddra ned till avsnittet **Innehåll** och välj **Endast meddelande**.
   
    ![](./media/mobile-engagement-android-send-push/campaign-content.png)
5. Nu har du ställt in den mest grundläggande kampanjen som går. Rulla nedåt igen och klicka på **skapa**-knappen för att spara din kampanj.
6. Sista steget: klicka på **Aktivera** om du vill aktivera din kampanj för att skicka push-meddelanden.
   
    ![](./media/mobile-engagement-android-send-push/campaign-activate.png)



<!--HONumber=Nov16_HO2-->


