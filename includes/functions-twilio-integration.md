Det här exemplet är med hjälp av den [Twilio](https://www.twilio.com/) -tjänsten för att skicka SMS-meddelanden till en mobiltelefon. Azure Functions redan har stöd för Twilio via den [Twilio bindning](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio), och funktionen används.

Det första du behöver är en Twilio-konto. Du kan skapa en fritt https://www.twilio.com/try-twilio. När du har ett konto kan du lägga till följande tre **appinställningar** i appen funktion.

| Appen inställningsnamn | Värdebeskrivning |
| - | - |
| **TwilioAccountSid**  | SID för ditt Twilio-konto |
| **TwilioAuthToken**   | Auth-token för ditt Twilio-konto |
| **TwilioPhoneNumber** | Telefonnumret som är associerade med ditt Twilio-konto. Det här används för att skicka SMS-meddelanden. |