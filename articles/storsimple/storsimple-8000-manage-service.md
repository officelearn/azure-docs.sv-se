---
title: Distribuera Tjänsten StorSimple Device Manager i Azure | Microsoft-dokument
description: I artikeln beskrivs hur du skapar och tar bort Tjänsten StorSimple Device Manager i Azure-portalen och beskriver hur du hanterar nyckeln till tjänstregistrering.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 1e75acc03209fdd7e613801c9152f24aaecfa6de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267786"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Distribuera StorSimple Device Manager-tjänsten för StorSimple 8000-serieenheter

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt

Tjänsten StorSimple Device Manager körs i Microsoft Azure och ansluter till flera StorSimple-enheter. När du har skapat tjänsten kan du använda den för att hantera alla enheter som är anslutna till Tjänsten StorSimple Device Manager från en enda central plats och därigenom minimera den administrativa bördan.

I den här självstudien beskrivs de steg som krävs för att skapa, ta bort, migrera tjänsten och hanteringen av tjänstregistreringsnyckeln. Informationen i den här artikeln gäller endast storsimam 8000-seriens enheter. Mer information om StorSimple-virtuella matriser finns i [distribuera en StorSimple Enhetshanteraren-tjänst för den virtuella storsimple-matrisen](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  Azure-portalen stöder enheter som kör Uppdatering 5.0 eller senare. Om enheten inte är uppdaterad installerar du uppdatering 5 omedelbart. Mer information finns i [Installera uppdatering 5](storsimple-8000-install-update-5.md). 
> - Om du använder en StorSimple Cloud Appliance (8010/8020) kan du inte uppdatera en molninstallation. Använd den senaste versionen av programvaran för att skapa en ny molninstallation med uppdatering 5.0 och växla sedan över till den nya molninstallationen som skapats. 
> - Alla enheter som kör Uppdatering 4.0 eller tidigare kommer att få nedsatt hanteringsfunktioner. 

## <a name="create-a-service"></a>Skapa en tjänst
Om du vill skapa en StorSimple Enhetshanteraren-tjänst måste du ha:

* En prenumeration med ett enterprise-avtal
* Ett aktivt Microsoft Azure-lagringskonto
* Faktureringsinformationen som används för åtkomsthantering

Endast prenumerationer med ett enterprise-avtal är tillåtna. Du kan också välja att generera ett standardlagringskonto när du skapar tjänsten.

En enda tjänst kan hantera flera enheter. En enhet kan dock inte sträcka sig över flera tjänster. Ett stort företag kan ha flera tjänstinstanser för att arbeta med olika prenumerationer, organisationer eller till och med distributionsplatser. 

> [!NOTE]
> Du behöver separata instanser av StorSimple Device Manager-tjänsten för att hantera StorSimple 8000-serieenheter och StorSimple Virtual Arrays.

Gör följande för att skapa en tjänst.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


För varje StorSimple Enhetshanterarens tjänst finns följande attribut:

* **Namn** – Namnet som tilldelades tjänsten StorSimple Device Manager när den skapades. **Det går inte att ändra tjänstnamnet när tjänsten har skapats. Detta gäller även för andra entiteter, till exempel enheter, volymer, volymbehållare och principer för säkerhetskopiering som inte kan döpas om i Azure-portalen.**
* **Status** – Tjänstens status, som kan vara **Aktiv,** **Skapa**eller **Online**.
* **Plats** – Den geografiska plats där StorSimple-enheten ska distribueras.
* **Prenumeration** – Faktureringsprenumerationen som är kopplad till din tjänst.

## <a name="delete-a-service"></a>Ta bort en tjänst

Innan du tar bort en tjänst kontrollerar du att inga anslutna enheter använder den. Om tjänsten används inaktiverar du de anslutna enheterna. Avaktiveringsåtgärden avbryter anslutningen mellan enheten och tjänsten, men bevarar enhetsdata i molnet.

> [!IMPORTANT]
> När en tjänst har tagits bort kan åtgärden inte återföras. Alla enheter som använde tjänsten måste återställas till fabriksinställningar innan de kan användas med en annan tjänst. I det här fallet går de lokala data på enheten, liksom konfigurationen, förlorade.

Gör följande för att ta bort en tjänst.

### <a name="to-delete-a-service"></a>Så här tar du bort en tjänst

1. Sök efter den tjänst som du vill ta bort. Klicka på **Ikonen Resurser** och ange sedan lämpliga termer för att söka. Klicka på den tjänst som du vill ta bort i sökresultaten.

    ![Söktjänst som ska tas bort](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Detta tar dig till StorSimple Device Manager serviceblad. Klicka på **Ta bort**.

    ![Ta bort tjänsten](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Klicka på **Ja** i bekräftelsemeddelandet. Det kan ta några minuter innan tjänsten tas bort.

    ![Bekräfta borttagning](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Hämta nyckel för tjänstregistrering

När du har skapat en tjänst måste du registrera din StorSimple-enhet med tjänsten. För att registrera din första StorSimple-enhet behöver du tjänstens registreringsnyckel. Om du vill registrera ytterligare enheter med en befintlig StorSimple-tjänst behöver du både registreringsnyckeln och krypteringsnyckeln för tjänstdata (som genereras på den första enheten under registreringen). Mer information om krypteringsnyckeln för tjänstdata finns i [StorSimple security](storsimple-8000-security.md). Du kan få registreringsnyckeln genom att komma åt **Nycklar** på bladet StorSimple Device Manager.

Utför följande steg för att hämta nyckeln för tjänstregistrering.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Förvara tjänstens registreringsnyckel på en säker plats. Du behöver den här nyckeln, liksom krypteringsnyckeln för tjänstdata, för att registrera ytterligare enheter med den här tjänsten. När du har hämtat tjänstregistreringsnyckeln måste du konfigurera enheten via Windows PowerShell för StorSimple-gränssnittet.

Mer information om hur du använder den här registreringsnyckeln finns i [Steg 3: Konfigurera och registrera enheten via Windows PowerShell för StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Återskapa nyckeln för tjänstregistrering
Du måste återskapa en tjänstregistreringsnyckel om du måste utföra nyckelrotation eller om listan över tjänstadministratörer har ändrats. När du återskapar nyckeln används den nya nyckeln endast för att registrera efterföljande enheter. De enheter som redan har registrerats påverkas inte av den här processen.

Utför följande steg för att återskapa en tjänstregistreringsnyckel.

### <a name="to-regenerate-the-service-registration-key"></a>Så här återskapar du tjänstregistreringsnyckeln
1. Gå till **Hanteringsnycklar**i **bladet StorSimple** **Enhetshanteraren &gt; ** .
    
    ![Bladet Nycklar](./media/storsimple-8000-manage-service/regenregkey2.png)

2. Klicka på **Återskapa**i bladet **Nycklar.**

    ![Klicka på återskapa](./media/storsimple-8000-manage-service/regenregkey3.png)
3. I **bladet Regenerera tjänstregistreringsnyckel** granskar du den åtgärd som krävs när nycklarna återskapas. Alla efterföljande enheter som är registrerade hos den här tjänsten använder den nya registreringsnyckeln. Klicka på **Återskapa** för att bekräfta. Du meddelas när regenereringen är klar.

    ![Bekräfta regenerering](./media/storsimple-8000-manage-service/regenregkey4.png)

4. En ny tjänstregistreringsnyckel visas.

5. Kopiera den här nyckeln och spara den för att registrera nya enheter med den här tjänsten.



## <a name="change-the-service-data-encryption-key"></a>Ändra krypteringsnyckeln för tjänstdata
Krypteringsnycklar för tjänstdata används för att kryptera konfidentiella kunddata, till exempel autentiseringsuppgifter för lagringskonto, som skickas från StorSimple Manager-tjänsten till StorSimple-enheten. Du måste ändra dessa nycklar regelbundet om IT-organisationen har en princip för nyckelrotation på lagringsenheterna. Nyckelförändringsprocessen kan vara något annorlunda beroende på om det finns en enda enhet eller flera enheter som hanteras av StorSimple Manager-tjänsten. Mer information finns i [StorSimples säkerhet och dataskydd](storsimple-8000-security.md).

Att ändra krypteringsnyckeln för tjänstdata är en trestegsprocess:

1. Med Windows PowerShell-skript för Azure Resource Manager godkänner du en enhet som ändrar krypteringsnyckeln för tjänstdata.
2. Med Windows PowerShell för StorSimple initierar du ändringen av tjänstdatakrypteringsnyckeln.
3. Om du har mer än en StorSimple-enhet uppdaterar du krypteringsnyckeln för tjänstdata på de andra enheterna.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Steg 1: Använda Windows PowerShell-skript för att auktorisera en enhet för att ändra krypteringsnyckeln för tjänstdata
Vanligtvis begär enhetsadministratören att tjänstadministratören auktoriserar en enhet för att ändra krypteringsnycklar för tjänstdata. Tjänstadministratören auktoriserar sedan enheten för att ändra nyckeln.

Det här steget utförs med det Azure Resource Manager-baserade skriptet. Tjänstadministratören kan välja en enhet som är kvalificerad att auktoriseras. Enheten har sedan behörighet att starta processen för ändring av tjänstdatakrypteringsnyckeln. 

Mer information om hur du använder skriptet finns i [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Vilka enheter kan auktoriseras för att ändra krypteringsnycklar för tjänstdata?
En enhet måste uppfylla följande kriterier innan den kan auktas för att initiera ändringar av krypteringsnyckel för tjänstdata:

* Enheten måste vara online för att vara berättigad till ändringsauktorisering för tjänstdatakrypteringsnyckel.
* Du kan auktorisera samma enhet igen efter 30 minuter om nyckeländringen inte har initierats.
* Du kan auktorisera en annan enhet, förutsatt att nyckeländringen inte har initierats av den tidigare auktoriserade enheten. När den nya enheten har auktoriserats kan den gamla enheten inte initiera ändringen.
* Du kan inte auktorisera en enhet medan överrullningen av krypteringsnyckeln för tjänstdata pågår.
* Du kan auktorisera en enhet när vissa av de enheter som är registrerade med tjänsten har rullat över krypteringen medan andra inte har det. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Steg 2: Använd Windows PowerShell för StorSimple för att initiera ändringen av nyckeln för tjänstdatakryptering
Det här steget utförs i Windows PowerShell för StorSimple-gränssnittet på den auktoriserade StorSimple-enheten.

> [!NOTE]
> Inga åtgärder kan utföras i Azure-portalen för din StorSimple Manager-tjänst tills nyckelförtrollningen är klar.


Om du använder den seriella konsolen för enheten för att ansluta till Windows PowerShell-gränssnittet utför du följande steg.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Så här initierar du ändringen av tjänstdatakrypteringsnyckeln
1. Välj alternativ 1 för att logga in med full åtkomst.
2. Skriv följande i kommandotolken:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. När cmdleten har slutförts får du en ny krypteringsnyckel för tjänstdata. Kopiera och spara den här nyckeln för användning i steg 3 i den här processen. Den här nyckeln används för att uppdatera alla återstående enheter som är registrerade med StorSimple Manager-tjänsten.
   
   > [!NOTE]
   > Den här processen måste initieras inom fyra timmar efter att en StorSimple-enhet har godkänts.
   > 
   > 
   
   Den här nya nyckeln skickas sedan till tjänsten för att skickas till alla enheter som är registrerade hos tjänsten. En avisering visas sedan på tjänstinstrumentpanelen. Tjänsten inaktiverar alla åtgärder på de registrerade enheterna och enhetsadministratören måste sedan uppdatera krypteringsnyckeln för tjänstdata på de andra enheterna. I/O (värdar som skickar data till molnet) kommer dock inte att störas.
   
   Om du har en enda enhet registrerad på din tjänst är överrullningsprocessen nu klar och du kan hoppa över nästa steg. Om du har flera enheter registrerade på din tjänst går du vidare till steg 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Steg 3: Uppdatera krypteringsnyckeln för tjänstdata på andra StorSimple-enheter
Dessa steg måste utföras i Windows PowerShell-gränssnittet på Din StorSimple-enhet om du har flera enheter registrerade i Tjänsten StorSimple Manager. Nyckeln som du fick i steg 2 måste användas för att uppdatera alla återstående StorSimple-enheter som registrerats med StorSimple Manager-tjänsten.

Utför följande steg för att uppdatera krypteringen av tjänstdata på enheten.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Så här uppdaterar du krypteringsnyckeln för tjänstdata på fysiska enheter
1. Använd Windows PowerShell för StorSimple för att ansluta till konsolen. Välj alternativ 1 för att logga in med full åtkomst.
2. Skriv i kommandotolken:`Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Ange den krypteringsnyckel för tjänstdata som du erhöll i [steg 2: Använd Windows PowerShell för StorSimple för att initiera ändringen av tjänstdatakrypteringsnyckeln](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Så här uppdaterar du krypteringsnyckeln för tjänstdata på alla 8010/8020-molninstallationer
1. Ladda ned och konfigurera [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) PowerShell-skriptet. 
2. Öppna PowerShell och skriv i kommandotolken:`Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Det här skriptet säkerställer att krypteringsnyckeln för tjänstdata är inställd på alla 8010/8020-molninstallationer under enhetshanteraren.

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Åtgärder som stöds på enheter som kör versioner före uppdatering 5.0
I Azure-portalen stöds endast StorSimple-enheter som kör Uppdatering 5.0 och högre. De enheter som kör äldre versioner har begränsat stöd. När du har migrerat till Azure-portalen använder du följande tabell för att förstå vilka åtgärder som stöds på enheter som kör versioner före uppdatering 5.0.

| Åtgärd                                                                                                                       | Stöds      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Registrera en enhet                                                                                                               | Ja            |
| Konfigurera enhetsinställningar som allmänt, nätverk och säkerhet                                                                | Ja            |
| Skanna, hämta och installera uppdateringar                                                                                             | Ja            |
| Inaktivera enhet                                                                                                               | Ja            |
| Ta bort enhet                                                                                                                   | Ja            |
| Skapa, ändra och ta bort en volymbehållare                                                                                   | Inga             |
| Skapa, ändra och ta bort en volym                                                                                             | Inga             |
| Skapa, ändra och ta bort en princip för säkerhetskopiering                                                                                      | Inga             |
| Gör en manuell säkerhetskopiering                                                                                                            | Inga             |
| Ta en schemalagd säkerhetskopia                                                                                                         | Inte tillämpligt |
| Återställa från en säkerhetskopia                                                                                                        | Inga             |
| Klona till en enhet som kör Uppdatering 3.0 och senare <br> Källenheten kör version före uppdatering 3.0.                                | Ja            |
| Klona till en enhet som kör versioner före uppdatering 3.0                                                                          | Inga             |
| Redundans som källenhet <br> (från en enhet som kör version före uppdatering 3.0 till en enhet som kör Uppdatering 3.0 och senare)                                                               | Ja            |
| Redundans som målenhet <br> (till en programvaruversion som körs före uppdatering 3.0)                                                                                   | Inga             |
| Rensa en avisering                                                                                                                  | Ja            |
| Visa principer för säkerhetskopiering, säkerhetskopieringskatalog, volymer, volymbehållare, övervakningsdiagram, jobb och aviseringar som skapats i klassisk portal | Ja            |
| Aktivera och stänga av enhetsstyrenheter                                                                                              | Ja            |


## <a name="next-steps"></a>Nästa steg
* Läs mer om [StorSimple-distributionsprocessen](storsimple-8000-deployment-walkthrough-u2.md).
* Läs mer om [hur du hanterar ditt StorSimple-lagringskonto](storsimple-8000-manage-storage-accounts.md).
* Läs mer om hur du [använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).
