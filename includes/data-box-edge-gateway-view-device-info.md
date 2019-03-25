---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58404096"
---
1. [Ansluta till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Använd den `Get-HcsApplianceInfo` att hämta information för din enhet.

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

    Här är en tabell som sammanställer några av de viktiga enhetsinformationen:
    
    | Parameter                             | Beskrivning                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | Det egna namnet på enheten som konfigurerats via det lokala webbgränssnittet under distributionen av enheten. Det egna namnet för standard är enhetens serienummer.  |   |
    | serialNumber                   | Enhetens serienummer är ett unikt nummer som tilldelas på fabriken.                                                                             |   |
    | Modell                          | Modell för din Data Box Edge eller Data Box-Gateway-enhet. Modellen är virtuella för Data Box-Gateway och fysiska för Data Box Edge.                   |   |
    | FriendlySoftwareVersion        | Eget strängen som motsvarar enhetens programvaruversion. För ett system som kör preview är eget programvaruversionen Data Box Edge 1902. |   |
    | HcsVersion                     | Programvaruversion HCS som körs på din enhet. Exempelvis är HCS programvaruversionen för Data Box Edge 1902 1.4.771.324.            |   |
    | LocalCapacityInMb              | Totalt antal lokal kapacitet för enheten i megabit.                                                                                                        |   |
    | IsRegistered                   | Det här värdet anger om enheten har aktiverats med tjänsten.                                                                                         |   |


