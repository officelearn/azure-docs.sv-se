---
title: "Flytta från klassiska Azure-portalen vanliga frågor och svar | Microsoft Docs"
description: "Innehåller svar på vanliga frågor om hur du flyttar StorSimple-enheter från klassiska Azure-portalen."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: ac57894e4f180f42f80479d2031f4dd5ddfdb1be
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="move-storsimple-device-manager-service-from-classic-to-azure-portal-frequently-asked-questions-faq"></a>Flytta StorSimple enheten Manager-tjänsten från klassiska Azure-portalen: vanliga frågor (FAQ)

## <a name="overview"></a>Översikt

Följande är frågor och svar som kan uppstå när du flyttar din StorSimple Device Manager-tjänsten som körs på klassiska Azure-portalen på Azure-portalen.

Frågor och svar ordnas i följande kategorier:

* Flytta StorSimple enheten Manager-tjänsten
* Flytta storage-konton
* Med Azure Resource Manager-baserade cmdletar
* Med hjälp av StorSimple Data Manager-tjänsten
* Övrigt

## <a name="moving-storsimple-device-manager-service"></a>Flytta StorSimple enheten Manager-tjänsten

### <a name="once-i-have-moved-to-azure-portal-can-i-still-create-a-storsimple-manager-service-in-the-classic-portal"></a>En gång I flyttats till Azure-portalen kan jag fortfarande skapa StorSimple Manager-tjänsten i den klassiska portalen?

Nej. När du har migrerat StorSimple Manager-tjänsten till Azure-portalen, kan du inte skapa en ny tjänst i den klassiska portalen. Dessutom [den klassiska portalen är inte tillgängligt från 8 januari 2018](https://azure.microsoft.com/updates/azure-portal-updates-for-classic-portal-users). 

### <a name="i-have-multiple-storsimple-managers-running-in-the-classic-portal-can-i-choose-which-ones-to-move-to-the-azure-portal"></a>Jag har flera StorSimple chefer som körs i den klassiska portalen. Kan jag välja vilka som ska flyttas till Azure portal?

Nej. Du kan inte välja vilka StorSimple-chefer att flytta till Azure-portalen. Alla StorSimple chefer under din prenumeration flyttas.


### <a name="i-initiated-the-migration-of-my-service-to-the-new-azure-portal-should-i-delete-the-storsimple-manager-from-the-classic-portal"></a>Jag påbörja migreringen av min-tjänsten för den nya Azure-portalen. Bör jag bort StorSimple Manager från den klassiska portalen? 

Nej. Försök inte att ta bort alla tjänster som du har flyttat från den klassiska portalen. Vänta tills migrering ska kunna slutföras, när alla StorSimple chefer har flyttat till den nya portalen kan du behöver inte ta bort några tjänster från den klassiska portalen. Slutligen är den klassiska portalen föråldrad.

### <a name="can-i-rename-my-storsimple-device-manager-service-in-the-azure-portal"></a>Kan jag byta namn på StorSimple Device Manager-tjänsten i Azure portal?

Nej. Tjänstens namn kan inte ändras när tjänsten har skapats i Azure-portalen. Samma beteende gäller även för andra entiteter, till exempel enheter, volymer, volymbehållare och principer för säkerhetskopiering.

### <a name="can-i-create-the-80108020-storsimple-cloud-appliances-with-azure-resource-manager-deployment-model"></a>Kan jag skapa 8010/8020 StorSimple moln installationer med Azure Resource Manager-modellen?

Ja. Du kan skapa nya 8010/8020 StorSimple moln installationer i Azure Resource Manager-distributionsmodellen. De underliggande virtuella datorerna för molnet hushållsapparater finns också i Resource Manager-distributionsmodellen.

### <a name="when-we-migrate-subscriptions-to-the-azure-portal-will-the-underlying-vms-for-the-storsimple-cloud-appliances-also-migrate-to-azure-resource-management-deployment-model"></a>När vi migrerar prenumerationer till Azure portal underliggande VMs för StorSimple moln apparater också migreras till Azure Resource Manager-distributionsmodellen?

Flytta för StorSimple-tjänsten är oberoende av hanteringen av de virtuella datorerna för StorSimple moln installationer. Du kan helt hantera de virtuella datorerna för StorSimple moln installationer i både klassiskt och Azure portal även idag.

### <a name="can-i-manage-existing-classic-80108020-storsimple-cloud-appliance-from-the-azure-portal"></a>Kan jag hantera befintliga klassiska 8010/8020 StorSimple moln installation från Azure portal?

Ja. De virtuella datorerna som är associerad med befintliga installationer av 8010/8020 moln kan hanteras från Azure-portalen.

Om du har skapat StorSimple moln installationer modellen 8010/8020 kör Update 3.0 och senare, du påverkas inte av tjänsten flytta till den nya Azure-portalen. Du bör kunna hantera dina molntjänster installationer utan problem. 

Om du har molnet enheter som kör versioner före uppdateringen 3.0 i den klassiska portalen, sedan du endast har begränsad funktionalitet som är tillgängliga. Mer information finns i [lista över åtgärder som inte stöds för enheter som kör tidigare versioner än uppdatering 3](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50).

Du kan inte uppdatera en molninstallation. Använd den senaste versionen av programvaran för att skapa en ny installation av molnet och växlar sedan över befintliga volymbehållarna till nya molntjänster enhet skapas. Mer information finns på [växla över till molnet-enhet](storsimple-8000-cloud-appliance-u2.md#fail-over-to-the-cloud-appliance)


### <a name="my-storsimple-8000-series-device-is-running-update-20-i-migrated-my-service-to-new-azure-portal-my-device-connected-successfully-but-it-seems-that-i-am-not-able-to-fully-manage-my-device-how-do-i-resolve-this-behavior"></a>Enheten StorSimple 8000-serien med uppdatering 2.0. Jag migrerade min tjänst till nya Azure-portalen. Min enhet ansluten men det verkar som att jag inte kan för att hantera enheten. Hur löser problemet?

Den nya Azure-portalen är stöds endast för StorSimple-enheter som kör uppdatering 3.0 och senare. Om enheten körs Update 2.0 bör har du endast begränsad funktioner som är tillgängliga för den här enheten. Mer information finns i [lista över åtgärder som inte stöds för enheter som kör tidigare versioner än uppdatering 3](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50).

Om du vill hantera din enhet måste du installera den senaste uppdateringen på enheten. Mer information finns på [installera uppdatering 5](storsimple-8000-install-update-5.md).

### <a name="i-just-moved-my-storsimple-manager-service-to-the-azure-portal-i-am-seeing-some-alerts-related-to-my-device-is-this-behavior-related-to-the-move"></a>Jag flytta bara StorSimple Manager-tjänsten till Azure-portalen. Jag får vissa aviseringar relaterade till enheten. Det här problemet rör flyttningen?

Nej. Flytta själva bör inte resultera i fel eller varningar. Följ avisering rekommendationerna för att åtgärda aviseringar.

### <a name="i-am-using-a-storsimple-50007000-series-device-are-these-also-supported-in-the-azure-portal"></a>Jag använder en serieenhet för StorSimple 5000/7000. Dessa stöds också i Azure portal?

Nej. StorSimple 5000/7000-serien enheter stöds inte i Azure-portalen.

### <a name="i-am-planning-to-migrate-data-from-storsimple-50007000-series-device-to-a-storsimple-8000-series-device-how-does-moving-a-service-to-azure-portal-impact-my-data-migration"></a>Jag planerar att migrera data från StorSimple 5000/7000-serieenhet till en enhet för StorSimple 8000-serien. Hur du flyttar en tjänst till Azure-portalen påverkar min datamigrering? 

Du kan migrera data från enheten StorSimple 5000/7000-serien till en enhet för StorSimple 8000-serien som körs i Azure-portalen. Om du vill aktivera du för migrering av data från 5000/7000-serien 8000-serien, måste du [logga ett supportärende med Microsoft-supporten](storsimple-8000-contact-microsoft-support.md).


## <a name="moving-subscriptions-storage-accounts-resource-groups"></a>Flytta prenumerationer, storage-konton, resursgrupper

### <a name="can-i-move-storsimple-device-manager-from-one-subscription-to-another-subscription-in-the-azure-portal"></a>Kan jag flytta StorSimple Enhetshanteraren från en prenumeration till en annan prenumeration i Azure portal?

Nej. Det går inte att flytta StorSimple enheten Manager-tjänsten från en prenumeration till en annan. Du kan utföra en manuell process som består av följande steg:

* Migrera data från StorSimple-enhet.
* Utför en fabriksåterställning på enheten kan detta tar bort alla lokala data på enheten.
* Registrera enheten med den nya prenumerationen till en StorSimple Device Manager-tjänst.
* Migrera data till enheten.

### <a name="do-i-have-to-migrate-the-storage-account-to-azure-resource-manager-deployment-model"></a>Måste jag migrera lagringskontot till Azure Resource Manager-modellen?

Nej. Ditt klassiska lagringskonton kan hanteras helt från Azure-portalen. Om du flyttar din StorSimple-tjänsten till Azure portal, fortsätter ditt lagringskonto att fungera som tidigare.

### <a name="what-happens-to-the-storage-account-after-the-service-is-migrated-to-the-azure-portal"></a>Vad händer med lagringskontot efter att tjänsten har migrerats till Azure portal?

Flytt av tjänsten är inte relaterat till hantering av storage-konto. Både klassiska och Azure Resource Manager storage-konton kan hanteras helt i Azure-portalen. När du flyttar din tjänst till Azure portal enheten ska fortsätta att köra med det här lagringskontot och det bör vara ingen inverkan på dina data.

### <a name="can-i-migrate-storsimple-device-manager-from-one-resource-group-to-another"></a>Kan jag migrera StorSimple Enhetshanteraren från en resursgrupp till en annan?

Nej. Du kan inte flytta en StorSimple Device Manager skapats med en resursgrupp till en annan resursgrupp.

## <a name="using-azure-resource-manager-based-cmdlets"></a>Med Azure Resource Manager-baserade cmdletar

### <a name="i-moved-to-the-new-azure-portal-now-my-scripts-based-on-azure-classic-deployment-model-powershell-cmdlets-are-not-working-what-do-i-need-to-do"></a>Jag har flyttats till den nya Azure-portalen. Nu fungerar inte mitt Azure klassisk distribution modellen PowerShell-cmdlet-baserade skript? Vad behöver jag göra?

De befintliga Azure klassisk distribution modellen PowerShell-cmdletarna stöds inte i Azure-portalen. Uppdatera skript för att hantera dina enheter via Azure Resource Manager. Mer information om hur du uppdaterar skripten går du till [prover för den nya Azure portalen](https://github.com/anoobbacker/storsimpledevicemgmttools).

### <a name="i-just-moved-to-the-azure-portal-and-needed-to-roll-over-the-service-data-encryption-key-where-is-this-option-in-the-azure-portal"></a>Jag kan bara flyttas till Azure-portalen och behövs för att rulla över krypteringsnyckel för tjänstdata. Var finns det här alternativet i Azure portal?

Alternativet för att rulla över krypteringsnyckel för tjänstdata är inte i Azure-portalen. Mer information om hur du gör detta förnyelse i Azure-portalen går du till [ändra krypteringsnyckel för tjänstdata](storsimple-8000-manage-service.md#change-the-service-data-encryption-key).

### <a name="i-am-using-windows-powershell-for-storsimple-cmdlets-on-the-storsimple-device-to-perform-operations-such-extract-a-support-package-are-these-cmdlets-affected-when-i-move-to-the-new-azure-portal"></a>Jag använder Windows PowerShell för StorSimple-cmdlets på StorSimple-enheten och utföra åtgärder sådana Extrahera ett supportpaket. Dessa cmdletar som påverkas när jag flytta till den nya Azure portalen?

Nej. Med din tjänst som flyttar till nya Azure-portalen, skall ingen inverkan på Windows PowerShell för StorSimple-cmdlets som är associerade med lokala StorSimple-enhet (som i sin tur påverkas inte av flyttningen). Du kan fortsätta att använda dessa cmdletar för att skapa ett supportpaket utan problem även i Azure-portalen. Endast de klassiska Azure-distribution modellen PowerShell-cmdletarna som påverkas av flyttningen.

## <a name="moving-storsimple-data-manager-service"></a>Flytta StorSimple Data Manager-tjänsten

### <a name="i-am-using-storsimple-data-manager-service-in-classic-azure-portal-how-should-i-proceed-with-this-move"></a>Jag använder StorSimple Data Manager-tjänsten i klassiska Azure-portalen. Hur ska fortsätta med den här flytta?

Om du använder StorSimple Data Manager-tjänsten kan flyttats du automatiskt till Azure-portalen.

## <a name="miscellaneous"></a>Övrigt

### <a name="i-am-running-storsimple-snapshot-manager-for-my-8000-series-device-is-there-any-impact-on-storsimple-snapshot-manager-when-i-move-to-azure-portal"></a>Jag kör StorSimple Snapshot Manager för enheten i 8000-serien. Finns det någon effekt på StorSimple Snapshot Manager när jag flyttar till Azure-portalen?

Nej. Det finns ingen inverkan på StorSimple Snapshot Manager när du flyttar din tjänst till Azure-portalen. Din enhet och StorSimple Snapshot Manager fortsätter att fungera som tidigare.

### <a name="can-i-rename-my-storsimple-device-volume-containers-or-volumes"></a>Kan jag byta namn på min StorSimple-enhet, volymbehållare eller volymer?

Nej. Du kan byta namn på enheter, volymer, volymbehållare eller principer för säkerhetskopiering i Azure-portalen.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [stöds åtgärder på enheter som kör versioner före uppdateringen 5.0](storsimple-8000-manage-service.md#supported-operations-on-devices-running-versions-prior-to-update-50).



