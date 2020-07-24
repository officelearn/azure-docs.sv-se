---
title: Data platser för virtuella Windows-datorer sjunker 2019 – Azure
description: En kort översikt över vilka platser som Windows Virtual Desktops data och metadata lagras i.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fa998ddf8225475bbcf657ed9feee951263d2743
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077581"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Data platser för virtuella Windows-datorer

>[!IMPORTANT]
>Det här innehållet gäller för hösten 2019-versionen som inte stöder Azure Resource Manager virtuella Windows Desktop-objekt. Om du försöker hantera Azure Resource Manager virtuella Windows-skrivbordet som introduceras i våren 2020-uppdateringen, se [den här artikeln](../data-locations.md).

Det virtuella Windows-skrivbordet är för närvarande tillgängligt för alla geografiska platser. Först kan tjänste-metadata endast lagras i geografin USA (US). Administratörer kan välja plats för att lagra användar data när de skapar värdar för virtuella datorer och associerade tjänster, till exempel fil servrar. Lär dig mer om Azures geografiska områden i [Azure datacenter-kartan](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft kontrollerar eller begränsar inte de regioner där du eller dina användare kan komma åt dina användar-och appdata.

>[!IMPORTANT]
>Windows Virtual Desktop lagrar global metadatainformation som klient namn, värdnamn, namn på App-grupp och användarens huvud namn i ett Data Center som finns i USA. Lagrade metadata är krypterade i vila och geo-redundanta speglar behålls i USA. Alla kunddata, till exempel appinställningar och användar data, finns på den plats kunden väljer och hanteras inte av tjänsten.

Tjänste-metadata replikeras i USA för katastrof återställning.