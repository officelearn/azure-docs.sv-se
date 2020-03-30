---
title: Portalprepar för StorSimple Virtual Array
description: Första självstudien för att distribuera StorSimple virtuell matris innebär att förbereda Azure-portalen
author: alkohli
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f2c7a6f9c1146627f81dc20386c59a91724b245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254539"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Distribuera StorSimple Virtual Array - Förbered Azure-portalen

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)


## <a name="overview"></a>Översikt

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Det här är den första artikeln i serien av självstudier för distribution som krävs för att fullständigt distribuera den virtuella matrisen som en filserver eller en iSCSI-server med hjälp av Resource Manager-modellen. I den här artikeln beskrivs de förberedelser som krävs för att skapa och konfigurera Tjänsten StorSimple Device Manager innan du etablerar en virtuell matris. Den här artikeln länkar också till en checklista för distributionskonfiguration och konfigurationsförutsättningar.

Du måste ha administratörsbehörighet för att utföra installationen och konfigurationen. Vi rekommenderar att du granskar checklistan för distributionskonfiguration innan du börjar. Portalförberedelserna tar mindre än tio minuter.

Informationen som publiceras i den här artikeln gäller distributionen av StorSimple-virtuella matriser i Azure-portalen och Microsoft Azure Government Cloud.

### <a name="get-started"></a>Komma igång
Distributionsarbetsflödet består i att förbereda portalen, etablera en virtuell matris i den virtualiserade miljön och slutföra installationen. Om du vill komma igång med StorSimple Virtual Array-distributionen som en filserver eller en iSCSI-server måste du referera till följande tabulerade resurser.

#### <a name="deployment-articles"></a>Distributionsartiklar

Om du vill distribuera den virtuella storsimple-matrisen läser du följande artiklar i den föreskrivna sekvensen.

| **#** | **I det här steget** | **Du gör det här ...** | **Och använd de här dokumenten.** |
| --- | --- | --- | --- |
| 1. |**Konfigurera Azure-portalen** |Skapa och konfigurera tjänsten StorSimple Device Manager innan du etablerar en Virtuell StorSimple-matris. |[Förbered portalen](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Etablera den virtuella matrisen** |För Hyper-V etablerar och ansluter du till en virtuell storsimple-matris på ett värdsystem som kör Hyper-V på Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2. <br></br> <br></br> För VMware etablerar och ansluter du till en StorSimple Virtual Array på ett värdsystem som kör VMware ESXi 5.0, 5.5, 6.0 eller 6.5.<br></br> |[Etablera en virtuell matris i Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Etablera en virtuell matris i VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Konfigurera den virtuella matrisen** |För filservern utför du den första installationen, registrerar filservern StorSimple och slutför enhetskonfigurationen. Sedan kan du etablera SMB-resurser. <br></br> <br></br> För iSCSI-servern utför du den första installationen, registrerar StorSimple iSCSI-servern och slutför enhetskonfigurationen. Du kan sedan etablera iSCSI-volymer. |[Konfigurera virtuell matris som filserver](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Konfigurera virtuell matris som iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Nu kan du börja konfigurera Azure-portalen.

## <a name="configuration-checklist"></a>Checklista för konfiguration

I konfigurationschecklistan beskrivs den information som du behöver samla in innan du konfigurerar programvaran på den virtuella storsimple-matrisen. Genom att förbereda den här informationen i förväg effektiviseras distributionen av StorSimple-enheten i din miljö. Beroende på om din StorSimple Virtual Array distribueras som en filserver eller en iSCSI-server behöver du någon av följande checklistor.

* Hämta [konfigurationschecklistan](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf)för Virtual Array File Server .
* Hämta checklistan för konfiguration av [StorSimple Virtual Array iSCSI Server.](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf)

## <a name="prerequisites"></a>Krav

Här hittar du konfigurationsförutsättningarna för tjänsten StorSimple Device Manager, din StorSimple Virtual Array och datacenternätverket.

### <a name="for-the-storsimple-device-manager-service"></a>För StorSimple Device Manager-tjänsten

Innan du börjar bör du kontrollera att:

* Du har ditt Microsoft-konto med autentiseringsuppgifter.
* Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.
* Din Microsoft Azure-prenumeration ska vara aktiverad för StorSimple Device Manager-tjänsten.

### <a name="for-the-storsimple-virtual-array"></a>För StorSimple Virtual Array

Innan du distribuerar en virtuell matris ska du se till att:

* Du har tillgång till ett värdsystem som kör Hyper-V på Windows Server 2008 R2 eller senare eller VMware (ESXi 5.0, 5.5, 6.0 eller 6.5) som kan användas för att etablera en enhet.
* Värdsystemet kan avsätta följande resurser för att etablera den virtuella matrisen:
  
  * Minst 4 kärnor.
  * Minst 8 GB RAM. Om du planerar att konfigurera den virtuella matrisen som filserver stöder 8 GB 2 miljoner filer. Du behöver 16 GB RAM för att stödja 2 - 4 miljoner filer.
  * Ett nätverksgränssnitt.
  * En virtuell disk på 500 GB för systemdata.

### <a name="for-the-datacenter-network"></a>För datacenternätverket

Innan du börjar bör du kontrollera att:

* Nätverket i ditt datacenter är konfigurerat enligt nätverkskraven för din StorSimple-enhet. Mer information finns i [Systemkraven för StorSimple Virtual Array](storsimple-ova-system-requirements.md).
* Din StorSimple Virtual Array har alltid en dedikerad 5 Mbit/s Internet-bandbredd (eller mer). Den här bandbredden ska inte delas med andra program.

## <a name="step-by-step-preparation"></a>Steg-för-steg-förberedelse

Använd följande steg-för-steg-instruktioner för att förbereda portalen för Tjänsten StorSimple Device Manager.

## <a name="step-1-create-a-new-service"></a>Steg 1: Skapa en ny tjänst

En enda instans av StorSimple Device Manager-tjänsten kan hantera flera StorSimple-virtuella matriser. Skapa en instans av StorSimple Device Manager-tjänsten genom att utföra stegen nedan. Om du har en befintlig StorSimple Enhetshanteraren-tjänst för att hantera dina virtuella matriser hoppar du över det här steget och går till [steg 2: Hämta tjänstregistreringsnyckeln](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> Om du inte har aktiverat automatiskt skapande av lagringskonton med din tjänst måste du skapa minst ett lagringskonto efter att du har skapat en tjänst.
> 
> * Om du inte har skapat ett lagringskonto automatiskt går du till [Konfigurera ett nytt lagringskonto för tjänsten](#optional-step-configure-a-new-storage-account-for-the-service) för detaljerade anvisningar.
> * Om du har aktiverat automatiskt skapande av ett lagringskonto går du till [steg 2: hämta nyckel för tjänstregistrering](#step-2-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Steg 2: Hämta nyckel för tjänstregistrering

När StorSimple Device Manager-tjänsten är igång måste du hämta tjänstregistreringsnyckeln. Den här nyckeln används för att registrera och ansluta din StorSimple-enhet till tjänsten.

Utför följande steg på [Azure-portalen](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> Tjänstregistreringsnyckeln används för att registrera alla StorSimple Device Manager-enheter som behöver registrera sig hos tjänsten StorSimple Device Manager.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Steg 3: Ladda ner den virtuella matrisbilden

När du har nyckeln till tjänstregistrering måste du hämta lämplig virtuell matrisavbildning för att etablera en virtuell matris på värdsystemet. De virtuella matrisavbildningarna är operativsystemspecifika och kan hämtas från snabbstartssidan i Azure-portalen.

> [!IMPORTANT]
> Programvaran som körs på StorSimple Virtual Array får endast användas med StorSimple Device Manager-tjänsten.
> 
> 

Utför följande steg på [Azure-portalen](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>Så här hämtar du den virtuella matrisavbildningen

1. Logga in på [Azure-portalen](https://portal.azure.com/). 
2. Klicka på Bläddra **> StorSimple-enhetshanterare i Azure-portalen**.
3. Välj en befintlig StorSimple Enhetshanteraren-tjänst. Klicka på **Snabbstart**i bladet **StorSimple Enhetshanteraren** . 
4. Klicka på länken som motsvarar den bild som du vill hämta från Microsoft Download Center. Avbildningsfilerna är cirka 4,8 GB.
   
   * VHDX för Hyper-V på Windows Server 2012 och senare
   * VIRTUELLD för Hyper-V på Windows Server 2008 R2 och senare
   * VMDK för VMWare ESXi 5.0, 5.5, 6.0 eller 6.5
5. Ladda ned och packa upp filen till en lokal enhet och skriv ned sökvägen till de uppackade filerna.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Valfritt steg: Konfigurera ett nytt lagringskonto för tjänsten

Det här steget är valfritt och bör endast utföras om du inte har aktiverat automatisk skapande av ett lagringskonto med din tjänst.

Om du behöver skapa ett Azure-lagringskonto i en annan region läser du [Så här skapar du ett lagringskonto](../storage/common/storage-account-create.md) för steg-för-steg-instruktioner.

Utför följande steg i [Azure-portalen](https://ms.portal.azure.com/) på tjänstsidan För StorSimple Enhetshanteraren för att lägga till ett befintligt Microsoft Azure-lagringskonto.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Så här lägger du till en lagringskontoautentiseringsinformation som har samma Azure-prenumeration som Tjänsten Enhetshanteraren

1. Navigera till tjänsten Enhetshanteraren, välj och dubbelklicka på den. Då öppnas **bladet Översikt.**
2. Välj **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration.**
3. Klicka på **Lägg till**.
4. Gör följande i bladet **Lägg till ett lagringskonto:**
   
   1. För **Prenumeration**väljer du **Aktuell**.
   
   2. Ange namnet på ditt Azure-lagringskonto.
   
   3. Välj **Aktivera** om du vill skapa en säker kanal för nätverkskommunikation mellan StorSimple-enheten och molnet. Välj **Inaktivera** endast om du arbetar i ett privat moln.
   
   4. Klicka på **Lägg till**. Du meddelas när lagringskontot har skapats.<br></br>
   
      ![Lägga till en befintlig lagringskontoautentisering](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Nästa steg

Nästa steg är att etablera en virtuell dator för den virtuella storsimple-matrisen. Läs de detaljerade instruktionerna i följande avsnitt, beroende på ditt värdoperativsystem:

* [Etablera en storsimple virtuell array i Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Etablera en StorSimple-virtuell array i VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

