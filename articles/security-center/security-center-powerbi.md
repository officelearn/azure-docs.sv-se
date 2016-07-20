<properties
   pageTitle="Kunskap genom statistik från Azure Security Center med Power BI| Microsoft Azure"
   description="Med innehållspaketet Azure Security Center Power BI är det lätt att hitta säkerhetsaviseringar, rekommendationer, angripna resurser och trender utifrån en datamängd som är särskilt anpassad efter dina rapporteringsbehov."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/03/2016"
   ms.author="yurid"/>

# Kunskap genom statistik från Azure Security Center med Power BI
I [Power BI-instrumentpanelen](http://aka.ms/azure-security-center-power-bi) för Azure Security Center kan du se, analysera och filtrera rekommendationer och säkerhetsaviseringar var du än är, även på mobila enheter. I Power BI-instrumentpanelen kan du se trender och angreppsmönster, se säkerhetsaviseringar indelade efter resurs eller käll-IP-adress och oåtgärdade säkerhetsrisker indelade efter resurs och hur lång tid det gått sedan de påträffades. Du kan även kombinera rekommendationerna och säkerhetsaviseringarna från Security Center med andra data på intressanta sätt, till exempel [Azure-granskningsloggarna](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) och [Azure SQL Database Auditing](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/), som båda har Power BI-instrumentpaneler. Siffrorna kan även exporteras till Excel för enkel rapportering om säkerhetsläget för molnresurser.

> [AZURE.NOTE] Informationen i det här dokumentet rör förhandsversionen av Azure Security Center.


##Öppna Power BI från Azure Security Centers instrumentpanel
Du kan även öppna Power BI-rapporter från instrumentpanelen i Azure Security Center. Så här gör du det: 

1. Klicka på **Explore in Power BI (Utforska i Power BI) **i instrumentpanelen i **Azure Security Center**.

    ![Ansluta till Azure Security Center med Power BI](./media/security-center-powerbi/security-center-powerbi-fig9-new.png) 

2. Bladet **Explore in Power BI (Utforska i Power BI)** öppnas på höger sida som på bilden nedan:

    ![Ansluta till Azure Security Center med Power BI](./media/security-center-powerbi/security-center-powerbi-fig2-new.png)

3. Om det är första gången du skapar Power BI-instrumentpanelen kan du välja mellan alternativen nedan i Power BI-bladet: 

    - **Security insights dashboard (Instrumentpanel för säkerhetsinformation)**: Välj det här alternativet om du vill skapa en instrumentpanel med säkerhetsstatus, trådar och identifieringar. Det här alternativet väljs ofta av utvecklare som har ansvar för analys av skyddsstatus och hittade varningar inom prenumerationerna.
    - **Policy management dashboard (Instrumentpanel för principhantering)**: Välj det här alternativet om du vill arbeta med hantering och säkerhetsrutiner.  Det här alternativet väljs ofta av centralt IT-ansvariga som mest arbetar med styrning och administration. I den här instrumentpanelen kan de se om säkerhetsrutinerna följs i organisationen.
    - Om du redan har en instrumentpanel med Power BI klickar du på **Go to your current Power BI dashboard (Gå till din befintliga Power BI-instrumentpanel)**.

4. För de här exempelinstruktionerna väljer du **Security insights dashboard (Instrumentpanel för säkerhetsinformation)**. Då öppnas fönstret nedan:

    ![Azure Security Center, instrumentpanel för säkerhetsinformation](./media/security-center-powerbi/security-center-powerbi-fig3-new.png)

5. Under **Authentication method (Autentiseringsmetod)** ska det stå **oAuth2**. Klicka på **Sign in (Logga in)**.
6. Fönstret **Power BI** öppnas och du ser en rapport som liknar den nedan:
    
    ![Instrumentpanel för säkerhetsinformation](./media/security-center-powerbi/security-center-powerbi-fig5.png)

> [AZURE.NOTE] Rapporten är inställd att uppdateras en gång om dagen. Om den här uppdateringen inte fungerar kan du läsa artikeln om [möjliga uppdateringsproblem med Azure Security Center Power BI](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/) för mer information om hur du felsöker problemet.

Här kan du se hur många säkerhetsaviseringar och rekommendationer det finns, samt hur många virtuella datorer, Azure SQL-databaser och nätverksresurser som övervakas i Azure Security Center

Det finns en länk till Azure Security Center som tar dig till Azure Portal. I de här diagrammen är det enkelt att få överblick över säkerhetsrekommendationer och säkerhetsaviseringar som:

- Resurssäkerhetshälsa
- Outförda rekommendationer generellt
- Rekommendationer för virtuella datorer
- Aviseringar över tid
- Angripna resurser
- Angripna IP-adresser

Bakom de olika diagrammen finns ytterligare information. Om du klickar på en ruta visas mer information. Bakom rutan med resurssäkerhetshälsa visas till exempel ytterligare information om outförda rekommendationer uppdelat efter resurs, som på bilden nedan:

![Rekommendationer](./media/security-center-powerbi/security-center-powerbi-fig6.png)

Om du klickar på någon av staplarna i diagrammet blir de andra inaktiva och fokus ligger endast på den stapel som är markerad. När du vill gå tillbaka till instrumentpanelen klickar du på **Azure Security Center** under alternativet **Dashboards (Instrumentpaneler)** till vänster på sidan.

> [AZURE.NOTE] Om du vill anpassa rapporten efter egna önskemål kan du redigera den och lägga till extra fält och ändra befintliga illustrationer. Läs artikeln om att [interagera med rapporter i redigeringsvyn i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/) för mer information.

Om du klickar på någon av rutorna **Alerts over Time (Aviseringar över tid), Attacked Resources (Angripna resurser)** och **Attacker IPs (Angripande IP-adresser)** får du upp liknande information. Det beror på att all information om de här tre variablerna sammanställs i rapporten under **Resources under Attack (Angripna resurser)** som på bilden nedan:

![Resurser som är angripna](./media/security-center-powerbi/security-center-powerbi-fig7.png)

Härifrån kan du även spara en kopia av rapporten, skriva ut den eller publicera den på webben med hjälp av de alternativ som finns i menyn **File (Arkiv)**.

![Arkivmenyn](./media/security-center-powerbi/security-center-powerbi-fig8.png)

## Utforska dina data i Azure Security Center med Power BI-tjänsterna

Anslut till [tjänsterna i innehållspaketet för Power BI](https://msit.powerbi.com/groups/me/getdata/services) i Power BI och följ instruktionerna nedan:

1. I fönstret **Content Pack for Power BI (Innehållspaket för Power BI)** ser du de två alternativen som visas nedan.

    ![Innehållspaket för Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new.png)

2. I det här exemplet klickar du på **Get (Hämta)** i rutan **Azure Security Center Policy Management (Azure Security Center-principhantering)**.

3. I fönstret **Connect to Azure Security Center Policy Management (Anslut till Azure Security Center-principhantering)** ska alternativet **oAuth2** vara markerat i listrutan under **Authentication Method (Autentiseringsmetod)** så som visas på bilden nedan. Klicka sedan på **Sign in (Logga in)**.

    ![Principhanteringsfönstret](./media/security-center-powerbi/security-center-powerbi-fig4-new.png)

4. Du omdirigeras nu till en autentiseringssida där du anger de inloggningsuppgifter som du brukar använda för att ansluta till Azure Security Center. När autentiseringen är avklarad börjar de data som ska visas i rapporten importeras. Meddelandet nedan kan visas i högra hörnet i webbläsaren medan importen pågår.

    ![Ansluta till Azure Security Center med Power BI](./media/security-center-powerbi/security-center-powerbi-fig4.png)

    >[AZURE.NOTE] När instrumentpanelen skapas för första gången kan det ta längre tid än vanligt, särskilt om du har många prenumerationer. 

5. När allt är färdigt öppnas rapporten **Policy Management (Principhantering)** i Power BI-instrumentpanelen i Azure Security Center.


## Nästa steg
I det här avsnittet har du fått lära dig hur du använder Power BI i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

- [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md): Här får du lära dig hur du kan börja använda Azure Security Center.
- [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md): Här får du lära dig hur du ställer in säkerhetsprinciper i Azure Security Center.
- [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
- [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
- [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.



<!--HONumber=Jun16_HO2-->


