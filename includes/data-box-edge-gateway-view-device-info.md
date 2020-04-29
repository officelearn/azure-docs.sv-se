---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "67187803"
---
1. [Anslut till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Använd `Get-HcsApplianceInfo` för att hämta information om enheten.

    I följande exempel visas användningen av denna cmdlet:

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    Här är en tabell som sammanfattar en del av viktig enhets information:
    
    | Parameter                             | Beskrivning                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | Det egna namnet på enheten enligt konfigurationen via det lokala webb gränssnittet under enhets distributionen. Det egna standard namnet är enhetens serie nummer.  |   |
    | Serienummer                   | Enhetens serie nummer är ett unikt nummer som tilldelas på fabriken.                                                                             |   |
    | Modell                          | Modellen för din Data Box Edge-eller Data Box Gateways enhet. Modellen är virtuell för Data Box Gateway och fysisk för Data Box Edge.                   |   |
    | FriendlySoftwareVersion        | Den egna strängen som motsvarar enhetens program varu version. För ett system som kör för hands version blir den egna program varu versionen Data Box Edge 1902. |   |
    | HcsVersion                     | HCS UPPDATERINGSKLIENTEN program varu version som körs på enheten. Till exempel är HCS UPPDATERINGSKLIENTEN-programvaran som motsvarar Data Box Edge 1902 1.4.771.324.            |   |
    | LocalCapacityInMb              | Enhetens totala lokala kapacitet i megabit.                                                                                                        |   |
    | IsRegistered                   | Det här värdet anger om enheten är aktive rad med tjänsten.                                                                                         |   |


