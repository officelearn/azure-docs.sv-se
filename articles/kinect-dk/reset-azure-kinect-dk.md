---
title: Återställa Azure Kinect DK
description: Beskriver hur du återställer en Azure Kinect DK-enhet till fabriksavbildningen
author: Teresa-Motiv
ms.author: v-tea
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/11/2020
ms.topic: how-to
keywords: kinect, reset
ms.custom:
- CI 113492
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: f851579712a9549c88f1ca652aea4f31457da132
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201906"
---
# <a name="reset-azure-kinect-dk"></a>Återställa Azure Kinect DK

Det kan hända att du någon gång behöver återställa Azure Kinect DK till fabriksavbildningen (till exempel om en uppdatering av den inbyggda programvaran inte installeras korrekt).

1. Stäng av Azure Kinect DK. Det gör du genom att dra ur USB-kabeln och strömkabeln.
  ![Ett diagram som visar placeringen av skruven som täcker återställningsknappen.](media/reset-azure-kinect-dk-diagram.png)
1. Du hittar återställningsknappen genom att ta bort skruven i stativmonteringslåset.
1. Anslut strömkabeln igen.
1. För in spetsen på ett uträtat gem i det tomma skruvhålet i stativmonteringslåset.
1. Tryck försiktigt gemet mot återställningsknappen och håll kvar.
1. Anslut USB-kabeln medan nu håller in återställningsknappen.
1. Efter cirka tre sekunder börjar strömindikatorn lysa gult. Släpp återställningsknappen när indikatorn ändrar färg.  
   
   När du har släppt återställningsknappen börjar strömindikatorn blinka med vitt och gult sken medan enheten återställs. 
1. Vänta tills strömindikatorn lyser med vitt fast sken.
1. Sätt tillbaka skruven i stativmonteringslåset, över återställningsknappen.
1. Använd Azure Kinect Viewer för att kontrollera att den inbyggda programvaran har återställts. Det gör du genom att starta [Azure Kinect Viewer](azure-kinect-viewer.md) och välja **Version av inbyggd programvara**. Nu kan du se vilken inbyggda programvara som är installerad på Azure Kinect DK-enheten.

Kontrollera alltid att den senaste inbyggda programvaran är installerad på enheten. Du kan hämta den senaste versionen av den inbyggda programvaran med hjälp av Azure Kinect Firmware Tool. Mer information om hur du kontrollerar statusen för den inbyggda programvaran finns i [Kontrollera versionen av enhetens inbyggda programvara](azure-kinect-firmware-tool.md#check-device-firmware-version).

## <a name="related-topics"></a>Relaterade ämnen

- [Om Azure Kinect DK](about-azure-kinect-dk.md)
- [Konfigurera Azure Kinect DK](set-up-azure-kinect-dk.md)
- [Maskinvaruspecifikationer för Azure Kinect DK: Driftmiljö](hardware-specification.md#operating-environment)
- [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md)
- [Azure Kinect Viewer](azure-kinect-viewer.md)
- [Synkronisering mellan flera Azure Kinect DK-enheter](multi-camera-sync.md)
