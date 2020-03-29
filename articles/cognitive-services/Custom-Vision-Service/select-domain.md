---
title: Välj en domän för ett projekt med anpassad vision - Datorseende
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas hur du väljer en domän för projektet i tjänsten Custom Vision.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 1569b6081adad4cae0855f9adfb4e14e910bf819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899455"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Välj en domän för ett anpassat projekt

Från inställningsbladet för custom vision-projektet kan du välja en domän för projektet. Välj den domän som ligger närmast ditt scenario.

## <a name="image-classification"></a>Bildklassificering

|Domain|Syfte|
|---|---|
|__Allmänna__| Optimerad för ett brett spektrum av bildklassificeringsuppgifter. Om ingen av de andra domänerna är lämplig, eller om du är osäker på vilken domän du ska välja, väljer du den allmänna domänen.|
|__Mat__|Optimerad för fotografier av rätter som du skulle se dem på en restaurang meny. Om du vill klassificera fotografier av enskilda frukter eller grönsaker använder du domänen Mat.|
|__Landmärken__|Optimerad för igenkännbara landmärken, både naturliga och konstgjorda. Den här domänen fungerar bäst när landmärket syns tydligt i fotografiet. Denna domän fungerar även om landmärket är något hindras av människor framför den.|
|__Detaljhandeln__|Optimerad för bilder som finns i en shoppingkatalog eller shoppingwebbplats. Om du vill ha hög precision klassificera mellan klänningar, byxor och skjortor, använd denna domän.|
|__Kompakta domäner__| Optimerad för begränsningar av realtidsklassificering på kantenheter.|

## <a name="object-detection"></a>Objektidentifiering

|Domain|Syfte|
|---|---|
|__Allmänt__| Optimerad för ett brett spektrum av objektidentifieringsuppgifter. Om ingen av de andra domänerna är lämplig, eller om du är osäker på vilken domän du ska välja, väljer du den allmänna domänen.|
|__Logotyp__|Optimerad för att hitta varumärkeslogotyper i bilder.|
|__Produkter på hyllor__|Optimerad för att upptäcka och klassificera produkter på hyllor.|
|__Kompakta domäner__| Optimerad för begränsningar av objektidentifiering i realtid på kantenheter.|

## <a name="compact-domains"></a>Kompakta domäner

De modeller som genereras av kompakta domäner kan exporteras för att köras lokalt. Modellens prestanda varierar beroende på vald domän. I tabellen nedan rapporterar vi modellstorlek och slutledningstid på Intel \[Desktop\]CPU och NVidia GPU 1 . 

> [!NOTE]
> Dessa siffror inkluderar inte förbearbetning och efterbearbetning tid.

|Aktivitet|Domain|Modellstorlek|CPU-inferens tid|GPU slutledning tid|
|---|---|---|---|---|
|Klassificering|Allmän (kompakt)|5 MB|13 ms|5 ms|
|Objektidentifiering|Allmän (kompakt)|45 MB|35 ms|5 ms|
|Objektidentifiering|Allmänt (kompakt) [S1]|14 MB|27 ms|7 ms|

## <a name="vaidk-vision-ai-dev-kit"></a>VAIDK (Vision AI Dev Kit)

När en kompakt domän väljs ett extra alternativ "Exportfunktioner" tillhandahålls som gör det möjligt att skilja mellan "Grundläggande plattformar" och "Vision AI Dev Kit".

Under _Exportfunktioner_ är de två alternativen:

- Grundläggande plattformar (Tensorflow, CoreML, ONNX, etc.)
- Vision AI Dev Kit.

När _Vision AI Dev Kit_ har valts är de _allmänna,_ _landmärken_och _detaljhandelsdomänerna_ men inte de kompakta _områdena Mat_ tillgängliga för bildklassificering medan både allmänna _(kompakta)_ och _allmänna (kompakta) [S1]_ är tillgängliga för objektidentifiering.

>[!NOTE]
>__Allmän (kompakt)__ domän för objektidentifiering kräver särskild efterbearbetningslogik. Mer information finns i ett exempelskript i det exporterade zip-paketet. Om du behöver en modell utan efterbearbetningslogik använder du __Allmänt (kompakt) [S1]__.

>[!IMPORTANT]
>Det finns ingen garanti för att de exporterade modellerna ger exakt samma resultat som förutsägelse-API:et i molnet. Liten skillnad i den löpande plattformen eller förbehandlingsimplementeringen kan orsaka större skillnad i modellutgångarna. Mer information om förbehandlingslogiken finns i [det här dokumentet](python-tutorial.md).

\[1\] Intel Xeon E5-2690 CPU och NVIDIA Tesla M60
