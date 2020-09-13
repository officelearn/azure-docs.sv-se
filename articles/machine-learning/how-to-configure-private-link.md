---
title: Konfigurera en privat slut punkt (för hands version)
titleSuffix: Azure Machine Learning
description: Använd Azures privata länk för att få säker åtkomst till din Azure Machine Learning-arbetsyta från ett virtuellt nätverk.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/03/2020
ms.openlocfilehash: b9b256a3d3c1636cac55bcb1790182240d2199c0
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661878"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Konfigurera en privat Azure-länk för en Azure Machine Learning arbets yta (förhands granskning)

I det här dokumentet får du lära dig hur du använder en privat Azure-länk med din Azure Machine Learning-arbetsyta. Information om hur du konfigurerar ett virtuellt nätverk för Azure Machine Learning finns i [Översikt över virtuell nätverks isolering och sekretess](how-to-network-security-overview.md)

> [!IMPORTANT]
> Att använda Azures privata länk med Azure Machine Learning-arbetsytan är för närvarande en offentlig för hands version. Den här funktionen är endast tillgänglig i regionerna **USA, östra**, **södra centrala** USA och **västra USA 2** . Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med Azures privata länk kan du ansluta till din arbets yta med en privat slut punkt. Den privata slut punkten är en uppsättning privata IP-adresser i det virtuella nätverket. Du kan sedan begränsa åtkomsten till din arbets yta så att den bara sker över de privata IP-adresserna. Privat länk hjälper till att minska risken för data exfiltrering. Mer information om privata slut punkter finns i artikeln [Azure Private Link](/azure/private-link/private-link-overview) .

> [!IMPORTANT]
> Azures privata länk påverkar inte Azures kontroll plan (hanterings åtgärder) som att ta bort arbets ytan eller hantera beräknings resurser. Till exempel skapa, uppdatera eller ta bort ett beräknings mål. De här åtgärderna utförs via det offentliga Internet som normalt. Data Plans åtgärder som att använda Azure Machine Learning Studio, API: er (inklusive publicerade pipeliner) eller SDK använder den privata slut punkten.
>
> Du kan stöta på problem vid försök att komma åt den privata slut punkten för din arbets yta om du använder Mozilla Firefox. Det här problemet kan vara relaterat till DNS via HTTPS i Mozilla. Vi rekommenderar att du använder Microsoft Edge av Google Chrome som en lösning.

> [!TIP]
> Azure Machine Learning beräknings instans kan användas med en arbets yta och en privat slut punkt. Den här funktionen är för närvarande en offentlig för hands version i regionerna **USA, östra**, **södra centrala** USA och **västra USA 2** .

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Skapa en arbets yta som använder en privat slut punkt

> [!IMPORTANT]
> För närvarande stöder vi bara aktivering av en privat slut punkt när du skapar en ny Azure Machine Learning-arbetsyta.

Mallen i [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) kan användas för att skapa en arbets yta med en privat slut punkt.

Information om hur du använder den här mallen, inklusive privata slut punkter, finns i [använda en Azure Resource Manager mall för att skapa en arbets yta för Azure Machine Learning](how-to-create-workspace-template.md).

## <a name="using-a-workspace-over-a-private-endpoint"></a>Använda en arbets yta över en privat slut punkt

Eftersom kommunikation till arbets ytan endast tillåts från det virtuella nätverket måste alla utvecklings miljöer som använder arbets ytan vara medlemmar i det virtuella nätverket. Till exempel en virtuell dator i det virtuella nätverket.

> [!IMPORTANT]
> För att undvika tillfälligt avbrott i anslutningen rekommenderar Microsoft att tömma DNS-cachen på datorer som ansluter till arbets ytan när du har aktiverat privat länk. 

Information om Azure Virtual Machines finns i Virtual Machines- [dokumentationen](/azure/virtual-machines/).


## <a name="next-steps"></a>Nästa steg

Mer information om hur du skyddar din Azure Machine Learning-arbetsyta finns i artikeln om [isolering av virtuella nätverk och sekretess översikt](how-to-network-security-overview.md) .