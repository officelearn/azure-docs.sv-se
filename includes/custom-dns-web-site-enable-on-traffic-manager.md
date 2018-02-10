När posterna för domännamnet har spridits ska du kunna använda webbläsaren för att verifiera att ditt domännamn kan användas för åtkomst till ditt webbprogram i Azure App Service.

> [!NOTE]
> Det kan ta lite tid för din CNAME sprids via DNS-systemet. Du kan använda en tjänst som <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> att verifiera att CNAME är tillgänglig.
> 
> 

Om du inte redan har lagts ditt webbprogram som en Traffic Manager-slutpunkt måste du göra detta innan namnmatchning fungerar som domänen namnet vägar i Traffic Manager. Traffic Manager dirigerar sedan till ditt webbprogram. Använd informationen i [Lägg till eller ta bort slutpunkter](../articles/traffic-manager/traffic-manager-endpoints.md) att lägga till ditt webbprogram som en slutpunkt i Traffic Manager-profilen.

> [!NOTE]
> Om webbappen inte visas när du lägger till en slutpunkt, kontrollera att den är konfigurerad för **Standard** läge för App Service-plan. Du måste använda **Standard** läge för ditt webbprogram för att fungera med Traffic Manager.
> 
> 

1. I webbläsaren och öppna den [Azure Portal](https://portal.azure.com).
2. I den **Web Apps** klickar du på namnet på ditt webbprogram, Välj **inställningar**, och välj sedan **anpassade domäner**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. I den **anpassade domäner** bladet, klickar du på **lägga till värdnamnet**.
4. Använd den **värdnamn** textrutor för att ange domännamnet för Traffic Manager ska associeras med det här webbprogrammet.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. Klicka på **verifiera** att spara konfigurationen av domain name.
6. När du klickar på **verifiera** Azure kommer startar domänverifiering arbetsflöde. Detta kommer att söka efter ägarskap för domänen som värdnamn tillgänglighet och rapporten slutfört eller detaljerade fel med normativ guidence om hur du åtgärdar felet.    
7. När valideringen har lyckats **lägga till värdnamnet** knappen blir aktiv och du kommer att kunna tilldela värdnamnet. Navigera till ditt domännamn i en webbläsare. Du bör nu se din app körs med ditt domännamn. 
   
   När konfigurationen är klar, visas det anpassade domännamnet i den **domännamn** avsnitt av ditt webbprogram.

Nu ska du kunna ange domännamnet för Traffic Manager i webbläsaren och se att det har tar dig till ditt webbprogram.

