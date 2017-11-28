---
title: "Identifiera och utvärdera lokala virtuella VMware-datorer för migrering till Azure med Azure migrera | Microsoft Docs"
description: "Beskriver hur du identifierar och utvärdera lokala virtuella VMware-datorer för migrering till Azure med tjänsten Azure migrera."
services: migration-planner
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a2521630-730f-4d8b-b298-e459abdced46
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: fd9513cda899a7ac2a51c4e1ab03a80ce0adf2fd
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Identifiera och utvärdera lokala virtuella VMware-datorer för migrering till Azure

Den [Azure migrera](migrate-overview.md) services utvärderar lokala arbetsbelastningar för migrering till Azure.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett Azure migrera projekt.
> * Konfigurera en lokal insamlaren virtuell dator (VM) för att identifiera lokala virtuella VMware-datorer för.
> * Gruppera virtuella datorer och skapa en utvärdering.


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prerequisites"></a>Krav

- **VMware**: du behöver minst en VMware VM finns på en ESXi-värd eller kluster som kör version 5.0 eller senare. Värd eller kluster måste hanteras av en vCenter-server som kör version 5.5, 6.0 eller 6.5.
- **vCenter konto**: du behöver ett skrivskyddat konto med administratörsbehörighet för vCenter-servern. Azure migrera använder kontot för att identifiera virtuella datorer.
- **Behörigheter**: på vCenter-server du behöver för att skapa en virtuell dator genom att importera en fil i. ÄGG format. 
- **Inställningar för statistik**: statistik inställningarna för vCenter-servern ska vara inställd på nivå 3 innan du börjar distributionen. Om är lägre än nivå 3 bedömning fungerar, men inte samlas in prestandadata för lagring och nätverk.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal
Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-project"></a>Skapa ett projekt

1. I Azure-portalen klickar du på **skapar du en resurs**.
2. Sök efter **Azure migrera**, och välj tjänsten (**Azure migrera (förhandsgranskning)** i sökresultaten. Klicka sedan på **Skapa**.
3. Ange ett projektnamn och Azure-prenumeration för projektet.
4. Skapa en ny resursgrupp.
5. Ange regionen där du vill skapa projektet och klicka sedan på **skapa**. Metadata som samlats in från lokala virtuella datorer kommer att lagras i den här regionen. Du kan bara skapa ett Azure migrera projekt i West centrala oss region för den här förhandsversionen. Du kan dock bedöma virtuella datorer för en annan plats.

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)
    


## <a name="download-the-collector-appliance"></a>Hämta insamlaren-enhet

Azure migrera skapar en lokal virtuell dator som kallas insamlaren installation. Den här virtuella datorn identifierar lokala virtuella VMware-datorer och skickar metadata om dem till tjänsten Azure migrera. Om du vill konfigurera insamlaren-enhet som du hämtar ett. ÄGG, och importera den till vCenter-servern lokalt för att skapa den virtuella datorn.

1. I Azure migrera-projektet klickar du på **komma igång** > **Discover & Bedöm** > **identifiera datorer**.
2. I **identifiera datorer**, klickar du på **hämta**, för att ladda ned den. ÄGG fil.
3. I **Kopiera projekt autentiseringsuppgifterna**, kopiera projekt-ID och nyckel. Du behöver dem när du konfigurerar insamlaren.

    ![Hämta .ova fil](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Kontrollera insamlaren-enhet

Kontrollera att den. ÄGG filen är säker, innan du distribuerar den.

1. Öppna ett kommandofönster administratör på datorn som du laddat ned filen.
2. Kör följande kommando för att generera hash för ägg:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning:```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Den genererade hashen måste matcha inställningarna.

    **Algoritmen** | **Hash-värde**
    --- | ---
    MD5 | dd27dd6ace28f9195a2b5d52a4003067 
    SHA1 | d2349e06a5d4693fc2a1c0619591b9e45c36d695
    SHA256 | 1492a0c6d6ef76e79269d5cd6f6a22f336341e1accbc9e3dfa5dad3049be6798

## <a name="create-the-collector-vm"></a>Skapa insamlaren VM

Importera den hämta filen till vCenter-servern.

1. I konsolen vSphere-klienten klickar du på **filen** > **distribuera OVF mallen**.

    ![Distribuera OVF](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. I guiden Distribuera OVF mall > **källa**, ange platsen för filen .ova.
3. I **namn** och **plats**, ange ett eget namn för VM-insamlare och inventeringen objekt som den virtuella datorn kommer att finnas.
5. I **värden/klustret**, anger du värden eller kluster som insamlaren VM körs.
7. Ange lagringsplats för insamlare VM i lagring.
8. I **diskformat**, ange disktyp och storlek.
9. I **nätverksmappning**, ange som kommer ansluta till insamlaren VM-nätverk. Nätverket måste internet-anslutning att skicka metadata till Azure. 
10. Granska och bekräfta inställningarna och klicka på **Slutför**.


## <a name="run-the-collector-to-discover-vms"></a>Kör insamlaren för att identifiera virtuella datorer

1. Högerklicka på den virtuella datorn i klientkonsolen vSphere > **öppna konsolen**.
2. Ange språk, tidszon och lösenord inställningar för produkten.
3. På skrivbordet klickar du på den **kör insamlaren** genväg.
4. Öppna i Azure migrera insamlaren **in krav**.
    - Acceptera licensvillkoren och läsa information från tredje part.
    - Insamlaren kontrollerar att den virtuella datorn har Internetåtkomst.
    - Om den virtuella datorn har åtkomst till internet via en proxyserver, klickar du på **proxyinställningar**, och ange proxyadress och lyssningsport. Ange autentiseringsuppgifter om proxyn behöver autentisering.
    - Insamlaren kontrollerar att profileraren Windows-tjänsten körs. Tjänsten installeras som standard på VM-insamlaren.
    - Hämta och installera VMware PowerCLI.
. I **identifiera datorer**, gör du följande:
    - Ange namn (FQDN) eller IP-adressen för vCenter-servern.
    - I **användarnamn** och **lösenord**, ange de skrivskyddade kontoautentiseringsuppgifter som insamlaren använder för att identifiera virtuella datorer på vCenter server.
    - I **samling scope**, väljer du en omfattning för identifiering av virtuell dator. Insamlaren kan bara identifiera virtuella datorer i angivet omfång. Omfång kan anges till en viss mapp, ett datacenter eller ett kluster. Det får inte innehålla fler än 1000 virtuella datorer. 
    - I **Taggkategori för att gruppera**väljer **ingen**.
1. I **Välj projekt**anger Azure migrera projekt-ID och nyckel som du kopierade från portalen. Om inte kopiera dem, öppna Azure-portalen från VM-insamlaren. I projektet **översikt** klickar du på **identifiera datorer**, och kopiera värdena.  
2. I **fullständig identifiering**, övervaka identifiering och kontrollera att metadata som samlas in från de virtuella datorerna är i ett omfång. Insamlaren ger en tid för ungefärlig identifiering.


### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

Identifiering av tiden beror på hur många virtuella datorer som du identifierar. Normalt tar för 100 virtuella datorer, när insamlaren är klar kör det runt en timme för att slutföra identifieringen. 

1. Klicka i projektet migrering Planner **hantera** > **datorer**.
2. Kontrollera att de virtuella datorerna som du vill identifiera visas på portalen.


## <a name="create-and-view-an-assessment"></a>Skapa och visa en utvärdering

Efter att virtuella datorer identifieras gruppera dem och skapa en utvärdering. 

1. I projektet **översikt** klickar du på **+ skapa assessment**.
2. Klicka på **visa alla** att granska inställningarna för utvärdering.
3. Skapa gruppen och ange ett gruppnamn.
4. Välj de datorer som du vill lägga till i gruppen.
5. Klicka på **skapa Assessment**, för att skapa gruppen och bedömningen.
6. När bedömningen har skapats kan du visa den på **översikt** > **instrumentpanelen**.
7. Klicka på **exportera assessment**, för att hämta det som en Excel-fil.

### <a name="sample-assessment"></a>Exempel assessment

Här är en exempelrapport assessment. Den innehåller information om virtuella datorer är kompatibla för Azure och uppskattade månatliga kostnader. 

![Rapport](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Azure beredskap

Den här vyn visar beredskap status för varje dator.

- För virtuella datorer som är klara rekommenderar Azure migrera VM-storlek i Azure.
- För virtuella datorer som inte är klar kan Azure migrera beskriver varför och innehåller steg.
- Azure migrera föreslår verktyg som du kan använda för migreringen. Om datorn är lämplig för migrering av lift och SKIFT rekommenderas Azure Site Recovery-tjänsten. Tjänsten Azure Database migrering rekommenderas om det är en dator i databasen.

  ![Bedömning beredskap](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Månatliga kostnadsuppskattning

Den här vyn visar Kostnadshantering för beräkning och lagring för varje dator. Kostnad uppskattningar beräknas med hjälp av rekommendationer för prestandabaserad storlek för en dator och dess diskar och assessment egenskaper.

Beräknade månatliga kostnader för beräkning och lagring sammanställs för alla virtuella datorer i gruppen. Du kan klicka på varje dator och öka detaljnivån för ytterligare information. 

![Bedömning VM-kostnad](./media/tutorial-assessment-vmware/assessment-vm-cost.png) 

Du kan öka detaljnivån till se kostnader för en specifik dator.

![Bedömning VM-kostnad](./media/tutorial-assessment-vmware/assessment-vm-drill.png) 

## <a name="next-steps"></a>Nästa steg

- [Läs](how-to-scale-assessment.md) hur du ställer in en bedömning för ett stort antal lokala datorer.
- Lär dig att skapa mer detaljerade assessment grupper med [datorn beroende mappning](how-to-create-group-machine-dependencies.md)
- [Lär dig mer](concepts-assessment-calculation.md) om hur bedömningar beräknas.
