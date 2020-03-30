---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187803"
---
1. [Anslut till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Använd `Get-HcsApplianceInfo` för att få information för din enhet.

    I följande exempel visas användningen av den här cmdleten:

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

    Här är en tabell som sammanfattar några av de viktiga enhetsinformation:
    
    | Parameter                             | Beskrivning                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | Det egna namnet på enheten som konfigurerats via det lokala webbgränssnittet under enhetsdistributionen. Standardnamnet är enhetens serienummer.  |   |
    | Serienummer                   | Enhetens serienummer är ett unikt nummer som tilldelats på fabriken.                                                                             |   |
    | Modell                          | Modellen för enheten Data Box Edge eller Data Box Gateway. Modellen är virtuell för Data Box Gateway och fysisk för Data Box Edge.                   |   |
    | FriendlySoftwareVersion        | Den vänliga strängen som motsvarar enhetens programvaruversion. För ett system som kör förhandsversion, skulle den vänliga programvaruversionen vara Data Box Edge 1902. |   |
    | HcsVersion (HcsVersion)                     | HCS-programvaruversionen som körs på enheten. Till exempel är HCS-programvaruversionen som motsvarar Data Box Edge 1902 1.4.771.324.            |   |
    | LocalCapacityInMb              | Den totala lokala kapaciteten för enheten i Megabits.                                                                                                        |   |
    | Ärregistrerad                   | Det här värdet anger om enheten är aktiverad med tjänsten.                                                                                         |   |


