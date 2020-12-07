---
title: Konfigurera beroende analys utan agent i Azure Migrate Server bedömning
description: Konfigurera en agent lös beroende analys i Azure Migrate Server bedömning.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: d84c85326c6f5d87189a2c24a3b13654f157cb05
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754291"
---
# <a name="analyze-machine-dependencies-agentless"></a>Analysera datorberoenden (agentlösa)

I den här artikeln beskrivs hur du konfigurerar en agent lös beroende analys i Azure Migrate: Server bedömning. Beroende [analys](concepts-dependency-visualization.md) hjälper dig att identifiera och förstå beroenden mellan datorer för utvärdering och migrering till Azure.


> [!IMPORTANT]
> Det finns för närvarande en för hands version av en beroende visualisering för virtuella VMware-datorer som har identifierats med verktyget Azure Migrate: Server bedömning.
> Funktioner kan vara begränsade eller ofullständiga.
> Den här för hands versionen täcks av kund support och kan användas för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Aktuella begränsningar

- I vyn beroende analys kan du för närvarande inte lägga till eller ta bort en server från en grupp.
- En beroende karta för en grupp med servrar är inte tillgänglig för närvarande.
- I ett Azure Migrate projekt kan beroende data insamling konfigureras samtidigt för 1000-servrar. Du kan analysera ett större antal servrar genom att sekvensera i batchar på 1000.

## <a name="before-you-start"></a>Innan du börjar

- [Granska](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) operativ system som stöds och de behörigheter som krävs.
- Kontrol lera att du:
    - Ha ett Azure Migrate-projekt. Om du inte gör det [skapar](./create-manage-projects.md) du en nu.
    - Kontrol lera att du har [lagt](how-to-assess.md) till verktyget Azure Migrate: Server utvärderings verktyg i projektet.
    - Konfigurera en [Azure Migrate-apparat](migrate-appliance.md) för att identifiera lokala datorer. [Konfigurera en installation](how-to-set-up-appliance-vmware.md) för virtuella VMware-datorer. Enheten identifierar lokala datorer och skickar metadata-och prestanda data till Azure Migrate: Server utvärdering.
- Kontrol lera att VMware-verktygen (senare än 10,2) är installerade på varje virtuell dator som du vill analysera.


## <a name="create-a-user-account-for-discovery"></a>Skapa ett användar konto för identifiering

Konfigurera ett användar konto så att Server utvärderingen kan komma åt den virtuella datorn för att identifiera beroenden. [Lär dig](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) mer om konto krav för virtuella Windows-och Linux-datorer.


## <a name="add-the-user-account-to-the-appliance"></a>Lägg till användar kontot till enheten

Lägg till användar kontot till enheten.

1. Öppna appen för hantering av appar. 
2. Navigera till panelen **Tillhandahåll vCenter-information** .
3. I **identifiera program och beroenden på virtuella datorer** klickar du på **Lägg till autentiseringsuppgifter**
3. Välj **operativ system**, ange ett eget namn för kontot och **User name** / **lösen ordet** för användar namn
6. Klicka på **Spara**.
7. Klicka på **Spara och starta identifiering**.

    ![Lägg till användar konto för virtuell dator](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Starta beroende identifiering

Välj de datorer där du vill aktivera beroende identifiering. 

1. Klicka på **identifierade servrar** i **Azure Migrate: Server bedömning**.
2. Klicka på ikonen **beroende analys** .
3. Klicka på **Lägg till servrar**.
4. På sidan **Lägg till servrar** väljer du den installation som identifierar relevanta datorer.
5. Välj datorerna i listan dator.
6. Klicka på **Lägg till servrar**.

    ![Starta beroende identifiering](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Du kan visualisera beroenden runt sex timmar efter start av beroende identifiering. Om du vill aktivera flera datorer kan du använda [PowerShell](#start-or-stop-dependency-discovery-using-powershell) för att göra det.

## <a name="visualize-dependencies"></a>Visualisera beroenden

1. Klicka på **identifierade servrar** i **Azure Migrate: Server bedömning**.
2. Sök efter den dator som du vill visa.
3. I kolumnen **beroenden** klickar du på **Visa beroenden**
4. Ändra den tids period som du vill visa kartan med i list rutan **tids längd** .
5. Expandera **klient** gruppen för att visa en lista med datorer som är beroende av den valda datorn.
6. Expandera **port** gruppen om du vill visa en lista med datorer som är beroende av den valda datorn.
7. Om du vill navigera till vyn karta för någon av de beroende datorerna klickar du på dator namnet > **belastnings Server karta**

    ![Expandera server port grupp och läsa in Server karta](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Expandera klient grupp ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Expandera den valda datorn om du vill visa information på process nivå för varje beroende.

    ![Expandera Server för att visa processer](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Process information för ett beroende är inte alltid tillgängligt. Om det inte är tillgängligt visas beroendet med processen markerad som "okänd process".

## <a name="export-dependency-data"></a>Exportera beroende data

1. Klicka på **identifierade servrar** i **Azure Migrate: Server bedömning**.
2. Klicka på ikonen **beroende analys** .
3. Klicka på **Exportera program beroenden**.
4. På sidan **Exportera program beroenden** väljer du den installation som identifierar relevanta datorer.
5. Välj start tid och slut tid. Observera att du bara kan hämta data under de senaste 30 dagarna.
6. Klicka på **Exportera beroende**.

Beroende data exporteras och hämtas i CSV-format. Den hämtade filen innehåller beroende data över alla datorer som är aktiverade för beroende analys. 

![Exportera beroenden](./media/how-to-create-group-machine-dependencies-agentless/export.png)

### <a name="dependency-information"></a>Beroende information

Varje rad i den exporterade CSV-filen motsvarar ett beroende som observerats inom den angivna tids perioden. 

I följande tabell sammanfattas fälten i den exporterade CSV-filen. Observera att fälten Server namn, program och process endast fylls i för servrar som har en agent lös beroende analys aktive rad.

**Fältnamn** | **Information**
--- | --- 
Timeslot | Timeslot under vilken beroendet observerades. <br/> Beroende data samlas in över 6 timmars fack för närvarande.
Käll Server namn | Namnet på käll datorn 
Käll program | Namnet på programmet på käll datorn 
Käll process | Namnet på processen på käll datorn 
Mål server namn | Namnet på mål datorn
Mål-IP-adress | Mål datorns IP-adress
Mål program | Namnet på programmet på mål datorn
Mål process | Namnet på processen på mål datorn 
Målport | Port nummer på mål datorn


## <a name="stop-dependency-discovery"></a>Stoppa beroende identifiering

Välj de datorer där du vill stoppa beroende identifiering. 

1. Klicka på **identifierade servrar** i **Azure Migrate: Server bedömning**.
2. Klicka på ikonen **beroende analys** .
3. Klicka på **ta bort servrar**.
3. På sidan **ta bort servrar** **väljer du den installation som identifierar** de virtuella datorer där du vill stoppa beroende identifiering.
4. Välj datorerna i listan dator.
5. Klicka på **ta bort servrar**.

Om du vill stoppa beroendet på flera datorer kan du använda [PowerShell](#start-or-stop-dependency-discovery-using-powershell) för att göra det.


## <a name="start-or-stop-dependency-discovery-using-powershell"></a>Starta eller stoppa beroende identifiering med PowerShell

Ladda ned PowerShell-modulen från [Azure PowerShell samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) lagrings platsen på GitHub.


### <a name="log-in-to-azure"></a>Logga in på Azure

1. Logga in på Azure-prenumerationen med hjälp av Connect-AzAccount cmdlet.

    ```PowerShell
    Connect-AzAccount
    ```
    Om du använder Azure Government använder du följande kommando.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Välj den prenumeration där du har skapat Azure Migrate-projektet 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importera den hämtade AzMig_Dependencies PowerShell-modulen

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>Aktivera eller inaktivera insamling av beroende data

1. Hämta listan med identifierade virtuella VMware-datorer i ditt Azure Migrate-projekt med hjälp av följande kommandon. I exemplet nedan är projekt namnet FabrikamDemoProject och den resurs grupp som det tillhör är FabrikamDemoRG. Listan över datorer kommer att sparas i FabrikamDemo_VMs.csv

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    I filen kan du se namnet på den virtuella datorns visnings namn, aktuell status för beroende samling och ARM-ID för alla identifierade virtuella datorer. 

2. Om du vill aktivera eller inaktivera beroenden skapar du en CSV-fil med indata. Filen måste ha en kolumn med huvudet "ARM-ID". Eventuella ytterligare rubriker i CSV-filen ignoreras. Du kan skapa en CSV-fil med hjälp av filen som genererades i föregående steg. Skapa en kopia av filen som behåller de virtuella datorer som du vill aktivera eller inaktivera beroenden för. 

    I följande exempel aktive ras beroende analys i listan över virtuella datorer i indatafilen FabrikamDemo_VMs_Enable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    I följande exempel inaktive ras beroende analys i listan över virtuella datorer i indatafilen FabrikamDemo_VMs_Disable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Visualisera nätverks anslutningar i Power BI

Azure Migrate erbjuder en Power BI mall som du kan använda för att visualisera nätverks anslutningar för många servrar samtidigt, och filtrera efter process och server. Du kan visualisera genom att läsa in Power BI med beroende data enligt anvisningarna nedan.

1. Ladda ned PowerShell-modulen och Power BI mal len från [Azure PowerShell samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) lagrings platsen på GitHub.

2. Logga in på Azure med hjälp av anvisningarna nedan: 
- Logga in på Azure-prenumerationen med hjälp av Connect-AzAccount cmdlet.

    ```PowerShell
    Connect-AzAccount
    ```

- Om du använder Azure Government använder du följande kommando.

    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

- Välj den prenumeration där du har skapat Azure Migrate-projektet 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importera den hämtade AzMig_Dependencies PowerShell-modulen

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Kör följande kommando. Det här kommandot hämtar beroende data i en CSV och bearbetar dem för att generera en lista med unika beroenden som kan användas för visualisering i Power BI. I exemplet nedan är projekt namnet FabrikamDemoProject och den resurs grupp som det tillhör är FabrikamDemoRG. Beroendena hämtas för datorer som identifieras av FabrikamAppliance. De unika beroendena kommer att sparas i FabrikamDemo_Dependencies.csv

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. Öppna mallen för hämtade Power BI

6. Läs in de nedladdade beroende data i Power BI.
    - Öppna mallen i Power BI.
    - Klicka på **Hämta data** i verktygsfältet. 
    - Välj **text/CSV** från vanliga data källor.
    - Välj beroende filen som hämtats.
    - Klicka på **Läs in**.
    - Du ser att en tabell importeras med namnet på CSV-filen. Du kan se tabellen i fält fältet till höger. Byt namn på den till AzMig_Dependencies
    - Klicka på Uppdatera från verktygsfältet.

    Diagrammet nätverks anslutningar och käll serverns namn, mål serverns namn, käll process namn, namn utsnitt för mål process, bör vara lätta med importerade data.

7. Visualisera kartan över filtrering av nätverks anslutningar efter servrar och processer. Spara filen.


## <a name="next-steps"></a>Nästa steg

[Gruppera datorer](how-to-create-a-group.md) för utvärdering.