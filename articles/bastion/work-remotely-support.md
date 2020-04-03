---
title: 'Arbeta på distans med Bastion: Azure Bastion'
description: På den här sidan beskrivs hur du kan utnyttja Azure Bastion för att fjärrstyra arbetet på grund av COVID-19-pandemin.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 182195190fed70b46185f98f595de6b6c32bbffe
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619386"
---
# <a name="working-remotely-using-azure-bastion"></a>Arbeta på distans med Azure Bastion

Azure Bastion spelar en central roll för att stödja fjärrarbetsscenarier genom att tillåta användare med internetanslutning att komma åt virtuella Azure-datorer. I synnerhet gör det det möjligt för IT-administratörer att hantera sina program som körs på Azure när som helst och var som helst runt om i världen.

>[!NOTE]
>I den här artikeln beskrivs hur du kan utnyttja Azure Bastion, Azure, Microsoft-nätverket och Azure-partnerekosystemet för att arbeta på distans och minska nätverksproblem som du står inför på grund av COVID-19-krisen.
>

## <a name="securely-access-virtual-machines"></a>Få åtkomst till virtuella datorer på ett säkert sätt

Azure Bastion tillhandahåller säker och sömlös RDP/SSH-anslutning till virtuella datorer i det virtuella Azure-nätverket, direkt i Azure-portalen, utan att använda en offentlig IP-adress. Mer information om Azure Bastion-arkitekturen och viktiga funktioner finns i [Vad är Azure Bastion](bastion-overview.md).

Azure Bastion distribueras per virtuellt nätverk, vilket innebär att företag kan konfigurera och hantera en Azure Bastion för att snabbt stödja fjärranvändaråtkomst till virtuella datorer i ett virtuellt Azure-nätverk. Mer information om hur du skapar och hanterar Azure Bastion finns i [Skapa en skyddsvärd](bastion-create-host-portal.md).

## <a name="next-steps"></a>Nästa steg

* Konfigurera Azure Bastion med [Azure-portalen,](bastion-create-host-portal.md) [PowerShell](bastion-create-host-powershell.md)eller Azure CLI.

* Läs [vanliga frågor om Bastion](bastion-faq.md) för ytterligare information.
