---
title: Red Hat Enterprise Linux prenumeration-avbildningar | Microsoft Docs
description: Lär dig mer om att överföra prenumerations avbildningar för Red Hat Enterprise Linux på Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: alsin
ms.openlocfilehash: afd59c44bea238b7dd6f116e491054f8b4a52d48
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486513"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-images-in-azure"></a>Red Hat Enterprise Linux ta med dina prenumerations avbildningar i Azure
Red Hat Enterprise Linux-avbildningar (RHEL) är tillgängliga i Azure via en PAYG-modell (pay-on-go-go) eller en BYOS-modell. Det här dokumentet ger en översikt över BYOS-avbildningarna i Azure.

## <a name="important-points-to-consider"></a>Viktiga saker att tänka på

- RHEL BYOS-avbildningarna i det här programmet är produktions klara RHEL-bilder som liknar RHEL PAYG-avbildningarna i Azure-galleriet/Marketplace. Registrerings processen för att hämta avbildningarna är i för hands version.

- Bilderna följer våra aktuella principer som beskrivs i [Red Hat Enterprise Linux avbildningar på Azure](./redhat-images.md)

- Standard Support principer gäller för virtuella datorer som skapats från de här avbildningarna

- De virtuella datorer som tillhandahålls från RHEL BYOS-avbildningar har inte RHEL avgifter kopplade till RHEL PAYG-avbildningar

- Bilderna är inte berättigade, så du måste använda prenumerations hanteraren för att registrera och prenumerera på de virtuella datorerna för att få uppdateringar från Red Hat direkt

- Det går för närvarande inte att växla mellan BYOS-och PAYG-fakturerings modeller för Linux-avbildningar dynamiskt. Omdistribution av den virtuella datorn från respektive avbildning krävs för att byta fakturerings modell

- Azure Disk Encryption (ADE) stöds på dessa RHEL BYOS-avbildningar. Stöd för ADE är för närvarande en för hands version. Du måste registrera dig med Red Hat med hjälp av prenumerations hanteraren innan du konfigurerar ADE. När du har registrerat dig för att konfigurera ADE kan du läsa: [aktivera Azure Disk Encryption för virtuella Linux IaaS-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-overview)

- Medan bilderna inte ändras (utöver standard uppdateringar och korrigeringar) är registrerings processen i för hands version och flödet blir ytterligare bättre för att effektivisera processen

- Du har fullständig kontroll över de virtuella datorerna som redan har allokerats från de här avbildningarna eller dess ögonblicks bilder, oavsett den slutliga implementeringen

## <a name="requirements-and-conditions-to-access-the-rhel-byos-images"></a>Krav och villkor för att få åtkomst till RHEL BYOS-avbildningar

1. Bekanta dig med program villkoren för [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) och registrera dig för [Red Hats registrerings sida för moln åtkomst](https://access.redhat.com/cloude/manager/image_imports/new).

1. Fyll i [formuläret RHEL BYOS Access Request](https://aka.ms/rhel-byos). Du måste ha till gång till ditt Red Hat-kontonummer och dina Azure-prenumerationer som du har för att komma åt RHEL BYOS-avbildningarna med.

1. Vid granskning och godkännande av både Red Hat och Microsoft aktive ras din Azure-prenumeration (er) för bild åtkomst.

### <a name="expected-time-for-image-access"></a>Förväntad tid för bild åtkomst

När du fyller i formuläret RHEL BYOS och accepterar villkoren, validerar Red Hat din behörighet för BYOS-avbildningarna inom tre arbets dagar och, om det är giltigt, får du till gång till BYOS-avbildningarna inom en arbets dag efteråt.

## <a name="use-the-rhel-byos-images-from-the-azure-portal"></a>Använda RHEL BYOS-avbildningar från Azure Portal

1. När din prenumeration har Aktiver ATS för RHEL BYOS-avbildningar kan du leta upp den i [Azure Portal](https://portal.azure.com) genom att gå till **skapa en resurs** och **Se alla**.

1. Längst upp på sidan visas att du har privata erbjudanden.

    ![Privata erbjudanden för Marketplace](./media/rhel-byos-privateoffers.png)

1. Du kan klicka på den lila länken eller rulla längst ned på sidan för att se dina privata erbjudanden.

1. Resten av etableringen i användar gränssnittet är ingen annan befintlig Red Hat-bild. Välj din RHEL-version och följ anvisningarna för att etablera den virtuella datorn. Med den här processen kan du också acceptera villkoren i avbildningen i det sista steget.

>[!NOTE]
>De här stegen gör att det inte går att aktivera RHEL BYOS-avbildningen för program distribution – ytterligare ett steg krävs enligt beskrivningen i avsnittet "Ytterligare information" nedan.

Resten av det här dokumentet fokuserar på CLI-metoden för att etablera och godkänna villkoren på avbildningen. Användar gränssnittet och CLI är helt utbytbara så långt som det slutliga resultatet (en etablerad RHEL BYOS VM) berörs.

## <a name="use-the-rhel-byos-images-from-the-azure-cli"></a>Använda RHEL BYOS-avbildningar från Azure CLI
I följande uppsättning instruktioner går vi igenom den första distributions processen för en virtuell RHEL-dator med hjälp av Azure CLI. Dessa instruktioner förutsätter att du har [installerat Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Se till att du använder alla små bokstäver i referenserna utgivare, erbjudande, plan och bild för alla följande kommandon

1. Kontrol lera att du har den önskade prenumerationen:
    ```azurecli
    az account show -o=json
    ```

1. Skapa en resurs grupp för din RHEL BYOS-VM:
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Acceptera avbildnings villkoren:
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >Dessa villkor måste godkännas *en gång per Azure-prenumeration, per avbildnings-SKU*.

1. Valfritt Verifiera distributionen av virtuella datorer med följande kommando:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Etablera den virtuella datorn genom att köra samma kommando som ovan utan argumentet `--validate`:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH till den virtuella datorn och kontrol lera att du har en berättigad avbildning. Det gör du genom att köra `sudo yum repolist`. I utmatningen uppmanas du att använda prenumerations hanteraren för att registrera den virtuella datorn med Red Hat.

## <a name="additional-information"></a>Ytterligare information
- Om du försöker etablera en virtuell dator för en prenumeration som inte är aktive rad för det här erbjudandet får du följande fel meddelande och du bör kontakta Microsoft eller Red Hat för att aktivera din prenumeration.
    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

- Om du skapar en ögonblicks bild från RHEL BYOS-avbildningen och publicerar avbildningen i det [delade bild galleriet](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)måste du ange plan information som matchar den ursprungliga källan till ögonblicks bilden. Kommandot kan till exempel se ut så (Observera plan parametrarna på den sista raden):
    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

- Om du använder Automation för att etablera virtuella datorer från RHEL BYOS-avbildningar måste du ange plan parametrar som liknar vad som visades ovan. Om du till exempel använder terraform kan du ange plan informationen i ett [plan block](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Nästa steg
* Läs mer om [Azure Red Hats uppdaterings infrastruktur](./redhat-rhui.md).
* Om du vill veta mer om Red Hat-bilderna i Azure går du till [dokumentations sidan](./redhat-images.md).
* Information om Red Hat support-principer för alla versioner av RHEL finns på sidan [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata) .