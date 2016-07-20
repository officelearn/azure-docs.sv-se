###Bevilja Mobile Engagement åtkomst till ditt Push-certifikat

Om du vill låta Mobile Engagement skicka push-meddelanden för dig, måste du bevilja Mobile Engagement åtkomst till ditt certifikat. Det görs genom att konfigurera och ange ditt certifikat i Mobile Engagement-portalen. Kontrollera att du har fått ditt .p12-certifikat enligt beskrivningen i [Apples dokumentation](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

1. Gå till din Mobile Engagement-portal. Se till att du befinner dig i rätt och klicka sedan på **Engage**-knappen längst ned:

    ![](./media/mobile-engagement-ios-send-push/engage-button.png)

2. Klicka på **Inställningar**-sidan i din Engagement-portal. Därifrån klickar du på avsnittet **Systemspecifik push-avisering** för att överföra ditt p12-certifikat:

    ![](./media/mobile-engagement-ios-send-push/engagement-portal.png)

3. Välj ditt p12, överför det och ange ditt lösenord:

    ![](./media/mobile-engagement-ios-send-push/native-push-settings.png)

##<a id="send"></a>Skicka ett meddelande till din app

Nu ska vi skapa en enkel push-meddelandekampanj som skickar ett push till vår app:

1. Gå till **Reach**-fliken i din Mobile Engagement-portal.

2. Klicka på **nytt meddelande** för att skapa din push-kampanj

    ![](./media/mobile-engagement-ios-send-push/new-announcement.png)

3. Ställ in de första fälten i din kampanj:

    ![](./media/mobile-engagement-ios-send-push/campaign-first-params.png)

    -   Ge din kampanj ett **namn** 
    -   Ställ in **Leveranstid** som **Endast utanför appen**: det är den enkla Apple-push-meddelandetypen som innehåller text.
    -   I meddelandetexten skriver du först **rubriken** som kommer att vara den första raden i push-meddelandet.
    -   Sedan skriver du ditt **meddelande** som kommer att vara den andra raden

4. Bläddra ned och välj **Endast meddelande** i innehållssektionen

    ![](./media/mobile-engagement-ios-send-push/campaign-content.png)

5. Nu har du ställt in den mest grundläggande kampanjen. Bläddra nedåt och klicka på **Skapa**-knappen för att spara din push-meddelandekampanj. 

6. Slutligen klickar du på **Aktivera** för att skicka push-meddelandet. 

    ![](./media/mobile-engagement-ios-send-push/campaign-activate.png)

7. Du kommer att kunna ta emot meddelandet på din iOS-enhet i meddelandecentret på följande vis:

    ![](./media/mobile-engagement-ios-send-push/iphone-notification.png)

8. Om du har en Apple Watch parad med iOS-enheten så kommer du att se meddelandet på din Apple Watch:

    ![](./media/mobile-engagement-ios-send-push/apple-watch.png)


 

 


<!--HONumber=Jun16_HO2-->


