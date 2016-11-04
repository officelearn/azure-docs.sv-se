1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com) och klicka sedan på **+NY** längst ner på skärmen.
2. Klicka på **Apptjänster**, **Mobile Engagement** och sedan på **Skapa**.
   
    ![](./media/mobile-engagement-create-app-in-portal/create-mobile-engagement-app.png)
3. I popup-fönstret som visas anger du följande information:
   
    ![](./media/mobile-engagement-create-app-in-portal/create-azme-popup.png)
   
   * **Programnamn**: namnet på ditt program. 
   * **Plattform**: målplattform för appen. Du måste skapa en Mobile Engagement-app per plattform som din mobila app riktar sig till. 
   * **Programmets resursnamn**: det namn som programmet kommer att vara nåbart via API:er och URL:er. 
   * **Plats**: Region/datacenter där appen och appsamlingen kommer att finnas.
   * **Samling**: Välj en samling som skapats tidigare eller välj Ny samling.
   * **Samlingsnamn**: representerar din programgrupp. Detta säkerställer också att alla dina appar finns i en grupp som tillåter aggregerade måttberäkningar. Du bör använda ditt företagsnamn eller din avdelning här om det är tillämpligt.
4. Välj appen som du nyss skapade i **Program**-fliken.
5. Klicka på **ANSLUTNINGSINFORMATION** för att visa anslutningsinställningarna som du lägger till i din SDK-integrering i din mobilapp.
6. Kopiera **ANSLUTNINGSSTRÄNGEN**. Det här är vad du behöver för att identifiera appen i din programkod och ansluta med Mobile Engagement från din app.
   
    ![](./media/mobile-engagement-create-app-in-portal/app-connection-info-page.png)

<!--HONumber=Jun16_HO2-->


