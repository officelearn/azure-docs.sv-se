---
title: Autentisera Azure Stream Analytics-jobb till Azure Data Lake Storage Gen1 utdata
description: Den här artikeln beskriver hur du använder hanterade identiteter för att autentisera ditt Azure Stream Analytics-jobb till Azure Data Lake Storage Gen1 utdata.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/18/2019
ms.custom: seodec18
ms.openlocfilehash: 4537d15f88732d4b0c3c3cf514d6b8528af10f81
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737475"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities-preview"></a>Autentisera Stream Analytics till Azure Data Lake Storage Gen1 med hjälp av hanterade identiteter (förhandsversion)

Azure Stream Analytics stöder hanterad identitetsautentisering med Azure Data Lake Storage (ADLS) Gen1 utdata. Identiteten är ett hanterat program i Azure Active Directory som representerar ett visst Stream Analytics-jobb och kan användas för att autentisera till en resurs. Hanterade identiteter utan begränsningar av användarbaserade autentiseringsmetoder, som behöver autentiseras på nytt på grund av ändring av lösenord eller användaren token kontolösenordet som sker var 90: e dag. Dessutom hanterade identiteter med hjälp av automatisering av Stream Analytics-jobbet distributioner som utdata till Azure Data Lake Storage Gen1.

Gå till den [åtta nya funktioner i Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/) blogginlägg för att registrera dig för den här förhandsversionen och Läs mer om nya funktioner.

Den här artikeln visar tre sätt att aktivera hanterad identitet för Azure Stream Analytics-jobb som producerar till en Azure Data Lake Storage Gen1 via Azure-portalen, Azure Resource Manager för malldistribution och Azure Stream Analytics-verktyg för Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure Portal

1. Starta genom att skapa ett nytt Stream Analytics-jobb eller genom att öppna ett befintligt jobb i Azure-portalen. På menyraden på vänster sida av skärmen, Välj **hanterad tjänstidentitet (förhandsversion)** finns under **konfigurera**.

   ![Konfigurera förhandsversionen av Stream Analytics-hanterad identitet](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Välj **Använd systemtilldelade hanterad tjänstidentitet (förhandsversion)** från fönstret som visas till höger. Klicka på **spara** till ett huvudnamn för tjänsten identitet för Stream Analytics-jobb i Azure Active Directory. Livscykeln för den nyligen skapade identiteten ska hanteras av Azure. När Stream Analytics-jobbet tas bort, raderas automatiskt associerade identiteten (det vill säga tjänstens huvudnamn) av Azure.

   När konfigurationen är sparad anges i objekt-ID (OID) för tjänstens huvudnamn som ägar-ID som visas nedan:

   ![ID för Stream Analytics tjänstens huvudnamn](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Tjänstens huvudnamn har samma namn som Stream Analytics-jobb. Om namnet på ditt jobb är till exempel **MyASAJob**, namnet på tjänstens huvudnamn har skapats är också **MyASAJob**.

3. I egenskapsfönstret utdata av ADLS Gen1 utdatamottagaren, klickar du på autentiseringsläge listrutan och välj **hanterad tjänstidentitet (förhandsversion)**.

4. Fyll i resten av egenskaperna. Mer information om hur du skapar ett ADLS-utdata finns [skapa ett Data lake Store-utdata med stream analytics](../data-lake-store/data-lake-store-stream-analytics.md). När du är klar klickar du på **spara**.

   ![Konfigurera Azure Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Gå till sidan översikt av ADLS-Gen1 och klicka på **datautforskaren**.

   ![Konfigurera Data Lake-lagring – översikt](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. I Data explorer-fönstret väljer **åtkomst** och klicka på **Lägg till** i fönstret åtkomst.

   ![Konfigurera åtkomst till Data Lake-lagring](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. I textrutan på den **Välj användare eller grupp** rutan skriver du namnet på tjänstens huvudnamn. Kom ihåg att namnet för tjänstens huvudnamn som också är namnet på det motsvarande Stream Analytics-jobbet. När du börjar skriva huvudnamnet visas under textrutan. Välj önskad tjänstens huvudnamn och klicka på **Välj**.

   ![Välj tjänstens huvudnamn](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. I den **behörigheter** fönstret, kontrollera den **skriva** och **kör** behörigheter och koppla det till **den här mappen och alla underordnade**. Klicka sedan på **Ok**.

   ![Välj Skriv och kör-behörighet](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Tjänstens huvudnamn i listan under **tilldelade behörigheter** på den **åtkomst** fönstret som visas nedan. Du kan nu gå tillbaka och börja ditt Stream Analytics-jobb.

   ![Stream Analytics åtkomst till listan i portalen](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Läs mer om Data Lake Storage Gen1 filsystemsbehörigheter i [åtkomstkontroll i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Stream Analytics-verktyg för Visual Studio

1. I JobConfig.json, ställer du in **Använd systemtilldelade identiteter** till **SANT**.

   ![Stream Analytics-jobbkonfigurationen hanterade identiteter](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. I egenskapsfönstret utdata av ADLS Gen1 utdatamottagaren, klickar du på autentiseringsläge listrutan och välj **hanterad tjänstidentitet (förhandsversion)**.

   ![ADLS utdata hanterade identiteter](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Fyll i resten av egenskaperna och klicka på **spara**.

4. Klicka på **skicka till Azure** i frågeredigeraren.

   När du har skickat jobbet göra verktygen två saker:

   * Skapar automatiskt en tjänst huvudnamn identitet för Stream Analytics-jobb i Azure Active Directory. Livscykeln för den nyligen skapade identiteten ska hanteras av Azure. När Stream Analytics-jobbet tas bort, raderas automatiskt associerade identiteten (det vill säga tjänstens huvudnamn) av Azure.

   * Automatiskt in **skriva** och **kör** behörigheter för ADLS-Gen1 prefix sökvägen som används i jobbet och tilldela den till den här mappen och alla underordnade.

5. Du kan skapa Resource Manager-mallar med följande egenskap med [Stream Analytics-CI. CD-Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) version 1.5.0 eller senare på en dator för build (utanför Visual Studio). Följ resurshanteraren malldistributionen stegen i nästa avsnitt för att hämta tjänsten huvudnamn och bevilja åtkomst till tjänstens huvudnamn via PowerShell.

## <a name="resource-manager-template-deployment"></a>Resource Manager för malldistribution

1. Du kan skapa en *Microsoft.StreamAnalytics/streamingjobs* resursen med en hanterad identitet genom att inkludera följande egenskap i avsnittet för Resource Manager-mallen:

   ```json
   "Identity": {
   "Type": "SystemAssigned",
   },
   ```

   Azure Resource Manager för att skapa och hantera identitet för Azure Stream Analytics-jobb anger den här egenskapen.

   **Exempeljobb**

   ```json
   { 
   "Name": "AsaJobWithIdentity", 
   "Type": "Microsoft.StreamAnalytics/streamingjobs", 
   "Location": "West US",
   "Identity": {
     "Type": "SystemAssigned", 
     }, 
   "properties": {
      "sku": {
       "name": "standard"
       },
   "outputs": [
         {
           "name": "string",
           "properties":{
             "datasource": {        
               "type": "Microsoft.DataLake/Accounts",
               "properties": {
                 "accountName": “myDataLakeAccountName",
                 "filePathPrefix": “cluster1/logs/{date}/{time}",
                 "dateFormat": "YYYY/MM/DD",
                 "timeFormat": "HH",
                 "authenticationMode": "Msi"
                 }
                 
   }
   ```
  
   **Jobbet exempelsvar**

   ```json
   { 
   "Name": "mySAJob", 
   "Type": "Microsoft.StreamAnalytics/streamingjobs", 
   "Location": "West US",
   "Identity": {
   "Type": "SystemAssigned",
    "principalId": "GUID", 
    "tenantId": "GUID", 
   }, 
   "properties": {
           "sku": {
             "name": "standard"
           },
   }
   ```

   Anteckna huvudkontots ID från svaret som jobbet att bevilja åtkomst till ADLS-resurser som krävs.

   Den **klient-ID** är ID för Azure Active Directory-klient där tjänstens huvudnamn skapas. Tjänstens huvudnamn har skapats i Azure-klient som är betrodd av prenumerationen.

   Den **typ** anger vilken typ av hanterad identitet som beskrivs i typer av hanterade identiteter. Endast System tilldelade-typen stöds.

2. Ge åtkomst till tjänstens huvudnamn med hjälp av PowerShell. Om du vill ge åtkomst till tjänstens huvudnamn med PowerShell, kör du följande kommando:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   Den **PrincipalId** är objekt-ID för tjänstens huvudnamn och visas på skärmen portalen när tjänstens huvudnamn har skapats. Om du har skapat den jobb med hjälp av en för malldistribution av resurshanteraren visas objekt-ID i Identity-egenskapen för jobb-svaret.

   **Exempel**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Mer information om kommandot ovan PowerShell avser den [Set-AzDataLakeStoreItemAclEntry](https://docs.microsoft.com/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry#optional-parameters) dokumentation.

## <a name="limitations"></a>Begränsningar
Den här funktionen stöder inte följande:

1.  **Flera innehavare åtkomst**: Tjänstens huvudnamn som skapats för en viss Stream Analytics-jobbet kommer att finnas på Azure Active Directory-klient som jobbet har skapats och kan inte användas mot en resurs som finns på en annan Azure Active Directory-klient. Du kan därför bara använda MSI på ADLS Gen 1-resurser som ligger inom samma Azure Active Directory-klient som din Azure Stream Analytics-jobb. 

2.  **[Tilldelade användaridentitet](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka/)**: stöds inte detta innebär att användaren inte kan ange sina egna tjänstens huvudnamn som ska användas av deras Stream Analytics-jobb. Tjänstens huvudnamn genereras av Azure Stream Analytics. 


## <a name="next-steps"></a>Nästa steg

* [Skapa ett Data lake Store-utdata med stream analytics](../data-lake-store/data-lake-store-stream-analytics.md)
* [Testa Stream Analytics-frågor lokalt med Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Testa realtidsdata lokalt med hjälp av Azure Stream Analytics-verktyg för Visual Studio](stream-analytics-live-data-local-testing.md) 
