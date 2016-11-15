

Push-meddelanden skickas vanligtvis via en serverdelstjänst, till exempel Mobile Apps eller ASP.NET, med hjälp av ett kompatibelt bibliotek. Du kan också använda REST-API:n direkt för att skicka meddelanden om ett bibliotek inte är tillgängligt för din serverdel. 

Här är en lista med andra guider du kan läsa som handlar om att skicka meddelanden:

* Azure Mobile Apps: Exempel på hur man skickar meddelanden från en Mobile Apps-serverdel integrerad med Notification Hubs finns i [Lägg till Push-meddelanden till din iOS-app](../articles/app-service-mobile/app-service-mobile-ios-get-started-push.md).  
* ASP.NET: [Använd Notification Hubs för push-meddelanden till användare](../articles/notification-hubs/notification-hubs-aspnet-backend-ios-apple-apns-notification.md).
* Azure Notification Hub Java SDK: Se [Använda Notification Hubs från Java](../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md) för information om att skicka meddelanden från Java. Det här har testats i Eclipse för Android-utveckling.
* PHP: [Använda Notification Hubs från PHP](../articles/notification-hubs/notification-hubs-php-push-notification-tutorial.md).

I nästa avsnitt av guiden kommer du att lära dig hur man använder [Notification Hub REST-gränssnittet](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) för att skicka meddelandet direkt i din app. Alla registrerade enheter tar emot meddelandet som skickas från vilken enhet som helst.  



<!--HONumber=Nov16_HO2-->


