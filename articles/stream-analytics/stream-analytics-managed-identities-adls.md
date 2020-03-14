---
title: Autentisera Azure Stream Analytics till Azure Data Lake Storage Gen1
description: Den här artikeln beskriver hur du använder hanterade identiteter för att autentisera ditt Azure Stream Analytics-jobb till Azure Data Lake Storage Gen1 utdata.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 01741ea56b9e6f55c1393e88fc7991d410c33119
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254383"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Autentisera Stream Analytics för att Azure Data Lake Storage Gen1 med hanterade identiteter

Azure Stream Analytics stöder hanterad identitetsautentisering med Azure Data Lake Storage (ADLS) Gen1 utdata. Identiteten är ett hanterat program i Azure Active Directory som representerar ett visst Stream Analytics-jobb och kan användas för att autentisera till en resurs. Hanterade identiteter utan begränsningar av användarbaserade autentiseringsmetoder, som behöver autentiseras på nytt på grund av ändring av lösenord eller användaren token kontolösenordet som sker var 90: e dag. Dessutom hanterade identiteter med hjälp av automatisering av Stream Analytics-jobbet distributioner som utdata till Azure Data Lake Storage Gen1.

Den här artikeln visar tre sätt att aktivera hanterad identitet för ett Azure Stream Analytics jobb som matar ut till en Azure Data Lake Storage Gen1 via Azure Portal, Azure Resource Manager mall-distribution och Azure Stream Analytics verktyg för Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure Portal

1. Starta genom att skapa ett nytt Stream Analytics-jobb eller genom att öppna ett befintligt jobb i Azure-portalen. Välj **hanterad identitet** som finns under **Konfigurera**på Meny raden på vänster sida av skärmen.

   ![Konfigurera Stream Analytics hanterad identitet](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Välj **Använd systemtilldelad hanterad identitet** från fönstret som visas till höger. Klicka på **Spara** till ett huvud namn för tjänsten för Stream Analytics jobbets identitet i Azure Active Directory. Livscykeln för den nyligen skapade identiteten ska hanteras av Azure. När Stream Analytics-jobbet tas bort, raderas automatiskt associerade identiteten (det vill säga tjänstens huvudnamn) av Azure.

   När konfigurationen är sparad anges i objekt-ID (OID) för tjänstens huvudnamn som ägar-ID som visas nedan:

   ![ID för Stream Analytics tjänstens huvudnamn](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Tjänstens huvudnamn har samma namn som Stream Analytics-jobb. Om namnet på ditt jobb till exempel är **MyASAJob**, är namnet på det skapade tjänst huvud namnet också **MyASAJob**.

3. I fönstret utmatnings egenskaper i ADLS Gen1 utgående mottagare klickar du på list rutan autentiseringsläge och väljer * * hanterad identitet * *.

4. Fyll i resten av egenskaperna. Mer information om hur du skapar en ADLS-utdata finns i [skapa ett data Lake Store-utdata med Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md). Klicka på **Spara**när du är färdig.

   ![Konfigurera Azure Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Gå till sidan Översikt i ADLS Gen1 och klicka på **data Utforskaren**.

   ![Konfigurera Data Lake-lagring – översikt](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. I fönstret data Utforskaren väljer du **åtkomst** och klickar på **Lägg till** i rutan åtkomst.

   ![Konfigurera åtkomst till Data Lake-lagring](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. I text rutan i fönstret **Välj användare eller grupp** skriver du namnet på tjänstens huvud namn. Kom ihåg att namnet för tjänstens huvudnamn som också är namnet på det motsvarande Stream Analytics-jobbet. När du börjar skriva huvudnamnet visas under textrutan. Välj önskat tjänst huvud namn och klicka på **Välj**.

   ![Välj tjänstens huvudnamn](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. I rutan **behörigheter** kontrollerar du **Skriv** -och **körnings** behörigheterna och tilldelar den till **den här mappen och alla underordnade**. Klicka sedan på **OK**.

   ![Välj Skriv och kör-behörighet](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Tjänstens huvud namn visas under **tilldelade behörigheter** i **åtkomst** fönstret som visas nedan. Du kan nu gå tillbaka och börja ditt Stream Analytics-jobb.

   ![Stream Analytics åtkomst till listan i portalen](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Mer information om Data Lake Storage Gen1 behörigheter för fil system finns i [Access Control i Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Stream Analytics verktyg för Visual Studio

1. I JobConfig. JSON anger du **Använd systemtilldelad identitet** till **True**.

   ![Stream Analytics hanterade identiteter för jobb konfiguration](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. I fönstret utmatnings egenskaper i ADLS Gen1 utgående mottagare klickar du på list rutan autentiseringsläge och väljer * * hanterad identitet * *.

   ![ADLS utdata hanterade identiteter](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Fyll i resten av egenskaperna och klicka på **Spara**.

4. Klicka på **Skicka till Azure** i Frågeredigeraren.

   När du skickar jobbet gör verktygen två saker:

   * Skapar automatiskt ett huvud namn för tjänsten för Stream Analytics jobbets identitet i Azure Active Directory. Livscykeln för den nyligen skapade identiteten ska hanteras av Azure. När Stream Analytics-jobbet tas bort, raderas automatiskt associerade identiteten (det vill säga tjänstens huvudnamn) av Azure.

   * Ange **Skriv** -och **körnings** behörigheter automatiskt för ADLS gen1 sökväg för prefix som används i jobbet och tilldela den till den här mappen och alla underordnade.

5. Du kan skapa Resource Manager-mallar med följande egenskap med hjälp av [Stream Analytics CI. CD NuGet Package](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) version 1.5.0 eller senare på en build-dator (utanför Visual Studio). Följ distributions stegen i Resource Manager-mallen i nästa avsnitt för att hämta tjänstens huvud namn och bevilja åtkomst till tjänstens huvud namn via PowerShell.

## <a name="resource-manager-template-deployment"></a>Resource Manager för malldistribution

1. Du kan skapa en *Microsoft. StreamAnalytics/streamingjobs-* resurs med en hanterad identitet genom att inkludera följande egenskap i resurs avsnittet i Resource Manager-mallen:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Azure Resource Manager för att skapa och hantera identitet för Azure Stream Analytics-jobb anger den här egenskapen.

   **Exempel jobb**
   
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
  
   **Exempel på jobb svar**

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

   Anteckna huvudkontots ID från svaret som jobbet att bevilja åtkomst till ADLS-resurser som krävs.

   **Klient-ID** är ID: t för den Azure Active Directory klient där tjänstens huvud namn skapades. Tjänstens huvudnamn har skapats i Azure-klient som är betrodd av prenumerationen.

   **Typen** anger typen av hanterad identitet enligt beskrivningen i typer av hanterade identiteter. Endast System tilldelade-typen stöds.

2. Ge åtkomst till tjänstens huvudnamn med hjälp av PowerShell. Om du vill ge åtkomst till tjänstens huvudnamn med PowerShell, kör du följande kommando:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** är objekt-ID: t för tjänstens huvud namn och visas på Portal skärmen när tjänstens huvud namn har skapats. Om du har skapat den jobb med hjälp av en för malldistribution av resurshanteraren visas objekt-ID i Identity-egenskapen för jobb-svaret.

   **Exempel**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Mer information om PowerShell-kommandot ovan finns i dokumentationen för [set-AzDataLakeStoreItemAclEntry](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) .

## <a name="limitations"></a>Begränsningar
Den här funktionen har inte stöd för följande:

1. **Åtkomst till flera klienter**: tjänstens huvud namn som skapats för ett angivet Stream Analytics jobb finns på den Azure Active Directory klient som jobbet skapades på och kan inte användas mot en resurs som finns på en annan Azure Active Directory-klient. Därför kan du bara använda MSI på ADLS gen 1-resurser som är inom samma Azure Active Directory-klient som ditt Azure Stream Analytics-jobb. 

2. **[Användarens tilldelade identitet](../active-directory/managed-identities-azure-resources/overview.md)** : stöds inte. Det innebär att användaren inte kan ange sitt eget tjänst huvud namn som ska användas av deras Stream Analytics-jobb. Tjänstens huvud namn genereras av Azure Stream Analytics.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett data Lake Store-utdata med Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md)
* [Testa Stream Analytics frågor lokalt med Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Testa Live data lokalt med Azure Stream Analytics verktyg för Visual Studio](stream-analytics-live-data-local-testing.md) 
