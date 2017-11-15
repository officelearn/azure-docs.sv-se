---
title: "Portalen prep för virtuell StorSimple-matrisen | Microsoft Docs"
description: "Första självstudierna för att distribuera StorSimple virtuell matris innebär att förbereda Azure-portalen"
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/14/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6685c5ab7768176a0c8e7084c8512d5345732d9a
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Distribuera StorSimple virtuell matris – förbereda den Azure-portalen

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)
## <a name="overview"></a>Översikt

Det här är den första artikeln i serien i kurserna om distribution krävs för att distribuera helt virtuella matrisen som en filserver eller en iSCSI-server med hjälp av Resource Manager-modellen. Den här artikeln beskriver förberedelser krävs för att skapa och konfigurera Enhetshanteraren för StorSimple-tjänsten innan du etablerar en virtuell matris. Den här artikeln innehåller också länkar i en checklista för distributionskonfiguration och konfiguration krav.

Du måste ha administratörsbehörighet för att utföra installationen och konfigurationen. Vi rekommenderar att du läser checklistan för distributionskonfiguration innan du börjar. Portalen förberedelse tar mindre än 10 minuter.

Den information som publiceras i den här artikeln gäller för distribution av virtuella StorSimple-matriser i Azure portal och Microsoft Azure Government-molnet.

### <a name="get-started"></a>Kom igång
Arbetsflöde för distribution består av förbereda portalen, etablera en virtuell matris i din virtualiserade miljö och slutföra installationen. Om du vill komma igång med virtuella StorSimple-matris-distribution som en filserver eller en iSCSI-server, måste du referera till följande tabellform resurser.

#### <a name="deployment-articles"></a>Artiklar om distribution

För att distribuera din virtuella StorSimple-matris, finns i följande artiklar i föreskrivna sekvensen.

| **#** | **I det här steget** | **Det gör du...** | **Och använda dessa dokument.** |
| --- | --- | --- | --- |
| 1. |**Konfigurera Azure-portalen** |Skapa och konfigurera din StorSimple Device Manager-tjänsten innan du etablerar en virtuell StorSimple-matris. |[Förbereda portalen](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Etablera virtuella matrisen** |Etablera för Hyper-V och ansluta till en virtuell StorSimple-matris på ett värdsystem som kör Hyper-V på Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2. <br></br> <br></br> För VMware kan etablera och ansluta till en virtuell StorSimple-matris på ett värdsystem med VMware ESXi 5.0, 5.5 eller 6.0.<br></br> |[Etablera en virtuell matris i Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Etablera en virtuell VMware-matris](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Konfigurera virtuella matrisen** |Utföra installationen för din filserver, registrera din StorSimple-filserver och slutför installationen av enheten. Du kan sedan etablera SMB-resurser. <br></br> <br></br> Utföra installationen för iSCSI-server, registrera StorSimple iSCSI-servern och slutför installationen av enheten. Du kan sedan etablera iSCSI-volymer. |[Konfigurera virtuella matris som filserver](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Konfigurera virtuella matris som iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Du kan nu börja ställa in Azure-portalen.

## <a name="configuration-checklist"></a>Checklista för konfiguration

Checklista för konfiguration beskrivs den information som du behöver samla in innan du konfigurerar programvaran på din virtuella StorSimple-matrisen. Förbereder den här informationen i förväg hjälper till att förenkla processen för att distribuera StorSimple-enheten i din miljö. Beroende på om din virtuella StorSimple-matris distribueras som en filserver eller en iSCSI-server, behöver du något av följande checklistor.

* Hämta den [checklista för konfiguration av StorSimple virtuella matris, File Server](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Hämta den [virtuella StorSimple-matris iSCSI checklista för konfiguration av servern](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Krav

Här hittar du konfigurationskraven för din StorSimple Device Manager-tjänst, din virtuella StorSimple-matris och datacenternätverket.

### <a name="for-the-storsimple-device-manager-service"></a>För StorSimple Device Manager-tjänsten

Innan du börjar bör du kontrollera att:

* Du har ditt Microsoft-konto med autentiseringsuppgifter.
* Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.
* Microsoft Azure-prenumerationen ska aktiveras för StorSimple enheten Manager-tjänsten.

### <a name="for-the-storsimple-virtual-array"></a>För den virtuella StorSimple-matrisen

Innan du distribuerar en virtuell matris, kontrollerar du att:

* Du har åtkomst till en värdsystem som kör Hyper-V på Windows Server 2008 R2 eller senare eller VMware (ESXi 5.0, 5.5 eller 6.0) som kan användas för att en etablera en enhet.
* Värddatorn är att dedikera följande resurser för att etablera virtuella matrisen:
  
  * Minst 4 kärnor.
  * Minst 8 GB RAM-minne. Om du planerar att konfigurera virtuella matrisen som filserver stöder 8 GB 2 miljoner filer. Behöver du 16 GB RAM-MINNET 2-4 miljoner stödfiler.
  * Ett nätverksgränssnitt.
  * En virtuell disk 500 GB efter systemdata.

### <a name="for-the-datacenter-network"></a>För datacenternätverket

Innan du börjar bör du kontrollera att:

* Nätverket i datacentret har konfigurerats enligt nätverkskraven för din StorSimple-enhet. Mer information finns i [StorSimple virtuell matris systemkrav](storsimple-ova-system-requirements.md).
* Din virtuella StorSimple-matris har en dedikerad 5 Mbit/s internetbandbredd (eller mer) vid alla tidpunkter. Den här bandbredden ska inte delas med andra program.

## <a name="step-by-step-preparation"></a>Förberedelse av steg

Använd följande steg för steg-instruktioner för att förbereda din portal för StorSimple Device Manager-tjänsten.

## <a name="step-1-create-a-new-service"></a>Steg 1: Skapa en ny tjänst

En instans av StorSimple enheten Manager-tjänsten kan hantera flera virtuella StorSimple-matriser. Skapa en instans av StorSimple Device Manager-tjänsten genom att utföra stegen nedan. Om du har en befintlig StorSimple Device Manager-tjänst för att hantera dina virtuella matriser, hoppa över det här steget och gå till [steg 2: Hämta nyckel för tjänstregistrering](#step-2-get-the-service-registration-key).

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

Utför följande steg i den [Azure-portalen](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> Nyckeln för tjänstregistrering används för att registrera alla Enhetshanteraren för StorSimple-enheter som behöver registreras med din StorSimple Device Manager-tjänst.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Steg 3: Ladda ned avbildningen virtuella matris

När du har nyckeln för tjänstregistrering, behöver du hämta lämplig virtuell matris-avbildning för att etablera en virtuell matrisen på värdsystemet. Virtuella matris-avbildningar är specifika för operativsystemet och kan hämtas från sidan Snabbstart i Azure-portalen.

> [!IMPORTANT]
> Programmet som körs på den virtuella StorSimple-matrisen får endast användas med Enhetshanteraren för StorSimple-tjänsten.
> 
> 

Utför följande steg i den [Azure-portalen](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>Den virtuella matris-bild

1. Logga in på [Azure-portalen](https://portal.azure.com/). 
2. I Azure-portalen klickar du på **Bläddra > StorSimple enhetshanterare**.
3. Välj en befintlig StorSimple Device Manager-tjänst. I den **StorSimple Enhetshanteraren** bladet, klickar du på **Snabbstart**. 
4. Klicka på länken som motsvarar den bild som du vill hämta från Microsoft Download Center. Bildfilerna är ungefär 4,8 GB.
   
   * VHDX för Hyper-V på Windows Server 2012 och senare
   * VHD för Hyper-V på Windows Server 2008 R2 och senare
   * VMDK för VMWare ESXi 5.0, 5.5 eller 6.0
5. Hämta och packa upp filen till en lokal enhet, och ett meddelande om där den uppackade filen finns.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Valfritt steg: konfigurera ett nytt lagringskonto för tjänsten

Det här steget är valfritt och bör bara genomföras om du inte har aktiverat automatiskt skapande av lagringskonton med din tjänst.

Om du behöver skapa ett Azure storage-konto i en annan region finns [hur du skapar ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account) stegvisa instruktioner.

Utför följande steg i den [Azure-portalen](https://ms.portal.azure.com/) på tjänstsidan StorSimple Enhetshanteraren att lägga till ett befintligt Microsoft Azure-lagringskonto.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Om du vill lägga till en autentiseringsuppgift för storage-konto har som samma Azure-prenumerationen som tjänsten Device Manager

1. Gå till Enhetshanteraren tjänsten, väljer och dubbelklicka på den. Då öppnas den **översikt** bladet.
2. Välj **lagringskontouppgifter** inom den **Configuration** avsnitt.
3. Klicka på **Lägg till**.
4. I den **Lägg till ett lagringskonto** bladet gör du följande:
   
    1. För **prenumeration**väljer **aktuella**.
   
    2. Ange namnet på ditt Azure storage-konto.
   
    3. Välj **aktivera** att skapa en säker kanal för nätverkskommunikation mellan din StorSimple-enhet och molnet. Välj **inaktivera** endast om du arbetar inom ett privat moln.
   
    4. Klicka på **Lägg till**. Du meddelas när lagringskontot har skapats.<br></br>
   
     ![Lägg till en befintlig lagring konto autentiseringsuppgift](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Nästa steg

Nästa steg är att etablera en virtuell dator för din virtuella StorSimple-matrisen. Beroende på din värdoperativsystemet detaljerade instruktioner finns i:

* [Etablera en virtuell StorSimple-matris i Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Etablera en virtuell StorSimple-matris på VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

