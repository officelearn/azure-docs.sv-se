---
title: Azure Machine Learning och FPGAs
description: Lär dig mer om att påskynda modeller och djupa neurala nätverk med FPGAs.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: 493b3f8de617146af534349e18ed49b83c46dee8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-a-field-programmable-gate-array-fpga"></a>Vad är en fältet programmable gate-matris (då FPGA)?

Fältet programmable gate matriser (då FPGA) är integrerade kretsar som kan ändras efter behov. Du kan ändra en då FPGA som behövs för att implementera nya logik. Azure Machine Learning maskinvara snabbare modeller kan du distribuera tränade modeller till FPGAs i Azure-molnet.

Den här funktionen tillhandahålls av projektet Brainwave som hanterar översätta djupa neurala nätverk (DNN) till ett då FPGA program. 

## <a name="why-use-an-fpga"></a>Varför ska jag använda en då FPGA?

FPGAs ger mycket låg latens och är särskilt effektiva för resultatfunktioner data på batchstorlek en (det finns inga krav för en stor Gruppstorlek).  De är kostnadseffektiv och tillgängliga i Azure.  Projektet Brainwave kan parallelize före utbildade DNNs över dessa FPGAs att skala upp din tjänst.

## <a name="next-steps"></a>Nästa steg

[Distribuera en modell som en webbtjänst på en då FPGA](how-to-deploy-fpga-web-service.md)

[Lär dig hur du installerar FPGA SDK](reference-fpga-package-overview.md)