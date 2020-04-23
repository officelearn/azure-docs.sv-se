---
title: Använda Windows-klientavbildningar i Azure
description: Så här använder du prenumerationsförmåner för Visual Studio för att distribuera Windows 7, Windows 8 eller Windows 10 i Azure för utvecklings-/testscenarier
author: cynthn
ms.subservice: imaging
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 51e4862fe55428f112841ef7176cdb916653de0b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083282"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Använda en Windows-klient i Azure för dev/test-scenarier
Du kan använda Windows 7, Windows 8 eller Windows 10 Enterprise (x64) i Azure för utvecklings-/testscenarier förutsatt att du har en lämplig Visual Studio-prenumeration (tidigare MSDN). I den här artikeln beskrivs berättigandekraven för att köra Windows 7, Windows 8.1, Windows 10 Enterprise i Azure och användning av följande Azure Gallery-avbildningar.

![Avbildningsinformation från Azure-portalen](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> För Windows 10 Pro och Windows 10 Pro N-avbildning i Azure Gallery läser du Så här distribuerar du Windows 10 på Azure med information om [multitenant hosting rights](windows-desktop-multitenant-hosting-deployment.md)
>![pro från Azure-portalen](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Behörighet för prenumeration
Aktiva Visual Studio-prenumeranter (personer som har skaffat en Prenumerationslicens för Visual Studio) kan använda Windows-klienten i utvecklings- och testningssyfte. Windows-klienten kan användas på din egen maskinvara och virtuella Azure-datorer som körs i alla typer av Azure-prenumeration. Windows-klienten kanske inte distribueras till eller används på Azure för normal produktionsanvändning eller används av personer som inte är aktiva Visual Studio-prenumeranter.

För din bekvämlighet är vissa Windows 10-avbildningar tillgängliga från Azure Gallery i [kvalificerade utvecklings-/testerbjudanden](#eligible-offers). Visual Studio-prenumeranter inom alla typer av erbjudanden kan också [förbereda och skapa](prepare-for-upload-vhd-image.md) en 64-bitars Windows 7-, Windows 8- eller Windows 10-avbildning och sedan ladda upp till [Azure](upload-generalized-managed.md). Användningen är begränsad till utveckling/test av aktiva Visual Studio-prenumeranter.

## <a name="eligible-offers"></a>Berättigade erbjudanden
I följande tabell beskrivs de erbjudande-ID:er som är kvalificerade att distribuera Windows 10 via Azure Gallery. Windows 10-bilderna är endast synliga för följande erbjudanden. Visual Studio-prenumeranter som behöver köra Windows-klienten i en annan erbjudandetyp kräver att du [förbereder och skapar](prepare-for-upload-vhd-image.md) en 64-bitars Windows 7-, Windows 8- eller Windows 10-avbildning och sedan laddar upp till [Azure](upload-generalized-managed.md).

| Erbjudandets namn | Erbjudandenummer | Tillgängliga klientavbildningar |
|:--- |:---:|:---:|
| [Dev/Test – betala per användning](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Visual Studio Enterprise-prenumeranter (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P (på andra sätt) |Windows 10 |
| [Visual Studio Professional-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P (0059P) |Windows 10 |
| [Visual Studio Test Professional-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P (0060P) |Windows 10 |
| [Visual Studio Premium med MSDN (förmån)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P (0061P) |Windows 10 |
| [Visual Studio Enterprise-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P (0063P) |Windows 10 |
| [Visual Studio Enterprise-prenumeranter (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P (0064P) |Windows 10 |
| [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Kontrollera din Azure-prenumeration
Om du inte känner till ditt erbjudande-ID kan du hämta det via Azure-portalen på något av följande två sätt:  

- I fönstret *Prenumerationer:*

  ![Erbjudande-ID-information från Azure-portalen](./media/client-images/offer-id-azure-portal.png) 

- Du kan också klicka på **Fakturering** och sedan på ditt prenumerations-ID. Erbjudande-ID:t visas i *fönstret Fakturering.*

Du kan också visa erbjudande-ID:t från [fliken Prenumerationer](https://account.windowsazure.com/Subscriptions) på Azure Account-portalen:

![Erbjudande-ID-information från Azure-kontoportalen](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Nästa steg
Du kan nu distribuera dina virtuella datorer med [PowerShell,](quick-create-powershell.md) [Resource Manager-mallar](ps-template.md)eller [Visual Studio](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

