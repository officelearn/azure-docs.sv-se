---
title: Gruppera datorer med hjälp av beroenden för datorn med Azure migrera | Microsoft Docs
description: Beskriver hur du skapar en bedömning med tjänsten Azure migrera datorn beroenden.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: a9850044266ec05cee5e32c6825609bcf969351d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Grupp datorer som använder datorn beroende mappning

Den här artikeln beskriver hur du skapar en grupp datorer för [Azure migrera](migrate-overview.md) bedömning av visualisera beroenden för datorer. Du använder vanligtvis den här metoden när du vill utvärdera grupper med virtuella datorer med högre tillförlitlighet av dubbelkontrollera datorn beroenden innan du kör en utvärdering. Med hjälp av beroende visualiseringen kan du effektivt kunna planera migreringen till Azure. Det hjälper dig se till att inget kvar och oväntat avbrott sker inte när du migrerar till Azure. Du kan identifiera alla beroende av varandra system som måste migreras tillsammans och identifiera om ett system som körs fortfarande används av användare eller är en kandidat för avställning av i stället för migrering. 


## <a name="prepare-machines-for-dependency-mapping"></a>Förbereda datorer för beroende mappning
Om du vill visa beroenden för datorer som du behöver hämta och installera agenter på varje lokal dator som du vill utvärdera. Dessutom om du har datorer med utan internet-anslutning, måste du hämta och installera [OMS gateway](../log-analytics/log-analytics-oms-gateway.md) på dem.

### <a name="download-and-install-the-vm-agents"></a>Hämta och installera VM-agenterna
1. I **översikt**, klickar du på **hantera** > **datorer**, och välj datorn som krävs.
2. I den **beroenden** kolumnen, klickar du på **installera agenter**. 
3. På den **beroenden** ladda ned och installera Microsoft Monitoring Agent (MMA) och beroende-agenten på varje virtuell dator som du vill utvärdera.
4. Kopiera arbetsytans ID och nyckel. Du behöver dem när du installerar MMA på den lokala datorn.

### <a name="install-the-mma"></a>Installera MMA

Installera agenten på en Windows-dator:

1. Dubbelklicka på den hämtade agenten.
2. På sidan **Välkommen** klickar du på **Nästa**. På sidan **Licensvillkor** klickar du på **Jag accepterar** för att acceptera licensen.
3. I **målmappen**, behålla eller ändra standardinstallationsmappen > **nästa**. 
4. I **installationsalternativ för Agent**väljer **Azure logganalys** > **nästa**. 
5. Klicka på **Lägg till** att lägga till en ny logganalys-arbetsyta. Klistra in i arbetsyte-ID och nyckel som du kopierade från portalen. Klicka på **Nästa**.


Installera agenten på en Linux-dator:

1. Över rätt paket (x86 eller x64) till Linux-datorn med scp/sftp.
2. Installera paketet med argumentet – installera.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>Installera beroendeagenten
1. Dubbelklicka på installationsfilen för att installera agenten beroende på en Windows-dator, och följ instruktionerna i guiden.
2. Installera agenten beroende på en Linux-dator, installera som rot med följande kommando:

    ```sh InstallDependencyAgent-Linux64.bin```

[Lär dig mer](../monitoring/monitoring-service-map-configure.md#supported-operating-systems) om operativsystem som stöds av beroendeagent. 

## <a name="create-a-group"></a>Skapa en grupp

1. När du har installerat agenterna, gå till portalen och klicka på **hantera** > **datorer**.
2. Sök efter den datorn där du installerade agenterna.
3. Den **beroenden** kolumnen för datorn visas nu som **Visa beroenden**. Klicka på kolumnen om du vill visa beroenden för datorn.
4. Beroende kartan för datorn visar följande information:
    - Inkommande (klienter) och utgående (servrar) TCP-anslutningar till/från datorn
        - De beroende datorer som inte har den MMA och beroende agenten är installerad är grupperade efter portnummer
        - Dependenct datorer som har MMA och beroende agenten installerad visas som separata rutor 
    - Processer som körs på datorn som du kan expandera varje dator kryssrutan för att visa processer
    - Egenskaper som fullständigt kvalificerade namn, operativsystem, etc. för MAC-adress för varje dator som du kan klicka på varje dator om du vill visa den här informationen

 ![Visa datorberoenden](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. Du kan titta på beroenden för olika tidsvaraktigheter genom att klicka på varaktighet i intervallet tidsangivelse. Som standard är området en timme. Du kan ändra tidsintervallet eller ange start- och slutdatum och varaktighet.
5. När du har identifierat beroende datorer som du vill gruppera använda Ctrl + klicka för att välja flera datorer på kartan och klicka på **grupperar datorer**.
6. Ange ett gruppnamn. Kontrollera att de beroende datorerna identifieras av Azure migrera. 

    > [!NOTE]
    > Om en beroende dator inte kan identifieras av Azure migrera, kan du lägga till den gruppen. Om du vill lägga till dessa datorer i gruppen som du behöver köra identifieringen igen med rätt omfattning i vCenter Server och se till att datorn har identifierats av Azure migrera.  

7. Om du vill skapa en bedömning för den här gruppen markerar du kryssrutan för att skapa en ny utvärdering för gruppen.
8. Klicka på **OK** att spara gruppen.

När gruppen skapas kan rekommenderas det att installera agenter på alla datorer i gruppen och förfina gruppen genom att visualisera beroendet av hela gruppen.

## <a name="next-steps"></a>Nästa steg

- [Lär dig hur](how-to-create-group-dependencies.md) förfina gruppen av visualisera grupp beroenden
- [Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
