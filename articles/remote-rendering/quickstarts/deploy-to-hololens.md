---
title: Distribuera Unity-exempel till HoloLens
description: Snabbstart som visar hur man får Unity-provet på HoloLens
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: 75b6629ea924ec17888ec2f981ff85ed917434c9
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679739"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>Snabbstart: Distribuera Unity-exempel till HoloLens

Den här snabbstarten beskriver hur du distribuerar och kör snabbstartsprovappen för Unity till en HoloLens 2.

I den här snabbstarten får du lära dig hur du:

> [!div class="checklist"]
>
>* Skapa exempelappen snabbstart för HoloLens
>* Distribuera exemplet till enheten
>* Kör exemplet på enheten

## <a name="prerequisites"></a>Krav

I den här snabbstarten distribuerar vi exempelprojektet från [Snabbstart: Rendera en modell med Unity](render-model.md).

Kontrollera att dina autentiseringsuppgifter sparas korrekt med scenen och att du kan ansluta till en session inifrån Unity-redigeraren.

## <a name="build-the-sample-project"></a>Skapa exempelprojektet

1. Öppna *inställningar för > skapa*.
1. Ändra *plattform* till **universell Windows-plattform**
1. Ange *målenhet* på **HoloLens**
1. Ställ in *arkitektur* till **ARM64**
1. Ange *inbyggtyp* till **D3D-projektbyggen** ![](./media/unity-build-settings.png)
1. Välj **Växla till plattform**
1. När du trycker på **Bygg** (eller "Bygg och kör") kommer du att bli ombedd att välja någon mapp där lösningen ska lagras
1. Öppna den genererade **Quickstart.sln** med Visual Studio
1. Ändra konfigurationen till **Release** och **ARM64**
1. Växla felsökningsläge till konfiguration **av fjärrmaskinslösning** ![](media/unity-deploy-config.png)
1. Bygg lösningen (F7)
1. För projektet "Snabbstart" går du till *Egenskaper > Felsökning*
    1. Kontrollera att *konfigurationsversionen* är aktiv
    1. Ange *felsökning för start* på **Fjärrdator**
    1. Ändra *maskinnamn* till IP för **dina HoleLens**

## <a name="launch-the-sample-project"></a>Starta exempelprojektet

1. Anslut HoloLens med en USB-kabel till datorn.
1. Starta felsökningsprogrammet i Visual Studio (F5). Appen distribueras automatiskt till enheten.

Exempelappen ska starta och sedan starta en ny session. Efter ett tag är sessionen klar och den fjärrrensade modellen visas framför dig.
Om du vill starta exemplet en andra gång senare kan du också hitta det från Startmenyn HoloLens nu.

## <a name="next-steps"></a>Nästa steg

I nästa snabbstart kommer vi att ta en titt på att konvertera en anpassad modell.

> [!div class="nextstepaction"]
> [Snabbstart: Konvertera en modell för rendering](convert-model.md)
