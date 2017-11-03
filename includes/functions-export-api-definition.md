## <a name="export-an-api-definition"></a>Exportera en API-definition
Du har en OpenAPI definition för din funktion från [en OpenAPI definition för en funktion för att skapa](../articles/azure-functions/functions-openapi-definition.md). Nästa steg i den här processen är att exportera API-definitionen så att PowerApps och Microsoft Flow kan användas i en anpassad API.

> [!IMPORTANT]
> Kom ihåg att måste du vara inloggad i Azure med samma autentiseringsuppgifter som du använder för din PowerApps och Microsoft Flow innehavare. Detta gör att Azure för att skapa anpassade API: et och göra den tillgänglig för både PowerApps och Microsoft Flow.

1. I den [Azure-portalen](https://portal.azure.com), klickar du på din app funktionsnamn (t.ex. **funktionen demo energi**) > **funktioner** > **API-definition** .

    ![API-definition](media/functions-export-api-definition/api-definition.png)

1. Klicka på **exportera till PowerApps + flödet**.

    ![Källan för API-definition](media/functions-export-api-definition/export-api-1.png)

1. Använd inställningarna som anges i tabellen i den högra rutan.

    |Inställning|Beskrivning|
    |--------|------------|
    |**Exportera läge**|Välj **Express** att automatiskt generera anpassade API: et. Att välja **manuell** export API-definition, men du måste importera den till PowerApps och Microsoft Flow manuellt. Mer information finns i [exportera till PowerApps och Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Miljö**|Välj den miljö som anpassade API: N ska sparas. Mer information finns i [miljöer översikt (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) eller [miljöer översikt (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Namn på anpassade API**|Ange ett namn som `Turbine Repair`.|
    |**API-nyckelnamn**|Ange det namn som appen och flödet bör finns i anpassat API-gränssnitt. Observera att exemplet innehåller användbar information.|
 
    ![Exportera till PowerApps och Microsoft Flow](media/functions-export-api-definition/export-api-2.png)

1. Klicka på **OK**. Anpassade API: et har nu skapats och lagts till i miljön som du angav.