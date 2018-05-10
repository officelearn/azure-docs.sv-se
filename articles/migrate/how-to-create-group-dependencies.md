---
title: Förfina en bedömning grupp med Gruppmappning av beroende i Azure migrera | Microsoft Docs
description: Beskriver hur du förfina bedömning med mappning av beroende i tjänsten Azure migrera.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/22/2017
ms.author: raynew
ms.openlocfilehash: 897b45782dee14099d5d7a7b12c49e2bfd60b309
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Förfina grupp med mappning av beroende

Den här artikeln beskriver hur du förfina en grupp med visualisera beroenden för alla datorer i gruppen. Du använder vanligtvis den här metoden när du vill begränsa medlemskap för en befintlig grupp genom att dubbelkontrollera grupp beroenden, innan du kör en utvärdering. Begränsa en grupp med beroende visualiseringen kan hjälpa dig att planera migreringen till Azure.You effektivt kan identifiera alla beroende av varandra system som behöver migreras tillsammans. Det hjälper dig se till att inget kvar och oväntat avbrott sker inte när du migrerar till Azure. 


> [!NOTE]
> Grupper som du vill visualisera beroenden får inte innehålla fler än 10-datorer. Om du har fler än 10 datorer i gruppen, rekommenderar vi att du dela upp den i mindre grupper om du vill utnyttja funktionen beroende visualiseringen.


# <a name="prepare-the-group-for-dependency-visualization"></a>Förbereda gruppen för beroende visualisering
Om du vill visa beroenden för en grupp som du behöver hämta och installera agenter på varje lokal dator som ingår i gruppen. Dessutom om du har datorer med utan internet-anslutning, måste du hämta och installera [OMS gateway](../log-analytics/log-analytics-oms-gateway.md) på dem.

### <a name="download-and-install-the-vm-agents"></a>Hämta och installera VM-agenterna
1. I **översikt**, klickar du på **hantera** > **grupper**går du till den nödvändiga gruppen.
2. I listan över datorer i den **beroendeagent** kolumnen, klickar du på **måste du installera** att visa instruktioner om hur du hämtar och installerar agenter.
3. På den **beroenden** ladda ned och installera Microsoft Monitoring Agent (MMA) och beroende-agenten på varje virtuell dator som ingår i gruppen.
4. Kopiera arbetsytans ID och nyckel. Du behöver dem när du installerar MMA på lokala datorer.

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

## <a name="refine-the-group-based-on-dependency-visualization"></a>Förfina grupp baserat på beroendet visualiseringen
När du har installerat agenter på alla datorer i gruppen kan du visualisera beroenden för gruppen och förfina genom att följa de nedanstående steg.

1. I Azure migrera projektet, under **hantera**, klickar du på **grupper**, och välj gruppen.
2. På sidan grupp **Visa beroenden**, för att öppna grupp beroende kartan.
3. Beroende kartan för gruppen visas följande information:
    - Inkommande (klienter) och utgående (servrar) TCP-anslutningar till/från alla datorer som ingår i gruppen
        - De beroende datorer som inte har den MMA och beroende agenten är installerad är grupperade efter portnummer
        - Dependenct datorer som har MMA och beroende agenten installerad visas som separata rutor 
    - Processer som körs på datorn som du kan expandera varje dator kryssrutan för att visa processer
    - Egenskaper som fullständigt kvalificerade namn, operativsystem, etc. för MAC-adress för varje dator som du kan klicka på varje dator om du vill visa den här informationen

     ![Visa gruppberoenden](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Klicka på tidsintervall om du vill ändra den om du vill visa mer detaljerad beroenden. Som standard är intervallet en timme. Du kan ändra tidsintervallet eller ange start- och slutdatum och varaktighet.
4. Kontrollera beroende datorer processen körs på varje dator och identifiera de datorer som ska läggas till eller tas bort från gruppen.
5. Använd Ctrl + klicka om du vill välja datorer på kartan för att lägga till eller ta bort dem från gruppen.
    - Du kan bara lägga till datorer som har identifierats.
    - Lägga till och ta bort datorer från en grupp upphäver tidigare bedömningar för den.
    - Du kan också skapa en ny utvärdering när du ändrar gruppen.
5. Klicka på **OK** att spara gruppen.

    ![Lägg till eller ta bort datorer](./media/how-to-create-group-dependencies/add-remove.png)

Om du vill kontrollera beroenden för en specifik dator som visas i gruppen beroende kartan [Ställ in datorn beroende mappning](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>Nästa steg

[Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
