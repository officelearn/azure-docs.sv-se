---
title: Distribuera Unity-exempel till skrivbordet
description: Snabbstart som visar hur du får Unity-exemplet på en stationär dator
author: christophermanthei
ms.author: chmant
ms.date: 03/20/2020
ms.topic: quickstart
ms.openlocfilehash: 93ec25123f60083d4b31cf54592b1a1c0dacab69
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679760"
---
# <a name="quickstart-deploy-unity-sample-to-desktop"></a>Snabbstart: Distribuera Unity-exempel till skrivbordet

Den här snabbstarten beskriver hur du distribuerar och kör snabbstartsprovappen för Unity till en stationär dator.

I den här snabbstarten får du lära dig hur du:

> [!div class="checklist"]
>
>* Skapa exempelappen snabbstart för skrivbordet
>* Distribuera exemplet till en dator
>* Kör exemplet på en dator

## <a name="prerequisites"></a>Krav

I den här snabbstarten distribuerar vi exempelprojektet från [Snabbstart: Rendera en modell med Unity](render-model.md).

Kontrollera att dina autentiseringsuppgifter sparas korrekt med scenen och att du kan ansluta till en session inifrån Unity-redigeraren.

## <a name="disable-virtual-reality-support"></a>Inaktivera stöd för virtuell verklighet

Endast platta skrivbordsappar stöds för närvarande på skrivbordet så VR-stöd måste inaktiveras.

1. Öppna *Redigera > Projektinställningar...*
1. Välj **Spelare** till vänster.
1. Välj **fliken Inställningar för universella inställningar för Windows-plattformen.**
1. Expandera **XR-inställningarna**.
1. Inaktivera **virtual reality-stöd**.
    ![spelarinställningar](./media/unity-disable-xr.png)
1. Expandera **Publiceringsinställningarna**ovan *än XR.*
1. Kontrollera att skrivbordet är markerat **i** **enhetsfamiljer som stöds.**

## <a name="build-the-sample-project"></a>Skapa exempelprojektet

1. Öppna *inställningar för > skapa*.
1. Ändra *plattform* till **universell Windows-plattform**.
1. Ställ in *målenheten* på **PC**.
1. Ange *arkitektur* till **x86**.
1. Ange *byggtyp* till **D3D Project**.
  ![Bygg inställningar](./media/unity-build-settings-pc.png)
1. Välj **Växla till plattform**.
1. När du trycker på **Bygg** (eller "Bygg och kör" kommer du att bli ombedd att välja någon mapp där lösningen ska lagras.
1. Öppna den genererade **Quickstart.sln** med Visual Studio.
1. Ändra konfigurationen till **Release** och **x86**.
1. Växla felsökningsläge till **Lokal dator**.
  ![Konfiguration av lösning](./media/unity-deploy-config-pc.png)
1. Bygg lösningen (F7).

## <a name="launch-the-sample-project"></a>Starta exempelprojektet

Starta felsökningsprogrammet i Visual Studio (F5). Appen distribueras automatiskt till datorn.

Exempelappen ska starta och sedan starta en ny session. Efter ett tag är sessionen klar och den fjärrrensade modellen visas framför dig.
Om du vill starta exemplet en andra gång senare kan du också hitta det från Start-menyn nu.

## <a name="next-steps"></a>Nästa steg

I nästa snabbstart kommer vi att ta en titt på att konvertera en anpassad modell.

> [!div class="nextstepaction"]
> [Snabbstart: Konvertera en modell för rendering](convert-model.md)
