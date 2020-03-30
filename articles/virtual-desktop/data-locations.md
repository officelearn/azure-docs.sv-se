---
title: Dataplatser för Windows Virtual Desktop – Azure
description: En kort översikt över vilka platser Windows Virtual Desktop data och metadata lagras i.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5f48586f38c310c77c61f470d3e2fbc819c33f71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128052"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Dataplatser för Windows Virtual Desktop

Windows Virtual Desktop är för närvarande tillgängligt för alla geografiska platser. Inledningsvis kan tjänstmetadata endast lagras i USA:s geografi. Administratörer kan välja plats för att lagra användardata när de skapar virtuella värdpooldatorer och associerade tjänster, till exempel filservrar. Läs mer om Azure-geografiska områden på [Azure datacenter-kartan](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft styr eller begränsar inte de regioner där du eller dina användare kan komma åt dina användar- och appspecifika data.

>[!IMPORTANT]
>Windows Virtual Desktop lagrar global metadatainformation som klientnamn, värdpoolnamn, appgruppnamn och användarnamn i ett datacenter i USA. De lagrade metadata krypteras i vila och geo-redundanta speglar underhålls inom USA. Alla kunddata, till exempel appinställningar och användardata, finns på den plats som kunden väljer och hanteras inte av tjänsten.

Tjänstmetadata replikeras i USA för haveriberedskap.