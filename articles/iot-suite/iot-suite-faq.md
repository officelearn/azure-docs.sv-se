<properties
  pageTitle="Vanliga frågor och svar om Azure IoT Suite | Microsoft Azure"
  description="Vanliga frågor och svar om IoT Suite"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="get-started-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="05/09/2016"
  ms.author="araguila"/>
   
# Vanliga frågor och svar om IoT Suite

### Vad är skillnaden mellan att ta bort en resursgrupp på Azure-portalen och att klicka på Ta bort för en förkonfigurerad lösning på azureiotsuite.com?

- Om du tar bort den förkonfigurerade lösningen på [azureiotsuite.com][lnk-azureiotsuite] tar du bort alla resurser som etablerades när du skapade den förkonfigurerade lösningen. Och om du har lagt till ytterligare resurser i resursgruppen så tas även dessa bort. 

- Om du tar bort resursgruppen på [Azure-portalen][lnk-azure-portal] tar du bara bort resurserna i den resursgruppen. Du måste även ta bort Azure Active Directory-programmet som är associerat med den förkonfigurerade lösningen på [den klassiska Azure-portalen][lnk-classic-portal].

### Hur många DocumentDB-instanser kan jag etablera i en prenumeration?

Fem. Du kan skapa en [Azure-supportbiljett][link-azuresupportticket] för att höja den här gränsen, men som standard kan du bara etablera fem DocumentDB-instanser per prenumeration. Detta innebär att du bara kan etablera upp till fem förkonfigurerade lösningar för fjärrövervakning i en viss prenumeration.

### Hur många kostnadsfria Bing Maps-API:er kan jag etablera i en prenumeration?

Två. Du kan bara skapa två kostnadsfria Bing Maps-API:er i en prenumeration. Fjärrövervakningslösningen etableras som standard med ett kostnadsfritt Bing Maps-API. Detta betyder att du bara kan etablera upp till två fjärrövervakningslösningar i en prenumeration utan några ändringar.

### Jag har en distribution av en fjärrövervakningslösning med en statisk karta. Hur lägger jag till en interaktiv Bing-karta? 
1. Hämta Bing Maps-API:et för Enterprise QueryKey från [Azure-portalen][lnk-azure-portal]: 
 1. Gå till resursgruppen där ditt Bing Maps-API för företag finns på [Azure-portalen][lnk-azure-portal].
 2. Klicka på Alla inställningar och sedan på Nyckelhantering. 
 3. Som du ser finns det två nycklar: MasterKey och QueryKey. Kopiera värdet för QueryKey.

     > [AZURE.NOTE] Har du inget Bing Maps-API för Enterprise-konto? Skapa ett på [Azure-portalen][lnk-azure-portal] genom att klicka på + Nytt, leta upp Bing Maps-API för företag och följ sedan anvisningarna för att skapa kontot.

2. Hämta den senaste koden från [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].

3. Kör en lokal distribution eller en molndistribution genom att följa riktlinjerna för kommandoradsbaserad distribution i mappen /docs/ i databasen. 

4. När du har kört en lokal eller molnbaserad distribution tittar du i rotmappen efter *.user.config-filen som skapades under distributionen. Öppna den här filen i en textredigerare. 

5. Ändra följande rad så att den inkluderar värdet som du kopierade från QueryKey: 
   
  `<setting name="MapApiQueryKey" value="" />`

### Kan jag skapa en förkonfigurerad lösning om jag har Microsoft Azure för DreamSpark?
För närvarande kan du inte skapa en förkonfigurerad lösning med ett [Microsoft Azure för DreamSpark][lnk-dreamspark]-konto. Du kan dock skapa ett [kostnadsfritt utvärderingskonto för Azure][lnk-30daytrial] på bara några minuter som du kan använda för att skapa en förkonfigurerad lösning.

### Hur tar jag bort en AAD-klient?

Information om hur du tar bort en Azure AD-klient finns i Eric Golpes blogginlägg [Walkthrough of Deleting an Azure AD Tenant][lnk-delete-aad-tennant].

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx



<!--HONumber=jun16_HO2-->


