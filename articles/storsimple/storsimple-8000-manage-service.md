---
title: Distribuera StorSimple Device Manager-tjänsten i Azure | Microsoft Docs
description: Beskriver hur du skapar och tar bort StorSimple Device Manager-tjänsten i Azure-portalen och beskriver hur du hanterar Registreringsnyckeln för tjänsten.
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
ms.openlocfilehash: eb1fe69a7fb99949ac95291c33e76c1a32bf5439
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310065"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Distribuera StorSimple Device Manager-tjänsten för enheter i StorSimple 8000-serien

## <a name="overview"></a>Översikt

StorSimple Device Manager-tjänsten körs i Microsoft Azure och ansluter till flera StorSimple-enheter. När du har skapat tjänsten kan använda du den för att hantera alla enheter som är anslutna till StorSimple Device Manager-tjänsten från en gemensam, central plats, vilket minimerar administrativa arbetet.

Den här självstudien beskrivs de steg som krävs för att skapa, ta bort eller migrering av tjänsten och hantering av nyckel för tjänstregistrering. Informationen i den här artikeln gäller endast för enheter i StorSimple 8000-serien. Mer information om StorSimple Virtual Array går du till [distribuera en StorSimple Device Manager-tjänst för StorSimple Virtual Array](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  Azure-portalen stöder enheter som kör uppdatering 5.0 eller senare. Installera uppdatering 5 omedelbart om enheten inte är uppdaterade. Mer information går du till [installera uppdatering 5](storsimple-8000-install-update-5.md). 
> - Om du använder en StorSimple Cloud Appliance (8010/8020) kan inte du uppdatera en molninstallation. Använd den senaste versionen av programvara för att skapa en ny molninstallation med uppdatering 5.0 och sedan växla över till nya molninstallationen skapas. 
> - Alla enheter som kör Update 4.0 eller tidigare uppstår minskade hanteringsfunktioner. 

## <a name="create-a-service"></a>Skapa en tjänst
Om du vill skapa en StorSimple Device Manager-tjänsten, måste du ha:

* En prenumeration med ett Enterprise-avtal
* Ett aktivt Microsoft Azure storage-konto
* Faktureringsinformation som används för access management

Endast prenumerationer med ett Enterprise-avtal är tillåtna. Du kan också välja att generera ett standard storage-konto när du har skapat tjänsten.

En enskild tjänst kan hantera flera enheter. Men kan inte en enhet omfatta flera tjänster. Ett stort företag kan ha flera instanser av tjänsten att arbeta med olika prenumerationer, organisationer eller även distribution platser. 

> [!NOTE]
> Du behöver separata instanser av StorSimple Device Manager-tjänsten för att hantera enheter i StorSimple 8000-serien och StorSimple Virtual Array.

Utför följande steg för att skapa en tjänst.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


För varje StorSimple Device Manager-tjänst finns följande attribut:

* **Namn på** – det namn som har tilldelats till din StorSimple Device Manager-tjänsten när den skapades. **Tjänstens namn kan inte ändras när tjänsten har skapats. Detta gäller även för andra entiteter, till exempel enheter, volymer, volymbehållare och principer för säkerhetskopiering som inte kan i Azure-portalen.**
* **Status för** – status för tjänsten, som kan vara **Active**, **skapa**, eller **Online**.
* **Plats** – den geografiska platsen där StorSimple-enheten ska distribueras.
* **Prenumeration** – faktureringsprenumerationen som är associerad med din tjänst.

## <a name="delete-a-service"></a>Ta bort en tjänst

Innan du tar bort en tjänst kan du kontrollera att inga anslutna enheter använder den. Om tjänsten används, inaktiverar du de anslutna enheterna. Åtgärden inaktivera Server anslutningen mellan enheten och tjänsten, men bevara enhetsdata i molnet.

> [!IMPORTANT]
> När en tjänst har tagits bort, kan inte åtgärden ångras. Alla enheter som med hjälp av tjänsten måste återställas till fabriksinställningarna innan den kan användas med en annan tjänst. I det här scenariot förloras lokala data på enheten, samt konfigurationen.

Utför följande steg för att ta bort en tjänst.

### <a name="to-delete-a-service"></a>Att ta bort en tjänst

1. Sök efter tjänsten som du vill ta bort. Klicka på **resurser** ikonen och ange lämpliga villkoren att söka. I sökresultaten klickar du på tjänsten som du vill ta bort.

    ![Search-tjänsten för att ta bort](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Då kommer du till bladet för StorSimple Device Manager-tjänsten. Klicka på **Ta bort**.

    ![Ta bort tjänst](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Klicka på **Ja** i meddelande om bekräftelse. Det kan ta några minuter för tjänsten som ska tas bort.

    ![Bekräfta borttagning](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Hämta nyckel för tjänstregistrering

När du har skapat en tjänst, kommer du behöva registrera din StorSimple-enhet med tjänsten. Om du vill registrera din första StorSimple-enhet, måste nyckeln för tjänstregistrering. För att registrera ytterligare enheter med en befintlig StorSimple-tjänst, behöver du både Registreringsnyckeln och tjänstdatakrypteringsnyckel (som genereras på den första enheten under registreringen). Läs mer om krypteringsnyckeln för tjänstdata [StorSimple-säkerhet](storsimple-8000-security.md). Du kan hämta nyckel för tjänstregistrering genom att gå till **nycklar** på bladet för StorSimple Device Manager.

Utför följande steg för att hämta Registreringsnyckeln för tjänsten.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Behåll nyckeln för tjänstregistrering på en säker plats. Du behöver den här nyckeln samt tjänstdatakrypteringsnyckel, att registrera ytterligare enheter med den här tjänsten. När du har fått nyckeln för tjänstregistrering, måste du konfigurera din enhet via Windows PowerShell för StorSimple-gränssnittet.

Mer information om hur du använder den här nyckeln för tjänstregistrering finns [steg3: Konfigurera och registrera enheten via Windows PowerShell för StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Återskapa nyckel för tjänstregistrering
Du måste återskapa en nyckel för tjänstregistrering om du behöver utföra nyckelrotation eller om listan över tjänstadministratörer har ändrats. När du återskapar en nyckel används den nya nyckeln endast för att registrera följande enheter. De enheter som redan har registrerats påverkas inte av den här processen.

Utför följande steg för att återskapa en nyckel för tjänstregistrering.

### <a name="to-regenerate-the-service-registration-key"></a>Återskapa nyckel för tjänstregistrering
1. I den **StorSimple Device Manager** gå till bladet **Management &gt;**  **nycklar**.
    
    ![Bladet Nycklar](./media/storsimple-8000-manage-service/regenregkey2.png)

2. I den **nycklar** bladet klickar du på **återskapa**.

    ![Klicka på Återskapa](./media/storsimple-8000-manage-service/regenregkey3.png)
3. I den **återskapa tjänstregistreringsnyckeln** bladet, granska åtgärden krävs när nycklarna har återskapats. Alla efterföljande enheter som registreras med den här tjänsten använder den nya Registreringsnyckeln. Klicka på **återskapa** att bekräfta. Du meddelas när omgenerering är klar.

    ![Bekräfta att återskapa](./media/storsimple-8000-manage-service/regenregkey4.png)

4. En ny nyckel för tjänstregistrering visas.

5. Kopiera den här nyckeln och spara den för att registrera nya enheter med den här tjänsten.



## <a name="change-the-service-data-encryption-key"></a>Ändra krypteringsnyckeln för tjänstdata
Tjänstdatakrypteringsnycklarna används för att kryptera konfidentiell kundinformation, till exempel autentiseringsuppgifter för lagringskonto, som skickas från StorSimple Manager-tjänsten till StorSimple-enheten. Du måste ändra dessa nycklar regelbundet om organisationen har en princip för rotation av lagringsenheter. Viktiga ändringprocessen kan skilja sig beroende på om det finns en enstaka enhet eller flera enheter som hanteras av StorSimple Manager-tjänsten. Mer information går du till [StorSimple säkerhet och dataskydd](storsimple-8000-security.md).

Ändra krypteringsnyckeln för tjänstdata är en process i 3 steg:

1. Använda Windows PowerShell-skript för Azure Resource Manager, auktorisera en enhet för att ändra krypteringsnyckeln för tjänstdata.
2. Med hjälp av Windows PowerShell för StorSimple, initiera data encryption key tjänständringen.
3. Om du har mer än en StorSimple-enhet kan du uppdatera tjänstdatakrypteringsnyckel på andra enheter.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Steg 1: Använda Windows PowerShell-skript för att auktorisera en enhet för att ändra krypteringsnyckeln för tjänstdata
Normalt begär enhetsadministratören att tjänstadministratören auktorisera en enhet för att ändra tjänstdatakrypteringsnycklarna. Tjänstadministratör kommer sedan att auktorisera enheten för att ändra nyckeln.

Det här steget utförs med hjälp av Azure Resource Manager-baserade skript. Tjänstadministratören kan välja en enhet som kan användas för att ha behörighet. Enheten har sedan behörighet att starta tjänsten data krypteringsprocessen ändringen. 

Mer information om hur du använder skriptet går du till [auktorisera ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Vilka enheter kan ha behörighet att ändra tjänstdatakrypteringsnycklarna?
En enhet måste uppfylla följande kriterier innan den kan ha behörighet att initiera tjänsten data encryption key ändras:

* Enheten måste vara online för att vara berättigade till auktoriseringen av tjänsten data encryption ändringen.
* Du kan auktorisera på samma enhet igen efter 30 minuter om viktiga ändringen inte har initierats.
* Du kan auktorisera en annan enhet, förutsatt att viktiga ändringen inte har initierats av tidigare behöriga enheten. När den nya enheten har auktoriserats kan inte gamla enheten initiera ändringen.
* Du kan inte godkänna en enhet när förnyelsen av krypteringsnyckeln för tjänstdata pågår.
* Du kan auktorisera en enhet när några av de enheter som registrerats för tjänsten har perioder krypteringen medan andra inte har. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Steg 2: Använd Windows PowerShell för StorSimple att initiera krypteringsnyckeln för tjänstdata ändra
Det här steget utförs i Windows PowerShell för StorSimple-gränssnittet på behöriga StorSimple-enheten.

> [!NOTE]
> Inga åtgärder kan utföras i Azure-portalen för StorSimple Manager-tjänsten tills nyckelförnyelse har slutförts.


Utför följande steg om du använder enhetens seriekonsol för att ansluta till Windows PowerShell-gränssnittet.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Initiera data encryption key tjänständringen
1. Välj alternativ 1 för att logga in med fullständig åtkomst.
2. Skriv följande i kommandotolken:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. När cmdleten har slutförts får du en ny krypteringsnyckel för tjänstdata. Kopiera och spara den här nyckeln för användning i steg 3 i den här processen. Den här nyckeln används för att uppdatera de återstående enheter som registrerats med StorSimple Manager-tjänsten.
   
   > [!NOTE]
   > Den här processen måste initieras inom fyra timmar för att auktorisera en StorSimple-enhet.
   > 
   > 
   
   Den här nya nyckeln skickas sedan till tjänsten att överföras till alla enheter som registreras med tjänsten. En avisering visas sedan på instrumentpanelen för tjänsten. Tjänsten kommer att inaktivera alla åtgärder på de registrerade enheterna och enhetsadministratören måste du uppdatera krypteringsnyckeln för tjänstdata på andra enheter. I/o (värdar som skickar data till molnet) kommer inte att avbrytas.
   
   Om du har en enda enhet som registrerats till tjänsten förnya processen är nu klar och du kan hoppa över nästa steg. Om du har flera enheter som registrerats till tjänsten kan du gå vidare till steg 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Steg 3: Uppdatera krypteringsnyckeln för tjänstdata på andra StorSimple-enheter
De här stegen måste utföras i Windows PowerShell-gränssnittet för StorSimple-enheten om du har flera enheter som är registrerade till StorSimple Manager-tjänsten. Den nyckel som du fick i steg 2 måste användas för att uppdatera alla återstående StorSimple-enheten registrerad med StorSimple Manager-tjänsten.

Utför följande steg för att uppdatera tjänsten datakryptering på din enhet.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Att uppdatera krypteringsnyckeln för tjänstdata på fysiska enheter
1. Använda Windows PowerShell för StorSimple för att ansluta till konsolen. Välj alternativ 1 för att logga in med fullständig åtkomst.
2. I Kommandotolken skriver du:  `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Ange den krypteringsnyckeln för tjänstdata som du fick i [steg 2: Använda Windows PowerShell för StorSimple för att initiera data encryption key tjänständringen](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Att uppdatera krypteringsnyckeln för tjänstdata på alla de 8010/8020-molninstallationer
1. Ladda ned och konfigurera [uppdatering CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) PowerShell-skript. 
2. Öppna PowerShell och Skriv vid kommandotolken:  `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Det här skriptet säkerställer att krypteringsnyckeln för tjänstdata har angetts för alla de 8010/8020-molninstallationer under Enhetshanteraren.

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Stöd för åtgärder på enheter som kör tidigare versioner än uppdatering 5.0
Endast de StorSimple-enheter som kör uppdatering 5.0 och senare stöds i Azure-portalen. Enheter som kör äldre versioner har begränsat stöd. När du har migrerat till Azure-portalen, Använd följande tabell för att förstå vilka åtgärder som stöds på enheter som kör tidigare versioner än uppdatering 5.0.

| Åtgärd                                                                                                                       | Stöds      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Registrera en enhet                                                                                                               | Ja            |
| Konfigurera inställningar för enheter, till exempel allmänna, nätverk och säkerhet                                                                | Ja            |
| Genomsöka, ladda ned och installera uppdateringar                                                                                             | Ja            |
| Inaktivera enhet                                                                                                               | Ja            |
| Ta bort enhet                                                                                                                   | Ja            |
| Skapa, ändra och ta bort en volymbehållare                                                                                   | Nej             |
| Skapa, ändra och ta bort en volym                                                                                             | Nej             |
| Skapa, ändra och ta bort en princip för säkerhetskopiering                                                                                      | Nej             |
| Gör en manuell säkerhetskopia                                                                                                            | Nej             |
| Ta en schemalagd säkerhetskopiering                                                                                                         | Inte tillämpligt |
| Återställa från en säkerhetskopia                                                                                                        | Nej             |
| Klona till en enhet som kör Update 3.0 och senare <br> Källenheten kör version före uppdatering 3.0.                                | Ja            |
| Klona till en enhet som kör versioner före uppdatering 3.0                                                                          | Nej             |
| Redundans som källenhet <br> (från en enhet som kör version före uppdatering 3.0 till en enhet som kör Update 3.0 och senare)                                                               | Ja            |
| Redundans som målenhet <br> (för att en enhet som kör programvaruversion före uppdatering 3.0)                                                                                   | Nej             |
| Ta bort en avisering                                                                                                                  | Ja            |
| Visa principer för säkerhetskopiering, säkerhetskopieringskatalogen, volymer, volymbehållare, Övervakningsdiagrammen, jobb och aviseringar skapade i den klassiska portalen | Ja            |
| Aktivera eller inaktivera styrenheter                                                                                              | Ja            |


## <a name="next-steps"></a>Nästa steg
* Läs mer om den [StorSimple distributionsprocessen](storsimple-8000-deployment-walkthrough-u2.md).
* Läs mer om [hantera ditt lagringskonto i StorSimple](storsimple-8000-manage-storage-accounts.md).
* Mer information om hur du [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).
