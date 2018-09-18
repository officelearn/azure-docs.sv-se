---
title: Gruppera datorer med datorberoenden med Azure Migrate | Microsoft Docs
description: Beskriver hur du skapar en utvärdering med datorberoenden med tjänsten Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 09/17/2018
ms.author: raynew
ms.openlocfilehash: 31cbec8eab499fac116eac6fa2addac4ec2a340b
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45737116"
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Gruppera datorer med datorberoende mappning

Den här artikeln beskriver hur du skapar en grupp datorer för [Azure Migrate](migrate-overview.md) utvärdering genom att visualisera beroenden för datorer. Du använder vanligtvis den här metoden när du vill utvärdera grupper med virtuella datorer med högre förtroende genom att dubbelkontrollera datorberoenden innan du kör en utvärdering. Visualisering av beroenden kan hjälpa dig att effektivt planera din migrering till Azure. Det hjälper dig att se till att inget kvar och förvåning avbrott sker inte när du migrerar till Azure. Du kan identifiera alla beroende av varandra system som måste migrera tillsammans och identifiera om ett system som körs fortfarande fungerar som värd för användare eller är en kandidat för inaktivering av i stället för migrering.


## <a name="prepare-for-dependency-visualization"></a>Förbereda för visualisering av beroenden
Azure Migrate använder Tjänstkarta-lösningen i Log Analytics för att aktivera beroendevisualisering av datorer.

### <a name="associate-a-log-analytics-workspace"></a>Associera en Log Analytics-arbetsyta
Om du vill använda visualisering av beroenden, måste du koppla en Log Analytics-arbetsyta, ny eller befintlig, med ett Azure Migrate-projekt. Du kan bara skapa eller koppla en arbetsyta i samma prenumeration där migration-projekt skapas.

- Att koppla en Log Analytics-arbetsyta till ett projekt i **översikt**går du till **Essentials** delen av projektet klickar du på **kräver konfiguration**

    ![Associera arbetsytan för Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- När du skapar en ny arbetsyta kan behöva du ange ett namn för arbetsytan. Arbetsytan skapas i samma prenumeration som migration-projekt och i en region i samma [Azure geografi](https://azure.microsoft.com/global-infrastructure/geographies/) som migration-projekt.
- Den **Använd befintlig** alternativet visas endast arbetsytor som skapas i regioner där Tjänstkarta är tillgänglig. Om du har en arbetsyta i en region där Tjänstkarta inte är tillgänglig kan visas den inte i listrutan.

> [!NOTE]
> Du kan inte ändra arbetsytan som är kopplad till ett migreringsprojekt.

### <a name="download-and-install-the-vm-agents"></a>Hämta och installera VM-agenterna
När du konfigurerar en arbetsyta, måste du hämta och installera agenter på varje lokal dator som du vill utvärdera. Dessutom om du har datorer med utan internet-anslutning, måste du hämta och installera [OMS gateway](../log-analytics/log-analytics-oms-gateway.md) på dem.

1. I **översikt**, klickar du på **hantera** > **datorer**, och välj datorn som krävs.
2. I den **beroenden** kolumnen, klickar du på **installera agenter**.
3. På den **beroenden** sidan, hämta och installera Microsoft Monitoring Agent (MMA) och beroendeagenten på varje virtuell dator som du vill utvärdera.
4. Kopiera arbetsytans ID och nyckel. Du behöver dem när du installerar MMA på den lokala datorn.

> [!NOTE]
> För att automatisera installationen av agenter som du kan använda valfri distributionsverktyg som System Center Configuration Manager eller använda våra partner-verktyget [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), som har en agent distributionslösning för Azure Migrate.

### <a name="install-the-mma"></a>Installera MMA

Installera agenten på en Windows-dator:

1. Dubbelklicka på den hämtade agenten.
2. På sidan **Välkommen** klickar du på **Nästa**. På sidan **Licensvillkor** klickar du på **Jag accepterar** för att acceptera licensen.
3. I **målmapp**, behålla eller ändra standardinstallationsmappen > **nästa**.
4. I **installationsalternativ för Agent**väljer **Azure Log Analytics** > **nästa**.
5. Klicka på **Lägg till** att lägga till en ny Log Analytics-arbetsyta. Klistra in i arbetsytans ID och nyckel som du kopierade från portalen. Klicka på **Nästa**.


Installera agenten på en Linux-dator:

1. Över rätt samling (x86 eller x64) till Linux-datorn med scp/sftp.
2. Installera paketet med hjälp av argumentet--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>Installera beroendeagenten
1. Installera beroendeagenten på en Windows-dator, dubbelklicka på installationsfilen och följ anvisningarna i guiden.
2. Installera beroendeagenten på en Linux-dator, installera som rot med följande kommando:

    ```sh InstallDependencyAgent-Linux64.bin```

Mer information om stöd för beroende-agenten för den [Windows](../monitoring/monitoring-service-map-configure.md#supported-windows-operating-systems) och [Linux](../monitoring/monitoring-service-map-configure.md#supported-linux-operating-systems) operativsystem.

[Läs mer](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) om hur du kan använda skript för att installera beroendeagenten.

## <a name="create-a-group"></a>Skapa en grupp

1. När du har installerat agenterna, gå till portalen och klicka på **hantera** > **datorer**.
2. Sök efter den datorn där du installerade agenterna.
3. Den **beroenden** kolumnen för datorn bör nu visa som **Visa beroenden**. Klicka på kolumnen om du vill visa datorns beroenden.
4. Beroendekarta för datorn visar följande information:
    - Inkommande (klienter) och utgående (servrar) TCP-anslutningar till och från datorn
        - Beroende datorer som inte har MMA och beroendeagenter agenten installerad är grupperade efter portnummer
        - Beroende datorer som har MMA och beroendeagenten installerat visas som separata rutor
    - Processer som körs på datorn, som du kan expandera varje datorrutan för att visa processer
    - Egenskaper som fullständigt kvalificerade namn, operativsystem, MAC-adress osv på varje dator som du kan klicka på varje datorrutan för att visa den här informationen

 ![Visa datorberoenden](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. Du kan titta på beroenden för olika tidsvaraktigheter genom att klicka på varaktighet i Intervalletikett tid. Intervallet är en timme som standard. Du kan ändra tidsintervallet eller ange start- och slutdatum och varaktighet.
5. När du har identifierat beroende datorer som du vill gruppera kan använda Ctrl + klicka för att välja flera datorer på kartan Klicka på **gruppera datorer**.
6. Ange ett gruppnamn. Kontrollera att de beroende datorerna identifieras av Azure Migrate.

    > [!NOTE]
    > Om en beroende dator inte kan identifieras av Azure Migrate, kan du lägga till den gruppen. Om du vill lägga till sådana datorer i gruppen som du behöver köra identifieringen igen med rätt omfattning i vCenter Server och se till att datorn identifieras av Azure Migrate.  

7. Om du vill skapa en utvärdering för den här gruppen kan du markera kryssrutan för att skapa en ny värdering för gruppen.
8. Klicka på **OK** ska sparas.

När gruppen har skapats, rekommenderar vi att du installerar agenter på alla datorer i gruppen och sedan ändra gruppen genom att visualisera beroenden för hela gruppen.

## <a name="next-steps"></a>Nästa steg

- [Lär dig hur](how-to-create-group-dependencies.md) att förfina gruppen genom att visualisera gruppberoenden
- [Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
