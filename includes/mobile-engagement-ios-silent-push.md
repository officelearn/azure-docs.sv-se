> [!IMPORTANT]
> För att kunna ta emot push-meddelanden från Mobile Engagement måste du aktivera `Silent Remote Notifications` i ditt program. Du måste lägga till värdet remote-notification i UIBackgroundModes-matrisen i din Info.plist-fil.
> 
> 

1. Öppna `info.plist`-filen i projektet
2. Högerklicka på det översta objektet i listan (`Information Property List`) och lägg till en ny rad
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png)
3. På den nya raden anger du `Required background modes`
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png)
4. Klicka på vänsterpilen för att expandera raden
5. Lägg till följande värde till objektet 0 `App downloads content in response to push notifications`
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png)
6. När du gjort ändringen borde XML-filen info.plist innehålla följande nyckel och värde:
   
        <key>UIBackgroundModes</key>
        <array>
        <string>remote-notification</string>
        </array>
7. Om du använder **Xcode 7+** och **iOS 9+**:
   
   * Aktivera **Push-meddelanden** i Mål > Ditt målnamn > Funktioner.

<!--HONumber=Jun16_HO2-->


