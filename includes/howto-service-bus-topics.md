## Vad är Service Bus-ämnen och -prenumerationer?

Service Bus-ämnen och -prenumerationer stöder en *publicera/prenumerera*-modell för meddelandekommunikation. När du använder ämnen och prenumerationer så kommunicerar inte komponenterna i ett distribuerat program direkt med varandra. Istället så utbyter de meddelanden via ett ämne, som agerar mellanhand.

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

Till skillnad från Service Bus-köer, där varje meddelande bearbetas av en enskild konsument så ger ämnen och prenumerationer en ”ett till många”-kommunikation med hjälp av ett publicera/prenumerera-mönster. Det är möjligt att registrera flera prenumerationer för ett ämne. När ett meddelande skickas till ett ämne så görs det tillgängligt för varje prenumeration för oberoende hantering/bearbetning.

En prenumeration på ett ämne liknar en virtuell kö som tar emot kopior av meddelanden som har skickats till ämnet. Alternativt kan du registrera filterregler för ett ämne på prenumerationsbasis, vilket låter dig filtrera eller begränsa vilka meddelanden till ett ämne som tas emot av vilka ämnesprenumerationer.

Service Bus-ämnen och -prenumerationer låter dig skala och bearbeta ett stort antal meddelanden för många användare och program.

## Skapa ett namnområde

För att komma igång med Service Bus-ämnen och -prenumerationer så måste du först skapa ett *namnområde för tjänsten*. Ett namnområde innehåller en omfattningsbehållare för adressering av Service Bus-resurser i ditt program.

Så här skapar du ett namnområde:

1.  Logga in på den [klassiska Azure-portalen][].

2.  I portalens vänstra navigationsfält klickar du på **Service Bus**.

3.  I den understa rutan i portalen klickar du på **Skapa**.   
    ![][0]

4.  I dialogrutan **Lägg till ett nytt namnområde** anger du ett namn för namnområdet. Systemet kontrollerar omedelbart om namnet är tillgängligt.   
    ![][2]

5.  Efter att du kontrollerat att namnet är tillgängligt, väljer du land eller region där namnområdet ska vara beläget (se till att använda samma land/region som du distribuerar dina beräkningsresurser i).

    > [AZURE.IMPORTANT] Välj **samma region** som du tänker välja när du distribuerar ditt program. Så får du bäst prestanda.

6.  Lämna de andra fälten i dialogrutan med sina standardvärden (**Meddelandetjänst** och **Standardnivå**) och klicka sedan på OK-kryssmarkeringen. Systemet skapar namnområdet och aktiverar det. Du kan behöva vänta några minuter medan systemet tilldelar resurser till ditt konto.

    ![][6]

## Skaffa standardautentiseringuppgifter för hantering av namnområdet

För att kunna utföra hanteringsåtgärder, som att skapa ett ämne eller en prenumeration i det nya namnområdet, så måste du skaffa autentiseringsuppgifter för hantering av namnområdet. Du kan hämta dessa autentiseringsuppgifter från portalen.

### Hämta autentiseringsuppgifter för hantering från portalen

1.  I det vänstra navigationsfönstret klickar du på **Service Bus**-noden för att visa listan över tillgängliga namnområden:   
    ![][0]

2.  Välj namnområdet som du nyss skapat från listan som visas:   
    ![][3]

3.  Klicka på **Anslutningsinformation**.   
    ![][4]

4.  I dialogrutan **Anslutningsinformation för åtkomst** hittar du den anslutningssträng som innehåller SAS-nyckeln och nyckelnamnet. Anteckna de här värdena, då du kommer att använda den här informationen senare för att genomföra åtgärder med namnområdet. 


  [klassiska Azure-portalen]: http://manage.windowsazure.com
  [0]: ./media/howto-service-bus-topics/sb-queues-13.png
  [2]: ./media/howto-service-bus-topics/sb-queues-04.png
  [3]: ./media/howto-service-bus-topics/sb-queues-09.png
  [4]: ./media/howto-service-bus-topics/sb-queues-06.png
  
  [6]: ./media/howto-service-bus-topics/getting-started-multi-tier-27.png




<!--HONumber=Jun16_HO2-->


