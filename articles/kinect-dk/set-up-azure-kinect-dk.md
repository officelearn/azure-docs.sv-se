---
title: Snabbstart – Konfigurera Azure Kinect DK
description: Den här snabbstarten beskriver hur du konfigurerar Azure Kinect DK-maskinvaran
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: quickstart
ms.date: 02/12/2020
keywords: azure, kinect, dev kit, azure dk, set up, hardware, quick, usb, power, viewer, sensor, streaming, setup, SDK, firmware
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: b33565f319de77385c966f742fa52391f4869eed
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211286"
---
# <a name="quickstart-set-up-your-azure-kinect-dk"></a>Snabbstart: Konfigurera Azure Kinect DK

Den här snabbstarten beskriver hur du konfigurerar Azure Kinect DK. Vi förklarar hur du testar visualiseringen av sensorströmmar och hur du använder [Azure Kinect Viewer](azure-kinect-viewer.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="system-requirements"></a>Systemkrav

Gå igenom [systemkraven](system-requirements.md) och kontrollera att värddatorns konfiguration uppfyller minimikraven för Azure Kinect DK.

## <a name="set-up-hardware"></a>Konfigurera maskinvara

> [!NOTE]
> Avlägsna skyddsfilmen på kameran innan du använder enheten.

1. Sätt i strömkontakten i uttaget på enhetens baksida. Anslut USB-strömadaptern till den andra änden av kabeln och sätt i adaptern i ett eluttag.
2. Anslut USB-datakabeln till enheten och sedan till en USB 3.0-port på datorn.
   >[!NOTE]
   >För bästa resultat ansluter du kabeln direkt till enheten och till datorn. Undvik att använda förlängningssladdar eller extra adaptrar.

3. Kontrollera att strömindikatorn (bredvid USB-kabeln) lyser med fast vitt sken.
  
   Det tar några sekunder att starta enheten. Enheten är redo att användas när strömningsindikatorn på framsidan släcks.  

   Mer information om strömindikatorn finns i [Vad betyder lampan?](hardware-specification.md#what-does-the-light-mean)

    ![Fullständiga enhetsfunktioner](./media/quickstarts/full-device-features.png)

## <a name="download-the-sdk"></a>Ladda ned SDK:n

1. Välj länken för att [ladda ned SDK:n](sensor-sdk-download.md).
2. Installera SDK:n på datorn.

## <a name="update-firmware"></a>Uppdatera den inbyggda programvaran

För att fungera korrekt kräver SDK:n den senaste versionen av enhetens inbyggda programvara. Du kan kontrollera och uppdatera den inbyggda programvaran genom att följa stegen i [Uppdatera den inbyggda programvaran för Azure Kinect DK](update-device-firmware.md).

## <a name="verify-that-the-device-streams-data"></a>Kontrollera att enheten strömmar data

1. Starta [Azure Kinect Viewer](azure-kinect-viewer.md). Du kan starta verktyget med någon av följande metoder:
   - Du kan starta visningsprogrammet från kommandoraden eller genom att dubbelklicka på den körbara filen. Filen, `k4aviewer.exe`, finns i SDK-verktygskatalogen (till exempel `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`, där `X.Y.Z` är den installerade SDK-versionen).
   - Du kan starta Azure Kinect Viewer från enhetens **startmeny**.
2. Välj **Öppna enhet** > **Start** i Azure Kinect Viewer.

    ![Azure Kinect Viewer](./media/quickstarts/viewer.png)

3. Kontrollera att verktyget visualiserar alla sensorströmmar:
   - Djupkamera
   - Färgkamera
   - IR-kamera
   - IMU
   - Mikrofoner

    ![Visualiseringsverktyg](./media/quickstarts/visualization-tool.png)

Konfigurationen av Azure Kinect DK är klar. Nu kan du börja utveckla ditt program eller integrera tjänster.

Om du har problem läser du [felsökningsavsnittet](troubleshooting.md).

## <a name="see-also"></a>Se även

- [Information om Azure Kinect DK-maskinvara](hardware-specification.md)
- [Uppdatera enhetens inbyggda programvara](update-device-firmware.md)
- Läs mer om [Azure Kinect Viewer](azure-kinect-viewer.md)

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Azure Kinect DK kan du lära dig att
> [!div class="nextstepaction"]
> [Registrera sensorströmmar till en fil](record-sensor-streams-file.md)
