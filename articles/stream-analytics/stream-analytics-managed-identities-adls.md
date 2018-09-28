---
title: Använda hanterade identiteter för att autentisera Azure Stream Analytics-jobb till Azure Data Lake Storage Gen1 utdata (förhandsversion)
description: ''
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/27/2018
ms.openlocfilehash: b79d529822f2b1acca9c8a120202b4ce4010949e
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414873"
---
# <a name="use-managed-identities-to-authenticate-azure-stream-analytics-jobs-to-azure-data-lake-storage-gen1-output-preview"></a>Använda hanterade identiteter för att autentisera Azure Stream Analytics-jobb till Azure Data Lake Storage Gen1 utdata (förhandsversion)

Azure Stream Analytics stöder hanterad identitetsautentisering med Azure Data Lake Storage (ADLS) Gen1 utdata. Identiteten är ett hanterat program i Azure Active Directory som representerar ett visst Stream Analytics-jobb och kan användas för att autentisera till en resurs. Hanterade identiteter utan begränsningar av användarbaserade autentiseringsmetoder, som behöver autentiseras på nytt på grund av ändring av lösenord eller användaren token kontolösenordet som sker var 90: e dag. Dessutom hanterade identiteter med hjälp av automatisering av Stream Analytics-jobbet distributioner som utdata till Azure Data Lake Storage Gen1.

Gå till den [åtta nya funktioner i Azure Stream Analytics](https://azure.microsoft.com/en-us/blog/eight-new-features-in-azure-stream-analytics/) blogginlägg för att registrera dig för den här förhandsversionen och Läs mer om nya funktioner.

Den här artikeln visar två sätt att aktivera hanterad identitet för Azure Stream Analytics-jobb som matar ut till en Azure Data Lake Storage Gen1: via Azure-portalen och via Azure Resource Manager för malldistribution.

## <a name="enable-managed-identity-with-azure-portal"></a>Aktivera hanterade identiteter med Azure-portalen

1. Starta genom att skapa ett nytt Stream Analytics-jobb eller genom att öppna ett befintligt jobb i Azure-portalen. På menyraden på vänster sida av skärmen, Välj **hanterad tjänstidentitet (förhandsversion)** finns under **konfigurera**.

   ![Konfigurera förhandsversionen av Stream Analytics-hanterad identitet](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Välj **Använd systemtilldelade hanterad tjänstidentitet (förhandsversion)** från fönstret som visas till höger. Klicka på **spara** att skapa ett huvudnamn för tjänsten identitet för Stream Analytics-jobb i Azure Active Directory. Livscykeln för den nyligen skapade identiteten ska hanteras av Azure. När Stream Analytics-jobbet tas bort, raderas automatiskt associerade identiteten (det vill säga tjänstens huvudnamn) av Azure.

   När konfigurationen är sparad anges i objekt-ID (OID) för tjänstens huvudnamn som ägar-ID som visas nedan:

   ![Stream Analytics huvudkonto-ID](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
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

   ![Välj en behörighet](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Tjänstens huvudnamn i listan under **tilldelade behörigheter** på den **åtkomst** fönstret som visas nedan. Du kan nu gå tillbaka och börja ditt Stream Analytics-jobb.

   ![Listan över appåtkomst](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Läs mer om Data Lake Storage Gen1 filsystemsbehörigheter i [åtkomstkontroll i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

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
           "properties": {
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
   Set-AzureRmDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   Den **PrincipalId** är objekt-ID för tjänstens huvudnamn och visas på skärmen portalen när tjänstens huvudnamn har skapats. Om du har skapat den jobb med hjälp av en för malldistribution av resurshanteraren visas objekt-ID i Identity-egenskapen för jobb-svaret.

   **Exempel**

   ```powershell
   PS > Set-AzureRmDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Mer information om kommandot ovan PowerShell avser den [Set-AzureRmDataLakeStoreItemAclEntry](https://docs.microsoft.com/powershell/module/azurerm.datalakestore/set-azurermdatalakestoreitemaclentry?view=azurermps-6.8.1&viewFallbackFrom=azurermps-4.2.0#optional-parameters) dokumentation.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Data lake Store-utdata med stream analytics](../data-lake-store/data-lake-store-stream-analytics.md)