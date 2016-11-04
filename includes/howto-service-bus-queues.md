## Vad är Service Bus-köer?
Service Bus-köer stöder kommunikation med hjälp av en **asynkron meddelandetjänst**. När du använder köer kommunicerar komponenter i ett distribuerat program inte direkt med varandra. De utbyter istället meddelanden via en kö som fungerar som en mellanhand (asynkron meddelandekö). En meddelandeproducent (avsändare) lämnar meddelandet till kön och fortsätter sedan dess bearbetning. En meddelandekonsument (mottagare) hämtar asynkront meddelandet från kön och bearbetar det. Producenten behöver inte vänta på ett svar från konsumenten för att kunna fortsätta bearbetningen och skicka fler meddelanden. Köer erbjuder **FIFO (First in, first out)**-leverans av meddelanden till en eller flera konkurrerande konsumenter. Det betyder att meddelanden mottas och bearbetas vanligtvis av mottagarna i den ordning som de lagts till i kön, och varje meddelande tas bara emot och bearbetas av en meddelandekonsument.

![QueueConcepts](./media/howto-service-bus-queues/sb-queues-08.png)

Service Bus-köer är en mångsidig teknologi som kan användas för en mängd olika scenarier:

* Kommunikation mellan webb- och arbetsroller i ett Azure-program med flera nivåer.
* Kommunikation mellan lokala appar och appar med Azure som värd, i en hybridlösning.
* Kommunikation mellan komponenter i ett distribuerat program som körs lokalt i olika organisationer eller avdelningar i en organisation.

Med hjälp av köer kan du skala ditt program enklare och med bättre återhämtning i din arkitektur.

## Skapa ett namnområde för tjänsten
För att komma igång med Service Bus-köer i Azure, måste du först skapa ett namnområde för tjänsten. Ett namnområde innehåller en omfattningsbehållare för adressering av Service Bus-resurser i ditt program.

Så här skapar du ett namnområde:

1. Logga in på den [klassiska Azure-portalen][klassiska Azure-portalen].
2. I portalens vänstra navigationsfält klickar du på **Service Bus**.
3. I den understa rutan i portalen klickar du på **Skapa**.
   ![](./media/howto-service-bus-queues/sb-queues-03.png)
4. I dialogrutan **Lägg till ett nytt namnområde** anger du ett namn för namnområdet. Systemet kontrollerar omedelbart om namnet är tillgängligt.   
   ![](./media/howto-service-bus-queues/sb-queues-04.png)
5. Efter att du kontrollerat att namnet är tillgängligt, väljer du land eller region där namnområdet ska vara beläget (se till att använda samma land/region som du distribuerar dina beräkningsresurser i).
   
   > [!IMPORTANT]
   > Välj **samma region** som du tänker välja när du distribuerar ditt program. Så får du bäst prestanda.
   > 
   > 
6. Lämna de andra fälten i dialogrutan med sina standardvärden (**Meddelandetjänst** och **Standardnivå**) och klicka sedan på OK-kryssmarkeringen. Systemet skapar namnområdet och aktiverar det. Du kan behöva vänta några minuter medan systemet tilldelar resurser till ditt konto.
   
   ![](./media/howto-service-bus-queues/getting-started-multi-tier-27.png)

Det tar ett tag för namnområdet du skapat att aktiveras och synas i portalen. Vänta tills statusen för namnområdet är **Aktiv** innan du fortsätter.

## Skaffa standardautentiseringuppgifter för hantering av namnområdet
För att kunna utföra hanteringsåtgärder, som att skapa en kö i det nya namnområdet så måste du skaffa autentiseringsuppgifter för hantering av namnområdet. Du kan hämta dessa autentiseringsuppgifter från den [klassiska Azure-portalen][klassiska Azure-portalen].

### Så här hämtar du autentiseringsuppgifter för hantering från portalen
1. I det vänstra navigationsfönstret klickar du på **Service Bus**-noden för att visa listan över tillgängliga namnområden:   
   ![](./media/howto-service-bus-queues/sb-queues-13.png)
2. Välj namnområdet som du nyss skapat från listan som visas:   
   ![](./media/howto-service-bus-queues/sb-queues-09.png)
3. Klicka på **Anslutningsinformation**.   
   ![](./media/howto-service-bus-queues/sb-queues-06.png)
4. I fönstret **Anslutningsinformation för åtkomst** hittar du den anslutningssträng som innehåller SAS-nyckeln och nyckelnamnet.   
   
   ![](./media/howto-service-bus-queues/multi-web-45.png)
5. Anteckna nyckeln eller kopiera den till Urklipp.

[klassiska Azure-portalen]: http://manage.windowsazure.com




<!--HONumber=Jun16_HO2-->


