---
title: Data platser för Windows Virtual Desktop fjäder 2020 – Azure
description: En kort översikt över vilka platser som Windows Virtual Desktops data och metadata lagras i.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 96072b8008f42f58ec6a3aff32728c087b8e3290
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080581"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Platser för data och metadata för virtuella Windows-datorer

>[!IMPORTANT]
>Det här innehållet gäller för våren 2020-uppdateringen med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder den virtuella Windows-datorn med version 2019 utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/data-locations-2019.md).
>
> Den virtuella Windows-skrivbordets våren 2020-uppdateringen är för närvarande en offentlig för hands version. Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. 
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Det virtuella Windows-skrivbordet är för närvarande tillgängligt för alla geografiska platser. Administratörer kan välja plats för att lagra användar data när de skapar värdar för virtuella datorer och associerade tjänster, till exempel fil servrar. Lär dig mer om Azures geografiska områden i [Azure datacenter-kartan](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft kontrollerar eller begränsar inte de regioner där du eller dina användare kan komma åt dina användar-och appdata.

>[!IMPORTANT]
>Windows Virtual Desktop lagrar global metadatainformation som klient namn, värdnamn, namn på App-grupp och användarens huvud namn i ett Data Center. Varje gång en kund skapar ett tjänst objekt måste de ange en plats för tjänst objekt. Platsen som de anger avgör var metadata för objektet ska lagras. Kunden väljer en Azure-region och metadata kommer att lagras i relaterad geografi. En lista över alla Azure-regioner och relaterade geografiska områden finns i [Azure](https://azure.microsoft.com/global-infrastructure/geographies/)-geografiska områden.

För tillfället stöder vi bara lagring av metadata i den USA (US) Azure geografi. Lagrade metadata är krypterade i vila och geo-redundanta speglar behålls i geografien. Alla kunddata, till exempel appinställningar och användar data, finns på den plats kunden väljer och hanteras inte av tjänsten. Fler geografiska områden kommer att bli tillgängliga när tjänsten växer.

Tjänste-metadata replikeras i Azure geografi för haveri beredskap.