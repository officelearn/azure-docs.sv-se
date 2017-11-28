---
title: "Gruppera datorer med hjälp av beroenden för datorn med Azure migrera | Microsoft Docs"
description: "Beskriver hur du skapar en bedömning med tjänsten Azure migrera datorn beroenden."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0527e34e-a078-405e-aeb9-c91a5808112a
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 5ed49f3dc71af6a8c1c7b6c9e38fd84452505aec
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2017
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Grupp datorer som använder datorn beroende mappning

Den här artikeln beskriver hur du skapar en grupp datorer för [Azure migrera](migrate-overview.md) assessment datorn beroende mappning. Du använder vanligtvis den här metoden när du vill utvärdera grupper med virtuella datorer med högre tillförlitlighet av dubbelkontrollera datorn beroenden innan du kör en utvärdering.



## <a name="prepare-machines-for-dependency-mapping"></a>Förbereda datorer för beroende mappning
För att inkludera datorer i beroende mappning, måste du hämta och installera agenter på varje lokal dator som du vill utvärdera. Dessutom om du har datorer med utan internet-anslutning, måste du hämta och installera [OMS gateway](../log-analytics/log-analytics-oms-gateway.md) på dem.

### <a name="download-and-install-the-vm-agents"></a>Hämta och installera agenter för VM
1. I **översikt**, klickar du på **hantera** > **datorer**, och välj datorn som krävs.
2. I den **beroenden** kolumnen, klickar du på **installera agenter**. 
3. På den **beroenden** ladda ned och installera Microsoft Monitoring Agent (MMA) och beroende-agenten på varje virtuell dator som du vill utvärdera.
4. Kopiera arbetsyte-ID och nyckel. Du behöver dem när du installerar MMA på den lokala datorn.

### <a name="install-the-mma"></a>Installera MMA

Installera agenten på en Windows-dator:

1. Dubbelklicka på den hämta agenten.
2. På den **Välkommen** klickar du på **nästa**. På den **licensvillkoren** klickar du på **jag accepterar** att acceptera licensvillkoren.
3. I **målmappen**, behålla eller ändra standardinstallationsmappen > **nästa**. 
4. I **installationsalternativ för Agent**väljer **Azure logganalys (OMS)** > **nästa**. 
5. Klicka på **Lägg till** att lägga till en ny OMS-arbetsyta. Klistra in i arbetsyte-ID och nyckel som du kopierade från portalen. Klicka på **Nästa**.


Installera agenten på en Linux-dator:

1. Över rätt paket (x86 eller x64) till Linux-datorn med scp/sftp.
2. Installera paketet med argumentet – installera.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>Installera agenten beroende
1. Dubbelklicka på installationsfilen för att installera agenten beroende på en Windows-dator, och följ instruktionerna i guiden.
2. Installera agenten beroende på en Linux-dator, installera som rot med följande kommando:

    ```sh InstallDependencyAgent-Linux64.bin```

[Lär dig mer](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems) om operativsystem som stöds av beroendeagent. 

## <a name="create-a-group"></a>Skapa en grupp

1. När du har installerat agenterna, gå till portalen och klicka på **hantera** > **datorer**.
2. Den **beroenden** kolumnen visas nu som **Visa beroenden**. Klicka på kolumnen om du vill visa beroenden.
3. Du kan verifiera för varje dator:
    - Om MMA och beroende-agenten är installerad och om datorn har identifierats.
    - Gästoperativsystemet som körs på datorn.
    - Inkommande och utgående IP-anslutningar och portar.
    - Processer som körs på datorer.
    - Beroenden mellan datorer.

4. Mer detaljerade beroenden, klickar du på tidsintervall om du vill ändra. Som standard är området en timme. Du kan ändra tidsintervallet eller ange start- och slutdatum och varaktighet.
5. När du har identifierat beroende datorer som du vill gruppera väljer datorer på kartan Klicka på **grupperar datorer**.
6. Ange ett gruppnamn. Kontrollera att datorn har identifierats av Azure migrera. Om det inte kör identifiering av processen lokal igen. Du kan köra en utvärdering direkt om du vill.
7. Klicka på **OK** att spara gruppen.

    ![Skapa en grupp med datorn beroenden](./media/how-to-create-group-machine-dependencies/create-group.png)

## <a name="next-steps"></a>Nästa steg

- [Lär dig hur](how-to-create-group-dependencies.md) förfina gruppen genom att kontrollera att gruppen beroenden
- [Lär dig mer](concepts-assessment-calculation.md) om hur bedömningar beräknas.
