---
title: "Självstudiekurs: DevOps med Azure Portal | Microsoft Docs"
description: "Lär dig om de olika DevOps-arbetsflödena i Azure Portal."
services: azure-portal
documentationcenter: 
author: mlearned
manager: douge
editor: mlearned
ms.assetid: 4f1c5bc1-c732-4d35-b5df-0fd68e547d38
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/05/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: eec7d1402bdea4e5433c473dd713eed23aa80464
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-devops-with-the-azure-portal"></a>Självstudiekurs: DevOps med Azure Portal
Azure-plattformen är full av flexibla DevOps-arbetsflöden. I den här självstudiekursen lär du dig hur du utnyttjar funktionerna i Azure Portal för att utveckla, testa, distribuera, felsöka, övervaka och hantera program som körs. Den här självstudiekursen fokuserar på följande:

1. Skapa en webbapp och aktivera kontinuerlig distribution
2. Utveckla och testa en app
3. Övervaka och felsöka en app
4. Allmänna programhanteringsaktiviteter

## <a name="creating-a-web-app-and-enabling-continuous-deployment"></a>Skapa en webbapp och aktivera kontinuerlig distribution
Skapa en webbapp med [Azure App Service](https://azure.microsoft.com/services/app-service/), som du ska använda i resten av den här kursen. Du ska börja med att aktivera kontinuerlig distribution från lagringsplatsen för din källkod till vår aktiva Azure-miljö.

1. Logga in i Azure Portal
2. Välj **Apptjänster** &gt; **ikonen Lägg till** och ange ett namn. Välj din prenumeration och skapa en ny resursgrupp som ska användas som behållare för tjänsten.
   
   Du kan använda resursgrupper om du vill hantera olika aspekter av lösningen, t.ex. fakturering, distributioner och övervakning, som en grupp med hjälp av [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
   
   ![image1][image1]
3. Din apptjänst skapas efter en liten stund. Ägna några minuter åt att utforska de olika menyalternativen för tjänsten på portalen.
   
   ![image2][image2]    
4. Klicka på URL:en. Lägg märke till de många olika tillgängliga alternativen för verktyg och lagerplatser. Du kan också använda önskade språk och ramverk, inklusive .NET, Java, Ruby.
   
   ![image3][image3]    
5. Med Azure-portalen blir kontinuerlig distribution en enkel process som endast kräver några få enkla steg. På Azure-portalen väljer du inställningar från ikonen för den apptjänst som du precis skapat.
   
   ![image4][image4]
   
   Rulla ned till publiceringsavsnittet från bladet som öppnas till höger.
   
   ![image5][image5]
6. Konfigurera några inställningar för att aktivera kontinuerlig distribution för appen. Klicka på Distributionskälla och sedan på Välj källa. Lägg märke till de olika alternativ som du kan välja mellan för källans lagerplats.
   
   ![image6][image6]
7. Välj GitHub i det här exemplet. Du kan också välja valfri lagringsplats och konfigurera autentiseringsuppgifterna.
   
   ![image7][image7]
8. Efter auktoriseringen till lagerplatsen kan du välja ett projekt och en gren som du vill distribuera. Det finns flera fiktiva exempel nedan.
   
   ![image8][image8]
9. Klicka på OK när du har valt projekt och gren. Nu bör du se meddelanden om en distribution.
   
   ![image9][image9]
10. Gå tillbaka till GitHub för att se webhooken som skapades för att integrera källkontrolldatabasen med Azure. Med några få enkla steg kan du integrera Azure Portal med GitHub.
    
    ![image10][image10]
11. Om du vill se den kontinuerliga distributionen kan du snabbt lägga till lite innehåll i datalagret. Som ett enkelt exempel kan du lägga till en textfil i en GitHub-databas. Du kan använda .NET, Ruby, Python eller någon annan typ av program med App Service. Lägg till en textfil eller ett ASP.NET MVC-, Java- eller Ruby-program i valfritt datalager.
    
    ![image11][image11]
12. När du har bekräftat lagerändringarna kan du se att en ny distribution har initierats i portalens meddelandefält. Klicka på Synkronisera om du inte ser ändringarna när du har sparat dem i lagret.
    
    ![image12][image12]
13. Om du i detta läge försöker läsa in sidan för apptjänsten kan fel 403 returneras. I det här exemplet beror det på att det inte finns någon typisk standarddokumentkonfiguration för sidan, t.ex. en fil som index.htm eller default.html. Du kan snabbt åtgärda detta med verktygsuppsättningen i Azure Portal.  Välj Inställningar &gt; Programinställningar i Azure Portal.
    
     ![image13][image13]
14. Ett blad öppnas för programinställningar. Ange namnet på sidan ”SamplePage.html” och klicka på Spara. Ägna några minuter åt att utforska de andra inställningarna.
    
    ![image14][image14]
15. Du kan också uppdatera URL:en i webbläsaren så att du är säker på att ändringarna visas. I vårt exempel fylls sidan med enkel text. Varje ytterligare ändring i datalagret skulle resultera i en ny automatisk distribution.
    
    ![image15][image15]
    
    Det är enkelt att aktivera kontinuerlig distribution med Azure Portal. Du kan också skapa mer komplexa publiceringskanaler och använda många andra tekniker med befintliga system för källkontroll och kontinuerlig integration för distribution till Azure, t.ex. genom att använda automatiska utvecklings- och versionshanteringssystem.

## <a name="develop-and-test-an-app"></a>Utveckla och testa en app
Nu ska du göra några ändringar i kodbasen och snabbt distribuera dina ändringar. Du ska också konfigurera prestandatestning för webbappen.

1. Välj App Services i navigeringsfönstret i Azure Portal och leta upp din apptjänst.
   
   ![image16][image16]
2. Klicka på Verktyg
   
   ![image17][image17]
3. Lägg märke till utvecklingskategorin under Verktyg. Det finns flera användbara verktyg här som vi kan använda för att arbeta med appar utan att lämna Azure Portal. Klicka på Konsol.
   
   ![image18][image18]
4. Du kan skicka kommandon live för din app i konsolfönstret. Skriv kommandot dir och tryck på Retur. Observera att kommandon som kräver utökade privilegier inte fungerar.
   
   ![image19][image19]
5. Gå tillbaka till kategorin Utveckla och välj Visual Studio Online. Obs! Visual Studio Online heter Visual Studio Team Services nu.
   
   ![image20][image20]
6. Aktivera webbläsarredigering för din app.
   
   ![image21][image21]
7. Ett webbtillägg installerar för din app. Ett tillägg lägger snabbt och enkelt till funktioner till appar i Azure. Observera några av de andra tilläggstyperna i skärmbilden nedan.
   
   ![image22][image22]
8. Klicka på OK när Visual Studio Online-tillägget installeras.
   
   ![image23][image23]
9. En webbläsarflik öppnas där du ser IDE-utvecklingsmiljön direkt i webbläsaren. Observera att miljön nedan är från Chrome.
   
   ![image24][image24]
10. Du kan utföra flera aktiviteter, till exempel redigera filer, lägga till filer och mappar och ladda ned innehåll från live-webbplatsen. Gör en enkel ändring i filen SamplePage.html.
    
    ![image25][image25]
11. Ändringarna sparas automatiskt efter en liten stund. Om du går tillbaka till sidan kan du se ändringarna. Tänk på att live-redigeringar som dessa sällan lämpar sig för produktionsmiljöer. Dock gör verktygen det mycket enkelt att göra snabba ändringar för utvecklings- och testmiljöer.
    
    ![image26][image26]
    
    ![image27][image27]
12. Gå tillbaka till verktygsbladet och klicka på Prestandatest under kategorin Utveckla.
    
    ![image28][image28]
13. Du måste ange ett Team Services-konto. Mer information finns här: [Skapa ett Team Services-konto](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
14. Klicka på Nytt för att skapa ett prestandatest.
    
    ![image29][image29]
    
    Konfigurera de olika värdena och klicka på Kör test längst ned i dialogrutan för att initiera ett prestandatest.
    
    ![image30][image30]
    
    ![image31][image31]
15. Du kan övervaka statusen när testet körs.
    
    ![image32][image32]
    
    När testet är klart kan du visa mer information genom att klicka på resultatet.
    
    ![image33][image33]
16. I det här exemplet skapade du en liten testkörning, vilket innebär att mängden data som ska analyseras är begränsad, men du kan se olika mått och köra om testet från den här vyn. Med Azure Portal är det enkelt att skapa, köra och analysera webbprestandatester. Skärmbilderna nedan visar prestandadata.
    
    ![image34][image34]
    
    ![image35][image35]
    
    ![image36][image36]

## <a name="monitoring-and-troubleshooting-an-app"></a>Övervaka och felsöka en app
Azure tillhandahåller många funktioner för övervakning och felsökning av program som körs.

1. Välj Verktyg för vår webbapp i Azure Portal.
   
   ![image37][image37]
2. Observera de olika verktygsalternativen för att felsöka potentiella problem med en aktiv app under kategorin Felsök. Du kan till exempel övervaka Live HTTP-trafik, aktivera självåterställning, visa loggar osv.
   
   ![image38][image38]
3. Välj Platsmått för att snabbt få en överblick över vissa HTTP-koder.
   
   ![image39][image39]
4. Välj Diagnostik som tjänst. Välj din programtyp och välj Kör.
   
   ![image40][image40]
   
   En insamling börjar.
   
   ![image41][image41]
5. Välj relevant logg för att diagnostisera möjliga problem. Du måste aktivera loggning för att se alla tillgängliga dataalternativ, till exempel HTTP-loggar.
   
   ![image42][image42]
   
   Genom att klicka på minnesdumpfilen kan du ladda ned och analysera en DebugDiag-analysrapport för att identifiera möjliga fel.
   
   ![image43][image43]
6. Om du vill visa mer data måste du aktivera ytterligare loggning. Navigera till webbappen och välj Inställningar i Azure Portal.
   
   ![image44][image44]
7. Bläddra ned till funktionskategorin och välj Diagnostikloggar.
   
      ![image45][image45]
8. Lägg märke till de olika loggningsalternativen. Aktivera webbserverloggning och klicka på Spara.
   
   ![image46][image46]
9. Gå tillbaka till verktygsområdet för appen och välj Diagnostik som tjänst och klicka på Kör om du vill köra datainsamlingen igen.
   
   ![image47][image47]
10. När inställningen för HTTP-loggning är aktiverad ser du data som samlats in för HTTP-loggar.
    
    ![image48][image48]
11. Genom att klicka på HTML-filloggen kan du skapa en detaljerad webbläsarbaserad rapport för vidare utredning.
    
    ![image49][image49]
12. Gå tillbaka till verktygsavsnittet i Azure Portal för appen. Bläddra ned till verktygsavsnittet och välj Processutforskaren.
    
    ![image50][image50]
13. Genom att välja Processutforskaren kan du visa information om processer som körs. Observera nedan att du kan visa mer detaljer om processer och även avsluta processer från Azure Portal.
    
    ![image51][image51]
    
    ![image52][image52]
14. Gå tillbaka till bladet Inställningar till vänster. Klicka på Ny supportförfrågan.
    
    ![image53][image53]
15. Från bladet till höger kan du fylla i information om problemen, ange kontaktinformation och ladda upp diagnostikdata. Med Azure Portal är det enkelt att arbeta med Microsofts support.
    
    ![image54][image54]
    
    ![image55][image55]
    
    Azure Portal ger åtkomst till kraftfulla och välbekanta verktygsmiljöer som gör det enkelt att övervaka och felsöka program som körs. Du kan också snabbt vidta åtgärder och till exempel återanvända processer, aktivera och inaktivera olika datasamlingar eller integrera med Microsofts support.

## <a name="general-application-management"></a>Allmän programhantering
När du hanterar program behöver du ofta utföra många olika slags aktiviteter, till exempel konfigurera säkerhetskopieringsstrategier, implementera och hantera identitetsproviders och konfigurera rollbaserad åtkomstkontroll. Precis som med de andra DevOps-miljöerna integrerar Azure-plattformen dessa uppgifter direkt på portalen.

1. För att vara säker på att webbappen är skyddad mot dataförlust måste du konfigurera säkerhetskopieringar. Navigera till området Inställningar för webbappen.
   
   ![image56][image56]
2. Rulla ned till kategorin Funktioner på bladet till höger.
   
    ![image57][image57]
3. Välj Säkerhetskopior. Ett blad öppnas till höger.
   
   ![image58][image58]
4. Klicka på Konfigurera, välj ett lagringskonto från bladet till höger.
   
   ![image59][image59]
5. Skapa och välj en lagringsbehållare för säkerhetskopiorna. Klicka på Skapa längst ned på bladet. Välj behållaren.
   
   ![image60][image60]
6. När du har valt behållaren kan du konfigurera scheman och säkerhetskopieringar för dina databaser. I det här scenariot klickar du på ikonen Spara.
   
    ![image61][image61]
7. När du har sparat bläddrar du tillbaka till bladet till vänster för Säkerhetskopieringar. Säkerhetskopiera programmet genom att klicka på Säkerhetskopiera nu.
   
    ![image62][image62]
8. En säkerhetskopia skapas efter en liten stund. Observera alternativet Återställ nu i skärmbilden nedan.
   
    ![image63][image63]
9. Klicka på Återställ nu och gå igenom alternativen på bladet till höger. Du kan välja lämplig säkerhetskopia och enkelt återställa till ett tidigare tillstånd om det behövs. Med hjälp av Azure-portalen har vi snabbt skapat en enkel haveriberedskapsstrategi för appen.
   
    ![image64][image64]
10. Gå tillbaka till bladet Inställningar till vänster och välj Autentisering/auktorisering under Funktioner.
    
     ![image65][image65]
11. Välj App Service-autentisering till höger på bladet. Lägg märke till de olika alternativ som du kan konfigurera med populära providers.
    
     ![image66][image66]
12. Välj önskat provideralternativ och observera alternativen för omfånget. Du kan ange ett app-ID och en apphemlighet och snabbt aktivera Facebook-autentisering för appen. Azure Portal erbjuder autentisering som en nyckelfärdig lösning för appar.
    
     ![image67][image67]
13. Gå tillbaka till bladet Inställningar och välj Användare under kategorin Resurshantering.
    
     ![image68][image68]
14. Utforska de olika alternativen för att lägga till roller och användare på bladet till höger. På Azure Portal kan du enkelt styra programmets rollbaserade åtkomst.
    
     ![image69][image69]

## <a name="summary"></a>Sammanfattning
I den här självstudiekursen demonstrerade vi en del av kraften i Azure-plattformen genom att snabbt aktivera kontinuerlig distribution för en webbapp, utföra olika utvecklings- och testningsaktiviteter, övervaka och felsöka en live-app och slutligen hantera nyckelstrategier som haveriberedskap, identiteter och rollbaserad åtkomstkontroll. Dessa DevOps-arbetsflöden kan enkelt integreras med Azure-plattformen, och du kan arbeta effektivt i kontexten för den aktuella aktiviteten.

## <a name="next-steps"></a>Nästa steg
* Azure Resource Manager är viktigt för att aktivera DevOps på Azure-plattformen.  Mer information finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
* Mer information om Azure App Service-distributioner finns i [Distribuera en app till Azure App Service](../app-service-web/web-sites-deploy.md)

[image1]: ./media/tutorial-azureportal-devops/image1.png
[image2]: ./media/tutorial-azureportal-devops/image2.png
[image3]: ./media/tutorial-azureportal-devops/image3.png
[image4]: ./media/tutorial-azureportal-devops/image4.png
[image5]: ./media/tutorial-azureportal-devops/image5.png
[image6]: ./media/tutorial-azureportal-devops/image6.png
[image7]: ./media/tutorial-azureportal-devops/image7.png
[image8]: ./media/tutorial-azureportal-devops/image8.png
[image9]: ./media/tutorial-azureportal-devops/image9.png
[image10]: ./media/tutorial-azureportal-devops/image10.png
[image11]: ./media/tutorial-azureportal-devops/image11.png
[image12]: ./media/tutorial-azureportal-devops/image12.png
[image13]: ./media/tutorial-azureportal-devops/image13.png
[image14]: ./media/tutorial-azureportal-devops/image14.png
[image15]: ./media/tutorial-azureportal-devops/image15.png
[image16]: ./media/tutorial-azureportal-devops/image16.png
[image17]: ./media/tutorial-azureportal-devops/image17.png
[image18]: ./media/tutorial-azureportal-devops/image18.png
[image19]: ./media/tutorial-azureportal-devops/image19.png
[image20]: ./media/tutorial-azureportal-devops/image20.png
[image21]: ./media/tutorial-azureportal-devops/image21.png
[image22]: ./media/tutorial-azureportal-devops/image22.png
[image23]: ./media/tutorial-azureportal-devops/image23.png
[image24]: ./media/tutorial-azureportal-devops/image24.png
[image25]: ./media/tutorial-azureportal-devops/image25.png
[image26]: ./media/tutorial-azureportal-devops/image26.png
[image27]: ./media/tutorial-azureportal-devops/image27.png
[image28]: ./media/tutorial-azureportal-devops/image28.png
[image29]: ./media/tutorial-azureportal-devops/image29.png
[image30]: ./media/tutorial-azureportal-devops/image30.png
[image31]: ./media/tutorial-azureportal-devops/image31.png
[image32]: ./media/tutorial-azureportal-devops/image32.png
[image33]: ./media/tutorial-azureportal-devops/image33.png
[image34]: ./media/tutorial-azureportal-devops/image34.png
[image35]: ./media/tutorial-azureportal-devops/image35.png
[image36]: ./media/tutorial-azureportal-devops/image36.png
[image37]: ./media/tutorial-azureportal-devops/image37.png
[image38]: ./media/tutorial-azureportal-devops/image38.png
[image39]: ./media/tutorial-azureportal-devops/image39.png
[image40]: ./media/tutorial-azureportal-devops/image40.png
[image41]: ./media/tutorial-azureportal-devops/image41.png
[image42]: ./media/tutorial-azureportal-devops/image42.png
[image43]: ./media/tutorial-azureportal-devops/image43.png
[image44]: ./media/tutorial-azureportal-devops/image44.png
[image45]: ./media/tutorial-azureportal-devops/image45.png
[image46]: ./media/tutorial-azureportal-devops/image46.png
[image47]: ./media/tutorial-azureportal-devops/image47.png
[image48]: ./media/tutorial-azureportal-devops/image48.png
[image49]: ./media/tutorial-azureportal-devops/image49.png
[image50]: ./media/tutorial-azureportal-devops/image50.png
[image51]: ./media/tutorial-azureportal-devops/image51.png
[image52]: ./media/tutorial-azureportal-devops/image52.png
[image53]: ./media/tutorial-azureportal-devops/image53.png
[image54]: ./media/tutorial-azureportal-devops/image54.png
[image55]: ./media/tutorial-azureportal-devops/image55.png
[image56]: ./media/tutorial-azureportal-devops/image56.png
[image57]: ./media/tutorial-azureportal-devops/image57.png
[image58]: ./media/tutorial-azureportal-devops/image58.png
[image59]: ./media/tutorial-azureportal-devops/image59.png
[image60]: ./media/tutorial-azureportal-devops/image60.png
[image61]: ./media/tutorial-azureportal-devops/image61.png
[image62]: ./media/tutorial-azureportal-devops/image62.png
[image63]: ./media/tutorial-azureportal-devops/image63.png
[image64]: ./media/tutorial-azureportal-devops/image64.png
[image65]: ./media/tutorial-azureportal-devops/image65.png
[image66]: ./media/tutorial-azureportal-devops/image66.png
[image67]: ./media/tutorial-azureportal-devops/image67.png
[image68]: ./media/tutorial-azureportal-devops/image68.png
[image69]: ./media/tutorial-azureportal-devops/image69.png

