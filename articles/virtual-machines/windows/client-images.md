---
title: Använda Windows Client-avbildningar i Azure
description: Så här använder du fördelarna med Visual Studio-prenumerationer för att distribuera Windows 7, Windows 8 eller Windows 10 i Azure för utveckling/testnings scenarier
author: cynthn
ms.subservice: imaging
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 26bfd9a65cd3e6d36b8190e87d382543cc7f0f60
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292061"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Använda en Windows-klient i Azure för dev/test-scenarier
Du kan använda Windows 7, Windows 8 eller Windows 10 Enterprise (x64) i Azure för utveckling/testning-scenarier förutsatt att du har en lämplig Visual Studio-prenumeration (tidigare MSDN). Den här artikeln beskriver behörighets kraven för att köra Windows 7, Windows 8,1, Windows 10 Enterprise i Azure och använda följande Azure Gallery-avbildningar.

![Avbildnings information från Azure Portal](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> För Windows 10 Pro-och Windows 10 Pro N-bilder i Azure-galleriet kan du läsa om [hur du distribuerar Windows 10 på Azure med värd rättigheter](windows-desktop-multitenant-hosting-deployment.md)Pro-avbildningar för flera innehavare 
> ![ från Azure Portal](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Prenumerations berättigande
Aktiva Visual Studio-prenumeranter (personer som har köpt en prenumerations licens för Visual Studio) kan använda Windows-klienten för utvecklings-och testnings ändamål. Windows-klienten kan användas på din egen maskin vara och virtuella Azure-datorer som körs i vilken typ av Azure-prenumeration som helst. Windows-klienten kan inte distribueras till eller användas på Azure för normal produktions användning eller används av personer som inte är aktiva Visual Studio-prenumeranter.

För din bekvämlighet är vissa Windows 10-avbildningar tillgängliga från Azure-galleriet inom [berättigade erbjudanden om utveckling/testning](#eligible-offers). Visual Studio-prenumeranter inom vilken typ av erbjudande som helst kan också [förbereda och skapa](prepare-for-upload-vhd-image.md) en 64-bitars Windows 7-, Windows 8-eller Windows 10-avbildning och sedan [Ladda upp till Azure](upload-generalized-managed.md). Användningen är begränsad till utveckling/testning av aktiva Visual Studio-prenumeranter.

## <a name="eligible-offers"></a>Berättigade erbjudanden
I följande tabell beskrivs de ID: n som är kvalificerade för att distribuera Windows 10 via Azure-galleriet. Windows 10-avbildningarna är bara synliga för följande erbjudanden. Visual Studio-prenumeranter som behöver köra Windows-klienten i en annan erbjudande typ kräver att du [förbereder och skapar](prepare-for-upload-vhd-image.md) en 64-bitars Windows 7-, Windows 8-eller Windows 10-avbildning och [laddar sedan upp till Azure](upload-generalized-managed.md).

| Erbjudandets namn | Erbjudande nummer | Tillgängliga klient avbildningar |
|:--- |:---:|:---:|
| [Dev/Test – betala per användning](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Prenumeranter på Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Visual Studio Professional-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Visual Studio Test Professional-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium med MSDN (förmån)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Visual Studio Enterprise-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Visual Studio Enterprise-prenumeranter (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Kontrol lera din Azure-prenumeration
Om du inte känner till ditt erbjudande-ID kan du hämta det via Azure Portal på något av följande två sätt:  

- I fönstret *prenumerationer* :

  ![Erbjud ID-information från Azure Portal](./media/client-images/offer-id-azure-portal.png) 

- Du kan också klicka på **fakturering** och sedan på ditt PRENUMERATIONS-ID. Erbjudande-ID visas i *fakturerings* fönstret.

Du kan också Visa erbjudande-ID: t från [fliken prenumerationer](https://account.windowsazure.com/Subscriptions) i Azure-konto portalen:

![Erbjud ID-information från Azure-konto portalen](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Nästa steg
Nu kan du distribuera dina virtuella datorer med hjälp av [PowerShell](quick-create-powershell.md), [Resource Manager-mallar](ps-template.md)eller [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
