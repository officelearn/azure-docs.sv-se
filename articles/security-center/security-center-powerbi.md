---
title: "Kunskap genom statistik från Azure Security Center med Power BI | Microsoft Docs"
description: "Med innehållspaketet Azure Security Center Power BI är det lätt att hitta säkerhetsaviseringar, rekommendationer, angripna resurser och trender utifrån en datamängd som är särskilt anpassad efter dina rapporteringsbehov."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 0ded6bc7-52e8-43b4-8940-0bee137526e3
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: yurid
ms.openlocfilehash: 10f7b8f20cc41a5ebb1b1376e2bf17be02600ae4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="get-insights-from-azure-security-center-data-with-power-bi"></a>Kunskap genom statistik från Azure Security Center med Power BI
I [Power BI-instrumentpanelen](http://aka.ms/azure-security-center-power-bi) för Azure Security Center kan du se, analysera och filtrera rekommendationer och säkerhetsaviseringar var du än är, även på mobila enheter. I Power BI-instrumentpanelen kan du se trender och angreppsmönster, se säkerhetsaviseringar indelade efter resurs eller käll-IP-adress och oåtgärdade säkerhetsrisker indelade efter resurs och hur lång tid det gått sedan de påträffades.

Du kan också kombinera Security Centers rekommendationer och säkerhetsaviseringar med andra data på intressanta sätt, till exempel använda data från [Azure-granskningsloggarna](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) och [Azure SQL Database Auditing](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/). Båda innehåller Power BI-instrumentpaneler och du kan också exportera dessa data till Excel för enkel rapportering av säkerhetstillståndet för dina molnresurser.

## <a name="using-azure-security-center-dashboard-to-access-power-bi"></a>Öppna Power BI från Azure Security Centers instrumentpanel
Du kan även öppna Power BI-rapporter från instrumentpanelen i Azure Security Center. Följ stegen:

1. Klicka på knappen **Power BI** på instrumentpanelen i **Azure Security Center**.

    ![Ansluta till Azure Security Center med Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-1-newUI-2017.png)
2. Bladet **Power BI** öppnas på höger sida som på bilden nedan:

    ![Ansluta till Azure Security Center med Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new11-2017.png)
3. Om det är första gången du skapar Power BI-instrumentpanelen kan du välja ett av följande alternativ i **Utforska i Power BI**-bladet:

   * **Security insights dashboard (Instrumentpanel för säkerhetsinformation)**: Välj det här alternativet om du vill skapa en instrumentpanel med säkerhetsstatus, trådar och identifieringar. Det här alternativet väljs ofta av utvecklare som har ansvar för analys av skyddsstatus och hittade varningar inom prenumerationerna.
   * **Policy management dashboard (Instrumentpanel för principhantering)**: Välj det här alternativet om du vill arbeta med hantering och säkerhetsrutiner.  Det här alternativet väljs ofta av centralt IT-ansvariga som mest arbetar med styrning och administration. I den här instrumentpanelen kan de se om säkerhetsrutinerna följs i organisationen.
   * Om du redan har en instrumentpanel med Power BI klickar du på **Go to your current Power BI dashboard (Gå till din befintliga Power BI-instrumentpanel)**.
4. Klicka på alternativet **Instrumentpanel för säkerhetsinformation** för det här exemplet. Om det är första gången du skapar en Power BI-instrumentpanel för Security Center, uppmanas du att installera innehållspaketet. Klicka på knappen **Hämta** i fönstret **Innehållspaket för Power BI**, som det visas på följande skärmbild:

    ![Azure Security Center, instrumentpanel för säkerhetsinformation](./media/security-center-powerbi/security-center-powerbi-fig1-new3.png)
5. Fönstret **Anslut till Azure Security Center Security Insights** visas. Kontrollera att **Autentisering**-metoden är **oAuth2** som det visas nedan och klicka på knappen **Logga in**.

    ![Autentisering](./media/security-center-powerbi/security-center-powerbi-fig1-new4.png)
6. Du kan bli ombedd att autentisera igen med dina Azure-autentiseringsuppgifter. Efter autentiseringen skapas instrumentpanelen. När instrumentpanelen har skapats visas en rapport med liknande struktur som den som visas på följande skärmbild:

    ![Power BI-instrumentpanel](./media/security-center-powerbi/security-center-powerbi-fig1-new5.png)

> [!NOTE]
> En uppdatering av rapporten schemaläggs för att ske dagligen. Om den här uppdateringen inte fungerar kan du läsa artikeln om [möjliga uppdateringsproblem med Azure Security Center Power BI](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/) för mer information om hur du felsöker problemet.
>
>

Här kan du se hur många säkerhetsaviseringar och rekommendationer det finns, samt hur många virtuella datorer, Azure SQL-databaser och nätverksresurser som övervakas i Azure Security Center.

Det finns en länk till Azure Security Center som tar dig till Azure Portal. I de här diagrammen är det enkelt att få överblick över säkerhetsrekommendationer och säkerhetsaviseringar som:

* Resurssäkerhetsstatus
* Outförda rekommendationer
* Rekommendationer för virtuella datorer
* Aviseringar över tid
* Angripna resurser
* Angripna IP-adresser

Bakom de olika diagrammen finns ytterligare information. Om du klickar på en ruta visas mer information. I ikonen **Resurssäkerhetsstatus** rutan med resurssäkerhetshälsa visas till exempel ytterligare information om outförda rekommendationer efter resurs, så som det visas på skärmbilden nedan:

![Rekommendationer](./media/security-center-powerbi/security-center-powerbi-fig1-new6.png)

Om du klickar på någon av staplarna i diagrammet blir de andra inaktiva och fokus ligger endast på den stapel som är markerad. När du vill gå tillbaka till instrumentpanelen klickar du på **Azure Security Center** under alternativet **Dashboards (Instrumentpaneler)** till vänster på sidan.

> [!NOTE]
> Om du vill anpassa rapporten efter egna önskemål kan du redigera den och lägga till extra fält och ändra befintliga illustrationer. Läs artikeln om att [interagera med rapporter i redigeringsvyn i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/) för mer information.
>
>

Om du klickar på någon av rutorna **Alerts over Time (Aviseringar över tid), Attacked Resources (Angripna resurser)** och **Attacker IPs (Angripande IP-adresser)** får du upp liknande information. Det beror på att all information om de här tre variablerna sammanställs i rapporten under **Resurser som är angripna** så som det visas på skärmbilden nedan:

![Resurser som är angripna](./media/security-center-powerbi/security-center-powerbi-fig1-new7.png)

Härifrån kan du även spara en kopia av rapporten, skriva ut den eller publicera den på webben med hjälp av de alternativ som finns i menyn **File (Arkiv)**.

![Arkivmenyn](./media/security-center-powerbi/security-center-powerbi-fig8.png)

## <a name="exploring-your-azure-security-center-data-with-power-bi-services"></a>Utforska dina data i Azure Security Center med Power BI-tjänsterna
Anslut till [tjänsterna i innehållspaketet för Power BI](https://msit.powerbi.com/groups/me/getdata/services) i Power BI och genomför följande steg:

1. I fönstret **Content Pack for Power BI (Innehållspaket för Power BI)** ser du de två alternativen som visas nedan.

    ![Innehållspaket för Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new.png)

   > [!NOTE]
   > Om du redan har verkställt den första delen av den här artikeln visas bara ett alternativ, Azure Security Center-principhantering.
   >
   >
2. I det här exemplet klickar du på **Get (Hämta)** i rutan **Azure Security Center Policy Management (Azure Security Center-principhantering)**.
3. I fönstret **Connect to Azure Security Center Policy Management (Anslut till Azure Security Center-principhantering)** ska alternativet **oAuth2** vara markerat i listrutan under **Authentication Method (Autentiseringsmetod)** så som visas på bilden nedan. Klicka sedan på **Sign in (Logga in)**.

    ![Principhanteringsfönstret](./media/security-center-powerbi/security-center-powerbi-fig1-new8.png)
4. Du omdirigeras nu till en autentiseringssida där du anger de inloggningsuppgifter som du brukar använda för att ansluta till Azure Security Center. När autentiseringen är avklarad börjar de data som ska visas i rapporten importeras. Meddelandet nedan kan visas i högra hörnet i webbläsaren medan importen pågår.

    ![Ansluta till Azure Security Center med Power BI](./media/security-center-powerbi/security-center-powerbi-fig4.png)

   > [!NOTE]
   > När instrumentpanelen skapas för första gången kan det ta längre tid än vanligt, särskilt om du har många prenumerationer.
   >
   >
5. När allt är färdigt öppnas rapporten **Principhantering** i Power BI-instrumentpanelen i Azure Security Center, en rapport som liknar den som visas nedan:

    ![Instrumentpanelen för principhantering](./media/security-center-powerbi/security-center-powerbi-fig1-new9.png)

## <a name="see-also"></a>Se även
I det här avsnittet har du fått lära dig hur du använder Power BI i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md) – Här får du lära dig hur du kan börja använda Azure Security Center.
* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper i Azure Security Center.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här finns vanliga frågor om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure
