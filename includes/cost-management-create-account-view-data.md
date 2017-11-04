## <a name="view-cost-data"></a>Visa kostnadsdata

Azure Cost Management från Cloudyn ger dig åtkomst till alla dina molnresursdata. I instrumentpanelsrapporterna hittar du både standardrapporter och anpassade rapporter i en flikvy. Följande är exempel på en populär instrumentpanel och en rapport som visar dig kostnadsdata direkt.

![Hanteringspanelen](./media/cost-management-create-account-view-data/mgt-dash.png)

I det här exemplet visar hanteringspanelen konsoliderade kostnader för företaget Contoso i alla molnresurser. Contoso använder Azure, AWS och Google. Instrumentpaneler ger översiktlig information och är snabba sätt att navigera till rapporter.  

Om du är osäker på rapportens syfte på en instrumentpanel hovrar du över **i**-symbolen så visas en förklaring. Klicka på valfri rapport på en instrumentpanel för att visa hela rapporten.

Du kan även visa rapporter i rapportmenyn högst upp i portalen. Vi tar en titt på utgifterna för Contosos Azure-resurs under de senaste 30 dagarna. Klicka på **Cost** (Kostnad) > **Cost Analysis** (Kostnadsanalys) > **Actual Cost Analysis** (Analys av faktiska kostnader). Rensa eventuella värden för taggar, grupper eller filter i rapporten.

![Analys av faktiska kostnader](./media/cost-management-create-account-view-data/actual-cost-01.png)

I det här exemplet är 75 970 USD totalkostnaden och budgeten är 130 000 USD.

Nu ändrar vi rapportformatet och anger grupper och filter för att begränsa resultaten för Azure-kostnader. Ställ in **Date Range** (Datumintervall) på de senaste 30 dagarna. Klicka högst upp till höger på kolumnsymbolen för att formatera som ett stapeldiagram och välj **Provider** under Groups (Grupper). Ställ sedan in ett filter för **Provider** på **Azure**.

![Analys av faktiska kostnader filtrerat](./media/cost-management-create-account-view-data/actual-cost-02.png)

I det här exemplet var totalkostnaden för Azure-resurser 3 839 USD under de senaste 30 dagarna.

Högerklicka på fältet Provider (Azure) och öka detaljnivån till **Resource types** (Resurstyper).

![öka detaljnivå](./media/cost-management-create-account-view-data/actual-cost-03.png)

Följande bild visar kostnaderna för Azure-resurser som Contoso har ådragit sig. Totalsumman var 3 839 USD. I det här exemplet var ungefär hälften av kostnaderna för lokalt redundant lagring och ungefär den andra hälften av kostnaderna var för olika VM-instanser.

![resurstyper](./media/cost-management-create-account-view-data/actual-cost-04.png)

Högerklicka på en resurstyp och välj **Cost Entities** (Kostnadsenheter) för att visa kostnadsenheter och de tjänster som har förbrukat resursen. Tjänsterna VM och Workers i DevOps har förbrukat 486,60 USD och 435,71 i det här exemplet. Totalsumman för båda är 922 USD.

![kostnadsenheter och tjänster](./media/cost-management-create-account-view-data/actual-cost-05.png)

Om du vill se en video som självstudier om hur du visar ditt moln fakturering data, se [analysera ditt moln fakturering data med Azure kostnaden Management med Cloudyn](https://youtu.be/G0pvI3iLH-Y).
