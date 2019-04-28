---
title: Använda Windows-klientavbildningar i Azure | Microsoft Docs
description: Hur du använder Visual Studio-prenumerationsförmåner för att distribuera Windows 7, Windows 8 eller Windows 10 i Azure för utveckling och testning
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 91c3880a-cede-44f1-ae25-f8f9f5b6eaa4
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: f791b17f2729af3efd2dff5d7884a168f8377154
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61403121"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Använda Windows-klienten i Azure för utveckling och testning
Du kan använda Windows 7, Windows 8 eller Windows 10 Enterprise (x64) i Azure för utveckling och testning förutsatt att du har en lämplig Visual Studio (tidigare MSDN)-prenumeration. Den här artikeln beskriver krav för berättigande för att köra Windows 7, Windows 8.1, Windows 10 Enterprise i Azure och användning av följande avbildningar i Azure-galleriet.

![Bild information från Azure portal](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> Windows 10 Pro och Windows 10 Pro N avbildningar i Azure-galleriet, finns i [hur du distribuerar Windows 10 på Azure med Multitenant som är värd för rättigheter](windows-desktop-multitenant-hosting-deployment.md)
>![Pro avbildningsdetaljerna från Azure portal](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Kvalificering för prenumeration
Aktiva Visual Studio-prenumeranter (personer som har skaffat en licens för Visual Studio-prenumeration) kan använda Windows-klient för utveckling och testning. Windows-klienten kan användas på maskinvara och virtuella Azure-datorer som körs i alla typer av Azure-prenumeration. Windows-klienten kan inte distribueras till eller användas på Azure för normal produktion eller användas av personer som inte är aktiva Visual Studio-prenumeranter.

För din bekvämlighet vissa Windows 10-avbildningar är tillgängliga från Azure-galleriet i [berättigade utveckling/testning erbjuder](#eligible-offers). Visual Studio-prenumeranter i någon typ av erbjudande kan också [rätt förbereda och skapa](prepare-for-upload-vhd-image.md) en 64-bitars Windows 7, Windows 8 eller Windows 10-avbildning och sedan [Överför till Azure](upload-generalized-managed.md). Användningen är begränsad till utveckling och testning av aktiva Visual Studio-prenumeranter.

## <a name="eligible-offers"></a>Giltiga erbjudanden
I följande tabell beskrivs erbjudande-ID som är berättigade att distribuera Windows 10 via Azure-galleriet. Windows 10-bilder är bara synliga för följande erbjudanden. Visual Studio-prenumeranter som ska köra Windows-klienten i en annan avtalsform kräver att du [rätt förbereda och skapa](prepare-for-upload-vhd-image.md) en 64-bitars Windows 7, Windows 8 eller Windows 10-avbildning och [sedan överför till Azure](upload-generalized-managed.md).

| Erbjudandets namn | Erbjudandets nummer | Tillgängliga client-avbildningar |
|:--- |:---:|:---:|
| [Betala per användning – utveckling/testning](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Prenumeranter på Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Visual Studio Professional-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Visual Studio Test Professional-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium med MSDN (förmån)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Visual Studio Enterprise-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Prenumeranter på Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise – utveckling/testning](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Kontrollera din Azure-prenumeration
Om du inte känner till ditt erbjudande-ID måste hämta du den via Azure-portalen i något av följande två sätt:  

- På den *prenumerationer* fönster:

  ![Erbjudande-ID-information från Azure portal](./media/client-images/offer-id-azure-portal.png) 

- Alternativt klickar du på **fakturering** och klicka sedan på ditt prenumerations-ID. Erbjudande-ID visas i den *fakturering* fönster.

Du kan också visa erbjudande-ID från den [”Subscriptions” fliken](https://account.windowsazure.com/Subscriptions) Azure-kontoportalen:

![Erbjudande-ID-information från Azure-kontoportalen](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Nästa steg
Du kan nu distribuera dina virtuella datorer med hjälp av [PowerShell](quick-create-powershell.md), [Resource Manager-mallar](ps-template.md), eller [Visual Studio](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

