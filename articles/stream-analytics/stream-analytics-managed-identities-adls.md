---
title: Autentisera Azure Stream Analytics till Azure Data Lake Storage Gen1
description: I den här artikeln beskrivs hur du använder hanterade identiteter för att autentisera ditt Azure Stream Analytics-jobb till Azure Data Lake Storage Gen1-utdata.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 01741ea56b9e6f55c1393e88fc7991d410c33119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254383"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Autentisera Stream Analytics till Azure Data Lake Storage Gen1 med hanterade identiteter

Azure Stream Analytics stöder autentisering av hanterad identitet med Adls-utdata (Azure Data Lake Storage) Gen1. Identiteten är ett hanterat program som registrerats i Azure Active Directory och som representerar ett givet Stream Analytics-jobb och kan användas för att autentisera till en riktad resurs. Hanterade identiteter eliminerar begränsningarna för användarbaserade autentiseringsmetoder, till exempel att behöva omauktorisera på grund av lösenordsändringar eller förfallodatum för användartoken som inträffar var 90:e dag. Dessutom hjälper hanterade identiteter till automatisering av Stream Analytics-jobbdistributioner som distribueras till Azure Data Lake Storage Gen1.

Den här artikeln visar tre sätt att aktivera hanterad identitet för ett Azure Stream Analytics-jobb som matas ut till en Azure Data Lake Storage Gen1 via Azure-portalen, Azure Resource Manager-malldistributionen och Azure Stream Analytics-verktygen för Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure Portal

1. Börja med att skapa ett nytt Stream Analytics-jobb eller genom att öppna ett befintligt jobb i Azure-portalen. Välj **Hanterad identitet** som finns under **Konfigurera**på menyraden som finns till vänster på skärmen.

   ![Konfigurera Hanterad identitet för Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Välj **Använd systemtilldelad hanterad identitet** i fönstret som visas till höger. Klicka på **Spara** i ett tjänsthuvudnamn för identiteten för Stream Analytics-jobbet i Azure Active Directory. Livscykeln för den nyskapade identiteten hanteras av Azure. När Stream Analytics-jobbet tas bort tas den associerade identiteten (det villans huvudnamn) automatiskt bort av Azure.

   När konfigurationen sparas visas tjänstens objekt-ID (OID) som huvudnamn enligt nedan:

   ![Huvud-ID för Stream Analytics-tjänsten](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Tjänstens huvudnamn har samma namn som Stream Analytics-jobbet. Om namnet på jobbet till exempel är **MyASAJob**är namnet på det skapade tjänsthuvudhuvudet också **MyASAJob**.

3. I fönstret utdataegenskaper i ADLS Gen1-utflödesmottagaren klickar du på listrutan Autentiseringsläge och väljer **Hanterad identitet **.

4. Fyll i resten av egenskaperna. Mer information om hur du skapar en ADLS-utdata finns i [Skapa en datasjölagringsutdata med direktanalys](../data-lake-store/data-lake-store-stream-analytics.md). När du är klar klickar du på **Spara**.

   ![Konfigurera Azure Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Navigera till översiktssidan för din ADLS Gen1 och klicka på **Data explorer**.

   ![Konfigurera översikt över lagring av datasjö](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. Välj **Access** i fönstret Datautforskaren och klicka på **Lägg till** i Access-fönstret.

   ![Konfigurera åtkomst till lagring av datasjö](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. Skriv namnet på tjänstens huvudnamn i textrutan i fönstret **Välj användare eller grupp.** Kom ihåg att namnet på tjänstens huvudnamn också är namnet på motsvarande Stream Analytics-jobb. När du börjar skriva huvudnamnet visas det under textrutan. Välj önskat huvudnamn för tjänsten och klicka på **Välj**.

   ![Välj ett huvudnamn för tjänsten](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. I fönstret **Behörigheter** kontrollerar du behörigheterna **Skriv** **och kör** och tilldelar den till den **här mappen och alla underordnade**. Klicka sedan på **Ok**.

   ![Välj skriv- och körningsbehörigheter](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Tjänstens huvudnamn visas under **Tilldelade behörigheter** i **åtkomstfönstret** enligt nedan. Nu kan du gå tillbaka och starta ditt Stream Analytics-jobb.

   ![Åtkomstlista för Stream Analytics i portalen](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Mer information om filsystembehörigheter för DataSjölagring Gen1 finns [i Åtkomstkontroll i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Stream Analytics-verktyg för Visual Studio

1. I JobConfig.json anger du **Använd systemtilldelad identitet** till **True**.

   ![Stream Analytics jobb config hanterade identiteter](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. I fönstret utdataegenskaper i ADLS Gen1-utflödesmottagaren klickar du på listrutan Autentiseringsläge och väljer **Hanterad identitet **.

   ![ADLS-utdatahanterade identiteter](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Fyll i resten av egenskaperna och klicka på **Spara**.

4. Klicka på **Skicka till Azure** i frågeredigeraren.

   När du skickar in jobbet gör verktygen två saker:

   * Skapar automatiskt ett tjänsthuvudnamn för identiteten för Stream Analytics-jobbet i Azure Active Directory. Livscykeln för den nyskapade identiteten hanteras av Azure. När Stream Analytics-jobbet tas bort tas den associerade identiteten (det villans huvudnamn) automatiskt bort av Azure.

   * Ange automatiskt **skriv-** och **körningsbehörigheter** för ADLS Gen1-prefixsökvägen som används i jobbet och tilldela den till den här mappen och alla underordnade.

5. Du kan generera Resource Manager-mallarna med följande egenskap med Hjälp av [Stream Analytics CI. CD Nuget paket](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) version 1.5.0 eller högre på en byggmaskin (utanför Visual Studio). Följ distributionsstegen för Resource Manager-mallen i nästa avsnitt för att få tjänstens huvudnamn och bevilja åtkomst till tjänstens huvudnamn via PowerShell.

## <a name="resource-manager-template-deployment"></a>Distribution av Resource Manager-mall

1. Du kan skapa en *Microsoft.StreamAnalytics/streamingjobs-resurs* med en hanterad identitet genom att inkludera följande egenskap i resursavsnittet i Resource Manager-mallen:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Den här egenskapen talar om för Azure Resource Manager att skapa och hantera identiteten för ditt Azure Stream Analytics-jobb.

   **Exempel på jobb**
   
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
                 "accountName": "myDataLakeAccountName",
                 "filePathPrefix": "cluster1/logs/{date}/{time}",
                 "dateFormat": "YYYY/MM/DD",
                 "timeFormat": "HH",
                 "authenticationMode": "Msi"
             }
           }
         }
       }
     }
   }
   ```
  
   **Exempel på jobbsvar**

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
   }
   ```

   Ta del av huvud-ID från jobbsvaret för att bevilja åtkomst till den ADLS-resurs som krävs.

   **Klient-ID:et** är ID för Azure Active Directory-klienten där tjänstens huvudnamn skapas. Tjänstens huvudnamn skapas i Azure-klienten som är betrodd av prenumerationen.

   **Typen** anger vilken typ av hanterad identitet som förklaras i typer av hanterade identiteter. Endast den systemtilldelade typen stöds.

2. Ge åtkomst till tjänstens huvudnamn med PowerShell. Om du vill ge åtkomst till tjänstens huvudnamn via PowerShell kör du följande kommando:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** är tjänstens huvudnamnsobjekt-ID och visas på portalskärmen när tjänstens huvudnamn har skapats. Om du skapade jobbet med hjälp av en Resource Manager-malldistribution visas objekt-ID:et i egenskapen Identitet för jobbsvaret.

   **Exempel**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Mer information om kommandot Ovan PowerShell finns i dokumentationen [Set-AzDataLakeStoreItemAclEntry.](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry)

## <a name="limitations"></a>Begränsningar
Den här funktionen stöder inte följande:

1. **Åtkomst till flera innehavare**: Huvudnamnen för tjänsten som skapats för ett visst Stream Analytics-jobb finns i Azure Active Directory-klienten som jobbet skapades på och kan inte användas mot en resurs som finns på en annan Azure Active Directory-klientorganisation. Därför kan du bara använda MSI på ADLS Gen 1-resurser som finns inom samma Azure Active Directory-klient som ditt Azure Stream Analytics-jobb. 

2. **[Användartilldelad identitet](../active-directory/managed-identities-azure-resources/overview.md)**: stöds inte. Det innebär att användaren inte kan ange sitt eget tjänsthuvudnamn som ska användas av deras Stream Analytics-jobb. Tjänstens huvudnamn genereras av Azure Stream Analytics.

## <a name="next-steps"></a>Nästa steg

* [Skapa en datasjölagringsutdata med strömanalys](../data-lake-store/data-lake-store-stream-analytics.md)
* [Testa Stream Analytics-frågor lokalt med Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Testa live-data lokalt med Hjälp av Azure Stream Analytics-verktyg för Visual Studio](stream-analytics-live-data-local-testing.md) 
