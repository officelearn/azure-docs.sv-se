---
title: Distribuera Unity-exempel till HoloLens
description: Snabb start som visar hur du hämtar Unity-exemplet på HoloLens
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: 75b6629ea924ec17888ec2f981ff85ed917434c9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80679739"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>Snabb start: Distribuera Unity-exempel till HoloLens

Den här snabb starten beskriver hur du distribuerar och kör appen snabb starts exempel för enhet till en HoloLens 2.

I den här snabb starten får du lära dig att:

> [!div class="checklist"]
>
>* Bygg appen snabb starts exempel för HoloLens
>* Distribuera exemplet till enheten
>* Kör exemplet på enheten

## <a name="prerequisites"></a>Krav

I den här snabb starten ska vi distribuera exempelprojektet från [snabb start: återge en modell med Unity](render-model.md).

Se till att dina autentiseringsuppgifter sparas korrekt med scenen och att du kan ansluta till en session inifrån Unit-redigeraren.

## <a name="build-the-sample-project"></a>Bygg exempelprojektet

1. Öppna *fil > versions inställningar*.
1. Ändra *plattform* till **universell Windows-plattform**
1. Ställ in *mål enheten* på **HoloLens**
1. Ange *arkitektur* till **arm64**
1. Ange *build-typ* till **D3D Project** ![build-inställningar](./media/unity-build-settings.png)
1. Välj **Växla till plattform**
1. När du trycker på **skapa** (eller "skapa och kör") kommer du att uppmanas att välja en mapp där lösningen ska lagras
1. Öppna den genererade **snabb starten. SLN** med Visual Studio
1. Ändra konfigurationen till **versions** -och **arm64**
1. Växla fel söknings läge till konfiguration av **fjärrdator** ![-lösning](media/unity-deploy-config.png)
1. Bygg lösningen (F7)
1. För projektet "snabb start" går du till *egenskaper > fel sökning*
    1. Kontrol lera att konfigurations *versionen* är aktiv
    1. Ange *att fel sökning ska startas* till **fjärrdatorn**
    1. Ändra *dator namn* till **IP-adressen för din HoleLens**

## <a name="launch-the-sample-project"></a>Starta exempelprojektet

1. Anslut HoloLens med en USB-kabel till din dator.
1. Starta fel söknings programmet i Visual Studio (F5). Appen installeras automatiskt på enheten.

Exempel appen bör starta och sedan starta en ny session. Efter en stund är sessionen klar och den fjärranslutna modellen visas framför dig.
Om du vill starta exemplet en gång senare kan du även hitta det från HoloLens-startmenyn.

## <a name="next-steps"></a>Nästa steg

I nästa snabb start tar vi en titt på att konvertera en anpassad modell.

> [!div class="nextstepaction"]
> [Snabb start: konvertera en modell för åter givning](convert-model.md)
